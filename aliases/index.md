---
layout: default
title: Aliases
summary: How aliases work in OpenLint today, what they can and can't do, with examples tested against OpenLint 1.0.0.
crumbs:
  - name: aliases
    url: /aliases/
---
# Aliases

An alias gives a name to one or more JSONPath expressions, so several rules can share the same selector. Write the path once, in one place, and when the document format changes you fix it there instead of in every rule.

This page describes how aliases work in OpenLint today. Every example was run against OpenLint 1.0.0. The formal definition is in the specification, under [aliases](https://spec.openlint.org/v1/draft/#aliases) and [selecting nodes](https://spec.openlint.org/v1/draft/#node-selection).

## Declaring and using an alias

Declare aliases under `aliases` at the root of the ruleset. A simple alias is a list of path expressions. A rule refers to it from `given` with a leading `#`:

```yaml
aliases:
  PathItem:
    - $.paths[*]
rules:
  path-item-has-summary:
    given: "#PathItem"
    then:
      field: summary
      function: truthy
```

Quote `"#PathItem"`. In YAML an unquoted `#` starts a comment.

## Names

An alias name starts with a letter, followed by letters, digits, `_` and `-`. A name like `1st-param` makes the ruleset invalid:

```
to avoid confusion the name must match /^[A-Za-z][A-Za-z0-9_-]*$/ regular expression
```

## Building on an alias

After the name you can carry on with more path, so one alias serves many rules:

```yaml
given: "#PathItem.get"                 # one operation
given: "#PathItem[get,put,post]"       # several
given: "#Servers.url"                  # a field of each match
```

An alias can also be built from other aliases. These are the aliases the built-in `spectral:oas` ruleset declares:

```yaml
aliases:
  PathItem:
    - $.paths[*]
  OperationObject:
    - "#PathItem[get,put,post,delete,options,head,patch,trace]"
  SecurityRequirementObject:
    - $.security[*]
    - "#OperationObject.security[*]"
```

An alias can't refer to itself, directly or through other aliases. OpenLint rejects the ruleset:

```
Alias "A" is circular. Resolution stack: A -> B -> A
```

## Several paths in one alias

List every place a thing can appear, and a rule using the alias checks all of them. This alias selects every `servers` entry OpenAPI 3 allows, which the built-in `oas3-server-*` rules don't (they only check the top level):

```yaml
aliases:
  Servers:
    - $.servers[*]
    - $.paths[*].servers[*]
    - $.paths[*][get,put,post,delete,options,head,patch,trace].servers[*]
    - $..callbacks[*][*].servers[*]
    - $..callbacks[*][*][get,put,post,delete,options,head,patch,trace].servers[*]
rules:
  server-https:
    message: "{{value}} must use https"
    given: "#Servers.url"
    then:
      function: pattern
      functionOptions:
        match: "^https://"
```

Listing the locations is more precise than `$..servers`, which also matches any property named `servers` in a schema or an example.

## Scoped aliases: one name, different paths per format

The same thing often lives in different places in different versions of a format. A **scoped alias** picks its paths by the format of the document being linted. It's an object with `targets`, and each target has `formats` and `given`. An optional `description` explains it.

```yaml
aliases:
  ParameterObject:
    description: Reusable parameters, wherever this version of OpenAPI keeps them.
    targets:
      - formats: [oas2]
        given:
          - $.parameters[*]
      - formats: [oas3]
        given:
          - $.components.parameters[*]
  ParameterName:
    - "#ParameterObject.name"
rules:
  parameter-name-flat:
    formats: [oas2, oas3]
    given: "#ParameterName"
    then:
      function: casing
      functionOptions:
        type: flat
```

On an OpenAPI 3 document this reports `components.parameters.P.name`, and on Swagger 2.0 `parameters.P.name`. Things to know:

- The rule should list the same `formats`, so it only runs where a target can match.
- When no target matches the document's format, the alias selects nothing and the rule reports nothing, silently.
- When more than one target matches, only the last one counts. That matters for overlapping identifiers such as `oas3` and `oas3_1`.

## Where aliases don't reach

Aliases belong to the ruleset that declares them. That has three consequences that often surprise people:

**1. You can't use an extended ruleset's aliases.** `spectral:oas` declares `OperationObject`, but extending it doesn't make the alias available to your rules:

```yaml
extends: [[spectral:oas, "off"]]
rules:
  my-rule:
    given: "#OperationObject"     # fails
    then:
      field: summary
      function: truthy
```

```
Alias "OperationObject" does not exist
```

Copy the aliases you need into your own ruleset.

**2. An inherited rule keeps its own ruleset's aliases.** A rule from `spectral:oas` resolves its aliases against `spectral:oas`, even if your ruleset declares an alias with the same name.

**3. An alias in an override only reaches the rules that override defines.** Declaring an alias with the same name in an override doesn't change the rules defined at the root, even for the files the override matches:

```yaml
aliases:
  T:
    - $.info.title
rules:
  r:
    given: "#T"
    then:
      function: falsy
overrides:
  - files: ["api.yaml"]
    aliases:
      T:
        - $.info.version    # has no effect on rule r
```

Rule `r` still checks `info.title` in `api.yaml`. These limits are open questions in the specification and [discussion #2](https://github.com/orgs/openlint/discussions/2).

## YAML gotcha: quote paths in flow lists

In a flow-style list, `[ ... ]`, a path containing `[*]` breaks the YAML, and OpenLint reports a confusing error ("Ruleset must have rules or extends or overrides defined"). Use a block list, or quote the path:

```yaml
given: ["$.components.parameters[*]"]   # works
given: [$.components.parameters[*]]     # breaks the ruleset
```

## Summary

| You can | You can't |
| -- | -- |
| Name one or many paths and use them from `given` with `#` | Use an alias declared in a ruleset you extend |
| Continue the path after the name (`#PathItem.get`) | Change an inherited rule's aliases |
| Build aliases from other aliases | Make an alias refer to itself |
| Pick paths per format with a scoped alias | Reach the root rules with an alias declared in an override |
| Declare aliases at the root or in a file-scoped override | Share aliases between rulesets, yet: there's no alias registry |

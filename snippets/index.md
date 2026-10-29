---
layout: default
title: Rule snippets
summary: Reusable regular expressions, JSONPath and aliases for writing OpenLint rules, each with its source.
crumbs:
  - name: snippets
    url: /snippets/
---
# Rule snippets

Building blocks for writing rules: aliases and JSONPath for selecting things, and regular expressions for checking them. Snippets marked **tested** were run against OpenLint 1.0.0. This page is a start; suggestions are welcome in [Discussions](https://github.com/orgs/openlint/discussions).

## Selecting things in OpenAPI (JSONPath and aliases)

`given` is JSONPath as the JSONPath-Plus library reads it (see [the specification](https://spec.openlint.org/v1/draft/#node-selection)). These are the aliases `spectral:oas` declares for itself. You can't reference them from your ruleset, so copy them in.

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

### Every `servers` entry, and only those (tested)

The built-in `oas3-server-trailing-slash` and `oas3-server-not-example.com` rules check only the top-level `$.servers`. OpenAPI 3 also allows `servers` on path items and operations, including operations inside callbacks. And a recursive `$..servers` also catches unrelated properties named `servers` in schemas and examples, which is the problem the Dutch API Design Rules hit.

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

Tested on a document with `servers` at the root, on a path, on an operation and in a callback, plus a response schema and an example with a `servers` property. It reported the four real ones and neither of the others.

### Object keys

Use `@key` as the `field` to check a map's keys instead of its values:

```yaml
given: $.paths
then:
  field: "@key"
  function: pattern
  functionOptions:
    notMatch: ".+\\/$"      # no trailing slash on a path
```

## Regular expressions from the built-in rulesets

ECMAScript syntax. In YAML, quote them and double every backslash.

| What | Pattern | Used by |
| -- | -- | -- |
| No trailing slash (path or channel key) | `notMatch: ".+\\/$"` | `path-keys-no-trailing-slash`, `asyncapi-channel-no-trailing-slash` |
| No query string in a path | `notMatch: "\\?"` | `path-not-include-query` |
| No query or fragment in a channel | `notMatch: "[\\?#]"` | `asyncapi-channel-no-query-nor-fragment` |
| No empty `{}` parameter | `notMatch: "{}"` | `path-declarations-must-exist` |
| Not a placeholder host | `notMatch: "example\\.com"` | `oas3-server-not-example.com` |
| Server URL without trailing slash | `notMatch: "./$"` | `oas3-server-trailing-slash` |
| URL-safe `operationId` | `match: "^[A-Za-z0-9-._~:/?#\\[\\]@!\\$&'()*+,;=]*$"` | `operation-operationId-valid-in-url` |
| Identifier: letters, digits, `_`, `-` | `match: "^[A-Za-z0-9_\\-]+$"` | `arazzo-workflow-workflowId`, `arazzo-step-stepId` |
| No `<script>` in Markdown | `notMatch: "<script"` | `no-script-tags-in-markdown` |
| No `eval(` in Markdown | `notMatch: "eval\\("` | `no-eval-in-markdown` |

## Casing without regex

For names, `casing` is clearer than a pattern: `camel`, `pascal`, `kebab`, `cobol`, `snake`, `macro`, `flat`.

```yaml
then:
  field: operationId
  function: casing
  functionOptions:
    type: camel
```

## Still to add

- [ ] Every parameter (path, operation, components) as a scoped alias for OpenAPI 2 and 3
- [ ] Every schema, including nested `properties`, `items` and `allOf` (for property-name casing)
- [ ] Every response and every media type
- [ ] AsyncAPI 3: every channel address, operation and message
- [ ] The most-used patterns from public style guides, with sources
- [ ] A JSONPath-Plus tester to link to, and the RFC 9535 differences to watch for

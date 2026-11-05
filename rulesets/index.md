---
layout: default
title: Rulesets
summary: How OpenLint rulesets are found, stored, extended and combined today, with examples tested against OpenLint 1.1.3.
crumbs:
  - name: rulesets
    url: /rulesets/
---
# Rulesets: extends, inheritance and storage

A ruleset is a file that says which rules run against your API documents. Most teams start from a built-in ruleset, change a few rules, and add their own. This page covers how that works today: where OpenLint finds a ruleset, how `extends` pulls other rulesets in, which definition wins when two disagree, and how to apply different rules to different files.

Every example was run against OpenLint 1.1.3 on this document, a minimal OpenAPI 3.1 file with an `http` server and an operation that has no description, tags or `operationId`:

```yaml
openapi: 3.1.0
info:
  title: Pets
  version: 1.0.0
servers:
  - url: http://api.example.com
paths:
  /pets:
    get:
      responses:
        '200':
          description: OK
```

The formal definitions are in the specification: [the ruleset document](https://spec.openlint.org/v1/draft/#ruleset) and [extends](https://spec.openlint.org/v1/draft/#extends).

## Where a ruleset lives

### Found automatically

With no `--ruleset` option, OpenLint looks in **the current working directory** for a file whose name matches:

```
openlint or spectral, with or without a leading dot,
ending in .yaml, .yml, .json, .js or .mjs
```

So `.openlint.yaml`, `.openlint.yml`, `.openlint.json`, `openlint.yaml` and an existing `.spectral.yaml` are all picked up. New projects should use `.openlint.yaml`. Existing Spectral projects don't need to rename anything.

Two things to know:

- It only looks in the directory you run the command from. It doesn't look next to the document you're linting, or in parent directories.
- **Keep one ruleset file per directory.** If there are several, for example `.spectral.yaml` and `.openlint.yaml`, OpenLint uses the first one the file system lists. That order isn't defined, so which ruleset runs can change from one machine to another.

If nothing matches, you get:

```
No ruleset has been found. Please provide a ruleset using the --ruleset CLI argument, or make sure your ruleset file matches .openlint.js/yaml/json or .spectral.js/yaml/json
```

### Named explicitly

`--ruleset` (or `-r`) points at any file, by any name, anywhere:

```bash
npx openlint lint openapi.yaml --ruleset governance/api-ruleset.yaml
```

In CI, name the ruleset explicitly. It makes the run independent of the working directory and of which other files happen to be there.

### Shared rulesets

A ruleset you share between repositories can be referenced from `extends` in three ways:

| Where it's stored | How you reference it | Tested here |
|---|---|---|
| A file in the same repository | a relative path: `./shared/base.yaml` | yes |
| An npm package | the package name: `"@acme/api-ruleset"` (its `main` file), or a file in it: `"@acme/api-ruleset/ruleset.yaml"` | yes |
| A URL | `https://example.com/ruleset.yaml` | no |

Relative paths resolve from the ruleset file that contains them, not from where you run the command. Quote package names in YAML, because `@` can't start a plain value.

## Starting from a built-in ruleset

`extends` takes the name of a built-in ruleset. The built-in rules are listed on [rules.openlint.org](https://rules.openlint.org/) and in the [built-in rules reference](/builtin-rules/).

```yaml
extends: spectral:oas
```

On the example document, that reports five warnings: `info-contact`, `info-description`, `operation-description`, `operation-operationId` and `operation-tags`.

`openlint:openapi` is also available, and today it gives the same result as `spectral:oas`. It's the name the OpenLint OpenAPI ruleset will grow under ([openlint#27](https://github.com/openlint/openlint/issues/27)). `spectral:oas` stays as it is for compatibility.

### Choosing which rules are on

A built-in ruleset has recommended rules, which are on by default, and others that aren't. Write `extends` as a pair to choose:

```yaml
extends: [[spectral:oas, recommended]]  # the default
extends: [[spectral:oas, all]]          # every rule on
extends: [[spectral:oas, off]]          # every rule off, ready to switch some on
```

With `all`, the example reports nine warnings instead of five, adding `info-license`, `license-url`, `oas3-server-not-example.com` and `openapi-tags`.

`off` is the way to pick only the rules you want:

```yaml
extends: [[spectral:oas, off]]
rules:
  operation-description: warn
```

That reports one warning, `operation-description`.

## Changing inherited rules

### Severity

Name an inherited rule under `rules` with a severity to change it, or `off` to turn it off. You don't repeat the rule's definition:

```yaml
extends: spectral:oas
rules:
  info-contact: off
  operation-description: error
```

`info-contact` is gone, `operation-description` is now an error, and the other three are unchanged.

Severities are `error`, `warn`, `info`, `hint` and `off`.

### Redefining a rule

Give an inherited rule's name a full definition to replace it completely. Here a shared ruleset requires `https` servers as an error, and a team narrows it to `https://api.` hosts and lowers it to `info`:

```yaml
# shared/base.yaml
rules:
  https-servers:
    description: Servers must use https
    given: $.servers[*].url
    severity: error
    then:
      function: pattern
      functionOptions:
        match: "^https://"
```

```yaml
extends: ./shared/base.yaml
rules:
  https-servers:
    description: Production servers must use https
    given: $.servers[*].url
    severity: info
    then:
      function: pattern
      functionOptions:
        match: "^https://api\\."
```

The result is one `https-servers` problem at `info`. The original definition no longer runs.

## Combining several rulesets

`extends` takes a list. The rules from every ruleset in it are combined:

```yaml
extends:
  - spectral:oas
  - ./shared/base.yaml
```

That reports the five `spectral:oas` warnings plus `https-servers` as an error.

### Which one wins

When the same rule appears more than once, **the last one wins**:

1. A ruleset's own `rules` win over everything it extends.
2. Within `extends`, a later entry wins over an earlier one.
3. Rulesets that extend other rulesets follow the same rules, all the way down.

With a team ruleset that extends the shared one and lowers `https-servers` to `warn`:

```yaml
# shared/team.yaml
extends: ./base.yaml
rules:
  https-servers: warn
```

| Your ruleset | `https-servers` is reported as |
|---|---|
| `extends: ./shared/team.yaml` | warn (team's own rules win over base) |
| `extends: [./shared/team.yaml, ./shared/base.yaml]` | **error** (base comes last) |
| `extends: [./shared/base.yaml, ./shared/team.yaml]` | warn (team comes last) |
| `extends: ./shared/base.yaml` with `rules: { https-servers: hint }` | hint (your own rules win) |

The second row catches people out: listing a base ruleset after one that customises it undoes the customisation. Put the most general rulesets first and the most specific last.

### What doesn't carry over

`extends` brings in rules. It doesn't bring in the other ruleset's aliases for your own rules to use. See [where aliases don't reach](/aliases/#where-aliases-dont-reach).

Avoid rulesets that extend each other in a circle. OpenLint 1.1.3 doesn't stop the loop and runs until it runs out of memory.

## Different rules for different files

`overrides` changes rules for files that match a pattern, inside one ruleset:

```yaml
extends: [spectral:oas, ./shared/base.yaml]
overrides:
  - files: ["internal/**/*.yaml"]
    rules:
      https-servers: off
      info-contact: off
```

Linting `api.yaml` reports all six problems. Linting the same document as `internal/api.yaml` reports four: `https-servers` and `info-contact` are off for internal APIs.

The specification describes what else an override can do, under [file-scoped overrides](https://spec.openlint.org/v1/draft/#file-scoped-override).

## Putting it together

A common layout for an organisation:

```
api-ruleset/              # an npm package or a shared repository
  ruleset.yaml            # extends a built-in, adds house rules
your-api/
  .openlint.yaml          # extends the shared ruleset, local exceptions
  openapi.yaml
```

```yaml
# your-api/.openlint.yaml
extends:
  - "@acme/api-ruleset"
rules:
  operation-tags: off      # one service-level exception, with a reason in review
overrides:
  - files: ["examples/**"]
    rules:
      https-servers: off
```

The organisation changes rules in one place. Each API keeps its exceptions visible in its own repository.

## Summary

- OpenLint finds `openlint` or `spectral` ruleset files (`.yaml`, `.yml`, `.json`, `.js`, `.mjs`, with or without a dot) in the current directory only. Keep one per directory, and use `--ruleset` in CI.
- `extends` takes built-in names, relative paths, npm packages and URLs, one or a list.
- `[[spectral:oas, all]]` and `[[spectral:oas, off]]` choose which built-in rules start on.
- Change an inherited rule's severity by name. Replace it with a full definition.
- The last definition wins: your own rules, then later `extends` entries over earlier ones.
- `overrides` applies different rules to different files.
- Aliases don't travel through `extends`.

Questions and gaps go to the [education discussion](https://github.com/orgs/openlint/discussions/25).

# learn.openlint.org

Guides, references and reusable snippets for writing and using OpenLint rulesets, served by GitHub Pages at [learn.openlint.org](https://learn.openlint.org).

Split out of [openlint/website](https://github.com/openlint/website) on 29 October 2026 so the education work has its own track. The old `openlint.org/learn/…` URLs redirect here.

## Pages

| Page | What |
| -- | -- |
| [OpenLint 101](https://learn.openlint.org/101/) | The ruleset format in six small, working examples |
| [Aliases](https://learn.openlint.org/aliases/) | How aliases work today, and where they don't reach |
| [Rule snippets](https://learn.openlint.org/snippets/) | Reusable regex, JSONPath and aliases |
| [Built-in rules](https://learn.openlint.org/builtin-rules/) | Every rule in the built-in rulesets |

## Contributing

Every example should be tested against a released OpenLint before it's published; say which version in the page. Changes go through pull requests. To preview:

```sh
jekyll serve
```

The layout and CSS are copied from openlint/website so the two sites look the same; change both together.

Licensed under the [Apache License 2.0](LICENSE).

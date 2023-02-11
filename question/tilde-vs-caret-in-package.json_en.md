This is the SemVer notation.

`^` symbol shows that we can use any version equal to or higher than the current minor (middle number) version. No higher in the major.

For example `^1.2.3` allows to use versions `1.2.3`, `1.2.4`, `1.3.1` but **not** `3.0.0`

`~ `symbol stays to accept only patch changes (last number) in the version. Not higher for major or minor.

For example `~1.2.3` allows to use versions `1.2.3`, `1.2.4`, but **not** `1.3.1` or `3.0.0`

See references: [Semantic versioning](https://semver.org/), [npm semver calculator](https://semver.npmjs.com/)

Would you like to [contribute to the solution](https://github.com/BFEdev/BFE.dev-solutions/blob/main/question/tilde-vs-caret-in-package.json_en.md)? [Contribute guideline](https://github.com/BFEdev/BFE.dev-solutions#how-to-contribute)

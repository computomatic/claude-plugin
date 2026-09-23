---
name: packaging-apm-artifacts
description: Validate and pack APM artifacts. Use when preparing an APM package or marketplace release.
---

# Package APM Artifacts

Use `apm.yml` as the canonical package manifest.

1. From the package root, run `apm compile --validate` to validate primitive
   frontmatter and structure without writing output.
2. Run `apm compile --dry-run` to inspect instruction placement, then run
   `apm pack --dry-run --verbose` to inspect the exact bundle contents.
3. Run `apm audit` and resolve findings. Exclude credentials, temporary files,
   and unrelated repository content.
4. Run `apm pack` only after validation, preview, and audit succeed.
5. Remember that compilation applies to instructions; installing skills,
   agents, and other primitives is handled by `apm install`.
6. Pack each package from its own directory. In a hybrid repository, run
   `apm pack` at the root separately to produce the marketplace artifact; it is
   not a substitute for per-package bundles.

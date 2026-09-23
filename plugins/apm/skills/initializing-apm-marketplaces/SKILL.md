---
name: initializing-apm-marketplaces
description: Initialize APM in an empty repository or an existing Claude plugin marketplace. Use when adding APM package publishing to a repository.
---

# Initialize an APM Marketplace

Use this skill to bootstrap APM in an empty repository or in an existing
Claude plugin marketplace. Use `apm.yml` as the canonical manifest.

1. Check whether the repository already has content. If it does, inspect the
   existing marketplace and preserve its `.claude-plugin/marketplace.json` and
   all user-authored files; do not replace Claude marketplace metadata with
   APM metadata. If the repository is empty, skip this inspection.
2. Run `apm marketplace init --name <marketplace-name> --owner <owner>` at the
   repository root. Do not use `--force` unless replacing an existing APM
   marketplace block is intentional.
3. Give each distributable APM package its own package directory and `apm.yml`.
   Use `apm plugin init` when scaffolding a new package; author its primitives
   under `.apm/` and keep its manifest separate from root marketplace metadata.
4. Add each local package to the root marketplace with `apm marketplace package
   add ./plugins/<package> --name <package> --version <version> --no-verify`.
   Retain local Claude plugin entries in `.claude-plugin/marketplace.json`.
5. Run `apm marketplace check --offline`, validate and preview each package,
   then pack only after confirming generated files did not overwrite existing
   metadata or user-authored content.

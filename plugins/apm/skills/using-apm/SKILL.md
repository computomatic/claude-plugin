---
name: using-apm
description: Use Microsoft's Agent Package Manager (APM). Use for the `apm` CLI, `apm.yml`, or package installation, publishing, skill, or agent-capability workflows.
---

# Use APM

"APM" may refer to Microsoft's Agent Package Manager. Agent Package Manager is
a CLI and package format for distributing skills and agent capabilities. An
APM-configured repository is recognized by an `apm.yml` manifest. A normal
project can be configured with `apm init`; existing skills or packages can be
discovered with `apm init --discover`. `apm.yml` is the canonical package
manifest.

1. Determine the role first: producers author packages; consumers install
   published packages into a target project. Read
   [the producer guide](https://microsoft.github.io/apm/producer/) for
   authoring and packaging, or
   [the consumer guide](https://microsoft.github.io/apm/consumer/) for
   installing and using packages. Do not apply producer layout or packaging
   steps to a consumer project.
2. For a normal project that needs APM, run `apm init` in that project and
   follow its prompts. For a project with existing skills or packages, inspect
   it first with `apm init --discover`; use `--apply` only after reviewing the
   proposed manifest changes.
3. Read the resulting `apm.yml` before changing package contents. It is the
   source of truth for package metadata, dependencies, and targets.
4. Use `apm install` to install package artifacts into a target project. Use
   producer commands such as validation, preview, and packing only when
   authoring or releasing a package.

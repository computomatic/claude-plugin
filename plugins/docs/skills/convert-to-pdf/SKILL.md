---
name: convert-to-pdf
description: Converts markdown documents to PDF using pandoc with typst as the PDF engine. Use when the user wants to turn a markdown doc into a PDF.
argument-hint: "[markdown file(s) and any styling/output direction]"
allowed-tools: Bash(pandoc:*), Bash(typst:*), Bash(ls:*), Read, Glob, Write, Edit
---

# Convert to PDF

Converts one or more markdown documents to PDF, following any direction the user gives about styling or output location.

## Workflow

1. Identify the source markdown file(s) from the argument or conversation. Use Glob if a path is ambiguous or partial.

2. Convert with pandoc using typst as the PDF engine:

   ```
   pandoc <input.md> -o <output.pdf> --pdf-engine=typst
   ```

   By default, write the PDF next to the input file with the same basename. Honor any output path the user specifies.

3. Apply user direction through pandoc options where possible:
   - Page setup: `-V margin=...`, `-V papersize=...`, `-V fontsize=...`
   - Metadata: `-M title="..."`, `-M author="..."`, `-M date="..."`
   - Table of contents: `--toc`

4. For styling beyond what pandoc variables cover (custom fonts, headers/footers, colors, layout), generate an intermediate typst file, edit it, then compile:

   ```
   pandoc <input.md> -o <doc.typ> --standalone
   typst compile <doc.typ> <output.pdf>
   ```

5. Verify the PDF exists with `ls -la` and report the output path to the user.

## Guidelines

- Keep defaults simple when the user gives no direction. Do not invent styling they did not ask for.
- Surface pandoc or typst errors plainly rather than silently retrying with different options.
- For multiple input files, ask whether the user wants one combined PDF or one PDF per file, unless the context makes it obvious.

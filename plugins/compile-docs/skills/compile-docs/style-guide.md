# Documentation Style Guide

This file defines the documentation standard that agents follow when compiling reference pages. Every compiled page must conform to these rules. Read this file in full before producing or reviewing any documentation.

## Audience

Assume readers are already familiar with the tool being documented. Do not explain general computing concepts. Focus on precise, complete reference material.

## Tone and Voice

- Write in direct, declarative prose.
- Use present tense throughout ("prints the output", not "will print the output").
- Use second person for instructions ("you can pass", "specify the flag").
- Never use em-dashes. Use commas, parentheses, or separate sentences instead.
- Never use subjective qualifiers ("easy", "simply", "obviously", "just").
- Prefer short sentences. Break complex ideas into multiple sentences rather than packing clauses together.

---

## Page Structure

Every documentation page follows a mandoc-inspired section ordering. Include all sections that apply to the tool. Sections appear in the order listed below.

### NAME

- A single line: the command name followed by a short description.
- Format: `command` followed by a dash and a phrase (not a full sentence).
- Example: `jq - command-line JSON processor`

### SYNOPSIS

- Show the invocation syntax in a fenced code block with no language tag.
- Use `<angle-brackets>` for required parameters and `[square-brackets]` for optional parameters.
- Show multiple invocation forms on separate lines if the command supports them.

```
jq [OPTIONS] <filter> [file ...]
```

### DESCRIPTION

- A concise summary of what the command does, its primary purpose, and its general behavior.
- Use bulleted lists for distinct behavioral points.
- Do not repeat the synopsis.

### OPTIONS / FLAGS

- Document every flag and option the command supports.
- Use a definition list format: the flag on its own line in backticks, followed by an indented description.
- State the default value when one exists.
- State whether the option takes an argument and what type.
- Group related flags together under subheadings when the list is long.

Format:

```markdown
`--raw-output`, `-r`
:   Output raw strings instead of JSON-encoded strings. When the result is a string, its characters are written directly to standard output without JSON quoting.
```

### EXIT STATUS / RETURN VALUES

- List each exit code and its meaning.
- Use a definition list or bulleted list.

### ENVIRONMENT

- Document every environment variable the command reads or writes.
- State the effect of each variable and any default behavior when the variable is unset.

### FILES

- List configuration files, cache files, or other filesystem paths the command uses.
- State the purpose of each file.

### EXAMPLES

- Provide realistic examples covering both simple and advanced usage.
- Each example consists of:
  - A brief explanation of what the example demonstrates.
  - A fenced code block with an appropriate language tag (`sh`, `json`, etc.).
  - Representative output where helpful, in a separate fenced code block or inline.
- Order examples from simple to complex.
- Use realistic data, not placeholder lorem ipsum.

### SEE ALSO

- List related commands and resources as relative markdown links.
- Use anchor links when pointing to a specific entry within another page.
- If a cross-reference target does not yet exist, append `<!-- TODO: target not yet written -->` on the same line.

### Sections Omitted by Default

The following sections are omitted unless the reference material explicitly warrants their inclusion:

- AUTHORS
- BUGS
- HISTORY
- COPYRIGHT
- STANDARDS
- CAVEATS

---

## Formatting and Conventions

### Inline Code

- Use backticks for command names, flags, option arguments, file paths, environment variable names, and any literal value: `` `--verbose` ``, `` `/etc/config.json` ``, `` `$HOME` ``.

### Fenced Code Blocks

- Always include a language tag when the content has a clear language: `` ```sh ``, `` ```json ``, `` ```yaml ``.
- Omit the language tag only for abstract synopsis blocks or plain text output.

### Placeholders

- Use `<angle-brackets>` for required placeholders: `<filter>`, `<file>`.
- Use `[square-brackets]` for optional placeholders: `[file ...]`, `[OPTIONS]`.
- Use `...` (ellipsis) to indicate repetition: `[file ...]`.

### Lists

- Prefer bulleted lists within prose sections for conciseness.
- Use definition lists (term on one line, `: `-prefixed definition indented below) for flags, options, exit codes, and environment variables.
- Tables are acceptable when documenting many simple options that share a uniform structure, but definition lists are preferred in most cases.

### Headings

- Use `##` for top-level page sections (NAME, SYNOPSIS, etc.).
- Use `###` for subsections within a section.
- Do not skip heading levels.

---

## Citation Rules

Every factual claim in a documentation page must be traceable to a source.

- Format: `[Source: <command or URL>]`
- Place the citation at the end of the relevant section or paragraph.
- When a section draws from multiple sources, cite each one.
- Prefer citing the tool's own help output or official documentation.

Examples of valid citations:

- `[Source: jq --help]`
- `[Source: https://jqlang.github.io/jq/manual/]`
- `[Source: man jq]`

---

## Cross-References and Duplication Policy

### Cross-References

- Use relative markdown links to reference other documentation pages: `[gh repo](../gh-repo.md)`.
- Use anchor links to point to specific entries: `[--raw-output](jq.md#--raw-output)`.
- When a link target has not been written yet, append a TODO comment on the same line:
  ```markdown
  [related-tool](related-tool.md) <!-- TODO: target not yet written -->
  ```

### Duplication Policy

- Every piece of key information has one canonical location.
- Minor repetition is acceptable when it aids readability (for example, restating a default value in an example).
- When information is documented in detail elsewhere, prefer a cross-reference over a full restatement.

---

## Completeness Requirements

Every documentation page must be exhaustive within its scope:

- Document all flags, options, subcommands, parameters, environment variables, exit codes, and configuration settings.
- State the default value for every option that has one.
- Mark each parameter as required or optional.
- Describe behavioral nuances, edge cases, and interactions between flags.
- Provide at least one simple example and one advanced example, each with:
  - A brief explanation.
  - A realistic command invocation.
  - Representative output.
- Do not leave sections empty. If a section does not apply, omit it entirely rather than including a placeholder.

---

## Complete Example Page

The following is a full documentation page for `jq`, demonstrating every applicable section with proper formatting, citations, and conventions.

---

## NAME

`jq` - command-line JSON processor

## SYNOPSIS

```
jq [OPTIONS] <filter> [file ...]
```

When no file is given, `jq` reads from standard input. Multiple files are processed in order.

## DESCRIPTION

- `jq` is a lightweight, flexible command-line JSON processor. It takes JSON input and produces filtered, transformed, or reformatted JSON output.
- Filters are written in the `jq` expression language. The simplest filter is `.`, which outputs the input unchanged.
- `jq` can handle streaming input: it reads a sequence of JSON values from stdin or files and applies the filter to each one independently.
- Output is written to standard output, one result per line by default.

[Source: jq --help]
[Source: https://jqlang.github.io/jq/manual/]

## OPTIONS / FLAGS

### Output Formatting

`--raw-output`, `-r`
:   Output raw strings instead of JSON-encoded strings. When the result is a string, its characters are written directly to standard output without JSON quoting. Non-string values are output as normal.

`--raw-output0`
:   Like `--raw-output`, but uses NUL (`\0`) as the record separator instead of newline. Useful for piping into `xargs -0`.

`--join-output`, `-j`
:   Like `--raw-output`, but does not output a newline after each result.

`--tab`
:   Indent output using tabs instead of the default two spaces.

`--indent <n>`
:   Indent output using `<n>` spaces. Must be between 0 and 7. Default: `2`.

`--compact-output`, `-c`
:   Produce compact output. Each JSON result is printed on a single line with no extra whitespace.

`--color-output`, `-C`
:   Force colorized output even when writing to a pipe or file.

`--monochrome-output`, `-M`
:   Disable colored output. This is the default when output is not a terminal.

`--sort-keys`, `-S`
:   Output object keys in sorted (alphabetical) order.

`--ascii-output`, `-a`
:   Escape all non-ASCII characters in strings with `\uXXXX` sequences.

### Input Handling

`--null-input`, `-n`
:   Do not read input. The filter is run once with `null` as input. Useful when constructing JSON from scratch.

`--raw-input`, `-R`
:   Treat each input line as a JSON string rather than parsing it as JSON. Each line becomes a string value.

`--slurp`, `-s`
:   Read all input values into an array and apply the filter to that array as a single value.

`--slurpfile <variable> <file>`
:   Read the JSON values from `<file>` into an array and bind it to `$<variable>` before running the filter. Does not consume stdin.

`--jsonargs`
:   Treat remaining positional arguments as JSON values and make them available via `$ARGS.positional`.

`--args`
:   Treat remaining positional arguments as strings and make them available via `$ARGS.positional`.

`--arg <name> <value>`
:   Bind `$<name>` to `<value>` as a string. Available within the filter expression.

`--argjson <name> <value>`
:   Bind `$<name>` to `<value>` parsed as JSON.

### Execution Control

`--exit-status`, `-e`
:   Set the exit status based on the output values. Exit with 1 if the last output is `false` or `null`. Exit with 5 if no outputs are produced. Otherwise exit with 0.

`--seq`
:   Use the application/json-seq MIME type scheme for input and output, prefixing each value with an ASCII Record Separator and appending an ASCII Line Feed.

`--stream`
:   Parse input in streaming fashion. Output path-value pairs for each leaf value in the input. Useful for processing very large inputs that do not fit in memory.

`--version`
:   Print the `jq` version and exit.

`--help`
:   Print a brief usage summary and exit.

[Source: jq --help]
[Source: https://jqlang.github.io/jq/manual/]

## EXIT STATUS

`0`
:   The program ran successfully and all output values are neither `false` nor `null` (when `--exit-status` is used), or the program ran without error (when `--exit-status` is not used).

`1`
:   The `--exit-status` flag was used and the last output value is either `false` or `null`.

`2`
:   A usage error occurred (invalid arguments, bad filter syntax, etc.).

`3`
:   A compile error occurred in the filter expression.

`5`
:   The `--exit-status` flag was used and no valid outputs were produced.

[Source: https://jqlang.github.io/jq/manual/]

## ENVIRONMENT

`JQ_COLORS`
:   Override the default colors used for JSON output. The value is a colon-separated list of ANSI escape codes for: `null`, `false`, `true`, `number`, `string`, `array`, `object`, `object-key`. Example: `JQ_COLORS="0;31:0;32:0;33:0;34:0;35:0;36:1;37:1;34"`. When unset, `jq` uses built-in defaults.

`NO_COLOR`
:   When set to a non-empty value, disables colored output. Takes precedence over `-C`.

`HOME`
:   Used to resolve `~/.jq` for the user library search path.

[Source: https://jqlang.github.io/jq/manual/]

## FILES

`~/.jq`
:   User-level `jq` library file. If this file exists, it is loaded before the main filter is executed. You can define helper functions here.

`$ORIGIN/lib/jq/`, `$HOME/.jq/lib/`
:   Library search paths for `import` and `include` directives.

[Source: https://jqlang.github.io/jq/manual/]

## EXAMPLES

### Extract a single field

Print the `name` field from a JSON object:

```sh
echo '{"name": "Alice", "age": 30}' | jq '.name'
```

Output:

```json
"Alice"
```

### Extract a field as raw text

Print the `name` field without JSON quoting:

```sh
echo '{"name": "Alice", "age": 30}' | jq -r '.name'
```

Output:

```
Alice
```

### Iterate over array elements

Print each element of an array on its own line:

```sh
echo '[1, 2, 3, 4, 5]' | jq '.[]'
```

Output:

```json
1
2
3
4
5
```

### Filter and transform objects from an array

Given a file `users.json` containing an array of user objects, select users older than 25 and output their names:

```sh
jq -r '[.[] | select(.age > 25)] | .[].name' users.json
```

With this input in `users.json`:

```json
[
  {"name": "Alice", "age": 30},
  {"name": "Bob", "age": 22},
  {"name": "Carol", "age": 28}
]
```

Output:

```
Alice
Carol
```

### Construct new objects

Reshape input by constructing a new object with renamed keys:

```sh
echo '{"first": "Alice", "last": "Smith", "age": 30}' | jq '{full_name: (.first + " " + .last), years: .age}'
```

Output:

```json
{
  "full_name": "Alice Smith",
  "years": 30
}
```

### Slurp multiple JSON values into an array

Combine separate JSON values from stdin into a single array:

```sh
printf '{"id":1}\n{"id":2}\n{"id":3}\n' | jq -s '.'
```

Output:

```json
[
  {"id": 1},
  {"id": 2},
  {"id": 3}
]
```

### Use variables with --arg

Parameterize a filter with an external value:

```sh
jq --arg name "Alice" '.[] | select(.name == $name)' users.json
```

With the same `users.json` as above, output:

```json
{
  "name": "Alice",
  "age": 30
}
```

### Advanced: Group and aggregate

Given a file `orders.json`, group orders by customer and compute the total for each:

```sh
jq '[group_by(.customer)[] | {customer: .[0].customer, total: [.[].amount] | add}]' orders.json
```

With this input in `orders.json`:

```json
[
  {"customer": "Alice", "amount": 50},
  {"customer": "Bob", "amount": 30},
  {"customer": "Alice", "amount": 25},
  {"customer": "Bob", "amount": 45}
]
```

Output:

```json
[
  {
    "customer": "Alice",
    "total": 75
  },
  {
    "customer": "Bob",
    "total": 75
  }
]
```

### Compact output for scripting

Produce one-line-per-object output suitable for piping to other tools:

```sh
jq -c '.[]' users.json
```

Output:

```
{"name":"Alice","age":30}
{"name":"Bob","age":22}
{"name":"Carol","age":28}
```

[Source: https://jqlang.github.io/jq/manual/]

## SEE ALSO

- [jq Language Reference](https://jqlang.github.io/jq/manual/)
- [curl](curl.md) <!-- TODO: target not yet written -->
- [xargs](xargs.md) <!-- TODO: target not yet written -->

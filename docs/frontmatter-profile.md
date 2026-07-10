# Fundus frontmatter profile

Status: implemented in P15  
Reviewed: 2026-07-10

Fundus uses YAML frontmatter delimited by `---` and a pinned, vendored `ruamel.yaml==0.19.1` round-trip codec. The package includes the pure-Python runtime and its MIT license, so source, built skill, plugin, and MCP executions use the same parser without a separate installation step.

## Supported values

The top-level document must be a mapping with non-empty string keys. Supported values are:

- strings, including quoted strings and multiline block strings;
- booleans;
- integers and floating-point numbers;
- dates and timestamps;
- null;
- lists containing only those scalar values.

Unknown keys with supported values are retained. Comments and scalar quoting are preserved when the round-trip representation permits it. Nested mappings, nested lists, sets, custom YAML tags, duplicate keys, non-string keys, and non-mapping roots fail with `FRONTMATTER_INVALID`; Fundus never silently drops them.

The known list fields are `aliases`, `projects`, `repos`, `supersedes`, `tags`, and `verified_against`. A scalar in one of these fields is deliberately normalized to a one-item list, so `tags: ticket` becomes `tags: [ticket]` semantically. Null becomes an empty list. Known timestamp fields normalize parsed date or timestamp values to ISO text because Fundus exposes them as operation metadata.

## Serialization and preservation

The serializer quotes values whenever YAML requires quoting and preserves unknown supported fields. A metadata-only operation keeps the note body exactly as decoded UTF-8, including blank lines, trailing spaces, and line endings. Before writing, Fundus reparses its output and refuses the operation if the body differs.

UTF-8 notes may use LF or CRLF. The existing newline convention is retained for rewritten frontmatter. A leading UTF-8 BOM is accepted and retained. Mixed line endings inside the body are left untouched by metadata-only operations. Invalid UTF-8 fails with `FRONTMATTER_INVALID`.

Operations that intentionally change body content use the note's existing frontmatter newline convention for the rewritten body. Newly created documents use UTF-8 without BOM and LF line endings.

## Delimiters and empty metadata

The opening delimiter must be the first content in the file, apart from an optional BOM, and must end with LF or CRLF. A missing closing delimiter fails with `FRONTMATTER_INVALID`. Empty frontmatter is valid YAML, although normal note operations may treat it as missing required concept metadata.

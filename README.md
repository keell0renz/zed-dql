# zed-dql

Zed editor extension for the **DQL** language ecosystem — the deterministic
data-query / validation / extraction DSL used by Customs OS. Adds file-type
recognition and LSP-driven editor features for `.dql`, `.dqlef`, `.dqlsf`, and
`.dqlsn` files.

## What you get today

- **File recognition** for `.dql`, `.dqlef`, `.dqlsf`, `.dqlsn`.
- **LSP integration** via the `dql-lsp` binary that ships with Customs OS:
  - Diagnostics (parse errors, schema-aware path checks)
  - Hover (field info from the SF profile when present)
  - Completion (keywords, formats, aliases)
  - Format-on-save
  - Document symbols (CHECK / FN / EXPORT / EXCEL outline)
  - Code actions (insert missing `END`, suggest `AS DATE` / `AS COUNTRY`)

## Known limitations

- **No tree-sitter syntax highlighting yet.** The grammar source ships in the
  sibling `tree-sitter-dql/` directory of this repo but has a few `dql_file` /
  `dqlef_file` / `version_directive` ambiguities that need resolving before
  `tree-sitter generate` succeeds. Until then, the extension is LSP-only.
  Contributions welcome.

## Install

This is a Zed **dev extension**. Two prerequisites:

1. **Clone Customs OS** (the LSP server lives there):

   ```
   git clone https://github.com/<you>/customs-os.git
   cd customs-os
   pnpm install
   chmod +x bin/dql-lsp.js
   ```

2. **Install the extension** in Zed:
   - Open Zed
   - `Cmd+Shift+P` → **"zed: install dev extension"**
   - Pick the directory `customs-os/extensions/zed-dql/`

After install, `.dql` / `.dqlef` / `.dqlsf` / `.dqlsn` files open with their
correct language type.

## Wire the LSP binary

`extension.toml` invokes `node bin/dql-lsp.js --stdio` relative to the open
workspace. If your workspace root isn't the Customs OS repo, override the
command in Zed settings (Cmd+,):

```json
{
  "lsp": {
    "dql-lsp": {
      "binary": {
        "path": "node",
        "arguments": ["/absolute/path/to/customs-os/bin/dql-lsp.js", "--stdio"]
      }
    }
  }
}
```

## Reload after changes

- **Extension config changes:** `Cmd+Shift+P` → **"zed: reload extensions"**.
- **LSP server code changes:** close & reopen the affected file, or
  `Cmd+Shift+P` → **"editor: restart language server"**.

## Repository layout

- `extension.toml` — Zed manifest, declares the LSP server binding
- `languages/{dql,dqlef,dqlsf,dqlsn}/config.toml` — per-surface file
  configuration (bracket pairs, comment syntax, etc.)
- `themes/file-icons.json` — file-icon mapping (manual setup; Zed dev
  extensions don't bundle icons automatically)

## Companion: tree-sitter-dql

The `tree-sitter-dql/` sibling directory in this repo contains a draft
grammar source. Once the ambiguities are fixed and `tree-sitter generate`
succeeds, this extension will be updated to reference it and full syntax
highlighting will turn on.

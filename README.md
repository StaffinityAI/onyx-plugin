# onyx-plugin

Fizzy Plugin for the Onyx Knowledge Management System.

A [Fizzy](https://github.com/fizzyedit/fizzy) plugin that turns the editor into an Onyx
client: it opens an Obsidian-style Markdown vault served by
[onyx-server](https://github.com/StaffinityAI/onyx-server) and edits it collaboratively.

> Status: early development. Nothing here is implemented yet — this README describes the
> intended shape of the plugin.

## Architecture

The plugin is written in Zig against the Fizzy plugin SDK (see
[PLUGINS.md](https://github.com/fizzyedit/fizzy/blob/main/docs/PLUGINS.md)).

- Claims `.md` files via `fileTypePriority` and owns their document lifecycle
  (`loadDocument` / `saveDocument` / `drawDocument`), so Onyx notes render through the
  plugin rather than the default text editor.
- Talks to `onyx-server` over the Onyx wire protocol, consumed as a Zig build module from
  the server package — protocol types are shared, not redeclared.
- Vault contents stay plain Markdown on disk; git remains the version control layer.

## Editing modes

Two modes over the same document, switchable per view:

**Side by side** — raw Markdown on the left, rendered preview on the right.
The Markdown side is a real code editor: Tree-sitter for syntax highlighting and
structural navigation, LSP for completion, hover, diagnostics, and go-to-definition
across `[[wikilinks]]`.

**Inline** — Obsidian-style WYSIWYG. Markup renders in place and reveals its source only
on the line the cursor occupies. No split, no separate preview.

## Collaborative editing

Collaboration is a core concern, not an add-on. Every user editing a document sees every
other user's cursor and selection in that document at all times, in both editing modes.
Remote cursors are always visible — never hidden behind a toggle or a presence panel.

## Building

```sh
zig build install
```

This installs the compiled plugin into Fizzy's plugin directory:

- macOS — `~/Library/Application Support/fizzy/plugins/onyx/onyx.dylib`
- Linux — `~/.config/fizzy/plugins/onyx/onyx.so`
- Windows — `%LOCALAPPDATA%/fizzy/plugins/onyx/onyx.dll`

The SDK is pinned to an `sdk-v*` release tag in `build.zig.zon`; the host rejects the
plugin outright on an ABI fingerprint or `min_sdk_version` mismatch.

## Related

- [onyx-server](https://github.com/StaffinityAI/onyx-server) — wire protocol and reference server.
- [fizzy](https://github.com/fizzyedit/fizzy) — the host editor.

## License

MIT. See [LICENSE](LICENSE).

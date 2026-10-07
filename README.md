# obsidian-open

Opens a markdown file (or a symlink to one) in Obsidian, inside its vault. If the note is already open, it jumps to that tab; otherwise it opens a new tab.

## Files

| File | Location |
|---|---|
| `obsidian-open` | `~/.local/bin/` (must be executable and on `PATH`) |
| yazi config | `~/.config/yazi/yazi.toml` |
| Mononote plugin | Obsidian → Community plugins |

## Usage

- **yazi:** hover a `.md` file → `Shift+O` → *Open in Obsidian*. (`o` / `Enter` still open `$EDITOR`.)
- **Terminal:** `obsidian-open note.md`

## How it works

1. Resolves symlinks to the real file in the vault.
2. Opens `obsidian://open?path=<real path>&paneType=tab`. Obsidian finds the owning vault itself.
3. Mononote closes the duplicate tab and focuses the existing one if the note was already open.

## Setup (from scratch)

```sh
mkdir -p ~/.local/bin
mv obsidian-open ~/.local/bin/
chmod +x ~/.local/bin/obsidian-open
which obsidian-open   # if empty: add `export PATH="$HOME/.local/bin:$PATH"` to ~/.zshrc
```

In `~/.config/yazi/yazi.toml`, add to the **existing** sections (no duplicate headers):

```toml
[opener]
obsidian = [
	{ run = "obsidian-open %s", desc = "Open in Obsidian", for = "macos" },
]

[open]
rules = [
	{ url = "*.md", use = [ "edit", "obsidian", "reveal" ] },   # must be FIRST
	# ...existing rules...
]
```

Restart yazi after editing.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `duplicate key` on launch | A section header appears twice — merge into the existing one. |
| No *Open in Obsidian* in menu | `*.md` rule isn't first (yazi uses first match), or the `obsidian` opener is missing. |
| Menu entry does nothing | Opener must use `%s`, not `"$@"` (yazi 26.x syntax). Check `which obsidian-open`. |
| Obsidian shows an error | Vault isn't open/registered in Obsidian. |
| Duplicate tabs | Mononote not installed or disabled. |

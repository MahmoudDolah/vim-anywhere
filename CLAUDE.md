# vim-anywhere

A tool that lets you edit any text field with Vim by invoking a global hotkey. It opens a temp file in Vim, and on close copies the contents to the system clipboard.

## How it works

1. A global hotkey triggers `bin/run`
2. `bin/run` creates a temp file in `/tmp/vim-anywhere/`
3. On Linux: spawns a terminal emulator running `vim` on that file; waits for it to close
4. On macOS: opens MacVim (`mvim --nofork`) on that file; waits for it to close
5. Contents of the temp file are copied to the clipboard

## Key files

- `bin/run` — the main script, invoked by the hotkey
- `install` — sets up the hotkey binding for GNOME, KDE, or i3; checks dependencies
- `uninstall` — removes the hotkey binding

## Platform notes

### Linux

`bin/run` detects the terminal emulator at runtime in this priority order:
ghostty → wezterm → alacritty → kitty → foot → xterm

Each has a slightly different invocation:
- `ghostty -e vim ...`
- `wezterm start -- vim ...`
- `alacritty -e vim ...`
- `kitty vim ...`
- `foot vim ...`
- `xterm -e vim ...`

Clipboard: `wl-copy` on Wayland, `xclip` on X11.

### macOS

Uses MacVim (`mvim`). The `--nofork` flag is required so the script blocks until MacVim closes. After close, `pbcopy` copies the buffer and AppleScript refocuses the previous app.

## Vimrc

If `~/.vimrc.min` exists it is passed to vim via `-u`. Falls back to `~/.gvimrc.min`. This lets you use a minimal config when invoked via vim-anywhere without affecting your normal Vim setup.

## Temp file history

Files persist in `/tmp/vim-anywhere/` until reboot. Useful for recovering recent edits:

```bash
vim $( ls /tmp/vim-anywhere | sort -r | head -n 1 )
```

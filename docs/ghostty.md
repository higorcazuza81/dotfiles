# Ghostty — keyboard shortcuts

Installed version: `1.3.1`. Config in `~/.config/ghostty/config`
(+ `~/.config/ghostty/auto/theme.ghostty` for the theme).

No keybinding is overridden in this setup — `config.ghostty` is empty, and
`config` only sets appearance/behavior. In other words, **everything below
is Ghostty's factory default**, obtained by running
`ghostty +list-keybinds`. That also means it works on any fresh Ghostty
install, independent of this dotfiles repository.

## What this `config` sets (behavior, not keybindings)

| Option | Value | Effect |
|---|---|---|
| `theme` | `iTerm2 Default` (overridden by `Catppuccin Mocha` in `auto/theme.ghostty`) | Color palette |
| `background-opacity` | `0.90` | Slightly transparent background |
| `background-blur` | `true` | Blur behind the window |
| `font-family` | `JetBrainsMono Nerd Font` | Font (the Nerd Font variant is required for starship/tmux icons) |
| `shell-integration-features` | `cursor,sudo,title,ssh-env,ssh-terminfo` | Cursor changes under `sudo`; tab title follows the running command; SSH propagates terminfo |
| `command` | `tmux new-session -A -s main` | Every new window opens straight into tmux |
| `confirm-close-surface` | `true` | Asks for confirmation before closing a pane with a running process |
| `notify-on-command-finish` | `unfocused`, after `30s` | Notifies when a long-running command finishes in an unfocused tab |

## Commands ↔ config

| Shortcut | Action |
|---|---|
| `Ctrl+,` | Opens `~/.config/ghostty/config` in the default editor |
| `Ctrl+Shift+,` | Reloads the config without restarting Ghostty |

## Tabs

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+T` | New tab |
| `Ctrl+Shift+W` | Close current tab |
| `Ctrl+Tab` / `Ctrl+Shift+Tab` | Next / previous tab |
| `Ctrl+Shift+→` / `Ctrl+Shift+←` | Next / previous tab (alternative) |
| `Ctrl+Page Down` / `Ctrl+Page Up` | Next / previous tab (alternative 2) |
| `Alt+1` … `Alt+8` | Jump directly to tab N |

## Panes (splits) — within the Ghostty window, distinct from tmux panes

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+O` | New split to the right |
| `Ctrl+Shift+E` | New split below |
| `Ctrl+Alt+←/→/↑/↓` | Move focus between splits |
| `Super+Ctrl+[` / `Super+Ctrl+]` | Previous / next split (cycle) |
| `Ctrl+Shift+Enter` | Zoom the current split (expand/collapse) |
| `Super+Ctrl+Shift+←/→/↑/↓` | Resize a split |

> **Important:** since this setup opens tmux automatically, I effectively
> have two overlapping "pane" systems — Ghostty splits (above) and tmux
> panes (`tmux.md`). In practice, I always prefer **tmux** panes
> (`Ctrl+b %` / `Ctrl+b "`): they survive closing the terminal and are also
> available over SSH. I only reach for Ghostty splits when I want two
> *different* tmux sessions side by side.

## Windows

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+N` | New Ghostty window |
| `Alt+F4` | Close window |
| `Ctrl+Shift+Q` | Quit Ghostty |
| `Ctrl+Enter` | Toggle fullscreen |

## Copy / paste / selection

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+C` | Copy |
| `Ctrl+Shift+V` | Paste |
| `Shift+Insert` | Paste (from the X11 selection, not the clipboard) |
| `Shift+←/→/↑/↓` | Adjust selection |
| `Ctrl+Shift+A` | Select all |

## Search and scrollback navigation

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+F` | Start a search in the scroll history |
| `Esc` | Exit search |
| `Shift+Page Up` / `Shift+Page Down` | Scroll a full page |
| `Shift+Home` / `Shift+End` | Jump to the top / bottom of the scrollback |
| `Ctrl+Shift+Page Up` / `Ctrl+Shift+Page Down` | Jump to the previous / next prompt |

## Font

| Shortcut | Action |
|---|---|
| `Ctrl+=` or `Ctrl++` | Increase font size |
| `Ctrl+-` | Decrease font size |
| `Ctrl+0` | Reset font size |

## Diagnostics

| Shortcut | Action |
|---|---|
| `Ctrl+Shift+I` | Open the inspector (rendering/event debugging) |

## Full keybinding reference

```bash
ghostty +list-keybinds     # all active keybindings, with their origin
ghostty +show-config       # full effective config (defaults + my overrides)
```

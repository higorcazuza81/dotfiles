# tmux — sessions, windows, panes and plugins

Installed version: `3.4`. Config in `~/.tmux.conf`. Theme:
`catppuccin/tmux` (flavor `mocha`). Since Ghostty already opens with
`tmux new-session -A -s main`, I'm **already inside tmux** as soon as I
open a terminal — I never type `tmux` to get started.

## Prefix

Every tmux command starts with the **prefix**. In this setup there are
**two**:

| Prefix | Config |
|---|---|
| `Ctrl+b` | tmux default |
| `Ctrl+a` | extra shortcut (`prefix2`), faster to type |

In the tables below, "prefix" means pressing `Ctrl+b` **or** `Ctrl+a`,
releasing it, then pressing the action key.

## Native keybinding discovery

| Shortcut | Action |
|---|---|
| prefix `?` | Lists **all** active keybindings (built-in + this config) — my go-to when I forget something |

## Sessions

| Shortcut / command | Action |
|---|---|
| prefix `s` | Opens the **sesh** popup (fuzzy session switcher — see the sesh section below) |
| prefix `L` | Reconnects to the **last** used session (`sesh last`) |
| prefix `d` | Detach from the session, leaving everything running |
| prefix `$` | Rename the current session |
| `tmux ls` (outside tmux) | List sessions |
| `tmux attach -t main` | Reconnect to the `main` session from outside tmux |
| `tmux kill-session -t <name>` | Kill a session |

## Windows (equivalent to "tabs" within a session)

| Shortcut | Action |
|---|---|
| prefix `c` | New window, opening **in `$HOME`** (customized — tmux's default is the current directory) |
| prefix `,` | Rename the current window |
| prefix `w` | List windows (visual navigation) |
| prefix `n` / prefix `p` | Next / previous window |
| prefix `0`–`9` | Jump directly to window N |
| prefix `&` | Close the current window (asks for confirmation) |

> Numbering starts at `1`, not `0` (`base-index 1`), and windows are
> automatically renumbered when one in the middle is closed
> (`renumber-windows on`).

## Panes (splits within a window)

| Shortcut | Action |
|---|---|
| prefix `"` | **Horizontal** split (pane below) — inherits the current directory (customized) |
| prefix `%` | **Vertical** split (pane beside) — inherits the current directory (customized) |
| prefix `x` | Closes the current pane **without asking for confirmation** (customized — the default asks `y/n`) |
| prefix `o` | Cycle focus between panes |
| prefix `←/→/↑/↓` (arrows) | Move focus to the pane in that direction |
| prefix `z` | Zoom the current pane (expands to fill the window, press again to restore) |
| prefix `Ctrl+←/→/↑/↓` (hold the prefix) | Resize a pane |
| Mouse | Click to switch panes, drag the border to resize (`mouse on`) |

## Zellij-style extras (custom to this config)

| Shortcut | Action |
|---|---|
| prefix `f` | **Floating terminal** — 80%×80% popup in the current directory |
| prefix `g` | **`htop` popup** (process monitor), 80%×80% |

## sesh — smart session switching

`sesh` uses `zoxide` to suggest directories/sessions and integrates with
`fzf`.

| Command | What it does |
|---|---|
| prefix `s` | Popup: `sesh list \| fzf` → connects to the chosen session |
| prefix `L` | Jumps straight back to the last session (no popup) |
| `sesh list` | Lists known sessions (active tmux sessions + zoxide directories) |
| `sesh connect <name>` | Connects to (or creates) a session with that name |
| `sesh last` | Same as prefix `L`, from the command line |
| `sesh clone <repo>` | Clones a git repo and opens a session in it |
| `sesh picker` | Opens the interactive picker outside of tmux too |

## Copy mode (vi-style — `mode-keys vi`)

| Shortcut | Action |
|---|---|
| prefix `[` | Enter copy mode (navigate the scrollback) |
| `h j k l` | Move the cursor (vim-style) |
| `v` | Start selection |
| `y` | Copy the selection and exit copy mode |
| `Ctrl+v` | Toggle rectangular (block) selection |
| `q` or `Esc` | Exit copy mode without copying |
| `Ctrl+b` / `Ctrl+f` (inside copy mode) | Previous / next page |
| `set-clipboard on` | Copying in tmux already goes to the system clipboard (X11/Wayland) |

## Session persistence — `tmux-resurrect` / `tmux-continuum`

Saves **on demand**; it does not restore automatically when Ghostty opens
(`@continuum-restore 'off'` — a deliberate decision, see `~/.tmux.conf`).

| Command (outside tmux, in bash) | What it does |
|---|---|
| `tmux-save` | Saves the current state of all sessions/panes |
| `tmux-restore` | Restores the last saved state |

`tmux-continuum` also saves **automatically in the background** every 15
minutes (`@continuum-save-interval '15'`), but only running `tmux-restore`
manually applies the snapshot.

## Installed plugins (`~/.tmux/plugins`, managed by TPM)

| Plugin | Purpose |
|---|---|
| `tpm` | tmux plugin manager (installs/updates the rest) |
| `tmux-sensible` | "Good defaults" that most setups should have |
| `tmux-yank` | Improves system-clipboard copy integration |
| `tmux-resurrect` | Manually save/restore sessions, windows, and panes |
| `tmux-continuum` | Background auto-save for `tmux-resurrect` |
| `tmux-battery` | Battery indicator (for the status bar) |
| `tmux-fzf` | Fuzzy tmux menus (sessions, windows, panes) via `fzf` |
| `catppuccin/tmux` | Status bar theme (flavor `mocha`) |

| TPM command (inside tmux) | Action |
|---|---|
| prefix `I` (capital I) | Install new plugins listed in `.tmux.conf` |
| prefix `U` | Update plugins |
| prefix `Alt+u` | Remove plugins no longer listed in `.tmux.conf` |

### tmux-fzf

| Shortcut | Action |
|---|---|
| prefix `F` (capital F) | Opens the `tmux-fzf` fuzzy menu (sessions/windows/panes/commands) |

## Relevant behavior settings (not commands, but they change day-to-day use)

| Setting | Practical effect |
|---|---|
| `escape-time 0` | No delay when pressing `Esc` (important for vim/neovim) |
| `history-limit 50000` | 50,000 lines of scrollback per pane |
| `detach-on-destroy off` | Closing a session's last window drops me into another session instead of exiting tmux |
| `status-position top` | Status bar sits at the top, not the bottom |

# tmux — sessions, windows, panes and plugins

Installed version: `3.4`. Config in `~/.tmux.conf`. Theme:
`catppuccin/tmux` (flavor `mocha`). Since Ghostty already opens with
`command = tmux`, I'm **already inside tmux** as soon as I open a
terminal; I never type `tmux` to get started.

Each new Ghostty window creates its own fresh, independent tmux session
(plain `tmux`, no fixed session name). This used to be
`tmux new-session -A -s main`, which meant every window attached to the
same `main` session: two windows showed the exact same content, since
they were literally the same session viewed by two clients. To reach a
specific context (a project, `pve1`, `pve2`) from any window, use `sesh`
(below) rather than relying on which window happens to be attached to it.

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
| prefix `N` | Creates a **new, named** session (prompts for a name), avoiding generic numbered sessions (`0`, `1`, ...) |
| prefix `L` | Reconnects to the **last** used session (`sesh last`) |
| prefix `d` | Detach from the session, leaving everything running |
| prefix `$` | Rename the current session |
| `tmux ls` (outside tmux) | List sessions |
| `tmux attach -t <name>` | Reconnect to a specific session from outside tmux |
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
`fzf`. Config in `~/.config/sesh/sesh.toml`.

| Command | What it does |
|---|---|
| prefix `s` | Popup: `sesh list \| fzf` → connects to the chosen session |
| prefix `L` | Jumps straight back to the last session (no popup) |
| `sesh list` | Lists known sessions (active tmux sessions + zoxide directories + configured ones) |
| `sesh connect <name>` | Connects to (or creates) a session with that name |
| `sesh last` | Same as prefix `L`, from the command line |
| `sesh clone <repo>` | Clones a git repo and opens a session in it |
| `sesh picker` | Opens the interactive picker outside of tmux too |

### Fixed sessions: `pve1` / `pve2`

`sesh.toml` declares two named sessions for the servers I connect to
constantly:

```toml
[[session]]
name = "pve1"
path = "~"
startup_command = "ssh pve1"

[[session]]
name = "pve2"
path = "~"
startup_command = "ssh pve2"
```

`startup_command` only runs the **first** time the session is created:
picking `pve1` from `prefix + s` creates the session and connects
automatically; every time after that, it just reattaches to the session
that was already alive (with the SSH connection still up). These sessions
are meant to stay alive for days; detach (prefix `d`) rather than closing
the pane between tasks; that's what keeps the SSH connection warm and the
remote shell's state intact.

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
| `tmux-prefix-highlight` | Status bar indicators: prefix pressed, synchronize-panes active |

| TPM command (inside tmux) | Action |
|---|---|
| prefix `I` (capital I) | Install new plugins listed in `.tmux.conf` |
| prefix `U` | Update plugins |
| prefix `Alt+u` | Remove plugins no longer listed in `.tmux.conf` |

### tmux-fzf

| Shortcut | Action |
|---|---|
| prefix `F` (capital F) | Opens the `tmux-fzf` fuzzy menu (sessions/windows/panes/commands) |

### Prefix / sync-panes indicator

The right side of the status bar shows nothing most of the time. Two
things light it up:

| Indicator | Meaning |
|---|---|
| ` ^B ` (brief flash) | The prefix key was just pressed, confirming tmux actually caught it |
| ` Sync ` (red, stays on) | `synchronize-panes` is **on**: every keystroke is being sent to *all* panes in the window at once |

The sync indicator matters specifically because `pve1` and `pve2` are kept
open at the same time: if synchronize-panes gets turned on by accident
(`prefix :` `setw synchronize-panes on`, or a stray keybind) and goes
unnoticed, a command typed for one server runs on both. The red `Sync`
badge is the tell. To turn it off: `prefix :` `setw synchronize-panes off`.

## Relevant behavior settings (not commands, but they change day-to-day use)

| Setting | Practical effect |
|---|---|
| `escape-time 0` | No delay when pressing `Esc` (important for vim/neovim) |
| `history-limit 50000` | 50,000 lines of scrollback per pane |
| `detach-on-destroy off` | Closing a session's last window drops me into another session instead of exiting tmux |
| `status-position top` | Status bar sits at the top, not the bottom |
| `@catppuccin_status_modules_right "null"` | Right side shows nothing from catppuccin (see below); the prefix/sync indicator is appended separately |

The status bar only shows session name (left) and window tabs (middle).
Directory and clock used to sit on the right but were removed: `starship`
already shows both, right where the cursor is. Repeating them at the top
of the screen was information in the wrong place, not extra information.

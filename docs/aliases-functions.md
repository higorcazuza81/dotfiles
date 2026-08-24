# Aliases and custom functions (`~/.bash_aliases`)

Loaded automatically by `~/.bashrc`. Everything here is specific to my
setup — none of it exists in a stock bash installation.

## Simple navigation shortcuts

| Command | Equivalent to |
|---|---|
| `..` | `cd ..` |
| `...` | `cd ../..` |
| `mkdir dir` | `mkdir -pv dir` (creates parent directories automatically and prints what was created) |

## Network

| Command | What it shows |
|---|---|
| `ports` | `ss -tulpn` — listening TCP/UDP ports, with the owning process |
| `myip` | `ip -brief address` — interface IPs, summarized |

## Git — short aliases

| Command | Equivalent to | Use |
|---|---|---|
| `gs` | `git status --short --branch` | Compact status, branch shown at the top |
| `gd` | `git diff` | Diff of unstaged changes |
| `gl` | `git log --oneline --graph --decorate -20` | Last 20 commits, as a graph |

## `ls` (from `~/.bashrc`, not `~/.bash_aliases`)

| Command | Equivalent to |
|---|---|
| `ll` | `ls -alF` (long format, all entries, file-type markers) |
| `la` | `ls -A` (all entries except `.` and `..`) |
| `l` | `ls -CF` (columnar, file-type markers) |

## Fuzzy functions (built on `fzf`)

These are functions rather than aliases because they need an argument, a
local variable, or early-exit logic for when I cancel the `fzf` prompt with
`Esc`.

### `sshf` — connect over SSH by picking a host visually

```bash
sshf
```
Reads the `Host` entries declared in `~/.ssh/config`, shows them in an
`fzf` picker, and connects to the one I select. Only works for hosts
already defined in that file (no `*`/`?` wildcards).

### `gco` — switch git branches with a fuzzy picker

```bash
gco
```
Lists local **and** remote branches (`git branch --all`), strips the
`remotes/origin/` prefix, shows them in an `fzf` picker, and runs `git
checkout` on the one I select.

### `fkill` — kill a process by picking it visually

```bash
fkill        # SIGTERM (15) — asks the process to terminate gracefully
fkill 9      # SIGKILL (9) — forces termination
```
Shows processes sorted by CPU usage. `Tab` marks multiple processes at once
(`fzf --multi`) so I can kill several in one go.

### `fe` — open a file in my editor, picked visually

```bash
fe
```
Opens an `fzf` file picker with a colored preview (via `bat`) and opens the
selected file in `$EDITOR` (defaults to `vim` if `$EDITOR` is unset).

### `frg` — search by CONTENT and jump straight to the line

```bash
frg "TODO"
frg "function.*login"
```
Unlike `Ctrl+T` (which searches by file **name**), `frg` runs a recursive
`grep` over file **content**, ignoring `.git`, `target/`, and
`node_modules/`, shows the matches in `fzf` with the matching line
highlighted in the preview, and opens the file positioned on the right line
(`vim +N file`).

## tmux — manual persistence (from `~/.bashrc`)

| Command | What it does |
|---|---|
| `tmux-save` | Saves the current state of all tmux sessions/panes (via `tmux-resurrect`) |
| `tmux-restore` | Restores the last saved state |

See `tmux.md` for the full context, including why restoration isn't
automatic.

## Summary table — everything that only exists on my machine

| Command | Type | Depends on |
|---|---|---|
| `sshf` | function | `fzf`, `~/.ssh/config` |
| `gco` | function | `fzf`, `git` |
| `fkill [signal]` | function | `fzf`, `ps` |
| `fe` | function | `fzf`, `bat`, `$EDITOR` |
| `frg <pattern>` | function | `fzf`, `grep`, `bat` |
| `gs`, `gd`, `gl` | alias | `git` |
| `ports`, `myip` | alias | `ss`, `ip` |
| `tmux-save`, `tmux-restore` | alias | `tmux`, `tmux-resurrect` plugin |
| `fd`, `bat` | alias + symlink | `fdfind`, `batcat` |

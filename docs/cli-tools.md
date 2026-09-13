# Installed CLI tools

Every tool below is confirmed with `command -v` on my machine. Versions as
of the date this manual was verified (2026-08-23):

| Tool | Version | Real binary | Alias/symlink |
|---|---|---|---|
| fzf | 0.44.1 | `fzf` | — |
| fd-find | 9.0.0 | `fdfind` | `fd` (alias + symlink in `~/.local/bin`) |
| bat | 0.24.0 | `batcat` | `bat` (alias + symlink in `~/.local/bin`) |
| eza | 0.18.2 | `eza` | `ll`/`la`/`l` point to it when present (see below); `exa` on this machine is the same `eza` binary under its old name, not the unmaintained original project |
| tree | v2.1.1 | `tree` | — |
| starship | 1.26.0 | `starship` | — |
| zoxide | 0.9.3 | `zoxide` | `z`/`zi`: my actual day-to-day way of changing directories, see below |
| shellcheck | (installed) | `shellcheck` | — |
| sesh | 2.26.2 | `sesh` | — |
| htop | 3.3.0 | `htop` | — |

> **Why do `fd` and `bat` have two names?** On Debian/Ubuntu (and therefore
> Pop!_OS) the binary names `fd` and `bat` already belonged to other
> packages in the repository, so the `fd-find` and `bat` packages install
> `fdfind` and `batcat` instead. The alias restores the short name in an
> interactive shell; the symlink in `~/.local/bin` restores it everywhere
> else — scripts, `xargs`, and variables such as `FZF_DEFAULT_COMMAND` that
> expand in a non-interactive shell, where aliases don't exist. I can prove
> the difference:
> ```bash
> fd --version              # resolves via the alias
> bash -c 'fd --version'    # only resolves because of the symlink
> ```

---

## fzf — fuzzy finder

Integrated into bash through `~/.bashrc`. Three global keybindings, plus
direct command-line use.

| Shortcut | Action | Engine used |
|---|---|---|
| `Ctrl+R` | Fuzzy search through **command history** | native fzf |
| `Ctrl+T` | Fuzzy search for **files**, inserts the path at the cursor | `fd` (respects `.gitignore`) |
| `Alt+C` | Fuzzy search for **directories**, `cd`s into the selection | `fd` |
| `Ctrl+X Ctrl+R` | Bash's **native** `reverse-i-search` (not fzf) — the only option on a server without fzf | — |

Inside any fzf window:

| Key | Action |
|---|---|
| Type text | Filters by fuzzy match |
| `↑`/`↓` or `Ctrl+J`/`Ctrl+K` | Navigate results |
| `Enter` | Select |
| `Esc` or `Ctrl+C` | Cancel |
| `Tab` (when `--multi` is active) | Mark an item for multi-selection |
| `?` (only in `Ctrl+R` in this setup) | Toggle the full-command preview |

Configured previews:
- `Ctrl+T` shows the file's content, colorized via `bat`.
- `Alt+C` shows the directory tree via `tree`.
- `Ctrl+R` shows the full command (useful when the history line is truncated).

Direct command-line use:
```bash
fzf                     # filters standard input
command | fzf           # filters the output of any command
fzf --multi             # allows selecting multiple items with Tab
git branch | fzf        # example: pick a branch fuzzily — this is the basis of the gco function
```

## fd — file search (my day-to-day replacement for `find`, though the `find` alias stays untouched)

```bash
fd pattern                    # search by name (regex/substring) from the current directory
fd pattern /path              # search starting from a specific directory
fd -e py                      # only files with a .py extension
fd -t f                       # only files (no directories)
fd -t d                       # only directories
fd -H                         # include hidden files (dotfiles)
fd -I                         # ignore .gitignore (search everything)
```

By default `fd` **already ignores** whatever is in `.gitignore` — that's
why `target/`, `node_modules/`, and similar directories never show up in
fzf's `Ctrl+T` results.

## bat — file viewer with syntax highlighting

```bash
bat file.py                       # shows the file with highlighting and paging
bat -A file.txt                   # shows invisible characters (tabs, line breaks)
bat --line-range 10:50 file.py    # shows only a range of lines
bat --diff file.py                # highlights diffs against git
```

By deliberate choice in this setup (see decision D4 in the dotfiles
`README.md`), **there is no `alias cat=bat`** — `bat` paginates and
colorizes by default, which silently breaks pipes in scripts. I use `bat`
deliberately when reading a file, and `cat` when chaining into another
command.

## zoxide: my actual way of changing directories

```bash
z partial-name      # jump to the most "frecent" directory matching the text
z -                  # go back to the previous directory
zi                   # open a fuzzy picker (integrated with fzf) over known directories
```

`z` is what I type day to day, not `cd`; it's faster once `zoxide` has
learned a directory (usually after visiting it once with a real `cd` or by
landing there via any other means: `z` records every directory bash
actually changes into, `cd` included). Plain `cd` still exists and still
works: nothing removes it, `zoxide` just makes it mostly unnecessary once
a path has been visited once.

## eza: `ls` replacement with icons and git status

```bash
eza --icons                                  # like ls, with file-type icons
eza -l --all --group-directories-first       # long format, dotfiles, dirs first
eza --tree --level=2 --icons                 # tree view, 2 levels deep
eza -l --git --icons                         # long format with per-file git status
```

`ll`, `la`, and `l` (see `aliases-functions.md`) resolve to `eza` when it's
installed, falling back to plain `ls` otherwise: same defensive pattern as
`fd`/`bat`, so the same `.bashrc` still works on a bare server that only
has coreutils. Plain `ls` itself is never aliased away, for the same reason
`cat`/`find` aren't: a foundational tool's name should always mean the
foundational tool.

## starship: prompt

Full segment-by-segment reference: [`starship.md`](./starship.md).

## tree — directory tree

```bash
tree                         # tree of the current directory
tree -L 2                    # limits depth to 2 levels
tree -a                      # includes hidden files
tree -C                      # forces color even outside an interactive terminal (used in the Alt+C preview)
tree -I 'node_modules|.git'  # ignores matching patterns
```

## sesh — tmux session manager

See also `tmux.md` (the `prefix s` and `prefix L` shortcuts).

```bash
sesh list                    # lists known sessions (tmux + zoxide directories)
sesh connect <name>          # connects, creating the session if it doesn't exist
sesh last                    # returns to the last used session
sesh clone <repo-url>        # clones a git repository and opens a session in it
sesh picker                  # interactive picker outside of tmux
sesh window                  # lists/switches windows in the current session
```

## shellcheck — bash script linter

```bash
shellcheck script.sh                          # analyzes and reports issues
shellcheck -s bash file                       # forces the bash dialect
shellcheck -e SC1090,SC2148 file               # ignores specific warning codes
```
Used by this dotfiles repository's `setup-terminal.sh` to validate
`.bashrc` and `.bash_aliases` before installing them.

## htop — interactive process monitor

```bash
htop
```

| Key inside htop | Action |
|---|---|
| `F6` | Sort by column (CPU, memory, etc.) |
| `F9` or `k` | Kill the selected process |
| `F5` | Toggle tree view |
| `/` | Search for a process by name |
| `F10` or `q` | Quit |

In this setup, `prefix g` in tmux already opens `htop` in a floating
popup — I don't need to type the command manually.

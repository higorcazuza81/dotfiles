# dotfiles

Personal configuration files for a Linux development environment, managed
with [chezmoi](https://www.chezmoi.io/) and version-controlled with Git.
Applied across two laptops running Pop!_OS and Ubuntu Server virtual
machines hosted on two Proxmox VE nodes.

This document describes what is tracked, how the repository is structured,
and the reasoning behind decisions that are not self-evident from the files
themselves.

## Contents

| Area | Files |
|---|---|
| Shell | `.bashrc`, `.bash_aliases`, `.bash_logout`, `.profile` |
| Git | `.gitconfig` |
| Terminal multiplexer | `.tmux.conf` |
| Prompt | `.config/starship.toml` |
| Terminal emulator | `.config/ghostty/` |
| Editor (terminal) | `.vimrc` |
| Editor (GUI) | `.config/Code/User/settings.json` |
| Terminal reference manual | [`docs/`](./docs/00-index.md) (versioned, not applied — see below) |

## Why chezmoi

Three dotfile managers were considered: GNU Stow (symlink farm, no logic
layer), yadm (treats `$HOME` itself as a Git working tree), and chezmoi
(maintains a separate source directory and computes a target state).

chezmoi was chosen for two reasons. First, it requires an explicit
`chezmoi add` per file rather than exposing the entire home directory to
Git, which reduces the chance of tracking something outside the intended
scope. Second, its templating system supports machine-specific variation
(by hostname, OS, or custom data) without maintaining parallel files, which
is relevant when the same configuration is applied to heterogeneous targets
— in this case, two laptops and multiple homelab VMs with different
hardware constraints.

## Repository layout

chezmoi stores files in its source directory (`~/.local/share/chezmoi`)
using a naming convention that encodes the target path and file attributes.
A leading dot is represented as `dot_`, since Git and most tools do not
handle literal dot-prefixed names well in a repository root.

```
dot_bashrc                          -> ~/.bashrc
dot_bash_aliases                    -> ~/.bash_aliases
dot_config/starship.toml            -> ~/.config/starship.toml
dot_config/ghostty/                 -> ~/.config/ghostty/
```

The mapping is deterministic and reversible; `chezmoi managed` lists every
tracked target path.

Two directories carry a tracked `.keep` file — `.config/tmux/` and
`.config/ghostty/auto/`. Both hold only generated or third-party content
that is excluded via `.chezmoiignore` (tmux plugin clones; the
theme-switcher's `theme.ghostty`), so without a placeholder the directory
itself would have nothing to commit — Git does not track empty
directories. These `.keep` files are a source-repo bookkeeping device only:
chezmoi does not apply them to the target machine (an empty file needs the
`empty_` source attribute to become a real target, which these
deliberately don't have), so they leave no trace under `$HOME`.

## Documentation

[`docs/`](./docs/00-index.md) is a hand-written reference manual for the
terminal stack (Ghostty, tmux, `fzf`/`fd`/`bat`/`zoxide`/`starship`, and the
fuzzy functions in `.bash_aliases`) — keybinding tables and command maps
verified against what these dotfiles actually configure, not a generic
cheat sheet. It is versioned in this repository but listed in
`.chezmoiignore`: it documents the configuration, it isn't itself a dotfile,
so applying it as `~/docs` on every machine (including headless VMs without
Ghostty) would make no sense.

## Shell configuration

### Load order

`.bashrc` follows a fixed section order: history, prompt, colors, aliases,
completion, `PATH`, tool integrations. One ordering constraint applies:
native bash-completion must load before the fzf shell integration. Both
register against the same completion mechanism, and whichever loads second
takes precedence — loading fzf first breaks `Ctrl+T` and `**<Tab>`
completion.

### fzf integration

fzf is integrated using its own code-generation flag when available
(`fzf --bash`), falling back to sourcing the on-disk scripts shipped by the
Debian/Ubuntu package when it is not. This keeps a single `.bashrc` working
across machines with different fzf versions, which varies between the
laptops and the homelab VMs depending on when each was last updated.

| Key | Action |
|---|---|
| `Ctrl+R` | fuzzy history search |
| `Ctrl+T` | insert a file path at the cursor |
| `Alt+C` | fuzzy `cd` into a subdirectory |
| `Ctrl+X Ctrl+R` | native bash reverse-i-search |

fzf takes over `Ctrl+R` by default. The native `reverse-i-search` is
relocated to `Ctrl+X Ctrl+R` rather than dropped, since environments without
fzf — a bare LXC container, a freshly provisioned VM — still need it.

That key combination is not free by default: bash binds `Ctrl+X Ctrl+R` to
`re-read-init-file` (reloads `~/.inputrc` without restarting the shell) out
of the box. The override is intentional: `re-read-init-file` sees little
practical use, and the trade-off is documented in the configuration itself
rather than left implicit.

`fd` is used as fzf's search backend instead of the default `find`, since it
respects `.gitignore` — build output such as `target/` never appears in
`Ctrl+T` results — and performs substantially faster on large trees.

### fd and bat

Debian and Ubuntu rename both binaries due to name collisions with
pre-existing packages: `fd` ships as `fdfind`, `bat` ships as `batcat`. Both
are restored under their upstream names through two independent mechanisms:

- An alias in `.bash_aliases`, valid only in interactive shells.
- A symlink in `~/.local/bin`, valid everywhere, including non-interactive
  contexts.

The distinction is functional, not redundant: `FZF_DEFAULT_COMMAND` and
related variables are expanded by a non-interactive subshell, where aliases
do not exist. Without the symlink, fzf would silently fall back to the
system `find` regardless of the alias being defined.

### Naming convention

No alias overrides the name of a foundational tool. There is no
`alias cat=bat` and no `alias find=fd`. `bat` paginates and colorizes by
default, which breaks pipes and scripts in subtle ways if it silently
replaces `cat`. `fd` has incompatible syntax with `find`, which remains the
tool available on any server regardless of what else is installed. Modern
tools are layered on top of the foundational one, never substituted for its
name.

## Vim configuration

`.vimrc` is a baseline productivity setup, not a plugin-managed IDE
configuration — no plugin manager, no LSP. It sets sane defaults (relative
line numbers, `**` search highlighting, system-clipboard integration,
`<leader>n` to clear search highlight) and per-filetype indentation for the
file types this environment touches most (YAML at 2 spaces, Python/Java at
4, Dockerfile detection).

`$EDITOR` is never exported in `.bashrc` or `.profile`. This is intentional
rather than an oversight: the fuzzy functions in `.bash_aliases` (`fe`,
`frg`) already fall back to `vim` via `"${EDITOR:-vim}"` when the variable
is unset, so `.vimrc` being present is what makes that fallback a usable
default rather than an empty promise.

## Excluded from version control

`.chezmoiignore` excludes:

- `.config/ghostty/auto/theme.ghostty` — regenerated automatically by the
  terminal (theme switching), not hand-authored configuration. Tracking it
  would produce a permanent, meaningless diff.
- `.config/tmux/plugins/` — third-party plugin clones, each with its own
  `.git` directory. These are reconstructed from `.tmux.conf` by the plugin
  manager on a fresh machine and are not user configuration.
- `.config/Code/User/globalStorage`, `workspaceStorage`, `History` — editor
  cache and session state, not configuration.
- Any path matching an SSH private key (`.ssh/`, `id_rsa*`, `id_ed25519*`,
  `id_ecdsa*`, `*.pem`) — excluded unconditionally, independent of which
  files a future `chezmoi add` targets.

## File permissions and umask

chezmoi does not store the exact permission bits of a tracked file. By
design, it records only whether a file is executable or private
(owner-only), and computes the mode to apply from the umask active on the
target machine at apply time.

A umask of `0002` (group-writable by default) rather than the more common
`0022` produces a permanent, content-free diff on every `chezmoi diff`
(`644` vs. `664`, `755` vs. `775`). On a machine where it matters —
`~/.ssh/config`, for instance — group-writable permissions can cause SSH to
refuse the file outright.

The fix is machine-local and intentionally not part of this repository:
chezmoi refuses to let its own configuration file be added to itself, since
it may carry machine-specific values or secrets.

```toml
# ~/.config/chezmoi/chezmoi.toml
umask = 0o022
```

This file is created manually on each machine whose default umask is not
`0022`, before running `chezmoi apply`.

## Public repository

This repository is public. Tracked files are checked for credentials,
tokens, and private key material with a pattern-based search before each
commit, and periodically with a dedicated secret scanner. No secret
material is intentionally version-controlled; SSH private keys are excluded
at the ignore-file level regardless of scan results, as described above.

`.gitconfig` is the one exception to "public means generic": it hardcodes
this author's name and GitHub no-reply email. Anyone forking or reusing
this repository needs to replace `[user]` before applying it, or their
commits will be attributed here instead.

## Setup on a new machine

```bash
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply higorcazuza81
```

This installs chezmoi, clones this repository, and applies every tracked
file to its corresponding path under `$HOME` in a single step.

If the target machine's default umask is not `0022`, create
`~/.config/chezmoi/chezmoi.toml` with the `umask` setting shown above before
running `chezmoi apply`, to avoid a permanent permission diff.

## Dependencies

The configuration assumes the following are installed. Package and binary
names are as found on Debian/Ubuntu-derived distributions — which covers
both Pop!_OS and Ubuntu Server, where two of the binaries are renamed:

| Tool | Debian/Ubuntu package | Binary name |
|---|---|---|
| fzf | `fzf` | `fzf` |
| fd | `fd-find` | `fdfind` |
| bat | `bat` | `batcat` |
| tree | `tree` | `tree` |
| starship | — (installed via upstream script) | `starship` |
| zoxide | — (installed via upstream script) | `zoxide` |
| git | `git` | `git` |

## License

No explicit license is applied. This is a personal configuration
repository, published for reference.

# dotfiles

Personal configuration files for a Linux development environment, managed with
[chezmoi](https://www.chezmoi.io/) and version-controlled with Git. Applied
across two personal laptops and two homelab nodes, all running Pop!_OS.

This document explains what is tracked, how the repository is structured, and
the reasoning behind decisions that are not self-evident from the files
themselves.

## Contents

| Area | Files |
|---|---|
| Shell | `.bashrc`, `.bash_aliases`, `.bash_logout`, `.profile` |
| Git | `.gitconfig` |
| Terminal multiplexer | `.tmux.conf` |
| Prompt | `.config/starship.toml` |
| Terminal emulator | `.config/ghostty/` |
| Editor | `.config/Code/User/settings.json` |

## Why chezmoi

Three dotfile managers were considered: GNU Stow (symlink farm, no logic
layer), yadm (treats `$HOME` itself as a Git working tree), and chezmoi
(maintains a separate source directory and computes a target state).

chezmoi was chosen for two reasons specific to this setup. First, it requires
an explicit `chezmoi add` per file rather than exposing the entire home
directory to Git, which lowers the chance of accidentally tracking something
outside the intended scope. Second, its templating system supports
machine-specific variation (by hostname, OS, or custom data) without
maintaining parallel files — relevant here since the repository targets four
machines with different hardware profiles (two laptops, two resource-constrained
homelab nodes).

## Repository layout

chezmoi stores files in its source directory (`~/.local/share/chezmoi`) using
a naming convention that encodes the target path and file attributes. A
leading dot is represented as `dot_`, since Git and most tools do not handle
literal dot-prefixed names well in a repository root.

```
dot_bashrc                          -> ~/.bashrc
dot_bash_aliases                    -> ~/.bash_aliases
dot_config/starship.toml            -> ~/.config/starship.toml
dot_config/ghostty/                 -> ~/.config/ghostty/
```

The mapping is deterministic and reversible; `chezmoi managed` lists every
tracked target path.

## Shell configuration

### Load order

`.bashrc` follows the file's original section order (history, prompt,
colors, aliases, completion, `PATH`, tool integrations) with one constraint
added: the native bash-completion block must load **before** the fzf
integration block. Both register against the same completion mechanism, and
whichever loads second wins — loading fzf first would silently break
`Ctrl+T` and `**<Tab>` completion.

### fzf integration

fzf is integrated via its own code-generation flag when available
(`fzf --bash`), falling back to sourcing the on-disk scripts shipped by the
Debian/Ubuntu package when it isn't. This keeps a single `.bashrc` working
across machines that may have different fzf versions (laptop vs. homelab
node package versions can drift).

| Key | Action |
|---|---|
| `Ctrl+R` | fuzzy history search |
| `Ctrl+T` | insert a file path at the cursor |
| `Alt+C` | fuzzy `cd` into a subdirectory |
| `Ctrl+X Ctrl+R` | native bash reverse-i-search |

fzf takes over `Ctrl+R` by default. The native `reverse-i-search` is
relocated to `Ctrl+X Ctrl+R` rather than dropped, since environments without
fzf (a bare LXC container, a freshly provisioned node) still need it.

That relocation has a documented side effect: `Ctrl+X Ctrl+R` was not free.
By default it is bound to `re-read-init-file` (reloads `~/.inputrc` without
restarting the shell). This was found by comparing `bind -p` output with and
without the custom `.bashrc` loaded, not anticipated in advance. The
override is accepted — `re-read-init-file` is rarely used — but is called
out explicitly in a comment rather than left silent.

`fd` is used as fzf's search backend instead of the default `find`, since it
respects `.gitignore` (build output such as `target/` never appears in
`Ctrl+T` results) and is substantially faster on large trees.

### fd and bat

Debian and Ubuntu rename both binaries due to name collisions with
pre-existing packages: `fd` ships as `fdfind`, `bat` ships as `batcat`. Both
are restored under their upstream names through two independent mechanisms:

- An alias in `.bash_aliases`, valid only in interactive shells.
- A symlink in `~/.local/bin`, valid everywhere, including non-interactive
  contexts.

The distinction matters concretely: `FZF_DEFAULT_COMMAND` and related
variables are expanded by a non-interactive subshell, where aliases do not
exist. Without the symlink, fzf would silently fall back to the system
`find` regardless of the alias being defined.

### Naming convention

No alias overrides the name of a foundational tool. There is no
`alias cat=bat` and no `alias find=fd`. `bat` paginates and colorizes by
default, which breaks pipes and scripts in subtle ways if it silently
replaces `cat`. `fd` has incompatible syntax with `find`, which remains the
tool available on any server regardless of what is installed locally. Modern
tools are added as a layer on top of the fundamental one, never as a
replacement for its name.

## Excluded from version control

`.chezmoiignore` excludes:

- `.config/ghostty/auto/theme.ghostty` — regenerated automatically by the
  terminal (theme switching), not hand-authored configuration. Including it
  would produce a permanent, meaningless diff.
- `.config/tmux/plugins/` — third-party plugin clones, each with its own
  `.git` directory. These are reconstructed from `.tmux.conf` on a fresh
  machine by the plugin manager itself and are not user configuration.
- `.config/Code/User/globalStorage`, `workspaceStorage`, `History` — editor
  cache and session state, not configuration.
- Any path matching an SSH private key (`.ssh/`, `id_rsa*`, `id_ed25519*`,
  `id_ecdsa*`, `*.pem`) — excluded unconditionally as a standing safety net,
  independent of whichever files a future `chezmoi add` targets.

## File permissions and umask

chezmoi does not store the exact permission bits of a tracked file. By
design, it records only whether a file is executable or private
(owner-only), and computes the actual mode to apply from the umask active on
the target machine at apply time.

This repository's origin machine runs with umask `0002` (group-writable by
default) rather than the more common `0022`. Left uncorrected, this produces
a permanent, content-free diff on every `chezmoi diff` (`644` vs. `664`,
`755` vs. `775`), and on a machine where it matters — `~/.ssh/config`, for
instance — group-writable permissions can cause SSH to refuse the file
outright.

The fix is machine-local, not part of this repository: chezmoi explicitly
refuses to let its own configuration file be added to itself, since it may
carry machine-specific values or secrets.

```toml
# ~/.config/chezmoi/chezmoi.toml
umask = 0o022
```

This file must be created manually on each new machine before running
`chezmoi apply`, if that machine's default umask is not `0022`.

## Public repository

This repository is public. Before each commit, tracked files are checked
for credentials, tokens, and private key material with a pattern-based
grep pass, and periodically with a dedicated secret scanner. No secret
material is intentionally version-controlled; SSH private keys are excluded
at the ignore-file level regardless of scanning results, as described above.

## Setup on a new machine

```bash
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply higorcazuza81
```

This installs chezmoi, clones this repository, and applies every tracked
file to the corresponding path under `$HOME` in a single step.

If the target machine's default umask is not `0022`, create
`~/.config/chezmoi/chezmoi.toml` with the `umask` setting shown above before
running `chezmoi apply` again, to avoid a permanent permission diff.

## Dependencies

The configuration assumes the following are installed. Package names as
found on Debian/Ubuntu-derived distributions, where two of them are renamed:

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

No explicit license is applied. This is a personal configuration repository,
published for reference and portfolio purposes.

# Terminal manual — Ghostty, tmux & CLI tools

This is my personal reference manual for the terminal environment I use
every day. It documents what each tool does, how I use it, and the full
command map — so I can look things up instead of relying on memory, and so
anyone reusing this dotfiles repository can understand the stack without
reverse-engineering the config files.

Every command listed here is verified against what is actually installed
and configured on my machines (`~/.config/ghostty`, `~/.tmux.conf`,
`~/.bashrc`, `~/.bash_aliases`, `~/.config/starship.toml`) — this is not a
generic cheat sheet copied from upstream docs.

## Contents

| File | Covers |
|---|---|
| [`ghostty.md`](./ghostty.md) | Ghostty keybindings — tabs, splits, fonts, search |
| [`tmux.md`](./tmux.md) | Prefix, sessions, windows, panes, copy mode, installed plugins, `sesh` |
| [`starship.md`](./starship.md) | Prompt segments: what each badge means, when it appears |
| [`cli-tools.md`](./cli-tools.md) | `fzf`, `fd`, `bat`, `zoxide`, `starship`, `tree`, `sesh`, `shellcheck`, `htop` |
| [`aliases-functions.md`](./aliases-functions.md) | Aliases and functions from `~/.bash_aliases` (`sshf`, `gco`, `fkill`, `fe`, `frg`) |

## How the stack fits together

```
Ghostty (terminal emulator)
  └─ always opens into: tmux (a fresh, independent session per window)
      └─ tmux (multiplexer — sessions, windows, panes)
          └─ bash (shell)
              ├─ starship    → prompt
              ├─ sesh        → prefix+s, fuzzy-jump between sessions
              ├─ zoxide      → "z" to jump between directories
              ├─ fzf         → Ctrl+R / Ctrl+T / Alt+C
              ├─ fd (fdfind) → fzf's search engine (respects .gitignore)
              ├─ bat (batcat)→ colored preview inside fzf
              └─ fuzzy functions → sshf, gco, fkill, fe, frg
```

The key thing to understand before reading the rest of these files: Ghostty
opens straight into tmux (`command = tmux` in `~/.config/ghostty/config`).
In practice, **every tmux shortcut is available from the first terminal I
open**; I never type `tmux` manually. Each new Ghostty window gets its own
fresh, independent tmux session rather than attaching to a shared one: two
windows never mirror each other. To jump to a specific context (a project,
a server), `prefix + s` opens `sesh`'s fuzzy switcher (see `tmux.md`).

## Where to start

1. `tmux.md`: the layer I use constantly (it's automatic), and `sesh` for jumping between sessions/servers.
2. `starship.md`: what the prompt is telling me at a glance.
3. `cli-tools.md`: `fzf` first (`Ctrl+R`/`Ctrl+T`/`Alt+C` are the highest-impact shortcuts).
4. `aliases-functions.md`: shortcuts that combine tmux, fzf, and git.
5. `ghostty.md`: the terminal emulator's own shortcuts (tabs, graphical splits, font).

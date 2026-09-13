# starship: prompt segments

Config in `~/.config/starship.toml`. Style: a single-line, chained
powerline prompt (palette: Gruvbox Dark), based on starship's official
["Gruvbox Rainbow"](https://starship.rs/presets/gruvbox-rainbow) preset,
adapted for this setup. Prompt input goes on its own second line.

Only installed on this laptop. `.bashrc` checks `command -v starship` and
falls back to a plain bold white-on-red `PS1` when it's absent, which is
the case on every server, so a server prompt is never mistakable for the
local one.

## Reading the chain, left to right

| Segment | Color | Shows | When |
|---|---|---|---|
| Identity | orange | Linux penguin `` + username (+ `@hostname` + container name) | Always. Same penguin regardless of distro (Pop!_OS, Ubuntu, ...): one consistent "you're on Linux" marker across every machine this is applied to |
| Directory | yellow | Current path, truncated to 3 components | Always |
| Git | aqua | Branch name, `+`/`?`/`!` status, ahead/behind | Inside a git repo |
| Language runtime | blue | Icon (+ version) for whatever's detected: Node, Python, Rust, Go, Java, Haskell, PHP, C/C++ | When the directory has that language's marker file |
| Kubernetes | purple | Context (+ namespace if not `default`) | When `kubectl` context detection triggers (a `kubeconfig` in scope) |
| Docker / conda / pixi | dark gray | Context / environment name | When the directory has relevant files (`only_with_files = true` for docker) |
| Time | dark cream | `HH:MM`, 24h | Always |

Second line: just the prompt character (`❯` in yellow on success, red on
error). Command input starts there, not mixed in with the badges above.

## Known cosmetic quirk

The arrows between segments are literal characters in the `format` string,
not conditional on whether the segment next to them has content. In a
plain directory with no git repo, no language marker, and no docker
context (e.g. `$HOME`), the chain still shows empty color bands between
directory and time. This is inherited from the upstream preset, not a bug
introduced here: it shows up any time optional segments in the middle of
a fixed chain have nothing to render.

## Why the identity segment always shows the same penguin

`os.symbols` maps every Linux distro this config might run on (`Pop`,
`Ubuntu`, `Debian`, `Arch`, `Fedora`, `CentOS`, `Alpine`, `Amazon`) to the
same generic penguin (`Linux = "󰌽"`), instead of each distro's own logo.
The two laptops run Pop!_OS; the homelab VMs run Ubuntu Server. Distro
identity isn't the useful signal here: "this is a Linux shell, not macOS
or Windows, and here's the hostname" is, and that reads faster as one
consistent glyph than as a rotating set of distro logos.

## A note on Nerd Font glyphs in this file

Every icon and separator in `starship.toml` is a Private Use Area Unicode
codepoint from the Nerd Font patch set (`JetBrainsMono Nerd Font`, set in
`~/.config/ghostty/config`). These are not visible/typeable characters in
a normal editor session; they were sourced byte-for-byte from starship's
own published preset rather than retyped from memory, specifically because
a hand-typed guess at one of these codepoints fails silently (renders as a
blank square, no error) rather than loudly. If a future edit to this file
needs a new icon, copy the exact codepoint from an existing preset or a
Nerd Font cheat sheet instead of retyping one from memory.

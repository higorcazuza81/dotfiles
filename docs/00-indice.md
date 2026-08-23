# Guia de terminal — Ghostty + tmux + ferramentas CLI

Material de estudo gerado a partir da configuração real deste computador
(`~/.config/ghostty`, `~/.tmux.conf`, `~/.bashrc`, `~/.bash_aliases`,
`~/.config/starship.toml`). Cada comando listado aqui foi conferido contra
o que está de fato instalado e configurado — não é uma lista genérica.

## Arquivos

| Arquivo | Conteúdo |
|---|---|
| [`ghostty.md`](./ghostty.md) | Atalhos de teclado do terminal Ghostty (abas, splits, fontes, busca) |
| [`tmux.md`](./tmux.md) | Prefixo, sessões, janelas, painéis, modo cópia, plugins instalados, `sesh` |
| [`ferramentas-cli.md`](./ferramentas-cli.md) | `fzf`, `fd`, `bat`, `zoxide`, `starship`, `tree`, `sesh`, `shellcheck`, `htop` |
| [`aliases-funcoes.md`](./aliases-funcoes.md) | Aliases e funções do `~/.bash_aliases` (`sshf`, `gco`, `fkill`, `fe`, `frg`) |

## Como está montada a pilha

```
Ghostty (terminal gráfico)
  └─ abre sempre em: tmux new-session -A -s main   (ver config = command)
      └─ tmux (multiplexador — sessões, janelas, painéis)
          └─ bash (shell)
              ├─ starship   → prompt
              ├─ zoxide     → "z" para pular de diretório
              ├─ fzf        → Ctrl+R / Ctrl+T / Alt+C
              ├─ fd (fdfind) → motor de busca do fzf (respeita .gitignore)
              ├─ bat (batcat) → preview colorido dentro do fzf
              └─ funções fuzzy → sshf, gco, fkill, fe, frg
```

Ponto chave para entender o resto dos arquivos: o Ghostty já abre direto
dentro de uma sessão tmux chamada `main` (veja `command = tmux new-session -A
-s main` em `~/.config/ghostty/config`). Ou seja, **todo atalho de tmux vale
desde o primeiro terminal que você abrir** — você não precisa digitar `tmux`
manualmente.

## Ordem sugerida de estudo

1. `tmux.md` — é a camada que você usa o tempo todo (é automática).
2. `ferramentas-cli.md` — `fzf` primeiro (Ctrl+R/Ctrl+T/Alt+C já mudam o dia a dia).
3. `aliases-funcoes.md` — atalhos que combinam tmux + fzf + git.
4. `ghostty.md` — atalhos do terminal em si (abas, splits gráficos, fonte).

# Ghostty — atalhos de teclado

Versão instalada: `1.3.1`. Config em `~/.config/ghostty/config`
(+ `~/.config/ghostty/auto/theme.ghostty` para o tema).

Nenhum keybind foi sobrescrito neste setup — `config.ghostty` está vazio e
`config` só define aparência/comportamento. Ou seja, **tudo abaixo é o
padrão de fábrica do Ghostty**, obtido rodando `ghostty +list-keybinds`.
Isso significa que também funciona em qualquer instalação nova do Ghostty,
sem depender deste dotfiles.

## O que este `config` define (não são atalhos, é comportamento)

| Opção | Valor | Efeito |
|---|---|---|
| `theme` | `iTerm2 Default` (com override para `Catppuccin Mocha` em `auto/theme.ghostty`) | Paleta de cores |
| `background-opacity` | `0.90` | Fundo levemente transparente |
| `background-blur` | `true` | Desfoque atrás da janela |
| `font-family` | `JetBrainsMono Nerd Font` | Fonte (precisa da variante Nerd Font para os ícones do starship/tmux) |
| `shell-integration-features` | `cursor,sudo,title,ssh-env,ssh-terminfo` | Cursor muda ao rodar `sudo`; título da aba segue o comando; SSH propaga terminfo |
| `command` | `tmux new-session -A -s main` | Toda janela nova já abre dentro do tmux |
| `confirm-close-surface` | `true` | Pede confirmação ao fechar painel com processo rodando |
| `notify-on-command-finish` | `unfocused`, após `30s` | Notifica quando um comando demorado termina em aba sem foco |

## Comandos ↔ config

| Comando | O que faz |
|---|---|
| `Ctrl+,` | Abre `~/.config/ghostty/config` no editor padrão |
| `Ctrl+Shift+,` | Recarrega a config sem reiniciar o Ghostty |

## Abas

| Atalho | Ação |
|---|---|
| `Ctrl+Shift+T` | Nova aba |
| `Ctrl+Shift+W` | Fechar aba atual |
| `Ctrl+Tab` / `Ctrl+Shift+Tab` | Próxima / aba anterior |
| `Ctrl+Shift+→` / `Ctrl+Shift+←` | Próxima / aba anterior (alternativa) |
| `Ctrl+Page Down` / `Ctrl+Page Up` | Próxima / aba anterior (alternativa 2) |
| `Alt+1` … `Alt+8` | Ir direto para a aba N |

## Painéis (splits) — dentro da janela do Ghostty, diferente dos painéis do tmux

| Atalho | Ação |
|---|---|
| `Ctrl+Shift+O` | Novo split à direita |
| `Ctrl+Shift+E` | Novo split abaixo |
| `Ctrl+Alt+←/→/↑/↓` | Mover foco entre splits |
| `Super+Ctrl+[` / `Super+Ctrl+]` | Split anterior / próximo (ciclo) |
| `Ctrl+Shift+Enter` | Zoom no split atual (expande/recolhe) |
| `Super+Ctrl+Shift+←/→/↑/↓` | Redimensionar split |

> **Importante:** como este setup abre tmux automaticamente, você tem dois
> sistemas de "painel" sobrepostos — os splits do Ghostty (acima) e os
> painéis do tmux (`tmux.md`). Na prática, prefira sempre os painéis do
> **tmux** (`Ctrl+b %` / `Ctrl+b "`): eles sobrevivem a fechar o terminal e
> aparecem também por SSH. Use os splits do Ghostty só quando quiser duas
> sessões tmux *diferentes* lado a lado.

## Janelas

| Atalho | Ação |
|---|---|
| `Ctrl+Shift+N` | Nova janela do Ghostty |
| `Alt+F4` | Fechar janela |
| `Ctrl+Shift+Q` | Sair do Ghostty |
| `Ctrl+Enter` | Alternar tela cheia |

## Copiar / colar / seleção

| Atalho | Ação |
|---|---|
| `Ctrl+Shift+C` | Copiar |
| `Ctrl+Shift+V` | Colar |
| `Shift+Insert` | Colar (da seleção X11, não do clipboard) |
| `Shift+←/→/↑/↓` | Ajustar seleção |
| `Ctrl+Shift+A` | Selecionar tudo |

## Busca e navegação no scrollback

| Atalho | Ação |
|---|---|
| `Ctrl+Shift+F` | Iniciar busca no histórico da tela |
| `Esc` | Sair da busca |
| `Shift+Page Up` / `Shift+Page Down` | Rolar página inteira |
| `Shift+Home` / `Shift+End` | Ir ao topo / fim do scrollback |
| `Ctrl+Shift+Page Up` / `Ctrl+Shift+Page Down` | Pular para o prompt anterior / próximo |

## Fonte

| Atalho | Ação |
|---|---|
| `Ctrl+=` ou `Ctrl++` | Aumentar fonte |
| `Ctrl+-` | Diminuir fonte |
| `Ctrl+0` | Resetar tamanho da fonte |

## Diagnóstico

| Atalho | Ação |
|---|---|
| `Ctrl+Shift+I` | Abrir o inspector (debug de renderização/eventos) |

## Ver a lista completa

```bash
ghostty +list-keybinds     # todos os keybinds ativos, com origem
ghostty +show-config       # config efetiva completa (default + seu arquivo)
```

# tmux — sessões, janelas, painéis e plugins

Versão instalada: `3.4`. Config em `~/.tmux.conf`. Tema: `catppuccin/tmux`
(flavor `mocha`). Como o Ghostty já abre com
`tmux new-session -A -s main`, você **já está dentro do tmux** assim que
abre um terminal — não precisa digitar `tmux` para começar.

## Prefixo

Todo comando de tmux começa com o **prefixo**. Neste setup existem **dois**:

| Prefixo | Config |
|---|---|
| `Ctrl+b` | padrão do tmux |
| `Ctrl+a` | atalho extra (`prefix2`), mais rápido de digitar |

Nas tabelas abaixo, "prefixo" significa apertar `Ctrl+b` **ou** `Ctrl+a` e
soltar, depois apertar a tecla da ação.

## Descoberta nativa de atalhos

| Atalho | Ação |
|---|---|
| prefixo `?` | Lista **todos** os keybinds ativos (built-in + deste config) — use isso quando esquecer algo |

## Sessões

| Atalho / comando | Ação |
|---|---|
| prefixo `s` | Abre popup do **sesh** (troca de sessão fuzzy — ver seção sesh abaixo) |
| prefixo `L` | Reconecta à **última** sessão usada (`sesh last`) |
| prefixo `d` | Desanexar (detach) da sessão, deixando tudo rodando |
| prefixo `$` | Renomear a sessão atual |
| `tmux ls` (fora do tmux) | Listar sessões |
| `tmux attach -t main` | Reconectar na sessão `main` de fora do tmux |
| `tmux kill-session -t <nome>` | Encerrar uma sessão |

## Janelas (windows — equivalentes a "abas" dentro de uma sessão)

| Atalho | Ação |
|---|---|
| prefixo `c` | Nova janela, abrindo **em `$HOME`** (customizado — o padrão do tmux seria o diretório atual) |
| prefixo `,` | Renomear janela atual |
| prefixo `w` | Listar janelas (navegação visual) |
| prefixo `n` / prefixo `p` | Próxima / janela anterior |
| prefixo `0`–`9` | Ir direto para a janela N |
| prefixo `&` | Fechar janela atual (pede confirmação) |

> Numeração começa em `1`, não em `0` (`base-index 1`), e janelas são
> renumeradas automaticamente ao fechar uma no meio (`renumber-windows on`).

## Painéis (panes — divisões dentro de uma janela)

| Atalho | Ação |
|---|---|
| prefixo `"` | Split **horizontal** (painel abaixo) — herda o diretório atual (customizado) |
| prefixo `%` | Split **vertical** (painel ao lado) — herda o diretório atual (customizado) |
| prefixo `x` | Fecha o painel atual **sem pedir confirmação** (customizado — o padrão pede `y/n`) |
| prefixo `o` | Alternar foco entre painéis |
| prefixo `←/→/↑/↓` (setas) | Mover foco para o painel na direção |
| prefixo `z` | Zoom no painel atual (expande para tela cheia da janela, aperta de novo pra voltar) |
| prefixo `Ctrl+←/→/↑/↓` (segure o prefixo) | Redimensionar painel |
| Mouse | Clicar troca de painel, arrastar a borda redimensiona (`mouse on`) |

## Funções extras estilo Zellij (customizadas neste config)

| Atalho | Ação |
|---|---|
| prefixo `f` | **Terminal flutuante** — popup de 80%×80% no diretório atual |
| prefixo `g` | **Popup com `htop`** rodando (monitor de processos), 80%×80% |

## sesh — troca de sessão inteligente

`sesh` usa `zoxide` para sugerir diretórios/sessões e integra com `fzf`.

| Comando | O que faz |
|---|---|
| prefixo `s` | Popup: `sesh list \| fzf` → conecta na sessão escolhida |
| prefixo `L` | Volta direto pra última sessão (sem popup) |
| `sesh list` | Lista sessões conhecidas (tmux ativas + diretórios do zoxide) |
| `sesh connect <nome>` | Conecta/cria sessão com esse nome |
| `sesh last` | Igual ao prefixo `L`, via linha de comando |
| `sesh clone <repo>` | Clona um repo git e já abre uma sessão nele |
| `sesh picker` | Abre o seletor interativo fora do tmux também |

## Modo cópia (vi-style — `mode-keys vi`)

| Atalho | Ação |
|---|---|
| prefixo `[` | Entrar em modo cópia (navegar o scrollback) |
| `h j k l` | Mover cursor (como no vim) |
| `v` | Iniciar seleção |
| `y` | Copiar seleção e sair do modo cópia |
| `Ctrl+v` | Alternar seleção em bloco (retangular) |
| `q` ou `Esc` | Sair do modo cópia sem copiar |
| `Ctrl+b` / `Ctrl+f` (dentro do modo cópia) | Página anterior / próxima |
| `set-clipboard on` | Cópia no tmux já vai pro clipboard do sistema (X11/Wayland) |

## Persistência de sessão — `tmux-resurrect` / `tmux-continuum`

Salva **sob demanda**, não restaura automático ao abrir o Ghostty
(`@continuum-restore 'off'` — decisão proposital, ver `~/.tmux.conf`).

| Comando (fora do tmux, no bash) | O que faz |
|---|---|
| `tmux-save` | Salva o estado atual de todas as sessões/painéis |
| `tmux-restore` | Restaura o último estado salvo |

O `tmux-continuum` também salva **automaticamente em background** a cada
15 minutos (`@continuum-save-interval '15'`), mas só o `tmux-restore`
manual é que aplica o snapshot.

## Plugins instalados (`~/.tmux/plugins`, gerenciados pelo TPM)

| Plugin | Para que serve |
|---|---|
| `tpm` | Gerenciador de plugins do tmux (instala/atualiza os demais) |
| `tmux-sensible` | Configurações "de bom senso" que praticamente todo setup deveria ter |
| `tmux-yank` | Melhora a integração de copiar para o clipboard do sistema |
| `tmux-resurrect` | Salvar/restaurar sessões, janelas e painéis manualmente |
| `tmux-continuum` | Auto-save em background do `tmux-resurrect` |
| `tmux-battery` | Indicador de bateria (para status bar) |
| `tmux-fzf` | Menus fuzzy do tmux (sessões, janelas, painéis) via `fzf` |
| `catppuccin/tmux` | Tema visual da status bar (flavor `mocha`) |

| Comando TPM (dentro do tmux) | Ação |
|---|---|
| prefixo `I` (I maiúsculo) | Instalar plugins novos listados no `.tmux.conf` |
| prefixo `U` | Atualizar plugins |
| prefixo `Alt+u` | Remover plugins que não estão mais no `.tmux.conf` |

### tmux-fzf

| Atalho | Ação |
|---|---|
| prefixo `F` (F maiúsculo) | Abre o menu fuzzy do `tmux-fzf` (sessões/janelas/painéis/comandos) |

## Configurações de comportamento relevantes (não são comandos, mas mudam o uso)

| Config | Efeito prático |
|---|---|
| `escape-time 0` | Sem delay ao apertar `Esc` (importante pra quem usa vim/neovim) |
| `history-limit 50000` | Scrollback de 50 mil linhas por painel |
| `detach-on-destroy off` | Ao fechar a última janela de uma sessão, cai em outra sessão em vez de sair do tmux |
| `status-position top` | Barra de status fica no topo, não embaixo |

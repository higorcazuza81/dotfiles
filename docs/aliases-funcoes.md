# Aliases e funções personalizadas (`~/.bash_aliases`)

Carregado automaticamente pelo `~/.bashrc`. Tudo aqui é específico deste
setup — não existe em uma instalação padrão do bash.

## Atalhos simples de navegação

| Comando | Equivale a |
|---|---|
| `..` | `cd ..` |
| `...` | `cd ../..` |
| `mkdir pasta` | `mkdir -pv pasta` (cria diretórios pais automaticamente e mostra o que foi criado) |

## Rede

| Comando | O que mostra |
|---|---|
| `ports` | `ss -tulpn` — portas TCP/UDP escutando, com processo dono |
| `myip` | `ip -brief address` — IPs das interfaces, formato resumido |

## Git — atalhos curtos

| Comando | Equivale a | Uso |
|---|---|---|
| `gs` | `git status --short --branch` | Status compacto, com branch no topo |
| `gd` | `git diff` | Diff do que não está staged |
| `gl` | `git log --oneline --graph --decorate -20` | Últimos 20 commits, em grafo |

## `ls` (do `~/.bashrc`, não do `~/.bash_aliases`)

| Comando | Equivale a |
|---|---|
| `ll` | `ls -alF` (lista tudo, formato longo, marca tipo de arquivo) |
| `la` | `ls -A` (lista tudo, exceto `.` e `..`) |
| `l` | `ls -CF` (lista em colunas, marca tipo de arquivo) |

## Funções fuzzy (usam `fzf` por baixo)

São funções — não aliases — porque precisam de argumento, variável local
ou lógica de saída antecipada quando você cancela o fzf com `Esc`.

### `sshf` — conectar via SSH escolhendo o host visualmente

```bash
sshf
```
Lê os `Host` declarados em `~/.ssh/config`, mostra num seletor `fzf` e
conecta no escolhido. Só funciona para hosts já cadastrados nesse arquivo
(sem wildcards `*`/`?`).

### `gco` — trocar de branch git de forma fuzzy

```bash
gco
```
Lista branches locais **e** remotas (`git branch --all`), remove o prefixo
`remotes/origin/`, mostra num seletor `fzf` e faz `git checkout` na
escolhida.

### `fkill` — matar processo escolhendo visualmente

```bash
fkill        # SIGTERM (15) — pedido educado pro processo terminar
fkill 9      # SIGKILL (9) — força a parada
```
Mostra os processos ordenados por uso de CPU. `Tab` marca vários processos
de uma vez (`fzf --multi`) para matar todos juntos.

### `fe` — abrir arquivo no editor, escolhendo visualmente

```bash
fe
```
Abre um seletor `fzf` de arquivos com preview colorido (via `bat`) e abre o
escolhido no `$EDITOR` (padrão: `vim`, se `$EDITOR` não estiver definido).

### `frg` — buscar por CONTEÚDO e abrir direto na linha

```bash
frg "TODO"
frg "function.*login"
```
Diferente do `Ctrl+T` (que busca por **nome** de arquivo), o `frg` faz
`grep` recursivo pelo **conteúdo**, ignorando `.git`, `target/` e
`node_modules/`, mostra os resultados num `fzf` com preview destacando a
linha encontrada, e abre o arquivo já posicionado na linha certa
(`vim +N arquivo`).

## tmux — persistência manual (do `~/.bashrc`)

| Comando | O que faz |
|---|---|
| `tmux-save` | Salva o estado atual de todas as sessões/painéis do tmux (via `tmux-resurrect`) |
| `tmux-restore` | Restaura o último estado salvo |

Ver `tmux.md` para o contexto completo (por que a restauração não é automática).

## Tabela-resumo — tudo que só existe **neste** computador

| Comando | Tipo | Depende de |
|---|---|---|
| `sshf` | função | `fzf`, `~/.ssh/config` |
| `gco` | função | `fzf`, `git` |
| `fkill [sinal]` | função | `fzf`, `ps` |
| `fe` | função | `fzf`, `bat`, `$EDITOR` |
| `frg <padrão>` | função | `fzf`, `grep`, `bat` |
| `gs`, `gd`, `gl` | alias | `git` |
| `ports`, `myip` | alias | `ss`, `ip` |
| `tmux-save`, `tmux-restore` | alias | `tmux`, plugin `tmux-resurrect` |
| `fd`, `bat` | alias + symlink | `fdfind`, `batcat` |

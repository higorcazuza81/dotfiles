# Ferramentas de terminal instaladas

Todas conferidas com `command -v` neste computador. Versões no momento em
que este guia foi gerado (2026-08-23):

| Ferramenta | Versão | Binário real | Alias/symlink |
|---|---|---|---|
| fzf | 0.44.1 | `fzf` | — |
| fd-find | 9.0.0 | `fdfind` | `fd` (alias + symlink em `~/.local/bin`) |
| bat | 0.24.0 | `batcat` | `bat` (alias + symlink em `~/.local/bin`) |
| tree | v2.1.1 | `tree` | — |
| starship | 1.26.0 | `starship` | — |
| zoxide | 0.9.3 | `zoxide` | — |
| shellcheck | (instalado) | `shellcheck` | — |
| sesh | 2.26.2 | `sesh` | — |
| htop | 3.3.0 | `htop` | — |

> **Por que `fd` e `bat` têm dois nomes?** No Debian/Ubuntu (e portanto
> Pop!_OS) os binários `fd` e `bat` já pertenciam a outros pacotes do
> repositório, então os pacotes `fd-find` e `bat` instalam `fdfind` e
> `batcat`. O alias devolve o nome curto em shell interativo; o symlink em
> `~/.local/bin` devolve o nome curto também em scripts, `xargs` e dentro de
> variáveis como `FZF_DEFAULT_COMMAND` (que rodam em shell não-interativo,
> onde alias não existe). Prove a diferença:
> ```bash
> fd --version              # resolve via alias
> bash -c 'fd --version'    # só resolve por causa do symlink
> ```

---

## fzf — busca fuzzy

Integrado ao bash via `~/.bashrc`. Três atalhos globais, mais o uso direto
do comando.

| Atalho | Ação | Motor usado |
|---|---|---|
| `Ctrl+R` | Busca fuzzy no **histórico de comandos** | fzf nativo |
| `Ctrl+T` | Busca fuzzy de **arquivos**, insere o caminho na linha atual | `fd` (respeita `.gitignore`) |
| `Alt+C` | Busca fuzzy de **diretórios**, dá `cd` neles | `fd` |
| `Ctrl+X Ctrl+R` | `reverse-i-search` **nativo** do bash (não é o fzf) — único disponível em servidor sem fzf | — |

Dentro de qualquer janela do fzf:

| Tecla | Ação |
|---|---|
| Digite texto | Filtra por fuzzy match |
| `↑`/`↓` ou `Ctrl+J`/`Ctrl+K` | Navega os resultados |
| `Enter` | Seleciona |
| `Esc` ou `Ctrl+C` | Cancela |
| `Tab` (quando `--multi` está ativo) | Marca item para seleção múltipla |
| `?` (só no `Ctrl+R` deste setup) | Mostra/esconde o preview do comando completo |

Preview configurado:
- `Ctrl+T` mostra o conteúdo do arquivo colorido via `bat`.
- `Alt+C` mostra a árvore do diretório via `tree`.
- `Ctrl+R` mostra o comando completo (útil quando a linha do histórico está truncada).

Uso direto em linha de comando:
```bash
fzf                     # filtra a entrada padrão (stdin)
comando | fzf           # filtra a saída de qualquer comando
fzf --multi             # permite selecionar vários itens com Tab
git branch | fzf        # exemplo: escolher uma branch fuzzy (é a base da função gco)
```

## fd — busca de arquivos (substitui o `find` no dia a dia, mas não o alias `find`)

```bash
fd padrao                     # busca por nome (regex/substring) a partir do dir atual
fd padrao /caminho            # busca a partir de um diretório específico
fd -e py                      # só arquivos com extensão .py
fd -t f                       # só arquivos (não diretórios)
fd -t d                       # só diretórios
fd -H                         # inclui arquivos ocultos (dotfiles)
fd -I                         # ignora o .gitignore (busca tudo mesmo)
```

Por padrão o `fd` **já ignora** o que está no `.gitignore` — por isso
`target/`, `node_modules/` etc. nunca aparecem no `Ctrl+T` do fzf.

## bat — leitura de arquivo com syntax highlight

```bash
bat arquivo.py                       # mostra com highlight e paginação
bat -A arquivo.txt                   # mostra caracteres invisíveis (tabs, quebras de linha)
bat --line-range 10:50 arquivo.py    # só um trecho
bat --diff arquivo.py                # destaca diffs em relação ao git
```

Por decisão deste setup (ver `README.md` do dotfiles, decisão D4), **não
existe `alias cat=bat`** — o `bat` pagina e colore por padrão, o que quebra
pipes de script de forma sutil. Use `bat` deliberadamente quando quiser ler;
use `cat` quando for encadear em outro comando.

## zoxide — `cd` com memória

```bash
z nome-parcial     # pula para o diretório mais "frecente" que combina com o texto
z -                # volta para o diretório anterior
zi                 # abre um seletor fuzzy (integrado ao fzf) entre os diretórios conhecidos
```

`z` **não substitui** `cd` — os dois convivem. `zoxide` aprende os
diretórios conforme você usa `cd` normalmente.

## starship — prompt

Não é um comando do dia a dia, mas vale saber ler o que ele mostra
(`~/.config/starship.toml`):

| Segmento do prompt | Quando aparece |
|---|---|
| Hostname (vermelho) | Só quando você está em uma sessão **SSH** |
| Branch git (verde) | Dentro de um repositório git |
| Status do git (`!` `?` `+`) | Modificado / não rastreado / staged |
| Ícone ☸ (kubernetes) | Quando há contexto kubectl ativo |
| Ícone 🐳 (docker) | Só quando há arquivos relacionados a Docker no diretório |
| "took Xs" (amarelo) | Só se o comando anterior levou mais de 2s |
| `❯` verde/vermelho | Sucesso/erro do último comando |

## tree — árvore de diretórios

```bash
tree                       # árvore do diretório atual
tree -L 2                  # limita a profundidade a 2 níveis
tree -a                    # inclui arquivos ocultos
tree -C                    # força cores mesmo fora de terminal interativo (usado no preview do Alt+C)
tree -I 'node_modules|.git'  # ignora padrões
```

## sesh — gerenciador de sessões tmux

Ver também `tmux.md` (atalhos `prefixo s` e `prefixo L`).

```bash
sesh list                    # lista sessões conhecidas (tmux + diretórios do zoxide)
sesh connect <nome>           # conecta, criando a sessão se não existir
sesh last                     # volta pra última sessão usada
sesh clone <url-do-repo>      # clona um repositório git e abre sessão nele
sesh picker                   # seletor interativo fora do tmux
sesh window                   # lista/alterna janelas da sessão atual
```

## shellcheck — lint de scripts bash

```bash
shellcheck script.sh                          # analisa e aponta problemas
shellcheck -s bash arquivo                    # força o dialeto bash
shellcheck -e SC1090,SC2148 arquivo           # ignora avisos específicos por código
```
Usado pelo `setup-terminal.sh` deste dotfiles para validar `.bashrc` e
`.bash_aliases` antes de instalar.

## htop — monitor de processos interativo

```bash
htop
```

| Tecla dentro do htop | Ação |
|---|---|
| `F6` | Ordenar por coluna (CPU, memória, etc.) |
| `F9` ou `k` | Matar processo selecionado |
| `F5` | Alternar visão em árvore |
| `/` | Buscar processo por nome |
| `F10` ou `q` | Sair |

Neste setup, `prefixo g` no tmux já abre o `htop` num popup flutuante —
não precisa digitar o comando manualmente.

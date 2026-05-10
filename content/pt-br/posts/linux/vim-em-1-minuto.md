---
title: NeoVim em 1 minuto!
slug: 1-minute-nvim
description: Resumo dos principais comandos e casos de uso do nvim!
summary: Resumo dos principais comandos e casos de uso do nvim!
tags:
  - vim
  - nvim
  - neovim
  - linux
  - cachyos
  - linux
categories:
  - linux
  - 1minute
author: Gabriel Maggioni
date: 2026-05-09T22:40:00
lastmod: 2026-05-10T12:42:00
showToc: true
TocOpen: false
draft: false
---

⚡Neovim em 1 minuto!
Cola prática pra instalar, configurar e usar o Neovim no dia a dia!

***

🧩 **O que é o Neovim?**
O Neovim é uma versão moderna do Vim — mais rápido, com suporte a plugins, LSP nativo (autocomplete igual a uma IDE) e configuração em Lua. É o editor favorito de quem vive no terminal.

***

📦 **Instalação**

| Distro | Comando |
| --- | --- |
| Arch / CachyOS | `sudo pacman -S neovim` |
| Fedora | `sudo dnf install neovim` |
| Ubuntu / Debian | `sudo apt install neovim` |
| Mac (brew) | `brew install neovim` |

> 💡 Verifique a versão com `nvim --version`. Prefira sempre **0.9+**.

***

🚪 **Abrir Arquivos**

| Comando | Função |
| --- | --- |
| `nvim arquivo.txt` | abrir arquivo |
| `nvim .` | abrir explorador de arquivos |
| `nvim` | abrir vazio |

***

🗂️ **Os 3 Modos que Você Precisa Entender**

| Modo | Como entrar | Pra que serve |
| --- | --- | --- |
| **Normal** | `Esc` | navegar, copiar, apagar |
| **Insert** | `i` | escrever texto |
| **Command** | `:` | salvar, sair, configurar |

> 💡 Você **sempre começa** no modo Normal. Esse é o mais importante.

***

✍️ **Entrar no Modo Insert**

| Tecla | Função |
| --- | --- |
| `i` | inserir antes do cursor |
| `a` | inserir após o cursor |
| `A` | inserir no fim da linha |
| `o` | nova linha abaixo |
| `O` | nova linha acima |
| `Esc` | voltar ao modo Normal |

***

💾 **Salvar e Sair**

| Comando | Função |
| --- | --- |
| `:w` | salvar |
| `:q` | sair |
| `:wq` | salvar e sair |
| `:q!` | sair sem salvar |
| `:wqa` | salvar e fechar todos os buffers |

***

🧭 **Navegação**

| Tecla | Movimento |
| --- | --- |
| `h j k l` | ← ↓ ↑ → |
| `w` | próxima palavra |
| `b` | palavra anterior |
| `0` | início da linha |
| `$` | fim da linha |
| `gg` | início do arquivo |
| `G` | fim do arquivo |
| `:10` | ir para linha 10 |
| `Ctrl + d` | descer meia tela |
| `Ctrl + u` | subir meia tela |

***

✂️ **Edição**

| Comando | Função |
| --- | --- |
| `dd` | apagar linha inteira |
| `yy` | copiar linha |
| `p` | colar abaixo |
| `P` | colar acima |
| `u` | desfazer |
| `Ctrl + r` | refazer |
| `x` | apagar caractere |
| `ciw` | apagar e editar palavra |
| `cc` | apagar e editar linha inteira |
| `D` | apagar do cursor até o fim da linha |

***

🔍 **Pesquisa e Substituição**

| Comando | Função |
| --- | --- |
| `/texto` | buscar no arquivo |
| `n` | próximo resultado |
| `N` | resultado anterior |
| `*` | buscar palavra sob o cursor |
| `:%s/velho/novo/g` | substituir em todo o arquivo |
| `:%s/velho/novo/gc` | substituir com confirmação |

***

🪟 **Janelas e Buffers**

| Comando | Função |
| --- | --- |
| `:split` | dividir horizontalmente |
| `:vsplit` | dividir verticalmente |
| `Ctrl + w + w` | alternar entre janelas |
| `Ctrl + w + h/j/k/l` | mover entre janelas |
| `:bn` | próximo buffer |
| `:bp` | buffer anterior |
| `:bd` | fechar buffer |

***

⚙️ **Configuração Básica**
O arquivo de config fica em `~/.config/nvim/init.lua` (Lua) ou `init.vim` (VimScript).

```bash
mkdir -p ~/.config/nvim
nvim ~/.config/nvim/init.lua
```

Config mínima pra começar bem:

```lua
-- Números de linha
vim.opt.number = true
vim.opt.relativenumber = true
-- Indentação
vim.opt.tabstop = 2
vim.opt.shiftwidth = 2
vim.opt.expandtab = true
-- Busca
vim.opt.ignorecase = true
vim.opt.smartcase = true
-- Visual
vim.opt.termguicolors = true
vim.opt.wrap = false
```

***

## 🚀 **Comandos mais usados no dia a dia - SALVE ESTA LISTA!**


| SALVAR E SAIR |  |
| --- | --- |
| Ação | Comando |
| Salvar | `:w` |
| Sair | `:q` |
| Salvar e sair | `:wq` |
| Sair sem salvar | `:q!` |


| INSERÇÃO DE TEXTO |  |
| --- | --- |
| Ação | Comando |
| Inserir antes do cursor | `i` |
| Inserir no fim da linha | `A` |
| Inserir no início da linha | `I` |
| Nova linha abaixo | `o` |
| Nova linha acima | `O` |


| EDIÇÃO BÁSICA |  |
| --- | --- |
| Ação | Comando |
| Apagar palavra | `dw` |
| Apagar linha | `dd` |
| Desfazer | `u` |
| Refazer | `Ctrl + r` |


| SUBSTITUIÇÃO |  |
| --- | --- |
| Ação | Comando |
| Substituir na linha | `:s/antigo/novo/g` |
| Substituir no arquivo | `:%s/antigo/novo/g` |


| COPIAR E COLAR |  |
| --- | --- |
| Ação | Comando |
| Copiar linha | `yy` |
| Copiar palavra | `yw` |
| Colar depois | `p` |
| Colar antes | `P` |


| SELEÇÃO |  |
| --- | --- |
| Ação | Comando |
| Selecionar texto | `v` |
| Selecionar linha | `V` |
| Selecionar bloco | `Ctrl + v` |
| Sair da seleção | `Esc` |

***

Com isso você já usa o Neovim de verdade — e quando pegar o jeito, nunca mais vai querer voltar 😄

***

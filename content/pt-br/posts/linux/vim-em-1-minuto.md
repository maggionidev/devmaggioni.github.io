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
lastmod: 2026-05-10T16:25:00
showToc: true
TocOpen: false
draft: false
---

Guia rápido pra instalar, configurar e usar Neovim no dia a dia.

***

## 🧠 O que é o Neovim?

O Neovim é uma versão moderna do Vim.

Ele é:

* mais rápido
* extensível com plugins
* suporta LSP nativo (autocomplete estilo IDE)
* configurado em Lua

Perfeito pra quem vive no terminal.

***

## 📦 Instalação

| Distro | Comando |
| --- | --- |
| Arch / CachyOS | `sudo pacman -S neovim` |
| Fedora | `sudo dnf install neovim` |
| Ubuntu / Debian | `sudo apt install neovim` |
| Mac (brew) | `brew install neovim` |

💡 Confira com:

```bash
nvim --version
```

Ideal: versão 0.9 ou superior.

***

## 🚪 Abrir arquivos

| Comando | Função |
| --- | --- |
| `nvim arquivo.txt` | abre um arquivo |
| `nvim .` | abre diretório |
| `nvim` | abre vazio |

***

## 🧭 Modos principais

| Modo | Como entrar | Uso |
| --- | --- | --- |
| Normal | `Esc` | navegar e editar |
| Insert | `i` | escrever texto |
| Command | `:` | comandos |

💡 Você sempre começa no modo Normal.

***

## ✍️ Modo Insert

| Tecla | Ação |
| --- | --- |
| `i` | antes do cursor |
| `a` | depois do cursor |
| `A` | fim da linha |
| `o` | nova linha abaixo |
| `O` | nova linha acima |
| `Esc` | voltar ao Normal |

***

## 💾 Salvar e sair

| Comando | Ação |
| --- | --- |
| `:w` | salvar |
| `:q` | sair |
| `:wq` | salvar e sair |
| `:q!` | sair sem salvar |
| `:wqa` | salvar e fechar tudo |

***

## 🧭 Navegação

| Tecla | Ação |
| --- | --- |
| `h j k l` | mover cursor |
| `w` | próxima palavra |
| `b` | palavra anterior |
| `0` | início da linha |
| `$` | fim da linha |
| `gg` | início do arquivo |
| `G` | fim do arquivo |
| `:10` | ir pra linha 10 |
| `Ctrl+d` | descer |
| `Ctrl+u` | subir |

***

## ✂️ Edição

| Comando | Ação |
| --- | --- |
| `dd` | apagar linha |
| `yy` | copiar linha |
| `p` | colar abaixo |
| `P` | colar acima |
| `u` | desfazer |
| `Ctrl+r` | refazer |
| `x` | apagar caractere |
| `ciw` | editar palavra |
| `cc` | editar linha |
| `D` | apagar até o fim |

***

## 🔍 Busca e substituição

| Comando | Ação |
| --- | --- |
| `/texto` | buscar |
| `n` | próximo |
| `N` | anterior |
| `*` | buscar palavra atual |
| `:%s/velho/novo/g` | substituir tudo |
| `:%s/velho/novo/gc` | substituir com confirmação |

***

## 🪟 Janelas e buffers

| Comando | Ação |
| --- | --- |
| `:split` | horizontal |
| `:vsplit` | vertical |
| `Ctrl+w w` | trocar janela |
| `Ctrl+w h/j/k/l` | navegar |
| `:bn` | próximo buffer |
| `:bp` | buffer anterior |
| `:bd` | fechar buffer |

***

## ⚙️ Configuração básica

Arquivo principal:

```bash
~/.config/nvim/init.lua
```

Criar:

```bash
mkdir -p ~/.config/nvim
nvim ~/.config/nvim/init.lua
```

Config mínima:

```lua
vim.opt.number = true
vim.opt.relativenumber = true

vim.opt.tabstop = 2
vim.opt.shiftwidth = 2
vim.opt.expandtab = true

vim.opt.ignorecase = true
vim.opt.smartcase = true

vim.opt.termguicolors = true
vim.opt.wrap = false
```

***

## 🚀 Atalhos essenciais

### Salvar e sair

| Ação | Comando |
| --- | --- |
| salvar | `:w` |
| sair | `:q` |
| salvar e sair | `:wq` |
| sair sem salvar | `:q!` |

***

### Inserção

| Ação | Comando |
| --- | --- |
| antes do cursor | `i` |
| fim da linha | `A` |
| início da linha | `I` |
| nova linha abaixo | `o` |
| nova linha acima | `O` |

***

### Edição rápida

| Ação | Comando |
| --- | --- |
| apagar palavra | `dw` |
| apagar linha | `dd` |
| desfazer | `u` |
| refazer | `Ctrl+r` |

***

### Copiar e colar

| Ação | Comando |
| --- | --- |
| copiar linha | `yy` |
| copiar palavra | `yw` |
| colar depois | `p` |
| colar antes | `P` |

***

### Seleção

| Ação | Comando |
| --- | --- |
| selecionar | `v` |
| linha inteira | `V` |
| bloco | `Ctrl+v` |
| sair | `Esc` |

***

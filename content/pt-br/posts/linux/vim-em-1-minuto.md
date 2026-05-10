---
title: Vim em 1 minuto!
slug: 1-minute-vim
description: Resumo dos principais comandos e casos de uso do vim!
summary: Resumo dos principais comandos e casos de uso do vim!
tags:
  - vim
  - linux
  - cachyos
  - linux
categories:
  - linux
  - 1minute
keywords:
  - '# ⚡ Vim em 1 minuto!'
  - '> Cola prática pra consultar enquanto aprende 😄'
  - ---
  - '# 🚪 Abrir Arquivos'
  - '| Comando           | Função          |'
  - '| ----------------- | --------------- |'
  - '| `vim arquivo.txt` | abrir arquivo   |'
  - '| `vim`             | abrir Vim vazio |'
  - ---
  - '# ✍️ Entrar no Modo de Escrita'
  - '| Tecla | Função                  |'
  - '| ----- | ----------------------- |'
  - '| `i`   | inserir texto           |'
  - '| `a`   | inserir após cursor     |'
  - '| `A`   | inserir no fim da linha |'
  - '| `o`   | nova linha abaixo       |'
  - '| `O`   | nova linha acima        |'
  - ---
  - '# ⛔ Voltar ao Modo Normal'
  - '| Tecla | Função              |'
  - '| ----- | ------------------- |'
  - '| `Esc` | sair do modo INSERT |'
  - ---
  - '# 💾 Salvar e Sair'
  - '| Comando | Função          |'
  - '| ------- | --------------- |'
  - '| `:w`    | salvar          |'
  - '| `:q`    | sair            |'
  - '| `:wq`   | salvar e sair   |'
  - '| `:q!`   | sair sem salvar |'
  - ---
  - '# 🧭 Navegação'
  - '| Tecla | Movimento |'
  - '| ----- | --------- |'
  - '| `h`   | esquerda  |'
  - '| `j`   | baixo     |'
  - '| `k`   | cima      |'
  - '| `l`   | direita   |'
  - ---
  - '# ✂️ Edição'
  - '| Comando    | Função       |'
  - '| ---------- | ------------ |'
  - '| `dd`       | apagar linha |'
  - '| `yy`       | copiar linha |'
  - '| `p`        | colar        |'
  - '| `u`        | desfazer     |'
  - '| `Ctrl + r` | refazer      |'
  - ---
  - '# 🔍 Pesquisa'
  - '| Comando     | Função             |'
  - '| ----------- | ------------------ |'
  - '| `/texto`    | procurar texto     |'
  - '| `n`         | próximo resultado  |'
  - '| `Shift + n` | resultado anterior |'
  - ---
  - '# 📄 Navegação em Arquivo'
  - '| Comando | Função            |'
  - '| ------- | ----------------- |'
  - '| `gg`    | início do arquivo |'
  - '| `G`     | fim do arquivo    |'
  - '| `:10`   | ir para linha 10  |'
  - ---
  - '# 🔥 Comandos Úteis no Dia a Dia'
  - '| Comando            | Função           |'
  - '| ------------------ | ---------------- |'
  - '| `:%s/velho/novo/g` | substituir texto |'
  - '| `:set number`      | mostrar linhas   |'
  - '| `:syntax on`       | ativar cores     |'
  - ---
  - '# 🧠 Fluxo Básico Que Você Vai Fazer Sempre'
  - '```text id="vflow1"'
  - vim arquivo.txt
  - i
  - (escreve)
  - Esc
  - :wq
  - '```'
  - ---
  - '# 🎯 Os 10 Que Vale Decorar Primeiro'
  - '```text id="vimtop10"'
  - i
  - Esc
  - :w
  - :q
  - :wq
  - :q!
  - dd
  - yy
  - p
  - u
  - '```'
  - Com isso sozinho você já consegue sobreviver tranquilamente no Vim 😄
author: Gabriel Maggioni
date: 2026-05-09T22:40:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

# ⚡Vim em 1 minuto!

> Cola prática pra consultar enquanto aprende!

***

# 🚪 Abrir Arquivos

| Comando | Função |
| --- | --- |
| `vim arquivo.txt` | abrir arquivo |
| `vim` | abrir Vim vazio |

***

# ✍️ Entrar no Modo de Escrita

| Tecla | Função |
| --- | --- |
| `i` | inserir texto |
| `a` | inserir após cursor |
| `A` | inserir no fim da linha |
| `o` | nova linha abaixo |
| `O` | nova linha acima |

***

# ⛔ Voltar ao Modo Normal

| Tecla | Função |
| --- | --- |
| `Esc` | sair do modo INSERT |

***

# 💾 Salvar e Sair

| Comando | Função |
| --- | --- |
| `:w` | salvar |
| `:q` | sair |
| `:wq` | salvar e sair |
| `:q!` | sair sem salvar |

***

# 🧭 Navegação

| Tecla | Movimento |
| --- | --- |
| `h` | esquerda |
| `j` | baixo |
| `k` | cima |
| `l` | direita |

***

# ✂️ Edição

| Comando | Função |
| --- | --- |
| `dd` | apagar linha |
| `yy` | copiar linha |
| `p` | colar |
| `u` | desfazer |
| `Ctrl + r` | refazer |

***

# 🔍 Pesquisa

| Comando | Função |
| --- | --- |
| `/texto` | procurar texto |
| `n` | próximo resultado |
| `Shift + n` | resultado anterior |

***

# 📄 Navegação em Arquivo

| Comando | Função |
| --- | --- |
| `gg` | início do arquivo |
| `G` | fim do arquivo |
| `:10` | ir para linha 10 |

***

# 🔥 Comandos Úteis no Dia a Dia

| Comando | Função |
| --- | --- |
| `:%s/velho/novo/g` | substituir texto |
| `:set number` | mostrar linhas |
| `:syntax on` | ativar cores |

***

# 🧠 Fluxo Básico Que Você Vai Fazer Sempre

```plain
vim arquivo.txt
i
(escreve)
Esc
:wq
```

***

# 🎯 Os 10 Que Vale Decorar Primeiro

```plain
i
Esc
:w
:q
:wq
:q!
dd
yy
p
u
```

Com isso sozinho você já consegue sobreviver tranquilamente no Vim 😄

***

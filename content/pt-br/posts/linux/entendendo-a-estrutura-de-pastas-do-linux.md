---
title: Entendendo a estrutura de pastas do Linux
slug: understanding-the-linux-folder-structure
description: Entenda de uma vez por todas como funciona o sistema de pastas e arquivos do linux
summary: Entenda de uma vez por todas como funciona o sistema de pastas e arquivos do linux
tags:
  - linux
  - aprendendo
  - sistema de arquivos
categories:
  - linux
keywords: []
author: Gabriel Maggioni
date: 2026-05-02T20:00:00
lastmod: ''
showToc: true
TocOpen: false
draft: true
---

## Introdução

Se você cresceu usando Windows, é bem provável que a ideia de "disco C:" esteja quase automática no seu cérebro. Programas em um lugar, arquivos pessoais em outro, tudo meio segmentado por letras que organizam o caos de forma bem intuitiva.

Aí você abre o Linux pela primeira vez e… nada de C:, D:, E:. Em vez disso, você encontra uma única árvore de diretórios, começando pela raiz, o famoso "/". E isso causa um pequeno curto no início, porque o jeito de pensar muda completamente.

No Linux, tudo faz parte do mesmo sistema de arquivos. Discos, pendrives, pastas do sistema, configurações, usuários, tudo vive dentro dessa estrutura hierárquica. Entender isso não é só um detalhe técnico, é praticamente a chave para começar a se sentir confortável dentro do sistema.

E quando essa virada de mentalidade acontece, o Linux deixa de parecer estranho e começa a fazer sentido de um jeito bem mais profundo do que parece à primeira vista.

***

## O sistema de arquivos do Linux

O linux geralmente é composto por essa estrutura de pastas básicas:

```plain
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
└── usr
└── var
```

vamos entender como cada pasta funciona e suas responsabilidades:

***

## `ROOT`

No Windows, as coisas costumam ficar geralmente em `C:\`. No Linux, a pasta principal do sistema fica em`/`.

`/` é a raiz do sistema, onde todas as pastas vivem. Basicamente, no root você vê todo o sistema aberto, e tudo oque tem nele.

![Print Sistema de Arquivos do Linux](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_200719.png "Print Sistema de Arquivos do Linux")

Perceba que, algumas pastas tem um ícone de "link", alguns tem um ícone de "cadeado", e algumas outras tem um "xis"(embora não apareça especificamente nesse print).

As pastas com o ícone de link são “links simbólicos” (symlinks) criados pelo sistema. Isso quer dizer que você pode ver a pasta `bin` dentro de `/`, mas ela não está realmente ali. Na prática, ela aponta para outro local do sistema, como se fosse um atalho, só que em nível de filesystem.


![print dolphin](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_202608.png)









***

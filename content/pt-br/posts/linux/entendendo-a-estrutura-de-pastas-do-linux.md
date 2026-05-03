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

## A pasta `ROOT` (ou `/`)

No Windows, as coisas costumam ficar geralmente em `C:\`. No Linux, a pasta principal do sistema fica em /.

`/` (ou "root") é a raiz do sistema, onde todas as pastas vivem. Basicamente, no root você vê todo o sistema aberto, e tudo oque tem nele.

![Print Sistema de Arquivos do Linux](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_200719.png "Print Sistema de Arquivos do Linux")

Perceba que, algumas pastas tem um ícone de "link", alguns tem um ícone de "cadeado" ou de "xis".

### As pastas com o ícone de link...

... são "links simbólicos" (symlinks) criados pelo sistema. Isso quer dizer que você pode ver a pasta `bin` dentro de `/`, mas ela não está realmente ali. Na prática, ela aponta para outro local do sistema, como se fosse um atalho, só que em nível de filesystem.

Se você olhar no print,  pode ver que o link está em `/`, mas aponta pra `usr/bin`.

![print dolphin](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_202608.png)

### As pastas com o ícone de xis ou cadeado...

...são pastas cujo seu usuário atual não possui acesso, ou privilégios para acessar (para acessá-las pode ser necessário digitar a senha do admin pra fazer alterações).

Note que, essas pastas são protegidas por um motivo: assegurar que você não vai estragar o seu sistema; Não é recomendado mecher nelas, a menos que você entenda o que está fazendo. O próprio sistema lhe avisa sobre os riscos:

![pasta admin](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_203527.png)

***

## Entendo as pastas 

### - `BIN`

O nome `bin` deriva de "binaries" (binários).

Ela é a pasta que contém os executáveis dos aplicativos que você usa.
Se você passear por esta pasta, verá vários nomes comuns no seu dia: 

Comandos como `ls`, `cp`, `mv`, `rm`, `cat`, `mkdir` aparecem por aqui, e são justamente esses pequenos executáveis que você usa o tempo todo sem nem pensar muito nisso.

Você pode, inclusive, jogar um executável seu aqui dentro (de alguma cli que você baixou ou criou) e poderá chamá-lo de qualquer lugar, apenas digitando o seu nome. 

### - `BOOT`

Como o nome sugere, esta é a pasta responsável pelo boot do seu sistema.
Ela armazena todos os arquivos e configurações responsáveis pela inicialização do seu linux.

Aqui você encontra a pasta do `GRUB`. Ele é o bootloader que entra em ação logo após o firmware da máquina iniciar o processo de boot. É ele quem decide qual sistema será carregado, especialmente em cenários de dual boot, permitindo escolher entre Linux, Windows ou qualquer outro sistema instalado antes de iniciar de fato o sistema operacional.

Aqui você encontra também, curiosamente, o próprio kernel do linux, no meu caso como uso o cachyos, ele usa sua própria versão customizada: "vmlinuz-linux-cachyos".

### - `DEV`

A pasta `dev` me enganou ao pensar ser "developer". Na real, ela é abreviação de "devices" (dispositivos).

Essa pasta é, muito interessante, e conceitualmente distinta do windows;

Ela abriga arquivos que, incrivelmente, representam o seu hardware. No linux, todo e qualquer dispositivo que você tem no seu pc é, olha que loucura, representado por um arquivo. Ou seja, seu teclado, mouse, hd, ssd, estão todos aqui.

![pasta dev](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_210543.png)

### - `ETC` 

Etcétara? Não sei oque significa, e há divergências sobre o real significado da palavra. No entanto isso não é relevante para nós.

Essa pasta costuma abrigar arquivos de configurações dos softwares do seu sistema. Ou seja, você pode configurar algo pela interface gráfica, e as configurações podem vir parar em algum lugar por aqui.

### - `HOME`

Essa é a pasta que nos interessa!

É aqui que ficam as pastas dos usuários, e seus respectivos arquivos. Essa é a pasta mais semelhante ao que temos no windows:

![print pasta home](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_211804.png)

Aqui é onde criamos os nossos arquivos, e trabalhamos no dia a dia.

***

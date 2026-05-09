---
title: Guia Completo de Snapshots Btrfs no Arch Linux com Snapper
slug: btrfs-snapshots-arch-linux
description: Aprenda como funcionam snapshots Btrfs no Arch Linux e CachyOS usando Snapper. Veja como criar snapshots automáticos, rollback, recuperar arquivos, limitar espaço usado e configurar subvolumes corretamente.
summary: Aprenda como funcionam snapshots Btrfs no Arch Linux e CachyOS usando Snapper. Veja como criar snapshots automáticos, rollback, recuperar arquivos, limitar espaço usado e configurar subvolumes corretamente.
tags:
  - btrfs
  - snapper
  - arch linux
  - cachyos
  - linux
  - snapshots
  - rollback
  - filesystem
  - subvolumes
  - copy on write
  - cow
  - grub-btrfs
  - pacman
  - backup linux
  - btrfs snapshots
  - linux backup
  - linux filesystem
  - ssd linux
  - snapper rollback
categories:
  - linux
keywords:
  - btrfs
  - snapper
  - arch linux
  - cachyos
  - linux
  - snapshots
  - rollback
  - filesystem
  - subvolumes
  - copy on write
  - cow
  - grub-btrfs
  - pacman
  - backup linux
  - btrfs snapshots
  - linux backup
  - linux filesystem
  - ssd linux
  - snapper rollback
  - btrfs guide
author: Gabriel Maggioni
date: 2026-05-08T19:24:00
lastmod: 2026-05-08T20:43:00
showToc: true
TocOpen: false
draft: false
---

## O que é Btrfs?

Btrfs é um sistema de arquivos moderno do Linux. Ele funciona como o "organizador” do SSD: decide como os arquivos são salvos, movidos e recuperados.

O diferencial dele é o **Copy-on-Write (CoW)**:

> em vez de sobrescrever arquivos diretamente, ele cria uma nova versão antes de alterar a antiga.

É isso que permite snapshots rápidos e rollback do sistema.

***

## Subvolumes

O Btrfs divide o sistema em subvolumes, que são tipo "mini partições” dentro do mesmo disco.

Normalmente no Arch/CachyOS:

```bash
@       → /
@home   → /home
```

Isso é importante porque:

* snapshot de `/` NÃO inclui `/home`
* sistema e arquivos pessoais ficam separados

Ver subvolumes:

```bash
sudo btrfs subvolume list /
```

Ver montagem:

```bash
findmnt -t btrfs
```

***

## Snapshots

Snapshot é uma "foto” do sistema naquele momento.

Eles são:

* quase instantâneos
* leves no começo
* ótimos pra desfazer updates quebrados

Criar snapshot da raiz:

```bash
sudo btrfs subvolume snapshot -r / /.snapshots/meu-snap-$(date +%Y-%m-%d_%H-%M)
```

Do home:

```bash
sudo btrfs subvolume snapshot -r /home /home/.snapshots/home-snap-$(date +%Y-%m-%d_%H-%M)
```

***

## Snapshot NÃO é backup

Snapshot fica no mesmo SSD.

| Problema | Snapshot resolve? |
| --- | --- |
| Update quebrou o sistema | ✓ |
| Arquivo apagado | ✓ |
| SSD morreu | ✗ |

Se o disco morrer, os snapshots morrem junto.

***

## Snapper

O Snapper automatiza snapshots.

Instalar:

```bash
sudo pacman -S snapper snap-pac grub-btrfs
```

Configurar:

```bash
sudo snapper -c root create-config /
sudo snapper -c home create-config /home
```

Ativar:

```bash
sudo systemctl enable --now snapper-timeline.timer
sudo systemctl enable --now snapper-cleanup.timer
```

Depois disso:

* snapshots automáticos são criados
* antes de cada `pacman -Syu` ele salva um PRE
* depois cria um POST

***

## Snapshot manual

```bash
# Criar snapshot manual antes de uma atualização arriscada
sudo snapper -c root create --description "antes-de-atualizar"
```

***

## Rollback

Listar snapshots:

```bash
sudo snapper list
```

Voltar para um snapshot antigo:

```bash
sudo snapper rollback N
sudo reboot
```

Isso literalmente volta o sistema para o estado anterior.

***

## Recuperar arquivo apagado

Copiar arquivo de dentro do snapshot:

```bash
sudo cp /.snapshots/2/snapshot/caminho/arquivo .
```

Ver diferenças:

```bash
sudo snapper diff 2..0
```

***

## Ver uso real do disco

`df -h` não mostra corretamente no Btrfs.

Use:

```bash
sudo btrfs filesystem usage /
```

Ver espaço dos snapshots:

```bash
sudo btrfs qgroup show -reF /
```

***

## Deletar snapshot corretamente

NÃO use `rm -rf`.

Correto:

```bash
sudo btrfs subvolume delete /.snapshots/meu-snap
```

***

## Limitar quantidade de snapshots

O Snapper permite limitar automaticamente quantos snapshots ficam salvos. Isso evita o SSD enchendo aos poucos sem perceber.

As configurações ficam em:

```bash
sudo nano /etc/snapper/configs/root
```

Se você também usa snapshots do `/home`:

```bash
sudo nano /etc/snapper/configs/home
```

***

### Limites dos snapshots automáticos

Procure estas linhas:

```ini
TIMELINE_LIMIT_HOURLY="5"
TIMELINE_LIMIT_DAILY="7"
TIMELINE_LIMIT_WEEKLY="4"
TIMELINE_LIMIT_MONTHLY="3"
TIMELINE_LIMIT_YEARLY="0"
```

Exemplo:

* mantém 5 snapshots horários
* 7 diários
* 4 semanais
* 3 mensais
* nenhum anual

Quando o limite é atingido, os snapshots mais antigos são apagados automaticamente.

***

### Limitar snapshots criados pelo pacman

Os snapshots PRE e POST feitos antes/depois de updates usam estas opções:

```ini
NUMBER_LIMIT="10"
NUMBER_LIMIT_IMPORTANT="5"
```

Exemplo:

* máximo de 10 snapshots normais
* máximo de 5 importantes

***

### Aplicar limpeza manualmente

Depois de alterar as configs, você pode forçar a limpeza:

```bash
sudo snapper cleanup number
sudo snapper cleanup timeline
```

***

### Ver quanto espaço os snapshots estão usando

Uso geral do Btrfs:

```bash
sudo btrfs filesystem usage /
```

Espaço exclusivo de cada snapshot:

```bash
sudo btrfs qgroup show -reF /
```

A coluna `excl` mostra quanto espaço seria liberado ao deletar aquele snapshot.

***

## Manutenção

Verificar integridade:

```bash
sudo btrfs scrub start -Bd /
```

Ver erros físicos do SSD:

```bash
sudo btrfs device stats /
```

***

## Resumo rápido

```text
Btrfs
├── CoW → copia antes de alterar
├── Subvolumes → @ e @home separados
├── Snapshots → rollback rápido
├── Snapper → automação
└── grub-btrfs → snapshots no boot
```

Fluxo normal:

```text
pacman -Syu
↓
snapshot PRE automático
↓
update
↓
snapshot POST automático
↓
deu problema?
↓
snapper rollback N
↓
reboot
```

***

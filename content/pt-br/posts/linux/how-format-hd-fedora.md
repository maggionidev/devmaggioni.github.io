---
title: Como formatar e montar automaticamente um HD no Linux
slug: ''
description: ''
summary: Guia prático para formatar um HD e configurar montagem automática no Fedora KDE usando ext4 e fstab
tags:
  - linux
  - fedora
  - kde plasma
  - mount
categories:
  - linux
keywords:
  - linux
  - kde
  - kde plasma
  - fedora
  - wayland
author: Gabriel Maggioni
date: 2026-04-27T16:10:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

## O que iremos fazer

* apagar tudo do disco
* criar partição nova
* formatar em ext4
* montar automaticamente no sistema

***

## 1. Identificar o disco correto

Antes de qualquer coisa:

```bash
lsblk
```

Saída típica:

```plain
sda      931.5G
└─sda1
nvme0n1  465.8G
```

O sistema geralmente está no NVMe. O HD secundário costuma ser `sda`.

Confirma isso com calma. Esse passo evita desastre.

***

## 2. Desmontar o disco

Se ele estiver montado automaticamente:

```bash
sudo umount /dev/sda1
```

Se reclamar que está em uso:

```bash
sudo umount -l /dev/sda1
```

***

## 3. Apagar tudo (tabela de partição)

```bash
sudo parted /dev/sda mklabel gpt
```

Isso limpa completamente o disco.

***

## 4. Criar nova partição

```bash
sudo parted -a opt /dev/sda mkpart primary ext4 0% 100%
```

Agora você terá uma partição nova ocupando todo o HD.

***

## 5. Formatar em ext4

```bash
sudo mkfs.ext4 -L DADOS /dev/sda1
```

Aqui você pode definir um nome (label). Não é obrigatório, mas ajuda a identificar depois.

***

## 6. Montar manualmente para teste

```bash
sudo mkdir /mnt/hd
sudo mount /dev/sda1 /mnt/hd
```

Teste rápido:

```bash
touch /mnt/hd/teste.txt
```

Se der permissão negada:

```bash
sudo chown -R $USER:$USER /mnt/hd
```

***

## 7. Descobrir o UUID

```bash
lsblk -f
```

Você vai ver algo assim:

```plain
sda1 ext4 UUID=0ad14adb-dd8a-4c93-875b-bde24e4f3892
```

Guarde esse valor.

***

## 8. Montagem automática no boot

Edite o arquivo:

```bash
sudo nano /etc/fstab
```

Adicione no final:

```plain
UUID=SEU_UUID_AQUI /mnt/hd ext4 defaults,nofail 0 2
```

Exemplo real:

```plain
UUID=0ad14adb-dd8a-4c93-875b-bde24e4f3892 /mnt/hd ext4 defaults,nofail 0 2
```

***

## 9. Testar sem reiniciar

```bash
sudo mount -a
```

Se não aparecer erro, está funcionando.

***

## Sobre permissões

Por padrão, o Linux usa dono e grupo. Se quiser acesso total para seu usuário:

```bash
sudo chown -R $USER:$USER /mnt/hd
```

Se quiser algo compartilhado entre usuários, o ideal é usar grupos ao invés de liberar tudo.

***

## Resultado final

Depois disso:

* o HD monta sozinho ao ligar o sistema
* não pede senha
* funciona como parte nativa do Fedora
* pronto para Steam, backups ou projetos

***

Se algo não montar no boot, geralmente é erro de digitação no fstab. Vale revisar com calma.

***

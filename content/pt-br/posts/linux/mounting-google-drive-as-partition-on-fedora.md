---
title: Montando o Google Drive como unidade no Linux
slug: ''
description: Usando o rclone para integrar o Google Drive ao Linux como se fosse uma unidade local, direto no sistema de arquivos
summary: Aprenda como usar o rclone para integrar o Google Drive ao Linux como se fosse uma unidade local, direto no sistema de arquivos
tags:
  - linux
  - wayland
  - rclone
  - google drive
categories:
  - linux
keywords:
  - google drive
  - linux
  - fedora
  - kde plasma
  - wayland
  - rclone
author: Gabriel Maggioni
date: 2026-04-26T11:08:00
lastmod: 2026-05-11T21:43:00
showToc: true
TocOpen: false
draft: false
---

## Introdução

Se você vinha do Windows, provavelmente conhece aquele comportamento do Google Drive aparecendo como um “disco” no gerenciador de arquivos. Era bem prático: acesso rápido aos arquivos e ainda dava pra usar como backup automático de pastas inteiras do sistema.

Nesse post a ideia é reproduzir isso no Linux, de um jeito simples e funcional usando rclone.

***

## Instalação

### 1. Instale o rclone e o fuse3

```bash
sudo dnf install rclone fuse3
```

Se seu sistema é arch:

```bash
sudo pacman -S rclone fuse3
```

***

### 2. Configurar o Google Drive

```bash
rclone config
```

Depois disso:

* `n` → new remote
* nome: `gdrive`
* escolha a opção referente ao Google Drive
* quando pedir autenticação, ele vai abrir um link no navegador
* você loga e autoriza normalmente
* volta pro terminal e finaliza a configuração

***

### 3. Criar a pasta de montagem

Essa pasta vai ser onde o Drive vai “aparecer” no sistema:

```bash
mkdir -p ~/gdrive
```

***

## Configurar para montar automaticamente no login

Agora vamos fazer isso iniciar sozinho quando você entrar no sistema.

### Criar o serviço do systemd

```bash
mkdir -p ~/.config/systemd/user

nano ~/.config/systemd/user/rclone-gdrive.service
```

O `nano` vai abrir um arquivo no terminal para edição. Cole isso:

```ini
[Unit]
Description=Google Drive via rclone
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount gdrive: /home/%u/gdrive \
    --vfs-cache-mode writes \
    --dir-cache-time 72h \
    --poll-interval 1m

ExecStop=/bin/fusermount3 -u /home/%u/gdrive
Restart=on-failure

[Install]
WantedBy=default.target
```

***

### Ativar o serviço

```bash
systemctl --user daemon-reload
systemctl --user enable rclone-gdrive
systemctl --user start rclone-gdrive
```

***

### Garantir que inicia no login

```bash
loginctl enable-linger $USER
```

***

## Troubleshooting

### 1. O Google Drive não apareceu no gerenciador de arquivos

Primeiro veja se o serviço realmente iniciou:

```bash
systemctl --user status rclone-gdrive
```

Se aparecer failed, veja o log completo:

```bash
journalctl --user -u rclone-gdrive -b
```

### 2. Testar o mount manualmente

Antes de culpar o systemd, teste o comando direto no terminal:

```bash
rclone mount gdrive: ~/gdrive --vfs-cache-mode writes
```

Isso normalmente mostra o erro real imediatamente.

### 3. Verificar se o remote existe

```bash
rclone listremotes
```

Precisa aparecer algo assim:

```plain
gdrive:
```

Se não aparecer, o remote não foi configurado corretamente no rclone config.

### 4. Verificar se a pasta de montagem existe

```bash
mkdir -p ~/gdrive
```

***

## Finalizando

Depois disso, reinicie o computador. Pode levar alguns segundos até o Google Drive aparecer no Dolphin como `gdrive`, já montado e pronto pra uso.

{{< asset file="linux/Captura_de_tela_20260426_111946.png" alt="dolphin" >}}

***

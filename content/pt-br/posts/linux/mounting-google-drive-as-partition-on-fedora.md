+++

title = 'Montando o Google Drive como unidade no Fedora'
description = "Usando o rclone para integrar o Google Drive ao Fedora como se fosse uma unidade local, direto no sistema de arquivos"
summary = "Aprenda como usar o rclone para integrar o Google Drive ao Fedora como se fosse uma unidade local, direto no sistema de arquivos"
tags = ["linux", "fedora", "kde plasma", "wayland", "rclone"]
categories = ["linux"]
keywords = ["google drive", "linux", "fedora", "kde plasma", "wayland", "rclone"]

author = "Gabriel Maggioni"
date = '2026-04-26T11:08:44-03:00'
#lastmod = '2026-04-26T11:08:44-03:00'
#publishDate = '2026-04-26T11:08:44-03:00'
#expiryDate = '2026-04-26T11:08:44-03:00'

showToc = true
TocOpen = false

draft = false

+++

## Introdução

Se você vinha do Windows, provavelmente conhece aquele comportamento do Google Drive aparecendo como um “disco” no gerenciador de arquivos. Era bem prático: acesso rápido aos arquivos e ainda dava pra usar como backup automático de pastas inteiras do sistema.

Nesse post a ideia é reproduzir isso no Linux, mais especificamente no Fedora 43 rodando Wayland com KDE Plasma, de um jeito simples e funcional usando rclone.

---

## Instalação

### 1. Instale o rclone e o fuse3

```bash
sudo dnf install rclone fuse3
```

---

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

---

### 3. Criar a pasta de montagem

Essa pasta vai ser onde o Drive vai “aparecer” no sistema:

```bash
mkdir -p ~/gdrive
```

---

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

---

### Ativar o serviço

```bash
systemctl --user daemon-reload
systemctl --user enable rclone-gdrive
systemctl --user start rclone-gdrive
```

---

### Garantir que inicia no login

```bash
loginctl enable-linger $USER
```

---

## Finalizando

Depois disso, reinicie o computador. Pode levar alguns segundos até o Google Drive aparecer no Dolphin como `gdrive`, já montado e pronto pra uso.

{{< asset file="linux/Captura_de_tela_20260426_111946.png" alt="dolphin" >}}

---

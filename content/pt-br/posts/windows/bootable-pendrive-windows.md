+++
# ========================
# 🧾 BASICO
# ========================
title = 'Como criar um pendrive bootável para instalar o windows?'
description = "Aprenda nesse post como criar um pendrive bootável para fazer uma instalação limpa do windows"
summary = "Criando pendrive bootável para instalação limpa do windows"
author = "Gabriel Maggioni"
#author = ["Gabriel Maggioni", "Outro Autor"] # múltiplos autores

draft = false

date = '2026-04-18T12:42:32-03:00'
#lastmod = '2026-04-18T12:42:32-03:00'
#publishDate = '2026-04-19T00:00:00-03:00'
#expiryDate = '2026-04-18T12:42:32-03:00'

# ========================
# 🔗 URL / SLUG
# ========================
#slug = "url-amigavel"
#url = "/url-custom/"
#aliases = ["/url-antiga/"]

# ========================
# 🏷️ TAXONOMIAS
# ========================
tags = ["windows", "boot", "formatação"]
categories = ["windows"]
keywords = ["windows", "pendrive", "pendrive bootável", "boot"]

# ========================
# 🔍 SEO / SOCIAL
# ========================
#canonicalURL = "https://seusite.com/post"
#images = ["images/cover.png"] # usado por OpenGraph/Twitter

# ========================
# 🖼️ CAPA DO POST
# ========================
#[cover]
#  image = "images/cover.png"
#  alt = "Descrição da imagem"
#  caption = "Legenda da imagem"
#  relative = false

# ========================
# 📑 TOC (ÍNDICE)
# ========================
showToc = true
TocOpen = false

# ========================
# 🧠 PAPERMOD FEATURES
# ========================
#ShowReadingTime = true
#ShowShareButtons = true
#ShowPostNavLinks = true
#ShowBreadCrumbs = true
#ShowCodeCopyButtons = true

# ========================
# 🔎 SEARCH
# ========================
#searchHidden = false

# ========================
# 💬 COMENTÁRIOS
# ========================
#comments = true

# ========================
# ✏️ EDIT LINK (GitHub)
# ========================
#[editPost]
#  URL = "https://github.com/seu-repo/content"
#  Text = "Sugerir alteração"
#  appendFilePath = true

# ========================
# 🎥 MÍDIA EXTRA
# ========================
#videos = ["video.mp4"]
#audio = ["audio.mp3"]

# ========================
# 📊 ORDEM / ORGANIZAÇÃO
# ========================
#weight = 1

# ========================
# 🌐 MULTILINGUA
# ========================
#lang = "pt-br"

+++

Bom, esse vai ser um guia prático, rápido e sem enrolação. 
Você vai precisar de um pendrive de, pelo menos, 16GB;
Tenha noção que o **pendrive será formatado e você perderá todos os arquivos dele**.

---

### 1. Baixe a `imagem oficial do windows 11` do site oficial da Microsoft: 

{{<rawhtml >}}
<a href="https://www.microsoft.com/pt-br/software-download/windows11" target="_blank" rel="noopener noreferrer">
Download windows 11
</a>
{{< /rawhtml >}}

(role a página até a seção "Baixar imagem de disco (ISO) do Windows 11 para dispositivos x64" > Baixar agora > selecionar idioma > confirmar > 64-bits baixar)

![print da página da microsoft](https://assets.maggioni.dev/posts/windows/bootable-pendrive-windows/2026-04-18_13-07.jpg)

---

### 2. Baixe o Rufus

O rufus é uma ferramenta que ajuda a formatar e criar drives USB inicializáveis, como chaves/drives USB, cartões de memória e etc.

{{< button 
  text="Baixar rufus" 
  link="https://rufus.ie/pt_BR/" 
  target="_blank"
  color="#1976D2" 
>}}

Após baixar o rufus, execute o `.exe` que você baixou;

---

### 3. Criar o pendrive bootável

Selecione essas opções na janela do Rufus, apontando para o seu pendrive:
(Cuidado para não selecionar a partição errada! Pois ela será formatada!)

![print da página da microsoft](https://assets.maggioni.dev/posts/windows/bootable-pendrive-windows/2026-04-18_13-182.jpg)

clique em "Iniciar" e aguarde o processo terminar.

---

E... pronto! Você já tem um pendrive bootável e pode estar o usando para formatar seu pc!
Esse post o foco é criar o pendrive. 

Eu abordo de forma completa o processo de formatação [aqui](https://maggioni.dev/posts/windows/how-to-format-your-pc)

---
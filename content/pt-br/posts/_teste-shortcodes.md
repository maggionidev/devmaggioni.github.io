+++
# ========================
# 🧾 BASICO
# ========================
title = 'Testando shortcodes'
description = "Descrição do post (SEO)"
summary = "Resumo curto que aparece em listagens"
author = "Gabriel Maggioni"
#author = ["Gabriel Maggioni", "Outro Autor"] # múltiplos autores

draft = true

date = '2026-04-17T20:52:59-03:00'
#lastmod = '2026-04-17T20:52:59-03:00'
#publishDate = '2026-04-17T20:52:59-03:00'
#expiryDate = '2026-04-17T20:52:59-03:00'

# ========================
# 🔗 URL / SLUG
# ========================
#slug = "url-amigavel"
#url = "/url-custom/"
#aliases = ["/url-antiga/"]

# ========================
# 🏷️ TAXONOMIAS
# ========================
tags = []
categories = []
keywords = []

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
searchHidden = true

# ========================
# 💬 COMENTÁRIOS
# ========================
comments = false

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

+++


## Testando shortcodes

---

### Figure

{{< figure src="/images/hello-world.png"
    alt="Imagem de exemplo"
    caption="Legenda aqui"
    title="Título da imagem"
    align="center"
    width="300"
    link="https://google.com"
    target="_blank"
 >}}

 ---

### Collapse

{{< details summary="Clique para ver mais" >}}
Conteúdo escondido aqui dentro 👀
{{< /details >}}

---

### InTextImg
Imagem no meio {{< InTextImg url="/images/hello-world.png" alt="GitHub" >}} do texto.

---

### rawhtml

{{< rawhtml >}}
<h1  style="color: red; font-size: 32px">Html inline</h1>
{{< /rawhtml >}}

---

### video

{{< video src="/videos/demo.mp4" >}}

---

### audio

{{< audio src="/audios/demo.mp3" >}}

---

### badge

{{< badge 
  text="drive"
  color="blue"
  logo="googledrive"
  logoColor="white"
    link="https://drive.google.com/"
>}}

{{< badge 
  text="mega"
  color="red"
  logo="mega"
  logoColor="white"
  link="https://mega.nz"
>}}

{{< badge 
  text="python"
  color="blue"
  logo="python"
  logoColor="white"
>}}

{{< badge 
  text="go"
  color="blue"
  logo="go"
  logoColor="white"
>}}

---

clique nesse botão {{< button 
  text="Botão 1" 
  link="https://google.com" 
  target="_blank"
  color="#16a34a" 
>}}

e nesse também {{< button 
  text="Botão 2" 
  link="#" 
  color="red" 
>}}
# Hugo + PaperMod

Documentação do meu blog pessoal feito com Hugo e usando o tema PaperMod.

---

## Sumário

* [Requisitos](#requisitos)
* [Instalação e uso local](#instalação-e-uso-local)
* [Criando posts](#criando-posts)
* [Archetypes](#archetypes)
* [Customização (sem alterar o tema)](#customização-sem-alterar-o-tema)
* [Shortcodes](#shortcodes)
* [Partials](#partials)
* [Estrutura do projeto](#estrutura-do-projeto)

---

## Requisitos

* Hugo **Extended** v0.112 ou superior
* Git

> ⚠️ A versão *Extended* é obrigatória para compilar SCSS

---

## Instalação e uso local

```bash
git clone https://github.com/maggionidev/maggionidev.github.io.git maggionidev-blog
git submodule update --init --recursive
cd maggionidev-blog

hugo server -D
```

Acesse:
`http://localhost:1313`

### Flags importantes

* `-D` → inclui drafts (`draft = true`)
* `--minify` → build otimizado para produção

```bash
hugo --minify
```

Saída: `public/`

---

## Criando posts

Conteúdo em português:

```
content/pt-br/posts/
```

### Via CLI

```bash
hugo new content/pt-br/posts/meu-novo-post.md
```

---

## Front matter (TOML padrão do projeto)

Este projeto usa **TOML**, não YAML.

```toml
+++
title = "Título do Post"
date = 2024-01-15T10:00:00-03:00
draft = false
description = "Descrição curta do post"
tags = ["tag1", "tag2"]
categories = ["categoria"]

[cover]
image = "images/capa.jpg"
alt = "Descrição da imagem"
caption = "Legenda opcional"
+++
```

### Boas práticas

* Use `draft = true` enquanto escreve
* Use slugs simples:

  ```
  meu-post-sobre-algo.md
  ```
* Prefira:

  * `static/images/`
  * ou Page Bundles

---

## Archetypes

Evita retrabalho ao criar conteúdo.

```
archetypes/
├── default.md
└── posts.md
```

### Exemplo (`archetypes/posts.md`)

```markdown
+++
title = "{{ replace .Name "-" " " | title }}"
date = {{ .Date }}
draft = true
description = ""
tags = []
categories = []

[cover]
image = ""
alt = ""
+++

Escreva o conteúdo aqui.
```

---

## Customização (sem alterar o tema)

> ❗ Regra do projeto: **NUNCA editar arquivos dentro de `themes/`**

O Hugo permite sobrescrever tudo pela raiz.

---

### CSS customizado

Use:

```
assets/css/extended/
```

Exemplo:

```
assets/css/extended/
├── custom.css
├── typography.css
└── dark-mode.css
```

### Exemplo

```css
.post-content {
  max-width: 740px;
}

body {
  font-family: "Inter", sans-serif;
}

a {
  color: #e05c2e;
}
```

---

## Shortcodes

Local:

```
layouts/shortcodes/
```

### Uso

```md
{{< nome parametro="valor" >}}
```

Com conteúdo:

```md
{{< callout tipo="aviso" >}}
Texto aqui
{{< /callout >}}
```

---

### Exemplo: Callout

```html
<div class="callout callout-{{ .Get "tipo" | default "info" }}">
  <p>{{ .Inner | markdownify }}</p>
</div>
```

---

### Exemplo: YouTube

```html
<div class="video-wrapper">
  <iframe
    src="https://www.youtube.com/embed/{{ .Get 0 }}"
    loading="lazy"
    allowfullscreen>
  </iframe>
</div>
```

Uso:

```md
{{< youtube-lite "ID_DO_VIDEO" >}}
```

---

## Partials

Local:

```
layouts/partials/
```

---

### Regra importante

Para modificar o tema:

* **copie o arquivo**
* **cole na raiz**
* **edite lá**

Exemplo:

```bash
cp themes/PaperMod/layouts/partials/footer.html layouts/partials/footer.html
```

---

### Estrutura comum

```
layouts/partials/
├── head.html
├── header.html
├── footer.html
├── comments.html
└── extend_head.html
```

---

### extend_head.html (recomendado)

Para adicionar scripts sem override completo:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">

<script defer data-domain="seusite.com.br"
  src="https://plausible.io/js/script.js">
</script>
```

---

### Criando partial

```html
<div class="author-bio">
  <img src="{{ .Params.author_avatar }}">
  <p>{{ .Params.author_bio }}</p>
</div>
```

Uso:

```go
{{ partial "author-bio.html" . }}
```

---

## Estrutura do projeto

```
.
├── archetypes/
├── assets/
│   └── css/
│       └── extended/
├── content/
│   └── pt-br/
│       └── posts/
├── layouts/
│   ├── partials/
│   └── shortcodes/
├── static/
├── themes/
│   └── PaperMod/   # NÃO editar diretamente
├── config.yml
└── README.md
```

---

## Links úteis

* [https://gohugo.io/documentation/](https://gohugo.io/documentation/)
* [https://github.com/adityatelange/hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)
* [https://github.com/adityatelange/hugo-PaperMod/wiki](https://github.com/adityatelange/hugo-PaperMod/wiki)
* [https://gohugo.io/content-management/shortcodes/](https://gohugo.io/content-management/shortcodes/)

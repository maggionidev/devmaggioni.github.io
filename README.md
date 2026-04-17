# Hugo + PaperMod

Documentação do projeto para desenvolvimento, criação de conteúdo e customização do tema.

---

## Sumário

- [Requisitos](#requisitos)
- [Instalação e uso local](#instalação-e-uso-local)
- [Criando posts](#criando-posts)
- [Archetypes](#archetypes)
- [Customizando o CSS](#customizando-o-css)
- [Shortcodes](#shortcodes)
- [Partials](#partials)

---

## Requisitos

- [Hugo Extended](https://gohugo.io/installation/) v0.112 ou superior (a versão **Extended** é obrigatória para compilar SCSS)
- Git

---

## Instalação e uso local

```bash
# Clone o repositório
git clone https://github.com/maggionidev/maggionidev.github.io.git maggionidev-blog
cd maggionidev-blog

# Inicie o servidor de desenvolvimento
hugo server -D
```

O site estará disponível em `http://localhost:1313`.

O flag `-D` inclui rascunhos (`draft: true`) na visualização local. Para buildar o site para produção:

```bash
hugo --minify
```

Os arquivos gerados ficam na pasta `public/`.

---

## Criando posts

Todo o conteúdo em português fica em:

```
content/pt-br/posts/
```

### Via CLI (recomendado)

```bash
hugo new pt-br/posts/meu-novo-post.md
```

O Hugo vai usar o archetype correspondente para gerar o arquivo com o front matter já preenchido.

### Estrutura do front matter

```yaml
---
title: "Título do Post"
date: 2024-01-15T10:00:00-03:00
draft: false
description: "Descrição curta do post para SEO e cards de listagem."
tags: ["tag1", "tag2"]
categories: ["categoria"]
cover:
  image: "caminho/para/imagem.jpg"
  alt: "Descrição da imagem de capa"
  caption: "Legenda opcional"
---
```

### Boas práticas

- Deixe `draft: true` enquanto o post não estiver pronto para publicar
- Prefira slugs em minúsculas e com hífens: `meu-post-sobre-algo.md`
- Imagens do post ficam melhor em `static/images/` ou junto ao conteúdo em [Page Bundles](https://gohugo.io/content-management/page-bundles/)

---

## Archetypes

A pasta `archetypes/` define os **templates de front matter** usados quando você roda `hugo new`. Ela evita retrabalho ao criar conteúdo novo.

```
archetypes/
├── default.md          # Usado quando não há archetype específico
└── posts.md            # Usado para hugo new pt-br/posts/...
```

### Exemplo de `archetypes/posts.md`

```markdown
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
description: ""
tags: []
categories: []
cover:
  image: ""
  alt: ""
---

Escreva o conteúdo aqui.
```

Quando você rodar `hugo new pt-br/posts/nome-do-post.md`, esse template é aplicado automaticamente, com o título gerado a partir do nome do arquivo.

---

## Customizando o CSS

O PaperMod oferece um ponto de extensão oficial para CSS sem precisar modificar os arquivos do tema diretamente. Qualquer arquivo dentro de:

```
themes/PaperMod/assets/css/extended/
```

é automaticamente incluído pelo tema na compilação final. Isso garante que suas customizações sobrevivam a atualizações do tema.

### Como usar

Crie quantos arquivos `.css` quiser dentro dessa pasta:

```
themes/PaperMod/assets/css/extended/
├── custom.css          # Estilos gerais
├── typography.css      # Ajustes de tipografia
└── dark-mode.css       # Overrides do modo escuro
```

> **Dica:** se você preferir não tocar nos arquivos do submódulo do tema, crie a mesma estrutura na raiz do projeto — o Hugo vai dar preferência aos arquivos locais:
>
> ```
> assets/css/extended/custom.css
> ```

### Exemplo de `custom.css`

```css
/* Ajuste de largura máxima do conteúdo */
.post-content {
  max-width: 740px;
}

/* Fonte personalizada */
body {
  font-family: "Inter", sans-serif;
}

/* Override de cor de destaque */
a {
  color: #e05c2e;
}
```

---

## Shortcodes

Shortcodes são **componentes reutilizáveis** que você usa dentro do Markdown dos posts. Ficam em:

```
layouts/shortcodes/
```

### Usando um shortcode no post

```
{{< nome-do-shortcode parametro="valor" >}}
```

Ou com conteúdo interno:

```
{{< callout tipo="aviso" >}}
Este é o conteúdo do callout.
{{< /callout >}}
```

### Exemplo: shortcode de callout

**`layouts/shortcodes/callout.html`**

```html
<div class="callout callout-{{ .Get "tipo" | default "info" }}">
  <p>{{ .Inner | markdownify }}</p>
</div>
```

**Uso no post:**

```
{{< callout tipo="aviso" >}}
Atenção: este recurso está em beta.
{{< /callout >}}
```

### Exemplo: shortcode de vídeo do YouTube com lazy load

**`layouts/shortcodes/youtube-lite.html`**

```html
<div class="video-wrapper">
  <iframe
    src="https://www.youtube.com/embed/{{ .Get 0 }}"
    loading="lazy"
    allowfullscreen>
  </iframe>
</div>
```

**Uso:**

```
{{< youtube-lite "dQw4w9WgXcQ" >}}
```

### Shortcodes nativos do Hugo úteis

| Shortcode | Uso |
|---|---|
| `{{< figure src="..." alt="..." >}}` | Imagem com legenda |
| `{{< gist usuario id >}}` | Embed de Gist do GitHub |
| `{{< highlight go >}}` | Bloco de código com syntax highlight |

---

## Partials

Partials são **fragmentos de template HTML** reutilizáveis, usados para construir o layout das páginas. Ficam em:

```
layouts/partials/
```

O PaperMod já inclui suas próprias partials. Para sobrescrever ou estender uma delas, basta criar um arquivo com o **mesmo nome e caminho** na pasta `layouts/partials/` do seu projeto, sem tocar nos arquivos do tema.

### Estrutura comum

```
layouts/partials/
├── head.html           # Tags <head>, meta tags, scripts
├── header.html         # Cabeçalho do site
├── footer.html         # Rodapé
├── comments.html       # Sistema de comentários
└── extend_head.html    # Injeção extra no <head> (suportada pelo PaperMod)
```

### Sobrescrevendo uma partial do PaperMod

Quer modificar o rodapé sem editar o tema? Copie o arquivo original e edite a cópia:

```bash
cp themes/PaperMod/layouts/partials/footer.html layouts/partials/footer.html
```

Agora edite `layouts/partials/footer.html` à vontade. O Hugo sempre vai preferir os arquivos do seu projeto aos do tema.

### Partial `extend_head.html`

O PaperMod expõe essa partial especificamente para injeções no `<head>` sem override completo. Útil para fontes, scripts de analytics, meta tags extras:

**`layouts/partials/extend_head.html`**

```html
<!-- Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">

<!-- Plausible Analytics -->
<script defer data-domain="seusite.com.br" src="https://plausible.io/js/script.js"></script>
```

### Criando uma partial do zero

**`layouts/partials/author-bio.html`**

```html
<div class="author-bio">
  <img src="{{ .Params.author_avatar }}" alt="Foto do autor">
  <p>{{ .Params.author_bio }}</p>
</div>
```

Para usar em um template de página:

```html
{{ partial "author-bio.html" . }}
```

---

## Estrutura geral do projeto

```
.
├── archetypes/              # Templates de front matter
├── assets/
│   └── css/
│       └── extended/        # CSS customizado (alternativa sem tocar no tema)
├── content/
│   └── pt-br/
│       └── posts/           # Posts em português
├── layouts/
│   ├── partials/            # Partials customizadas ou sobrescritas
│   └── shortcodes/          # Shortcodes do projeto
├── static/                  # Arquivos estáticos (imagens, favicons, etc.)
├── themes/
│   └── PaperMod/
│       └── assets/
│           └── css/
│               └── extended/ # CSS customizado direto no tema
├── config.yml               # Configuração principal do Hugo
└── README.md
```

---

## Links úteis

- [Documentação do Hugo](https://gohugo.io/documentation/)
- [Repositório do PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- [Wiki do PaperMod](https://github.com/adityatelange/hugo-PaperMod/wiki)
- [Shortcodes nativos do Hugo](https://gohugo.io/content-management/shortcodes/)
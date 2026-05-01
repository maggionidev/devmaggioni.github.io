# Como Criar um Blog Profissional igual ao maggioni.dev

Este guia completo ensina a configurar, estruturar e hospedar um blog pessoal ou técnico utilizando o **Hugo** (um dos geradores de sites estáticos mais rápidos do mercado), versionado no **GitHub** e hospedado gratuitamente no **GitHub Pages**.

---

## 1. Pré-requisitos

Antes de iniciar, certifique-se de que sua máquina possui as seguintes ferramentas instaladas:

* **Git** instalado e configurado.
* **Hugo** (versão `extended` recomendada para suporte a SASS/SCSS).
* Um editor de código (como o VS Code).

---

## 2. Passo a Passo de Instalação e Configuração

### Passo 2.1: Criar o novo site

Abra o terminal ou prompt de comando na pasta onde deseja criar o projeto e execute o comando:

```bash
hugo new site my-blog
cd my-blog
```

### Passo 2.2: Inicializar o Git

Transforme a pasta do seu blog em um repositório Git:

```bash
git init
```

### Passo 2.3: Adicionar o Tema (PaperMod)

O tema **PaperMod** é minimalista, otimizado para SEO e excelente para blogs técnicos. Adicione-o como um submódulo do Git para facilitar as atualizações:

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### Passo 2.4: Configurar o arquivo `hugo.yaml`

O arquivo `hugo.yaml` é onde ficam todas as configs do seu blog.
Você pode ler a [documentação oficial do papermod](https://github.com/adityatelange/hugo-PaperMod/wiki/Features) para ter uma noção melhor do que está fazendo;

Abra o arquivo `hugo.yaml` na raiz do projeto e substitua o conteúdo pelo seguinte modelo base:

```yaml
baseURL: 'https://localhost:1313/'
theme: PaperMod

defaultContentLanguage: pt-BR
defaultContentLanguageInSubdir: true

pagination:
  pagerSize: 5

enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false

languages:

  pt-BR:
    locale: pt-BR
    label: Português (Brasil)
    weight: 1
    contentDir: content/pt-br
    title: Gabriel Maggioni

    params:
      profileMode:
        enabled: true
        title: Meu blog

        subtitle: |
          Olá, Bem vindo ao meu blog 👋

        imageUrl: /some-image.webp
        imageWidth: 120
        imageHeight: 120
        imageTitle: my image

        buttons:
          - name: Ver Posts
            url: posts

      description: 'Programação...'

caches:
  images:
    dir: ':cacheDir/images'

minify:
  disableXML: true
  minifyOutput: true

params:
  env: production

  author: Nome do Autor
  DateFormat: 02/01/2006
  ShowLastMod: true
  LastModFormat: 02/01/2006

  defaultTheme: auto
  disableThemeToggle: false
  comments: false

  keywords:
    - programação

  images:
    - some-image.webp

  ShowReadingTime: true
  ShowShareButtons: true
  ShowPostNavLinks: true
  ShowBreadCrumbs: true
  ShowCodeCopyButtons: true
  ShowWordCount: true
  ShowRssButtonInSectionTermList: true
  UseHugoToc: true

  ShareButtons:
    - whatsapp
    - x
    - reddit
    - telegram
    - linkedin

  disableSpecial1stPost: false
  disableScrollToTop: false
  hidemeta: false
  hideSummary: false

  showtoc: true
  tocopen: false

  assets:
    favicon: /favicon.ico
    favicon16x16: /favicon-16x16.webp
    favicon32x32: /favicon-32x32.webp
    apple_touch_icon: /apple-touch-icon.webp

  label:
    text: Home
    icon: /apple-touch-icon.webp
    iconHeight: 45

  socialIcons:
    - name: youtube
      url: 'https://youtube.com/@maggionidev'
    - name: tiktok
      url: 'https://tiktok.com/@maggionidev'
    - name: instagram
      url: 'https://instagram.com/maggionidev'
    - name: x
      url: 'https://x.com/maggionidev'
    - name: github
      url: 'https://github.com/maggionidev'

  cover:
    hidden: false
    hiddenInList: false
    hiddenInSingle: false

  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    location: 0
    distance: 1000
    threshold: 0.1
    minMatchCharLength: 0
    limit: 10
    keys:
      - title
      - permalink
      - summary
      - content

menu:
  main:
    - identifier: posts
      name: posts
      url: posts/
      weight: 10

    - identifier: search
      name: search
      url: /search/
      weight: 20

    - identifier: categories
      name: categories
      url: /categories/
      weight: 30

    - identifier: tags
      name: tags
      url: /tags/
      weight: 40

markup:
  highlight:
    noClasses: false

outputs:
  home:
    - HTML
    - RSS
    - JSON
```

---

## 3. Criando seu Primeiro Conteúdo

Para criar uma nova postagem ou página, utilize a estrutura de linha de comando do Hugo:
> primeiro garanta que existe uma página pt-br/posts dentro de content

```bash
hugo new content/pt-br/posts/meu-primeiro-post.md
```

Edite o arquivo recém-criado com o editor de sua preferência. A estrutura do cabeçalho (*front matter*) deve ser semelhante a esta:

```yaml
---

title: '{{ replace .File.ContentBaseName "-" " " | title }}'
description: Descrição do post (SEO)
summary: Resumo curto que aparece em listagens
tags: []
categories: []
keywords: []
author: Nome do autor
date: '{{ .Date }}'
showToc: true
TocOpen: false
draft: true

---
```

---

## 4. Testando Localmente

Para visualizar o blog em tempo real no seu navegador, execute:

```bash
hugo server -D
```

Acesse o endereço exibido no terminal (geralmente `http://localhost:1313/`) para testar o visual e as funcionalidades.

---

## 5. Deploy no GitHub Pages

Para colocar o site no ar usando o GitHub Pages:

1. Crie um novo repositório no [GitHub](https://github.com) com o nome `seu-usuario.github.io`.
2. Adicione o repositório remoto ao seu projeto local:
   ```bash
   git remote add origin git@github.com:seu-usuario/seu-usuario.github.io.git
   ```
3. Configure o GitHub Actions para compilar e fazer o deploy automaticamente. Crie o arquivo `.github/workflows/hugo.yml`:

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.150.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v5
```

4. Faça o commit e o push dos arquivos:
   ```bash
   git add .
   git commit -m "feat: estrutura inicial do blog"
   git push -u origin main
   ```

---

## 6. Próximos Passos Recomendados

### 6.1. Adicionar um Domínio Customizado no GitHub Pages

Para substituir o endereço padrão `seu-usuario.github.io` pelo seu próprio domínio registrado (como o seu domínio `.dev`):

1. **Configuração de DNS (no seu registrador, ex: GoDaddy):**
   - Acesse o painel de DNS do seu provedor de domínio.
   - Adicione os seguintes registros apontando para os servidores do GitHub Pages:

| Tipo | Nome / Host | Valor / Destino |
| :--- | :--- | :--- |
| `CNAME` | `www` ou `@` | `seu-usuario.github.io` |
| `A` | `@` | `185.199.108.153` |
| `A` | `@` | `185.199.109.153` |
| `A` | `@` | `185.199.110.153` |
| `A` | `@` | `185.199.111.153` |

2. **Configuração no Repositório GitHub:**
   - Acesse o repositório no GitHub e vá em **Settings** > **Pages**.
   - No campo **Custom domain**, insira o seu domínio (ex: `maggioni.dev`) e salve.
   - Marque a opção **Enforce HTTPS**.

---

### 6.2. Utilizar um CMS (ex: Sveltia CMS)

Se você deseja uma interface de administração amigável para criar e editar posts sem precisar interagir diretamente com arquivos Markdown no editor de código:

1. **O que é o Sveltia CMS:**
   - Um CMS de código aberto, moderno e sem servidor (*headless CMS*), ideal para sites estáticos. Ele pode se conectar diretamente ao seu repositório no GitHub para ler e gravar arquivos Markdown.

2. **Como configurar o Sveltia CMS no projeto:**
   - Crie uma página de administração dentro da pasta `content` ou na pasta estática (`static/admin`).
   - Crie um arquivo `static/admin/index.html` com a seguinte estrutura:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Painel de Administração - Sveltia CMS</title>
  <script type="module" src="https://unpkg.com/@sveltia/cms@latest/dist/sveltia-cms.js"></script>
</head>
<body>
  <sveltia-cms config="config.json"></sveltia-cms>
</body>
</html>
```

crie também um `config.yml`:

```yml
backend:
  name: github
  repo: SEU_USUARIO/SEU_REPO
  branch: main
  use_graphql: false

site_url: https://seusite.com

media_folder: static/images
public_folder: /images

locale: "pt"

collections:
  - name: "posts_pt"
    label: "Posts PT-BR"
    folder: "content/pt-br/posts"
    create: true
    slug: "{{slug}}"
    extension: md
    format: frontmatter
    fields:
      - { label: "Título", name: "title", widget: "string" }
      - { label: "Data", name: "date", widget: "datetime" }
      - { label: "Rascunho", name: "draft", widget: "boolean", default: false }
      - { label: "Conteúdo", name: "body", widget: "markdown" }
```

3. **Autenticação:**
   - Acesse `https://seu-dominio.com/admin/` e faça login usando um **Personal Access Token (PAT)** do GitHub para interagir com o repositório de forma segura.

---

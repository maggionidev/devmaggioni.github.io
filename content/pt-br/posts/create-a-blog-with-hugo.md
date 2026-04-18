+++
title = 'Tudo que você precisa saber sobre Hugo - Crie e gerencie seu blog'
description = "Guia completo para criar, configurar e publicar um blog com o Hugo"
summary = "Aprenda a instalar o Hugo, criar seu blog, instalar temas, personalizar e publicar do zero"
author = "Gabriel Maggioni"
draft = false
date = '2026-04-16T09:31:00-03:00'
keywords = ["hugo", "blog", "site estático", "gohugo", "tema", "deploy"]
categories = ["desenvolvimento"]
tags = ["hugo", "blog", "go"]
showToc = true
TocOpen = false
+++

# Hugo - Guia Completo para Criar e Gerenciar seu Blog

Nesse post, iremos abordar os seguintes temas:

- O que é Hugo e por que usá-lo
- Instalação
- Criando seu primeiro site
- Estrutura de pastas
- Instalando e configurando temas
- Criando e organizando conteúdo
- Comandos essenciais
- Personalizando o tema
- Deploy e publicação

---

## O que é Hugo e por que usá-lo?

**Hugo** é um gerador de sites estáticos escrito em Go. Ele pega seus arquivos Markdown e transforma em um site HTML completo em milissegundos - sem banco de dados, sem servidor PHP, sem complexidade.

**Vantagens:**
- ⚡ Extremamente rápido (builds em milissegundos)
- 🔒 Mais seguro (sem backend vulnerável)
- 💸 Hospedagem gratuita ou barata (Netlify, Vercel, Github Pages)
- ✍️ Escreva posts em Markdown simples
- 🎨 Centenas de temas prontos

> 💡 Sites como o blog da Cloudflare e documentações de grandes projetos open source usam Hugo.

---

## Instalação

### Linux (Debian/Ubuntu)
```bash
sudo apt install hugo
```

Para a versão mais recente (recomendado):
```bash
sudo snap install hugo
```

### macOS
```bash
brew install hugo
```

### Windows

Pelo **Chocolatey**:
```powershell
choco install hugo-extended
```

Pelo **Winget**:
```powershell
winget install Hugo.Hugo.Extended
```

> ⚠️ Prefira sempre a versão **extended** - ela suporta SCSS/SASS, necessário para a maioria dos temas modernos.

### Verificando a instalação
```bash
hugo version
```

---

## Criando seu primeiro site

```bash
hugo new site meu-blog
cd meu-blog
```

Isso cria a estrutura base do projeto. Em seguida, inicie um repositório Git:

```bash
git init
```

---

## Estrutura de pastas

```
meu-blog/
├── archetypes/       # Templates padrão para novos conteúdos
├── assets/           # Arquivos processados (SCSS, JS)
├── content/          # Seus posts e páginas em Markdown
├── data/             # Arquivos de dados (JSON, YAML, TOML)
├── i18n/             # Traduções
├── layouts/          # Templates HTML customizados
├── public/           # Site gerado (não versionar)
├── resources/        # Cache de assets processados
├── static/           # Arquivos estáticos (imagens, fontes, CSS)
├── themes/           # Temas instalados
└── hugo.toml         # Configuração principal do site
```

| Pasta | O que você vai mexer |
|---|---|
| `content/` | Sempre - seus posts ficam aqui |
| `static/` | Para adicionar imagens e arquivos |
| `layouts/` | Para customizar o tema |
| `hugo.toml` | Para configurar o site |
| `themes/` | Para instalar/atualizar temas |

---

## Instalando e configurando temas

### Onde encontrar temas

- [themes.gohugo.io](https://themes.gohugo.io) - catálogo oficial com centenas de opções

### Instalando via Git Submodule (recomendado)

```bash
git submodule add https://github.com/autor/nome-do-tema.git themes/nome-do-tema
```

Ativar o tema no `hugo.toml`:

```toml
theme = "nome-do-tema"
```

### Atualizando o tema

```bash
git submodule update --remote --merge
```

### Instalando via clone simples (sem submodule)

```bash
git clone https://github.com/autor/nome-do-tema.git themes/nome-do-tema
```

> ⚠️ Com clone simples você não consegue atualizar facilmente. Prefira o submodule.

### Exemplo com o tema PaperMod (muito popular)

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

`hugo.toml`:
```toml
baseURL = "https://seusite.com/"
languageCode = "pt-br"
title = "Meu Blog"
theme = "PaperMod"
```

---

## Configuração principal - hugo.toml

O arquivo `hugo.toml` controla tudo do seu site. Exemplo completo:

```toml
baseURL = "https://seusite.com/"
languageCode = "pt-br"
title = "Meu Blog"
theme = "PaperMod"
paginate = 10
enableRobotsTXT = true
enableEmoji = true

[params]
  author = "Seu Nome"
  description = "Um blog sobre tecnologia e desenvolvimento"
  ShowReadingTime = true
  ShowShareButtons = true
  ShowPostNavLinks = true
  ShowBreadCrumbs = true
  ShowCodeCopyButtons = true
  ShowToc = true

[params.homeInfoParams]
  Title = "Olá! 👋"
  Content = "Bem-vindo ao meu blog sobre tecnologia"

[[params.socialIcons]]
  name = "github"
  url = "https://github.com/seu-usuario"

[[params.socialIcons]]
  name = "linkedin"
  url = "https://linkedin.com/in/seu-usuario"

[menu]
  [[menu.main]]
    name = "Posts"
    url = "/posts/"
    weight = 1
  [[menu.main]]
    name = "Sobre"
    url = "/about/"
    weight = 2
  [[menu.main]]
    name = "Tags"
    url = "/tags/"
    weight = 3
```

---

## Criando e organizando conteúdo

### Criando um novo post

```bash
hugo new content posts/meu-primeiro-post.md
```

O arquivo será criado em `content/posts/` com o front matter padrão:

```markdown
+++
title = 'Meu Primeiro Post'
date = '2026-04-16T00:00:00-03:00'
draft = true
+++
```

### Campos do front matter

```toml
+++
title = "Título do Post"
description = "Descrição curta para SEO"
summary = "Resumo exibido na listagem"
author = "Seu Nome"
date = '2026-04-16T00:00:00-03:00'
lastmod = '2026-04-16T00:00:00-03:00'  # Data de última modificação
draft = true                             # true = não publica
tags = ["hugo", "blog"]
categories = ["desenvolvimento"]
keywords = ["hugo", "blog", "tutorial"]
cover:
  image = "posts/meu-post/capa.jpg"     # Imagem de capa
  alt = "Descrição da imagem"
showToc = true                           # Índice no post
TocOpen = false                          # Índice aberto por padrão
+++
```

### Organizando com pastas (Page Bundles)

Para posts com imagens, use a estrutura de bundle:

```
content/
└── posts/
    └── meu-post/
        ├── index.md      ← conteúdo do post
        ├── capa.jpg      ← imagem de capa
        └── diagrama.png  ← outras imagens
```

Assim você referencia as imagens diretamente no Markdown:

```markdown
![Diagrama](diagrama.png)
```

### Criando uma página estática

```bash
hugo new content about.md
```

```markdown
+++
title = "Sobre"
layout = "page"
+++

Olá, sou desenvolvedor e gosto de escrever sobre tecnologia...
```

### Rascunhos

Posts com `draft = true` não são publicados. Para visualizá-los localmente:

```bash
hugo server -D
```

Quando estiver pronto para publicar, mude para:

```toml
draft = false
```

---

## Comandos essenciais

### Servidor local

```bash
hugo server          # Inicia o servidor em localhost:1313
hugo server -D       # Inclui rascunhos (drafts)
hugo server -p 8080  # Porta customizada
hugo server --bind 0.0.0.0  # Acessível na rede local
```

O servidor tem **live reload** - qualquer alteração no conteúdo ou tema atualiza o navegador automaticamente.

### Build do site

```bash
hugo                 # Gera o site na pasta /public
hugo --minify        # Minifica HTML, CSS e JS
hugo -e production   # Usa configuração de produção
```

### Gerenciamento de conteúdo

```bash
# Criar novo post
hugo new content posts/titulo-do-post.md

# Criar nova página
hugo new content sobre.md

# Listar todo o conteúdo
hugo list all

# Listar apenas drafts
hugo list drafts

# Listar posts futuros (publishDate no futuro)
hugo list future
```

### Módulos e temas

```bash
# Atualizar submódulos (temas via git submodule)
git submodule update --remote --merge

# Ver versão do Hugo
hugo version

# Verificar configuração
hugo config

# Limpar cache
hugo --gc
```

---

## Personalizando o tema

Hugo segue uma hierarquia de templates: **seu layout sobrescreve o do tema**. Você nunca precisa editar os arquivos dentro de `themes/` diretamente.

### Sobrescrevendo um template

Copie o arquivo do tema para a pasta `layouts/` do seu projeto:

```bash
# Exemplo: sobrescrever o template de post único
cp themes/PaperMod/layouts/_default/single.html layouts/_default/single.html
```

Agora edite `layouts/_default/single.html` à vontade. O tema original não é alterado.

### Estrutura de layouts

```
layouts/
├── _default/
│   ├── baseof.html     # Template base (wrapping)
│   ├── list.html       # Página de listagem
│   └── single.html     # Página de post único
├── partials/
│   ├── header.html     # Cabeçalho
│   └── footer.html     # Rodapé
├── index.html          # Página inicial
└── 404.html            # Página de erro
```

### Adicionando CSS customizado

Crie o arquivo `assets/css/extended/custom.css` (no PaperMod e temas similares):

```css
/* Exemplo: mudar a fonte do corpo */
body {
  font-family: 'Inter', sans-serif;
}

/* Mudar cor de destaque */
a {
  color: #ff6b6b;
}
```

Ou adicione no `static/css/custom.css` e referencie no `hugo.toml`:

```toml
[params]
  customCSS = ["css/custom.css"]
```

### Adicionando JS customizado

```toml
[params]
  customJS = ["js/custom.js"]
```

### Shortcodes customizados

Shortcodes são componentes reutilizáveis dentro dos posts. Crie em `layouts/shortcodes/`:

**`layouts/shortcodes/aviso.html`:**
```html
<div class="aviso aviso-{{ .Get "tipo" | default "info" }}">
  {{ .Inner | markdownify }}
</div>
```

Uso no Markdown:
```markdown
{{</* aviso tipo="atenção" */>}}
Isso é muito importante!
{{</* /aviso */>}}
```

### Variáveis úteis nos templates

```html
{{ .Title }}          <!-- Título do post -->
{{ .Date }}           <!-- Data -->
{{ .Params.author }}  <!-- Campo customizado do front matter -->
{{ .Content }}        <!-- Conteúdo completo do post -->
{{ .Summary }}        <!-- Resumo -->
{{ .ReadingTime }}    <!-- Tempo de leitura estimado -->
{{ .WordCount }}      <!-- Contagem de palavras -->
{{ .Permalink }}      <!-- URL completa -->
{{ .Site.Title }}     <!-- Título do site -->
```

---

## Imagens e assets

### Adicionando imagens estáticas

Coloque em `static/images/` e referencie assim:

```markdown
![Descrição](/images/foto.jpg)
```

### Processamento de imagens (Hugo Pipes)

Hugo pode redimensionar imagens automaticamente:

```html
{{ $img := resources.Get "images/foto.jpg" }}
{{ $resized := $img.Resize "800x" }}
<img src="{{ $resized.RelPermalink }}" alt="Foto">
```

---

## Deploy e publicação

### Github Pages

**1.** Crie um repositório no Github chamado `seu-usuario.github.io`

**2.** Adicione o remote e envie:
```bash
git remote add origin git@github.com:seu-usuario/seu-usuario.github.io.git
git push -u origin main
```

**3.** Crie o arquivo `.github/workflows/hugo.yml`:

```yaml
name: Deploy Hugo

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

**4.** No Github, vá em **Settings → Pages** e selecione a branch `gh-pages`.

---

### Netlify (mais simples)

**1.** Conecte seu repositório Github no [netlify.com](https://netlify.com)

**2.** Configure o build:

| Campo | Valor |
|---|---|
| Build command | `hugo --minify` |
| Publish directory | `public` |

**3.** Adicione a variável de ambiente:

```
HUGO_VERSION = 0.145.0
```

Pronto - a cada `git push`, o Netlify faz o deploy automaticamente.

---

### Vercel

Conecte o repositório no [vercel.com](https://vercel.com) e selecione o framework **Hugo**. O Vercel detecta automaticamente as configurações necessárias.

---

## Resumo dos comandos do dia a dia

```bash
# Novo post
hugo new content posts/meu-post.md

# Visualizar com rascunhos
hugo server -D

# Gerar site para produção
hugo --minify

# Atualizar tema
git submodule update --remote --merge

# Ver configuração ativa
hugo config
```

---

Com isso você tem tudo que precisa para criar, personalizar e publicar um blog profissional com Hugo. É uma das ferramentas mais simples e poderosas para quem quer ter um blog sem complexidade de backend. ✍️🚀

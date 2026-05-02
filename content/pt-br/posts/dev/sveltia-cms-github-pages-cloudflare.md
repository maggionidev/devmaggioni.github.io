---

title: 'Sveltia CMS com GitHub Pages e Cloudflare Workers'

description: Como configurar o Sveltia CMS no GitHub Pages usando Cloudflare Workers para autenticação OAuth - sem depender do Netlify.

summary: Como configurar o Sveltia CMS no GitHub Pages usando Cloudflare Workers para autenticação OAuth - sem depender do Netlify.

tags: 
    - cms
    - hugo
    - github-pages
    - cloudflare
    - sveltia

categories:
    - dev

author: Gabriel Maggioni
date: 2026-05-02T13:00:00
showToc: true
TocOpen: false
draft: false

---

Se você usa Hugo hospedado no GitHub Pages e queria um CMS visual para gerenciar seu conteúdo sem precisar do Netlify, esse post é pra você.

---

## Introdução

Eu construí este blog usando o Hugo como gerador de sites estáticos e o tema PaperMod, que me dá uma base leve, rápida e bem organizada para conteúdo técnico e pessoal. No começo isso parece suficiente, e de fato funciona muito bem só com Markdown e build estático.

Mas conforme o projeto cresce, começa a ficar claro que editar tudo “na mão” vira um gargalo. Publicar um post novo, gerenciar imagens, ajustar front matter, lidar com estrutura de pastas… tudo isso começa a pesar no fluxo. É aí que a ideia de um CMS entra naturalmente, não como luxo, mas como uma forma de manter o blog vivo sem transformar cada atualização em um pequeno projeto paralelo.

Dentro desse contexto entra o Sveltia CMS. Ele é uma interface moderna para gerenciamento de conteúdo em projetos estáticos, funcionando como uma camada amigável por cima dos arquivos do repositório. Em vez de editar Markdown diretamente, você passa a ter uma experiência mais guiada, quase como um editor de blog tradicional, mas sem abrir mão da simplicidade e do controle total que o Hugo oferece.

A escolha por ele vem dessa tentativa de equilibrar duas coisas que normalmente brigam entre si: liberdade total do sistema de arquivos e uma experiência de edição mais fluida, quase “sem fricção”.

---

## Por que não simplesmente usar o Netlify CMS?

O Netlify CMS (agora Decap CMS) usa o `api.netlify.com` como intermediário para autenticação OAuth com o GitHub. Se o seu site não está hospedado no Netlify, isso simplesmente não funciona - e você recebe um `Not Found` na hora do login.

O **Sveltia CMS** é um fork moderno e mais rápido do Netlify CMS, e resolve esse problema permitindo que você plugue seu próprio servidor OAuth.

---

## O problema com `usePKCE: true`

Você pode ter visto na documentação do Sveltia que existe uma opção `usePKCE: true` que eliminaria a necessidade de um servidor OAuth. Mas atenção:

> **Isso não funciona com GitHub.** O suporte a PKCE pelo GitHub ainda não foi lançado. A opção só funciona com GitLab. Mesmo com `usePKCE: true` no config, o Sveltia cai no fluxo padrão e tenta usar o `api.netlify.com` - que retorna 404 se seu site não está lá.

A solução correta é usar o **Sveltia CMS Authenticator**, um Cloudflare Worker oficial feito pelo time do Sveltia.

---

## Pré-requisitos

- Site Hugo rodando no GitHub Pages com domínio customizado (veja a minha [wiki](https://github.com/maggionidev/maggionidev.github.io/wiki))
- Conta e Domínio no Cloudflare ([ensinei nesse post](https://maggioni.dev/pt-br/posts/dev/how-to-use-backblaze-to-host-your-images/))
- Node.js instalado localmente
- `wrangler` CLI do Cloudflare

## Passo 1: Criar um OAuth App no GitHub

Acesse **GitHub → Settings → Developer settings → OAuth Apps → New OAuth App** e preencha:

- **Application name:** Sveltia CMS (ou qualquer nome)
- **Homepage URL:** `https://seu-dominio.com`
- **Authorization callback URL:** `https://sveltia-cms-auth.SEU_USUARIO.workers.dev`

> A callback URL será atualizada depois que você criar o Worker. Por enquanto, coloque um placeholder.

Após criar, anote o **Client ID** e gere um **Client Secret** - ele só aparece uma vez.

---

## Passo 2: Instalar o Wrangler e fazer login

```bash
npm install -g wrangler
wrangler login
```

O comando abrirá o browser para autenticar com sua conta Cloudflare.

---

## Passo 3: Deploy do Sveltia CMS Authenticator

Clone o repositório oficial e instale as dependências:

```bash
git clone https://github.com/sveltia/sveltia-cms-auth
cd sveltia-cms-auth
npm install
```

Configure as variáveis secretas (o CLI vai pedir que você cole os valores):

```bash
wrangler secret put GITHUB_CLIENT_ID
wrangler secret put GITHUB_CLIENT_SECRET
```

Faça o deploy:

```bash
wrangler deploy
```

A URL do Worker será exibida no terminal - algo como:

```
https://sveltia-cms-auth.SEU_USUARIO.workers.dev
```

---

## Passo 4: Atualizar o callback URL no GitHub

Volte no seu OAuth App no GitHub e atualize o **Authorization callback URL** para a URL  do seu Worker:

```
https://sveltia-cms-auth.SEU_USUARIO.workers.dev/callback
```

---

## Passo 5: Configurar o Sveltia CMS

No seu repositório Hugo, crie (ou atualize) o arquivo `static/admin/config.yml`:

```yaml
backend:
  name: github
  repo: SEU_USUARIO/SEU_REPO
  branch: main
  base_url: https://sveltia-cms-auth.SEU_USUARIO.workers.dev/callback

site_url: https://seu-dominio.com
media_folder: static/images
public_folder: /images

collections:
  # suas collections aqui
```

E o `static/admin/index.html`:

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

---

## Passo 6: Commit e push

```bash
git add static/admin/
git commit -m "feat: adiciona sveltia cms com cloudflare workers oauth"
git push
```

Aguarde o GitHub Actions fazer o deploy e acesse `https://seu-dominio.com/admin/`.

---

## Verificando se funcionou

Ao clicar em **Login with GitHub**, o fluxo correto é:

1. Redireciona para `github.com/login/oauth/authorize` com o **seu** `client_id`
2. Você autoriza o app
3. GitHub redireciona para o seu Worker (`workers.dev/?code=...`)
4. Worker troca o code pelo token e redireciona de volta para o CMS
5. Você entra autenticado ✅

---

Se ainda aparecer `api.netlify.com` na URL, limpe o cache do browser (`Ctrl+Shift+R`) ou teste em aba anônima - o Sveltia às vezes mantém configuração antiga em cache.

---

Com isso, você tem um CMS visual completo, rodando 100% no GitHub Pages, com autenticação segura via Cloudflare Workers - sem precisar migrar para o Netlify ou manter nenhuma infraestrutura adicional.

---
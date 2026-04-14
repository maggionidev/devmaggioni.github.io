
+++
title = "⚡ Principais Comandos do Hugo (Guia Rápido)"
date = 2026-04-13T21:00:00-03:00
tags = ["hugo", "cli", "comandos", "dev"]
categories = ["guia"]
author = "Gabriel"
draft = false
description = "Lista prática dos comandos mais usados do Hugo para desenvolvimento e deploy."
ShowReadingTime = true
ShowBreadCrumbs = true
ShowPostNavLinks = true
ShowWordCount = true
showToc = true
TocOpen = false
searchHidden = false

+++

## 🚀 Introdução

Se você está começando com o **Hugo**, dominar os comandos principais vai acelerar MUITO seu fluxo de trabalho.

Aqui está um guia direto ao ponto 👇

---

## 🏗️ Criar um novo projeto

```bash
hugo new site meu-blog
````

Cria toda a estrutura inicial do projeto.

---

## 📂 Entrar no projeto

```bash
cd meu-blog
```

---

## 🧩 Adicionar um tema

Exemplo com PaperMod:

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

Depois configure no `config.toml`:

```toml
theme = "PaperMod"
```

---

## 📝 Criar um novo post

```bash
hugo new br/posts/meu-post.md
```

Isso cria um arquivo já com frontmatter.

---

## ▶️ Rodar o servidor local

```bash
hugo server
```

Acesse:
👉 [http://localhost:1313](http://localhost:1313)

---

## 👻 Incluir posts em draft

```bash
hugo server -D
```

Muito útil durante desenvolvimento.

---

## 🏗️ Gerar build do site

```bash
hugo
```

Isso gera a pasta:

```
/public
```

---

## 📦 Build com baseURL (deploy)

```bash
hugo --baseURL "https://seusite.com"
```

Importante para produção.

---

## 🧹 Limpar cache e rebuild completo

```bash
hugo --gc --minify
```

* `--gc`: remove arquivos não usados
* `--minify`: otimiza HTML/CSS/JS

---

## 🔍 Ver logs detalhados

```bash
hugo server --debug
```

Ótimo pra troubleshooting.

---

## 🌐 Criar conteúdo em outra linguagem

```bash
hugo new content/en/posts/post.md
```

---

## ⚙️ Estrutura importante

Depois de alguns comandos, seu projeto fica assim:

```
content/
themes/
layouts/
static/
public/
config.toml
```

---

## 💡 Dicas práticas

✔ Use `-D` sempre no desenvolvimento
✔ Use `--minify` no deploy
✔ Sempre confira o `baseURL` antes de publicar
✔ Evite commitar a pasta `/public` (dependendo do setup)

---

## 🚀 Fluxo ideal de uso

```bash
hugo new site meu-blog
cd meu-blog
git init

# adicionar tema
# configurar

hugo new posts/primeiro-post.md
hugo server -D

# produção
hugo --minify
```

---

## 🎯 Conclusão

Com esses comandos você já consegue:

✅ Criar projetos
✅ Gerenciar conteúdo
✅ Rodar localmente
✅ Gerar build para produção

---

## 🔥 Próximo nível

Depois disso, você pode explorar:

* taxonomias avançadas
* custom layouts
* shortcodes
* CI/CD com GitHub Pages

---

Dominar o CLI do Hugo é metade do caminho pra ter um blog rápido e profissional 🚀

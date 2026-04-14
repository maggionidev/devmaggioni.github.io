+++
title = "🚀 Meu Primeiro Post com Hugo + PaperMod"
date = 2026-04-13T19:00:00-03:00
tags = ["hugo", "blog", "paperMod", "dev"]
categories = ["teste"]
author = "Gabriel"
draft = false
description = "Guia completo para começar com Hugo usando o tema PaperMod."
ShowReadingTime = true
ShowBreadCrumbs = true
ShowPostNavLinks = true
ShowWordCount = true
showToc = true
TocOpen = false
searchHidden = false
[cover]
    image = "/images/hello-world.png" # Pode ser um caminho local ou uma URL externa [3, 4]
    alt = "Descrição da imagem para acessibilidade" 
    relative = false # Defina como 'true' se estiver 
+++

## 👋 Bem-vindo

Esse é o meu primeiro post usando **Hugo + PaperMod**.

Aqui eu vou testar tudo que é essencial para um blog moderno:

* SEO
* busca
* tabela de conteúdo
* código
* imagens

---

## 📚 Estrutura básica de um post

Todo post no Hugo começa com o **frontmatter**:

```toml
+++
title = "Título do post"
date = 2026-04-13
tags = ["exemplo"]
draft = false
+++
```

Depois disso vem o conteúdo em Markdown.

---

## 🔍 Busca funcionando

Se você está lendo isso e a busca está funcionando:

👉 significa que:

* `index.json` foi gerado
* `search.md` está correto
* o post não está oculto

---

## 🧠 Tabela de conteúdo (TOC)

Esse post usa:

```toml
showToc = true
```

Isso gera automaticamente o índice 👇

---

## 💻 Código com highlight

```bash
hugo new site meu-blog
cd meu-blog
git init
```

```javascript
console.log("Hello Hugo 🚀");
```

---

## 🖼️ Imagens

Você pode usar imagens externas:

![Exemplo](https://picsum.photos/800/400)

Ou locais dentro do projeto.

---

## ⚙️ Configurações importantes

No seu `config.toml`, o essencial é:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

Sem isso, a busca não funciona.

---

## 🚀 Próximos passos

Agora você pode:

* criar novos posts:

```bash
hugo new posts/segundo-post.md
```

* rodar o servidor:

```bash
hugo server -D
```

---

## 🎯 Conclusão

Você agora tem:

✅ Post completo
✅ SEO básico
✅ Busca funcionando
✅ Tema configurado corretamente

---

Se quiser evoluir:

* adicionar comentários
* melhorar UI
* integrar analytics
* deploy automático

Esse é só o começo 🚀

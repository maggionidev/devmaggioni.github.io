# 🚀 Como clonar e rodar este blog (Hugo + PaperMod)

Este guia explica, de forma direta e organizada, como clonar o repositório, iniciar o ambiente local e entender onde mexer no código para personalizar o blog.

---

## 📥 1. Clonando o repositório

```bash
git clone https://github.com/maggionidev/maggionidev.github.io.git maggionidev-blog
```

### O que esse comando faz

* Baixa o repositório do GitHub para sua máquina
* Cria uma pasta local chamada `maggionidev-blog`
* Evita usar o nome padrão do repositório, deixando mais organizado

---

## 🔗 2. Inicializando submódulos

```bash
git submodule update --init --recursive --remote --merge
```

### O que isso significa

* Inicializa submódulos do Git (geralmente temas como PaperMod)
* Baixa dependências externas do projeto
* Mantém tudo atualizado com a versão mais recente dos submódulos
* O `--recursive` garante que submódulos dentro de submódulos também sejam carregados

---

## 📂 3. Entrando na pasta do projeto

```bash
cd maggionidev-blog
```

### O que isso faz

* Move o terminal para dentro do projeto
* A partir daqui todos os comandos serão executados no contexto do blog

---

## 🧪 4. Rodando o servidor local

```bash
hugo server -D
```

### Explicação detalhada

* Inicia o servidor local do Hugo
* `-D` inclui posts em rascunho (drafts)
* O site fica acessível normalmente em:
  `http://localhost:1313`

### O que acontece por trás

* Hugo compila o conteúdo Markdown
* Aplica o tema PaperMod
* Renderiza HTML em tempo real
* Atualiza automaticamente ao salvar arquivos

---

## ⚙️ 5. Estrutura de configuração (hugo.yml)

O arquivo `hugo.yml` controla praticamente todo o comportamento do site.

---

### 🌐 Configuração base

```yaml
baseURL: 'https://maggioni.dev/'
theme: PaperMod
```

* `baseURL`: URL oficial do site em produção
* `theme`: define o tema ativo (PaperMod neste caso)

---

### 🌍 Idiomas

```yaml
defaultContentLanguage: pt-BR
defaultContentLanguageInSubdir: true
```

* Define português como idioma padrão
* Ativa suporte a subdiretórios como `/pt-br/` e `/en/`

---

### 📄 Paginação

```yaml
pagination:
  pagerSize: 5
```

* Define quantos posts aparecem por página
* Aqui: 5 posts por página

---

### 🧱 Build settings

```yaml
enableRobotsTXT: true
buildDrafts: false
buildFuture: false
buildExpired: false
```

* `enableRobotsTXT`: ativa SEO básico
* `buildDrafts`: não publica rascunhos
* `buildFuture`: não publica posts com data futura
* `buildExpired`: ignora posts expirados

---

### 📊 Serviços externos

```yaml
services:
  disqus:
    shortname: XXXXX
  googleAnalytics:
    id: XXXXXX
```

* Disqus: sistema de comentários
* Google Analytics: rastreamento de visitantes

---

### 🔐 Privacidade

```yaml
privacy:
  googleAnalytics:
    disable: false
    respectDoNotTrack: true
```

* Respeita o "Do Not Track" do navegador
* Controla privacidade do analytics

---

## 🌎 Idiomas detalhados

O site tem dois idiomas configurados: português e inglês.

Cada idioma define:

* pasta de conteúdo
* título do site
* textos do perfil
* botões da home

Exemplo:

```yaml
pt-BR:
  contentDir: content/pt-br
  title: Gabriel Maggioni
```

### O que isso significa na prática

* Cada idioma tem sua própria pasta de posts
* Exemplo:

  * `content/pt-br/`
  * `content/en/`

---

## 🎨 Personalização visual (params)

Aqui fica tudo relacionado à UI do blog.

### 🧑 Perfil da home

```yaml
profileMode:
  enabled: true
```

* Ativa layout de perfil na home
* Mostra avatar, descrição e botões

---

### 🧾 SEO e conteúdo

```yaml
description: 'Programação, Blender, Unity e DevLogs...'
keywords:
```

* Descrição usada por motores de busca
* Keywords ajudam SEO e indexação

---

### 🧩 Funcionalidades do tema

```yaml
ShowReadingTime: true
ShowShareButtons: true
ShowPostNavLinks: true
```

* Mostra tempo de leitura
* Botões de compartilhar
* Navegação entre posts

---

## 🧭 Menu do site

```yaml
menu:
  main:
```

Define a barra de navegação:

* posts
* search
* categories
* tags
* about

Cada item tem:

* nome
* URL
* prioridade (weight)

---

## 🧠 Onde editar o site

### 🧩 layouts/partials

* Controla HTML do site
* Header, footer, componentes reutilizáveis
* Ideal para mudanças estruturais

---

### 🎨 assets/

* CSS, JS e imagens do sistema
* Usado para personalizar aparência
* Aqui você altera estilos globais

---

### 📄 content/

* Onde ficam os posts do blog
* Separado por idioma

---

## 🔥 Resumo rápido do fluxo

1. Clona o repo
2. Inicializa submódulos
3. Entra na pasta
4. Roda `hugo server -D`
5. Edita config, layouts ou assets
6. O site atualiza automaticamente

---

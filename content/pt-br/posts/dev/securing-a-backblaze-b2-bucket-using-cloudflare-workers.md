+++

title = 'Como proteger um bucket Backblaze B2 usando Cloudflare Workers (sem custo inesperado)'
description = "Aprenda a proteger um bucket Backblaze B2 usando Cloudflare Workers, tornando-o privado,  e servindo imagens com cache sem custo inesperado."
summary = "Guia prático garantindo que imagens sejam servidas apenas pelo seu domínio com custo controlado."
tags=["backblaze", "cloudflare", "dns", "domain configuration"]
categories=["dev", "cdn"]
keywords = ["backblaze b2", "cloudflare workers", "bucket privado", "cdn imagens", "wrangler deploy", "hugo static site"]

author = "Gabriel Maggioni"
draft = false
date = '2026-04-22T19:54:10-03:00'
#lastmod = '2026-04-22T19:54:10-03:00'
#publishDate = '2026-04-22T19:54:10-03:00'
#expiryDate = '2026-04-22T19:54:10-03:00'

showToc = true
TocOpen = false

+++

## Introdução

Se você usa o Backblaze B2 pra servir imagens estáticas, provavelmente começou do jeito mais simples possível: bucket público e links diretos.

(Eu ensinei como configurar isso do zero, nesse post => {{< link href="/posts/dev/how-to-use-backblaze-to-host-your-images" text="configurando um bucket b2 para servir imagens estáticas">}})

Funciona. Até não funcionar mais.

O problema aparece quando alguém começa a acessar esses links direto. Pode ser bot, hotlink em outro site, scraping… e de repente você está pagando download direto do B2 sem nem perceber.

A solução não é só esconder o bucket. É forçar todo o tráfego a passar por um proxy controlado com cache. Foi exatamente isso que implementei usando Cloudflare Workers.

---

## O problema real

Quando seu bucket é público, qualquer um pode acessar:

`https://seu-bucket.s3.us-east-005.backblazeb2.com/imagem.png`

Mesmo que você use um CDN na frente, esse endpoint continua aberto.

Isso significa:

- bypass do cache
- custo direto no B2
- perda total de controle

---

A arquitetura correta:

Depois da configuração, o fluxo fica assim:

Usuário → media.seudominio.com → Cloudflare Worker → Backblaze (privado)

O usuário nunca fala direto com o B2.

---

**O que vamos fazer**
1. Criar um Worker como proxy
2. Assinar requests para um bucket privado
3. Servir tudo via domínio próprio
4. Cachear no edge da Cloudflare
5. Bloquear acesso direto ao B2

---

## Pré requisitos

- Conta + Bucket configurado no {{< link href="https://www.backblaze.com/">}}
- Conta + Domínio na {{< link href="https://www.cloudflare.com/pt-br/">}}
- {{< link href="https://nodejs.org/pt-br" text="Node.js">}} instalado
- {{< link href="https://git-scm.com/install/windows" text="Git">}} instalado
- Visual Studio Code ou outra IDE

 {{< warning 
  title="Sente-se perdido?" 
  text="Leia meu post anterior: [Hospedagem de Imagens com Backblaze B2 e Cloudflare: CDN Gratuito](posts/dev/how-to-use-backblaze-to-host-your-images/) "
>}}

---

**Instalando o Wrangler**

```bash
npm install -g wrangler
```

Login:

```bash
wrangler login
```

---

## Usando o template oficial

O Backblaze já fornece um template pronto:

```bash
git clone https://github.com/backblaze-b2-samples/cloudflare-b2 b2-proxy

cd b2-proxy

npm i
```

Abra essa pasta com `code .` ou navegue até ela pelo vscode, vamos precisar editar alguns códigos.

---

**Criando a Application Key**

No {{< link href="https://secure.backblaze.com/app_keys.htm" text="B2">}}:

"Add a new application key":

- acesso: Read Only
- escolha seu bucket
- sem prefixo (deixe vazio)

**Anote no seu bloco de notas:**

- Key ID
- Application Key (só aparece uma vez depois some, anote!)

---

## Configurando o Worker

No vscode, na pasta que você clonou do github, encontre o `wrangler.toml`.
você vai ver que existem algumas variáveis para serem preenchidas. Insira os dados correspondentes à sua conta.

```toml
[vars]
B2_APPLICATION_KEY_ID = "SEU_KEY_ID"
B2_ENDPOINT = "s3.us-east-005.backblazeb2.com"
BUCKET_NAME = "seu-bucket"
ALLOW_LIST_BUCKET = "false"
```

Agora, no cmd:

```bash
wrangler secret put B2_APPLICATION_KEY
```

Vai lhe perguntar o valor. Cole a sua Application Key (lembra que eu mandei anotar no bloco de notas?!)

---

## Vamos fazer um teste

Antes de mandar isso pra Cloudflare e derrubarmos nosso site (panic) vamos fazer um teste para saber se está tudo nos conformes:

Provavelmente existe um arquivo `.dev.vars`.
Se não existir, crie-a;

edite a var:

```
B2_APPLICATION_KEY = "sua key aqui"
```

Essa é a mesma application key que você colou no terminal antes. Vamos usar ela um momento aqui apenas para testar localmente.

Rode:

```bash
wrangler dev
```

Isso vai expor algum localhost da sua máquina, não sei qual, mas vai aparecer nos logs, basta clicar segurando ctrl encima do url.

Teste no browser:

`http://localhost:8787/imagem.png` (exemplo! use o localhost que foi aberto e apareceu no log do terminal, e claro, substitua "imagem.png" por alguma imagem que esteja no seu bucket).

Se funcionar aqui, pode respirar fundo, metade do caminho!
Se não funcionou... provavelmente você configurou algo errado ou pulou etapas.

---

## Deploy (atenção aqui)

```bash
wrangler deploy
```

---

## Calma calma lá - última configuração

 {{< warning 
  title="Não pule essa etapa, se não todo o nosso trabalho não valerá de nada!"
>}}

Já tá funcionando, mas ainda falta a configuração final: 

Vai no seu bucket B2 > Balde de configurações (ou bucket settings):
marque **arquivos de segmento** como **privado**;
Defina "Balde de informações" ou "Bucket infos" como `{"cache-control":"public, max-age=31536000"}`;

Clique em salvar;

---

## Agora, o teste definitivo;

Se tudo foi configurado certo, deve ter o seguinte comportamento:

Coloque na raiz do seu bucket uma imagem de teste, hello-world.png;
Copia o link que o B2 vai fornecer dela, abra uma guia anônima e tente acessar;

deve aparecer algo como:

```json
{
  "code": "unauthorized",
  "message": "",
  "status": 401
}
```

ok, isso é definitivamente bom;
Agora acesse pelo seu subdomínio:

`media.seusite.com/hello-world.png`;

deve aparecer uma linda imagem de hello world;

---

## Resumo

Você aprendeu nesse post, a como deixar seu bucket backblaze B2 privado, deixando-o mais seguro contra ataques, e principalmente, preservando sua carteira de gastos desnecessários;

A partir de agora, seus arquivos só poderão ser acessados com segurança pelo seu próprio subdomínio configurado na cloudflare, com cache ativo, e com custo de banda praticamente 0!

De nada!

---

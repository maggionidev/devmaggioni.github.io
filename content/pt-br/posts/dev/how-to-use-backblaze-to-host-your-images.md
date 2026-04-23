+++
# ========================
# 🧾 BASICO
# ========================
title = 'Hospedagem de Imagens com Backblaze B2 e Cloudflare: CDN Gratuito'
description = "Aprenda a configurar CDN gratuito para imagens usando Backblaze B2 e Cloudflare. Guia passo a passo com Transform Rules, CNAME e SSL para blogs estáticos."
summary = "Descubra como hospedar os assets do seu blog estático com custo quase zero, usando o subdomínio do seu próprio domínio e cache de borda global."
author = "Gabriel Maggioni"
tags=["backblaze", "cloudflare", "dns", "domain configuration"]
categories=["dev", "cdn"]

draft = false

date = '2026-04-21T11:30:23-03:00'
lastmod = '2026-04-22T19:30:00-03:00'

+++

Se você tem um blog estático no GitHub Pages, em algum momento você vai esbarrar no mesmo problema: onde hospedar as imagens sem pagar caro, sem URLs feias e sem prejudicar o carregamento?

Essa foi exatamente a pergunta que me fiz quando comecei a montar o `maggioni.dev`. A resposta que encontrei foi combinar o **Backblaze B2** com o **Cloudflare**. Nesse post vou te mostrar exatamente como configurar isso do zero.

Eu simplesmente faço upload dos meus arquivos no Backblaze, e posso usar o link dos meus arquivos para colocar em meus posts, como por exemplo:
{{< link href="https://assets.maggioni.dev/hello-world.png">}}.

Muito legal, e fica com cara profissional.

{{< warning 
  title="Leia esse post até o final!" 
  text="Ao fim desse post lhe darei 2 dicas super importantes, de como manter seu bucket seguro e previnir que acabe gerando custos desnecessários no seu backblaze!"
>}}

---

## O que é o Backblaze B2?

O Backblaze B2 é um serviço de armazenamento em nuvem focado em custo baixo e performance. Diferente do armazenamento convencional, ele organiza os dados como "objetos", o que permite que cada imagem ou arquivo seja acessado via URL de forma rápida e direta.

Na prática, pro meu blog, ele funciona como o "back-end" onde os assets ficam guardados, enquanto o GitHub Pages cuida apenas do código e do texto.

---

## Por que usar B2 + Cloudflare?

Antes de partir pra configuração, vale entender os quatro motivos que tornam essa combinação especialmente boa pra quem mantém um blog estático.

**1. Custo-Benefício Imbatível**

Enquanto o Amazon S3 tem uma estrutura de preços cheia de variáveis e cobranças por requisição, o B2 é direto: os primeiros 10 GB são gratuitos e, depois disso, o custo por GB é uma fração do que os grandes players cobram.

**2. Taxa de Saída Zero**

Essa é a maior vantagem pra quem usa Cloudflare. Normalmente, provedores de nuvem cobram pelo "egress", ou seja, pelo dado que sai do servidor toda vez que alguém acessa sua imagem. Graças à **Bandwidth Alliance**, a transferência entre Backblaze B2 e Cloudflare é totalmente gratuita.

Você paga só pelo armazenamento. Não importa quantas pessoas acessem suas fotos, a banda de saída não custa nada.

**3. URLs Profissionais e Limpas**

Com as Transform Rules que vamos configurar, você não vai expor URLs genéricas tipo `f005.backblazeb2.com/...`. Suas imagens ficam servidas pelo seu próprio subdomínio, como `assets.maggioni.dev`. Melhor pro SEO, melhor pra confiança do leitor.

**4. Desempenho Global com Edge Caching**

Ao colocar o Cloudflare na frente do B2, as imagens ficam cacheadas nos servidores de borda da Cloudflare ao redor do mundo. Um leitor em Portugal baixa a imagem de um servidor em Lisboa, não dos EUA. A diferença no tempo de carregamento (LCP) é considerável.

---

## Requisitos

- Possui um domínio

---

## Parte 1: Configurando o Cloudflare no seu Domínio

Comprei meu domínio na GoDaddy. Gosto de lá por ser rápido de configurar. Mas pra nossa solução funcionar, precisamos tirar as mãos da GoDaddy do DNS e transferir essa responsabilidade pro Cloudflare.

> Se você comprou seu domínio em outro registrador, o processo é parecido. Basta encontrar a opção de "nameservers" na interface do seu registrador.

### Transferindo os nameservers para o Cloudflare

**No painel da GoDaddy:**

1. Acesse sua conta e vá em **Foto de perfil → Meus Produtos**
2. Escolha o domínio e clique em **Gerenciar meu domínio**
3. Vá na aba **DNS → Servidores de nome**
4. Clique em **Alterar servidores de nome**
5. Marque a opção **Usarei meus próprios servidores de nome**
6. Deixa essa página aberta, você vai precisar voltar aqui

**No Cloudflare:**

1. Acesse {{< link href="https://www.cloudflare.com/pt-br/" >}} e crie uma conta se ainda não tem
2. Clique em **Adicionar → Conectar um domínio** e informe o seu domínio (ex: `meusite.com`)
3. Selecione **Importar DNS automaticamente** e clique em continuar
4. O Cloudflare vai exibir dois nameservers próprios, algo parecido com:
   - `sid.ns.cloudflare.com`
   - `vita.ns.cloudflare.com`
5. Copie os dois

**De volta na GoDaddy:**

Cole os nameservers nos campos que você deixou abertos e salve.

A GoDaddy vai transferir a administração do domínio pro Cloudflare. Isso pode levar alguns minutos. No painel da Cloudflare, fique atualizando até aparecer que o domínio está ativo.

---

## Parte 2: Criando sua Conta e Bucket no Backblaze B2

### Criar a conta

Acesse {{< link href="https://www.backblaze.com/" >}}, crie uma conta e ative o **B2 Cloud Storage** nas configurações.

### Criar um bucket público

No painel do Backblaze:

1. Vá em **B2 Cloud Storage → Buckets → Create a Bucket**
2. Configure assim:
   - **Bucket name:** `nome-bucket-unico-no-mundo` (mínimo 6 caracteres, no meu caso vou colocar como "maggioni-blog-assets")
   - **Privacy:** Public

> ⚠️ Atenção! Nessa etapa ele vai pedir um cartão de crédito! Não se desespere! Apenas use um cartão temporário, ele vai te cobrar 1 dólar. Depois pode remover o cartão ;)

3. Clique em **Create a Bucket**
4. Copie o valor do campo **Endpoint** que aparece depois de criar (algo tipo `f000.backblazeb2.com`). Você vai precisar desse endereço logo.

---

## Parte 3: Conectando Cloudflare ao Backblaze

### Criar o registro CNAME

No painel da Cloudflare, vá em **DNS → Registros → Add Novo Registro**.

| Campo | Valor |
|---|---|
| Type | CNAME |
| Name | media |
| Target | f000.backblazeb2.com (o endpoint que você copiou) |
| Proxy status | Proxied (ícone laranja ativado) |

Salva. Agora `assets.seusite.com` aponta pro Backblaze, mas ainda precisamos dizer *qual* bucket ele deve servir.

### Criar a Transform Rule

Sem essa regra, alguém poderia usar seu domínio pra acessar o bucket de outra pessoa no Backblaze, já que o endpoint é compartilhado entre clientes. A regra garante que as requisições sempre vão pro *seu* bucket específico.

Vai em **Regras → Visão Geral → Criar regra → Regra de reescrita de URL**.

preencha o filtro:

- **Nome da regra:** "Reescrita de URL do Backblaze".

- **Se as solicitações recebidas coincidirem…:**

Selecione "Personalizar expressão do filtro"

Vai abrir um input para você preencher, selecione campo como "Nome do host", e Operador como "é igual a", e em valor, adicione "assets.seusite.com"

- **Depois… Regravar em...:**

Vai abrir outro input para você preencher, selecione "dynamic" e em valor cole "concat("/file/nome-do-seu-bucket", http.request.uri.path)"
(no meu caso ficou "concat("/file/maggioni-blog-assets", http.request.uri.path)")

- **Consulta:**

Deixe marcado "preservar";

---

Salve. Clique em Implantar.

O que isso faz na prática: uma requisição pra `https://assets.seusite.com/foto.jpg` vira `https://f000.backblazeb2.com/file/nome-do-seu-bucket/foto.jpg` antes de chegar no Backblaze. Transparente pra quem acessa, seguro pra você.

---

### Configurar o SSL/TLS

O Backblaze só aceita HTTPS. Precisa garantir que o Cloudflare também vai usar HTTPS pra se comunicar com ele, do contrário as requisições vão falhar.

Vai em **SSL/TLS → Visão Geral → Configurar → Custom SSL/TLS**, seleciona **Full (Strict)** e salva.

---

## Testando tudo

☢️ Pode levar alguns minutos pra funcionar! Apenas aguarde! Limpe o cache e reinicie o navegador!

Acesse no navegador:

```
https://assets.seusite.com/nome-da-imagem.jpg
```

exemplo real:

{{< link href="https://assets.maggioni.dev/hello-world.png" >}}

Se a imagem apareceu, funcionou. Nos seus posts do Hugo, agora você usa esse subdomínio direto:

```markdown
![Descrição da imagem](https://assets.seusite.com/nome-da-imagem.jpg)
```

![Descrição da imagem](https://assets.maggioni.dev/hello-world.png)

---

## Dica opcional #1: Cache e performance extra 

Por padrão, o Cloudflare já cacheia arquivos de imagem com base na extensão. Se quiser controle maior, vá em **Caching → Cache Rules** e crie uma regra específica pro subdomínio `assets.seusite.com` com um TTL longo, algo como 1 mês.

Imagem estática não muda. Faz sentido cachear bastante.

No Cloudflare, encontre as `Cache Rules`;

Crie uma nova regra de cache:

- **Nome da regra:** cache-media-images;
- **Se as solicitações recebidas coincidirem…:** Personalizar expressão do filtro;
- **no criador de expressão** cole: `http.host eq "assets.seusite.com"`;
- **Então...Elegibilidade de cache:** Qualificado para cache;
- **TTL da borda > Ignore o cabeçalho de controle de cache e use este TTL:** coloque 1 mês;
- **Navegador TTL: Substitua a origem e use este TTL:** coloque 1 mês

clique em **salvar/implantar**;

---

## Dica opcional #2: Deixar seu bucket privado 

Tudo oque a gente configurou até agora... funciona muito bem...
No entanto, existe um problema de segurança nisso tudo:

- quando você envia um arquivo pro seu bucket b2 público, ele vai gerar uma URL semelhante à essa:
`https://f005.backblazeb2.com/file/maggioni-blog-assets/hello-world.png`;

Ocorre que, ao deixar seu bucket público, qualquer pessoa pode acessar esse link. Ou usar a sua imagem que está no blackbaze em outro lugar, rede social, outro blog, etc...

**E é aí que mora o perigo:** Se a requição bater direto no b2, invés do nosso CDN que configuramos na cloudflare, isso **vai te gerar custos.**

Se lembra que, no ínicio desse post eu mencionei a **Bandwidth Alliance**? Certo, a Bandwidth Alliance faz com que não importa quantas pessoas acessem aos seus arquivos, você não paga pelo tráfego, apenas pelo armazenamento (10gb gratuitos depois vai cobrando a mais se extrapolar). Isso só é possível se a requição bater no nosso Cloudflare primeiro:

- `https://assets.maggioni.dev/hello-world.png` - ✔️ Tráfego grátis garantido
- `https://f005.backblazeb2.com/file/maggioni-blog-assets/hello-world.png` - ❌ Se cair direto na b2, vai gerar custos

"Mas não é apenas eu divulgar o meu link assets.meusite.com e esconder o link direto da backblaze?" - Sim... e não.

Até funciona, um usuário comum vai usar o seu subdomínio e não vai nem saber da existência da backblaze. No entanto, um bug em seu site, ou até mesmo um ataque hacker, pode descobrir a backblaze por trás do seu subdomínio (nem é tão difícil assim na verdade, só inspecionar o código da página) e fazer requisições infinitas, o que vai derrubar a sua conexão, e se seu cartão estiver ativo, vai ficar cobrando sem parar...

"Ok Gabriel, então é só eu ir lá no meu bucket e mudar o botão pra privado?" - Não hehehehe;
Se você fizer isso, vai simplesmente parar de funcionar;

**Será necessário uma configuração adicional para resolver essa situação**.
Caso tenha interesse em seguir o tutorial, sinta-se a vontade para ler meu próximo post:
[deixando seu backblaze b2 mais seguro - e sem riscos de perder grana](/posts/dev/securing-a-backblaze-b2-bucket-using-cloudflare-workers)

---

## Conclusão

No final das contas, você tem um CDN global, custo perto de zero e URLs limpas com seu próprio domínio. O Backblaze cuida do armazenamento, o Cloudflare cuida da entrega, e você não paga nada pela banda de saída graças à Bandwidth Alliance.

Pra um blog estático no GitHub Pages, é difícil achar solução melhor.

---

=> [crie um blog estático com hugo!](posts/dev/create-a-blog-with-hugo/)
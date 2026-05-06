---
title: IA rodando offline com Ollama + OpenWebUI no CachyOS (KDE)
slug: ai-running-offline-with-ollama-and-openwebui
description: Tenha sua própria IA rodando localmente na sua máquina, offline e grátis!
summary: Tenha sua própria IA rodando localmente na sua máquina, offline e grátis!
tags:
  - linux
  - cachyos
  - ollama
  - llm
  - docker
  - ia offline
  - dev
categories:
  - llms
  - dev
keywords: []
author: Gabriel Maggioni
date: 2026-05-05T21:02:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

## Introdução

Uma ferramente que eu venho usando muito, extremamente importante e genuinamente útil, é o NotebookLM do Google.

O NotebookLM é uma ferramenta - tipo um chat gpt - só que mais focada: ele funciona como um assistente de leitura e análise baseado nos  arquivos que eu (usuário) forneco pra ele. Isso muda bastante o jogo, porque eu mesmo defino de onde vem a informação, eu mesmo crio minhas fontes.

Diferente de algo como o ChatGPT, que é mais geral e responde com base em conhecimento amplo (que as vezes viaja muito na maionese), o NotebookLM trabalha em cima de um conjunto fechado de dados que você forneceu. Ou seja, ele não tenta "adivinhar". **Ele consulta direto na fonte.** Nem preciso dizer que isso é **perfeito para estudar** e tirar dúvidas sobre seus estudos.

Se eu quero que meu Notebook seja sobre, digamos, Egito antigo: eu vou no NotebookLM e anexo vários PDFs (de preferência com OCR) sobre a história do Egito, junto links de artigos, vídeos no YouTube, e vou montando uma base sólida sobre esse assunto. Aos poucos vira uma base boa sobre o assunto.

A partir daí, sempre que eu perguntar algo sobre Egito antigo, ele responde baseado nessas fontes. Não é aquele conhecimento genérico solto, é algo ancorado no material que eu escolhi. Na prática, isso corta bastante as alucinações e deixa a resposta muito mais confiável dentro daquele contexto específico.

O Gemini recentemente lançou uma integração com o NotebookLM, então ficou ainda mais fácil de usar.

Bom, onde eu quero chegar é o seguinte: apesar de ser muito bom, ele tem algumas limitações que começam a pesar dependendo do uso. A primeira é óbvia, mas incomoda mais do que parece: você precisa estar online o tempo todo. Sem internet, simplesmente não existe.

A segunda já entra num ponto mais sensível. Usando o NotebookLM ou qualquer coisa ligada ao Google, você inevitavelmente passa dados pelos servidores do Google. Pra uso comum, sinceramente, pouca gente liga, já estamos expostos de várias formas mesmo. Só que muda completamente de figura quando entra dado confidencial no meio. Aí não é mais "tanto faz".

No meu trabalho, por exemplo, surgiu uma ideia interessante: criar um acervo centralizado com todos os documentos, e usar IA pra consultar, resumir, puxar informações específicas. Na teoria, perfeito.

Na prática, dois problemas apareceram bem rápido.

O primeiro é jurídico. A gente lida com documentos de terceiros, então entra direto em Lei Geral de Proteção de Dados. Não é só cuidado, é obrigação mesmo. Não dá pra sair subindo isso em qualquer serviço cloud.

O segundo é técnico. Estamos falando de dezenas de gigabytes de arquivos. Não é pouca coisa. Nem o próprio NotebookLM foi pensado pra segurar esse volume bruto de dados de forma confortável.

Resultado… descartei o uso dele pra esse cenário. Não por ser ruim, longe disso, mas porque não encaixa. Falta capacidade pra esse volume e, principalmente, não dá pra correr o risco de expor dados sensíveis ou alimentar modelo externo com esse tipo de conteúdo.

A saída acabou sendo bem mais "pé no chão": rodar tudo local.

Entram aí o Ollama junto com o Open WebUI. Basicamente, você recria a ideia do NotebookLM, só que dentro da sua própria máquina ou servidor. Sem internet obrigatória, sem terceiros no meio, e com controle total dos dados.

Dá mais trabalho? Dá. Mas nesse tipo de contexto… é o tipo de controle que deixa de ser luxo e vira requisito.

***

## Para qual sistema esse post é destinado

Esse tutorial é pra distros Linux baseadas em Arch (CachyOS recomendado).

***

## Requisitos

Bom, não precisa ser gênio pra saber que isso não vai rodar em qualquer batata. Não precisa ser um PC top, mas alguns requisitos mínimos são necessários pra funcionar bem:

```plain
- RAM: 16GB é o ideal. Menos que isso e você vai sofrer.
- CPU: AMD Ryzen série 3000 ou superior, ou Intel Core i5 10ª geração pra cima. Nada muito antigo.
- SSD: obrigatório.
- GPU: dá pra rodar só no CPU, mas uma GPU ajuda bastante. NVIDIA então, melhor ainda.
```

Pra referência, esse é o PC que usei pra escrever esse tutorial:

```plain
- RAM: 32GB
- CPU: Ryzen 7 5600x
- SSD: 500GB (tá caro essa parada, queria 1TB 🫠)
- GPU: RX 6600
```

***

## Instalando o Ollama

O Ollama é basicamente um servidor local que gerencia modelos de linguagem. Ele baixa, armazena e serve os modelos via uma API HTTP simples. A instalação oficial é via script:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Sim, é um `curl | sh`. Se isso te incomoda por princípio - e é um princípio válido - o código está no GitHub e você pode inspecionar antes. Na prática, o script detecta sua distribuição, instala as dependências necessárias e configura o serviço systemd automaticamente.

Verifique se funcionou:

```bash
ollama --version
```

Se retornar uma versão, tá ótimo. Agora baixe um modelo pra testar. O Llama 3 é um bom ponto de partida:

```bash
ollama run llama3
```

Esse comando baixa o modelo (são alguns gigabytes) e já abre uma sessão interativa no terminal assim que termina. Se sua máquina for mais modesta em RAM, use a versão menor:

```bash
ollama run llama3:8b
```

A versão `8b` ocupa menos memória e é consideravelmente mais rápida em hardware sem GPU dedicada. Pra uso com documentos e perguntas diretas, a diferença de qualidade é aceitável.

***

## Rodando o Ollama como serviço

Você não quer ter que lembrar de iniciar o Ollama manualmente toda vez. Configure como serviço systemd:

```bash
systemctl enable ollama
systemctl start ollama
```

Pra confirmar que está respondendo:

```bash
curl http://localhost:11434
```

A resposta deve ser algo como `Ollama is running`. Se retornar isso, o servidor está no ar e a API está acessível. Essa URL é o que o Open WebUI vai usar pra se comunicar com os modelos.

***

## Sessão bônus: configurar o Ollama pra rodar na GPU

Por padrão o Ollama rodou no CPU aqui. O que não é tão ruim, mas fica mais lento do que rodando na GPU. Pra mudar isso, execute os comandos abaixo (depois de instalar o Ollama, né):

```bash
sudo systemctl stop ollama  # para o Ollama antes de editar
sudo systemctl edit ollama
```

Vai abrir o editor no terminal pra editar o arquivo de configuração do Ollama. Cola isso ali:

```plain
[Service]
Environment="OLLAMA_VULKAN=1"
Environment="GGML_VK_VISIBLE_DEVICES=0"
Environment="OLLAMA_MAX_VRAM=5871947673"
```

Pra salvar: `CTRL + O` > Enter > `CTRL + X` > Enter.

> **Atenção:** isso vai fazer o Ollama usar bastante VRAM. Se for jogar ou fazer outra coisa pesada, para o serviço antes: `sudo systemctl stop ollama`.

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

A partir de agora os modelos vão rodar direto na GPU. Vai ficar mais rápido, mas cuidado pra não instalar um modelo grande demais e travar tudo.

Pra monitorar o uso da GPU em tempo real:

```bash
radeontop
```

***

## Instalando o Docker

O Open WebUI é distribuído como imagem Docker, então você precisa do Docker instalado e funcionando. No CachyOS:

```bash
sudo pacman -S docker
```

Habilitar e iniciar o serviço:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Adicionar seu usuário ao grupo `docker` pra não precisar de `sudo` em todo comando:

```bash
sudo usermod -aG docker $USER
```

Aqui é sério: **reinicie o computador**. Não adianta tentar pular essa etapa.

```bash
sudo reboot
```

Teste básico pra confirmar que o Docker está funcionando:

```bash
docker run hello-world
```

Se retornar a mensagem de boas-vindas do Docker, tudo certo.

***

## Troubleshooting: Docker se recusando a funcionar

O Docker não estava subindo aqui. Dava erro quando tentava iniciar. _Se funcionou de primeira pra você, pode pular essa sessão tranquilamente._

O CachyOS atualiza kernels com frequência. Se você instalou uma atualização de kernel mas não reiniciou, o sistema está rodando um kernel enquanto os módulos instalados são de outro. O Docker depende de módulos do kernel pra networking e namespacing - se os módulos não batem com o kernel em execução, ele simplesmente não sobe.

Sintoma típico: o `systemctl start docker` falha com erro de `start-limit`, ou o daemon cai imediatamente após subir.

A correção é reiniciar pra carregar o kernel correto:

```bash
sudo reboot
```

Depois de reiniciar, confirme qual kernel está rodando:

```bash
uname -r
```

Compare com o que está instalado. Se agora baterem, tente subir o Docker novamente.

***

## Rodando o Open WebUI

Suba o container do Open WebUI (isso vai baixar mais alguns GBs):

```bash
docker run -d \
  --network=host \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

O `--network=host` é intencional aqui. Sem ele, o container tentaria alcançar o Ollama via `localhost:11434`, mas o `localhost` dentro de um container Docker não é o mesmo `localhost` do host - é o loopback interno do container, onde nada está escutando. Com `--network=host`, o container usa a rede do host diretamente e tudo resolve certinho.

O volume `-v open-webui:/app/backend/data` persiste os dados da interface - histórico de conversas, documentos enviados, configurações. Se você remover e recriar o container futuramente, os dados continuam lá.

O download da imagem vai levar alguns minutos na primeira vez. Depois disso, o container sobe em segundos.

***

## Acessando a interface

Quando o terminal liberar, acompanhe os logs pra garantir que não tem nada estranho acontecendo:

```bash
docker logs -f open-webui
```

**Open WebUI:** http://localhost:8080

> Pode demorar vários minutos pra carregar a página na primeira vez. Tenha paciência.

Na primeira vez que acessar o WebUI, ele vai pedir pra criar uma conta local - é só pra organizar as conversas, não tem nenhuma sincronização externa.

***

## Conectando o Open WebUI ao Ollama

Às vezes a interface sobe mas não consegue falar com o Ollama. Pra resolver isso:

Clique na **foto de perfil** > **Configurações** > **Configurações do Admin** > **Conexões** > **API Ollama**

Insira o endereço do Ollama (geralmente `http://localhost:11434`) e salve.

Pra confirmar que o Ollama está rodando no endereço certo:

```bash
curl http://localhost:11434
# esperado: "Ollama is running!"
```

***

## Comandos úteis para o dia a dia

### Ollama

Ver os modelos em execução:

```bash
ollama ps
```

Parar um modelo (liberar memória):

```bash
ollama stop nome-do-modelo
```

### Docker

Ver containers rodando agora:

```bash
docker ps
```

Ver tudo que você tem, incluindo parados:

```bash
docker ps -a
```

Ver imagens instaladas:

```bash
docker images
```

Ver logs de um container em tempo real:

```bash
docker logs -f nome_ou_id_do_container
```

Iniciar o Open WebUI (caso tenha parado):

```bash
docker start open-webui
```

***

## Deixando parecido com o NotebookLM (RAG com seus documentos)

Essa é a parte boa. O NotebookLM do Google é legal porque você joga seus PDFs lá e consegue conversar com eles - o modelo responde com base no conteúdo dos seus arquivos, não só no que ele aprendeu no treinamento. O Open WebUI faz exatamente isso, e de forma bem completa.

Essa funcionalidade se chama **RAG** (Retrieval-Augmented Generation). Em vez de depender só da memória do modelo, o sistema busca trechos relevantes dos seus documentos e os injeta na conversa em tempo real. O resultado é um modelo que "sabe" o que está nos seus arquivos.

### 1. Criar uma Knowledge Base (base de conhecimento)

Na barra lateral esquerda, clique em **Workspace** e depois em **Knowledge**. Clique no **+** pra criar uma nova base de conhecimento. Dê um nome, crie, e depois faça upload dos seus arquivos - PDF, Markdown, TXT, tudo funciona.

### 2. Usar a Knowledge Base no chat

No chat, antes de digitar sua pergunta, digite `#` e selecione a sua Knowledge Base. Vai aparecer um ícone de documento acima do campo de texto confirmando que está ativa. A partir daí, as respostas vêm embasadas no seu conteúdo.

### 3. Ajustar o contexto do modelo (importante!)

Por padrão, o Ollama usa um contexto de 2048 tokens, o que é pouco demais pra RAG funcionar bem. Um PDF razoável já passa disso fácil. Pra aumentar:

**Painel Admin** > **Models** > clique no modelo > **Advanced Parameters** > aumente o **Context Length** pra pelo menos `8192`. Se seu modelo suportar, joga `16384` ou mais.

### 4. Melhorar a qualidade da busca (opcional, mas recomendado)

Pra deixar a recuperação de informações mais precisa:

**Painel Admin** > **Settings** > **Documents**

- **Hybrid Search:** ative. Combina busca semântica com busca por palavras-chave. Faz diferença pra documentos técnicos onde termos específicos importam.
- **Chunk Min Size Target:** coloque algo em torno de `1000`. Evita que o sistema divida seus documentos em pedaços pequenos demais, o que piora a qualidade das respostas.
- **Embedding Model:** o padrão já funciona, mas se quiser melhorar, configure `nomic-embed-text` - você baixa via Ollama com `ollama pull nomic-embed-text` e depois seleciona nas configurações.

### 5. Usar diretamente no chat (sem Knowledge Base)

Se quiser algo mais rápido e pontual, você pode simplesmente arrastar um arquivo direto pra conversa. O modelo vai usar aquele documento como contexto pra aquela sessão específica. Funciona bem pra leitura rápida de PDFs.

***

Pronto. Você tem agora um NotebookLM local, privado, que não manda nada pra nuvem, roda no seu hardware e ainda aceita os modelos que você quiser. Enjoy. 🤙

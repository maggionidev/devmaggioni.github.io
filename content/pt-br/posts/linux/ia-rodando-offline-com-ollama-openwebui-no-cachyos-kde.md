---
title: IA rodando offline com Ollama + OpenWebUI no CachyOS (KDE)
slug: ''
description: Tenha sua própria IA rodando localmente na sua máquina, offline e grátis!
summary: Tenha sua própria IA rodando localmente na sua máquina, offline e grátis!
tags:
  - linux
  - cachyos
  - ollama
  - llm
  - docker
  - ia offline
categories:
  - linux
  - llms
keywords: []
author: Gabriel Maggioni
date: 2026-05-05T21:02:00
lastmod: ''
showToc: true
TocOpen: false
draft: true
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

## Pra qual sistema é esse post

Esse tutorial é pra distros Linux baseado em Arch (CachyOS recomendado)

***

## Requisitos

Bom, não precisa ser um gênio para saber que isso não vai rodar em qualquer batata. Não precisa ser um PC top, mas precisa de alguns requisitos mínimos pra funcionar bem: 

```plain
- RAM: 16GB de RAM é interessante.
- CPU: AMD Ryzen série 3000 ou superior, ou um Intel Core i5 de 10ª geração pra cima, nada muito antigo.
- SSD: obrigatório.
- GPU: Dá de rodar só no CPU, mas é bom ter uma GPU. Melhor ainda se for NVIDIA.
```

Para comparação, esse é o PC que estou rodando esse tutorial:

```plain
- RAM: 32GB
- CPU: Ryzen 7 5600x
- SSD: 500GB (Tá caro essa parada, queria 1TB 🫠)
- GPU: RX 6600
```

### Sessão bônus: configurar Ollama pra rodar na GPU

Pra mim o Ollama venho rodando direto no CPU. Oque não é tão ruim, mas fica mais lento que se fosse na GPU. Pra rodar o Ollama na sua GPU, execute os comandos abaixo (depois de instalar o Ollama né!):

```bash
sudo systemctl stop ollama # se tiver rodando o ollama para ele
sudo systemctl edit ollama
```

vai abrir no terminal pra editar o arquivo de configuração do Ollama. Cola ali:

```
[Service]
Environment="OLLAMA_VULKAN=1"
Environment="GGML_VK_VISIBLE_DEVICES=0"
Environment="OLLAMA_MAX_VRAM=5871947673"
``` 

Pra salvar:
CTRL + O > Enter > CTRL + X > Enter.

> Isso vai fazer com que Ollama use a sua GPU. (isso vai usar bastante VRAM, precisa parar o Ollama - `sudo systemctl stop ollama` - se for jogar ou fazer outra coisa pesada pra GPU);

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
``` 

A partir de agora os modelos vão rodar direto na GPU! Vai ficar mais rápido, mas cuidado pra não instalar modelo muito pesado pra não ficar lento. 

Para verificar o uso da gpu use o comando `radeontop`.

***

## Instalando o Ollama

O Ollama é basicamente um servidor local que gerencia modelos de linguagem. Ele baixa, armazena e serve os modelos via API HTTP simples. A instalação oficial é via script:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Sim, é um `curl | sh`. Se isso te incomoda por princípio (e é um princípio válido), o código está no GitHub e você pode inspecionar antes. Na prática, o script detecta sua distribuição, instala as dependências necessárias e configura o serviço systemd automaticamente.

Verifique se funcionou:

```bash
ollama --version
```

Se retornar uma versão, a instalação está boa. Agora baixe um modelo para testar. O Llama 3 é um ponto de partida razoável:

```bash
ollama run llama3
```

Esse comando baixa o modelo (são alguns gigabytes) e já abre uma sessão interativa no terminal assim que termina. Se sua máquina for mais modesta em RAM, use a versão menor:

```bash
ollama run llama3:8b
```

A versão 8b ocupa menos memória e é consideravelmente mais rápida em hardware sem GPU dedicada. Para uso com documentos e perguntas diretas, a diferença de qualidade é aceitável.

***

## Rodando o Ollama como serviço

Você não quer ter que lembrar de iniciar o Ollama manualmente toda vez. Configure como serviço systemd:

```bash
systemctl enable ollama
systemctl start ollama
```

Para confirmar que está respondendo:

```bash
curl http://localhost:11434
```

A resposta deve ser algo como `Ollama is running`. Se retornar isso, o servidor está no ar e a API está acessível. Essa URL é o que o Open WebUI vai usar para se comunicar com os modelos.

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

Adicionar seu usuário ao grupo `docker` para não precisar de `sudo` a cada comando:

```bash
sudo usermod -aG docker $USER
```

Você vai precisar reiniciar o computador! É sério!

```bash
sudo reboot
```

***

Teste básico para confirmar que o Docker está funcionando:

```bash
docker run hello-world
```

Se retornar a mensagem de boas-vindas do Docker, está tudo certo.

***

## Troubleshooting: Docker se recusando a funcionar

O docker não tava instalando pra mim. Dava erro quando tentava iniciar. *Se funcionou de primeira pra você, nem  precisa perder tempo lendo essa sessão.*

O CachyOS atualiza kernels com frequência. Se você instalou uma atualização de kernel mas não reiniciou, o sistema está rodando um kernel enquanto os módulos instalados são de outro. O Docker depende de módulos do kernel para networking e namespacing — se os módulos não batem com o kernel em execução, ele simplesmente não sobe.

Sintoma típico: o `systemctl start docker` falha com erro de `start-limit` ou o daemon cai imediatamente após subir.

A correção é reiniciar para carregar o kernel correto:

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

Suba o container da open-webui (isso vai baixar mais alguns GBs)

```bash
docker run -d \
  --network=host \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

O `--network=host` é intencional aqui. Sem ele, o container tentaria alcançar o Ollama via `localhost:11434`, mas o `localhost` dentro de um container Docker não é o mesmo que o `localhost` do host — é o loopback interno do container, onde nada está escutando. Com `--network=host`, o container usa a rede do host diretamente, e `localhost:11434` resolve corretamente para o Ollama que está rodando no host.

O volume `-v open-webui:/app/backend/data` persiste os dados da interface — histórico de conversas, documentos enviados, configurações. Se você remover e recriar o container futuramente, os dados continuam lá.

O download da imagem vai levar alguns minutos na primeira vez. Depois disso, o container sobe em segundos.

***

## Acessando a interface

Com tudo rodando:

- **Open WebUI**: http://localhost:8080
- **Ollama API**: http://localhost:11434

Na primeira vez que acessar o WebUI, ele vai pedir para criar uma conta local — é apenas para organizar conversas, não tem nenhuma sincronização externa até onde eu vi.

***

## Comandos básicos Docker e Ollama

Você vai precisar de alguns comandos básicos para gerenciar uma LLM com o Open WebUi:

### Ollama

1. Ver as LLMs em execução

```bash
ollama ps
```

Se aparecer coisa é por que tá rodando.

2. Parar uma LLM (liberar memória)

```bash
ollama stop nome-do-modelo
```

### Docker

1. Ver os containers que tu pussui

Isso mostra oque tá rodando *agora*:
```bash
docker ps
```

Isso mostra tudo oque você tem, mesmo parado:
```bash
docker ps -a
```

2. Ver as images instaladas:
```bash
docker images
```

3. Ver logs dentro de um container em tempo real

```bash
docker logs -f nome_ou_id_do_container
```

4. Rodar uma imagem
```bash
docker run -d nome-da-imagem
docker logs -f nome-da-imagem # <- ver os logs em tempo real
```

no nosso caso pra rodar o open-webui use sempre esse comando:

```bash
docker run -d \
  --network=host \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

***


## Conectando o Open WebUI ao Ollama

Às vezes a interface sobe mas não consegue falar com o Ollama. O erro mais comum é algo como:

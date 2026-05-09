---
title: Tudo oque você precisa saber sobre Docker no Linux
slug: all-about-docker-in-linux
description: Aprenda a instalar e usar Docker no Linux de forma prática e completa. Guia detalhado sobre containers, Docker Compose, volumes, redes, comandos essenciais, Lazydocker e boas práticas para Arch Linux e distribuições baseadas em Arch
summary: Aprenda a instalar e usar Docker no Linux de forma prática e completa. Guia detalhado sobre containers, Docker Compose, volumes, redes, comandos essenciais, Lazydocker e boas práticas para Arch Linux e distribuições baseadas em Arch
tags:
  - docker
  - cachyos
  - arch linux
  - docker compose
  - containers
  - lazydocker
  - linux
  - devops
  - virtualização
  - containerização
categories: []
keywords:
  - docker
  - cachyos
  - arch linux
  - docker compose
  - containers
  - lazydocker
  - linux
  - devops
  - virtualização
  - containerização
  - docker tutorial
  - docker no arch
  - docker no cachyos
  - postgreSQL docker
  - nginx docker
  - docker volumes
  - docker networks
  - dockerfile
  - docker hub
  - linux tutorial
  - yay
  - aur
  - terminal linux
  - desenvolvimento linux
  - self hosted
  - homelab
  - servidores linux
  - compose yaml
  - docker comandos
  - guia docker
author: Gabriel Maggioni
date: 2026-05-08T21:51:00
lastmod: 2026-05-08T21:51:00
showToc: true
TocOpen: false
draft: false
---

## Introdução: O que é Docker?

Docker é uma plataforma de **containerização**. Em vez de instalar um software diretamente no seu sistema, você o executa dentro de um **container** - um ambiente isolado, com tudo que aquele software precisa para funcionar (bibliotecas, configs, dependências), sem interferir no resto do sistema.

Pense num container como uma **caixinha selada** que contém a aplicação e tudo que ela precisa. Você pode criar, destruir, pausar e clonar essas caixinhas livremente.

### Container vs Máquina Virtual

| Característica | Container Docker | Máquina Virtual |
| --- | --- | --- |
| Inicialização | Segundos | Minutos |
| Tamanho | MBs | GBs |
| Isolamento | Processo/FS | Sistema operacional completo |
| Overhead | Mínimo | Alto |
| Portabilidade | Total | Parcial |

  ---

## Por que usar Docker?

- **Sem poluir o sistema:** Teste softwares, bancos de dados e serviços sem instalar nada diretamente no CachyOS.
- **"Funciona na minha máquina" → funciona em qualquer máquina:** O container carrega o ambiente junto. Se funciona aqui, funciona no servidor.
- **Isolamento real:** Um banco de dados PostgreSQL rodando em container não conflita com outro na porta diferente.
- **Fácil de destruir e recriar:** Errou a configuração? `docker rm` e começa do zero em segundos.
- **Desenvolvimento local limpo:** Rode stacks completas (app + banco + cache) com um único comando via Compose.

  ---

## Instalação no CachyOS

### 1. Instalar o Docker

```plain
sudo pacman -S docker
```

### 2. Habilitar e iniciar o serviço

```plain
sudo systemctl enable --now docker
```

### 3. Adicionar seu usuário ao grupo docker

Isso evita precisar de `sudo` em todo comando docker:

```plain
sudo usermod -aG docker $USER
```

> ⚠️ **Importante:** Reinicie o seu PC.

### 4. Testar a instalação

```plain
docker run hello-world
```

Se aparecer uma mensagem de boas-vindas, está funcionando. ✅

### 5. Instalar o Docker Compose (plugin)

```plain
sudo pacman -S docker-compose
```

  ---

## Conceitos essenciais

| Conceito | O que é |
| --- | --- |
| **Image** | Template somente-leitura. É o "molde" do container. |
| **Container** | Uma instância rodando de uma image. |
| **Dockerfile** | Arquivo de instruções para construir uma image. |
| **Registry** | Repositório de images. O padrão é o Docker Hub. |
| **Volume** | Pasta persistente fora do container (dados sobrevivem ao `rm`). |
| **Network** | Rede virtual entre containers. |
| **Compose** | Ferramenta para orquestrar múltiplos containers via arquivo YAML. |

### Fluxo básico

```plain
Dockerfile → build → Image → run → Container
                  ↑
             Docker Hub (pull)
```

  ---

## Comandos completos

### 🖼️ Images

```plain
# Baixar uma image do Docker Hub
docker pull ubuntu
docker pull nginx:latest
docker pull postgres:16

# Listar images locais
docker images
docker image ls

# Remover uma image
docker rmi nome-da-image
docker image rm nome-da-image

# Remover todas as images não usadas
docker image prune

# Remover todas as images (inclusive as usadas)
docker image prune -a

# Construir image a partir de um Dockerfile
docker build -t minha-app:1.0 .
docker build -t minha-app:latest -f Dockerfile.prod .

# Inspecionar uma image
docker inspect nome-da-image

# Ver histórico de camadas de uma image
docker history nome-da-image
```

  ---

### 📦 Containers

```plain
# Criar e iniciar container (básico)
docker run nginx

# Rodar em background (detached)
docker run -d nginx

# Dar um nome ao container
docker run -d --name meu-nginx nginx

# Mapear porta (host:container)
docker run -d -p 8080:80 nginx
# Acesse em: http://localhost:8080

# Rodar e acessar o terminal interativamente
docker run -it ubuntu bash

# Rodar e remover ao sair (descartável)
docker run --rm -it ubuntu bash

# Definir variável de ambiente
docker run -d -e POSTGRES_PASSWORD=senha123 postgres

# Montar volume
docker run -d -v /meu/host/pasta:/container/pasta nginx
docker run -d -v meu-volume:/data postgres

# Limitar recursos
docker run -d --memory="512m" --cpus="1.0" nginx

# Listar containers rodando
docker ps

# Listar todos os containers (inclusive parados)
docker ps -a

# Iniciar/parar/reiniciar container
docker start nome-ou-id
docker stop nome-ou-id
docker restart nome-ou-id

# Parar todos os containers rodando
docker stop $(docker ps -q)

# Remover container
docker rm nome-ou-id

# Remover container forçadamente (mesmo rodando)
docker rm -f nome-ou-id

# Remover todos os containers parados
docker container prune

# Ver logs do container
docker logs nome-ou-id

# Logs em tempo real
docker logs -f nome-ou-id

# Últimas N linhas de log
docker logs --tail 50 nome-ou-id

# Entrar no terminal de um container em execução
docker exec -it nome-ou-id bash
docker exec -it nome-ou-id sh  # se não tiver bash

# Rodar comando dentro do container
docker exec nome-ou-id ls /var/www

# Ver uso de recursos em tempo real
docker stats

# Ver estatísticas de um container específico
docker stats nome-ou-id

# Inspecionar detalhes do container
docker inspect nome-ou-id

# Copiar arquivo do host para o container
docker cp arquivo.txt nome-ou-id:/destino/

# Copiar arquivo do container para o host
docker cp nome-ou-id:/caminho/arquivo.txt ./
```

  ---

### 💾 Volumes

```plain
# Criar volume nomeado
docker volume create meu-volume

# Listar volumes
docker volume ls

# Inspecionar volume (ver onde fica no host)
docker volume inspect meu-volume

# Remover volume
docker volume rm meu-volume

# Remover volumes não usados
docker volume prune
```

  ---

### 🌐 Redes

```plain
# Listar redes
docker network ls

# Criar rede
docker network create minha-rede

# Conectar container a uma rede
docker network connect minha-rede nome-do-container

# Rodar container já em uma rede específica
docker run -d --network minha-rede nginx

# Inspecionar rede
docker network inspect minha-rede

# Remover redes não usadas
docker network prune
```

  ---

### 🧹 Limpeza geral

```plain
# Remover tudo que não está sendo usado (containers, images, redes, volumes)
docker system prune

# Remover TUDO incluindo volumes (⚠️ cuidado com dados)
docker system prune --volumes -a

# Ver quanto espaço o Docker está ocupando
docker system df
```

  ---

## Docker Compose

O Compose permite definir e rodar **múltiplos containers** com um único arquivo `docker-compose.yml`.

### Exemplo de arquivo `docker-compose.yml`

```plain
version: "3.9"

services:
  app:
    build: .
    ports:
            - "3000:3000"
    environment:
            - DATABASE_URL=postgres://user:senha@db:5432/meudb
    depends_on:
            - db
    volumes:
            - .:/app

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: senha
      POSTGRES_DB: meudb
    volumes:
            - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### Comandos do Compose

```plain
# Subir todos os serviços (em background)
docker compose up -d

# Subir e forçar rebuild das images
docker compose up -d --build

# Ver logs de todos os serviços
docker compose logs -f

# Ver logs de um serviço específico
docker compose logs -f db

# Ver status dos serviços
docker compose ps

# Parar os serviços (sem remover)
docker compose stop

# Parar e remover containers e redes
docker compose down

# Parar, remover containers, redes E volumes
docker compose down -v

# Rodar comando em um serviço
docker compose exec app bash
docker compose exec db psql -U user -d meudb

# Escalar serviço (ex: 3 instâncias da app)
docker compose up -d --scale app=3

# Rebuild de apenas um serviço
docker compose up -d --build app
```

  ---

## Lazydocker - TUI para Docker

O **Lazydocker** é uma interface de terminal (TUI) para gerenciar Docker visualmente, sem precisar digitar comandos toda hora. Pense no `lazygit`, mas para Docker.

### Instalação no CachyOS

```plain
# Via AUR com yay
yay -S lazydocker-bin
```

### Como usar

```plain
# Abrir o lazydocker
lazydocker
```

### Navegação básica

| Tecla | Ação |
| --- | --- |
| `↑` / `↓` | Navegar entre itens |
| `Tab` | Alternar entre painel esquerdo e direito |
| `Enter` | Selecionar / expandir |
| `[` / `]` | Mudar aba do painel direito (Logs, Stats, Config...) |
| `x` | Abrir menu de ações do item selecionado |
| `e` | Editar `docker-compose.yml` |
| `q` | Sair |

### O que você faz no lazydocker (sem digitar comandos)

- Ver todos os containers, images e volumes em tempo real
- Iniciar, parar, reiniciar e remover containers
- Ver logs com auto-scroll
- Ver uso de CPU, memória e rede
- Acessar o shell de um container
- Gerenciar Compose (subir/derrubar serviços)
- Limpar imagens e containers não usados
  ---

## Dicas e boas práticas

### Nunca rodar Docker como root desnecessariamente

Adicione seu usuário ao grupo `docker` (já feito na instalação) e esqueça o `sudo`.

### Sempre nomear seus containers

```plain
# Ruim
docker run -d nginx

# Bom
docker run -d --name proxy-nginx nginx
```

### Usar `.dockerignore`

Crie um `.dockerignore` na raiz do projeto para não copiar lixo para a image:

```plain
node_modules
.git
*.log
.env
dist
```

### Variáveis sensíveis em `.env`

```plain
# docker-compose.yml
env_file:
    - .env
```

```plain
# .env (nunca suba isso pro git!)
POSTGRES_PASSWORD=minha-senha-secreta
SECRET_KEY=abc123
```

### Verificar se um container reinicia automaticamente

```plain
docker run -d --restart unless-stopped --name nginx-prod nginx
```

Opções de `--restart`:

- `no` - nunca reinicia (padrão)
- `always` - sempre reinicia
- `on-failure` - reinicia se sair com erro
- `unless-stopped` - reinicia sempre, exceto se parado manualmente

  ---

## Cheatsheet

```markdown
# IMAGENS
docker pull image:tag          # Baixar image
docker images                  # Listar images
docker rmi image               # Remover image
docker build -t nome .         # Construir image

# CONTAINERS
docker run -d -p 8080:80 --name nome image   # Criar e rodar
docker ps                      # Listar rodando
docker ps -a                   # Listar todos
docker stop / start nome       # Parar / iniciar
docker rm nome                 # Remover
docker logs -f nome            # Ver logs ao vivo
docker exec -it nome bash      # Acessar terminal
docker stats                   # Monitorar recursos

# COMPOSE
docker compose up -d           # Subir stack
docker compose down            # Derrubar stack
docker compose logs -f         # Logs ao vivo
docker compose ps              # Status dos serviços
docker compose exec serv bash  # Terminal no serviço

# LIMPEZA
docker system prune            # Limpar tudo não usado
docker system df               # Ver uso de espaço

# LAZYDOCKER
lazydocker                     # Abrir TUI
```

  ---

> 💡 **Dica final:** Para uso cotidiano no desenvolvimento, o Compose + Lazydocker é a combinação perfeita. Você define sua stack em YAML uma vez e gerencia tudo visualmente pelo lazydocker sem decorar comandos.
>   ---

---
title: Como instalar uma distro Linux (Fedora KDE)
slug: ''
description: 'Guia completo para instalar o Fedora Linux: aprenda a criar um pendrive bootável com suporte a múltiplas ISOs usando o Ventoy, configurar a BIOS/UEFI e realizar a instalação passo a passo até a primeira inicialização.'
summary: 'Guia para instalar o Fedora Linux: aprenda a criar um pendrive bootável com suporte a múltiplas ISOs usando o Ventoy, configurar a BIOS/UEFI e realizar a instalação passo a passo até a primeira inicialização.'
tags:
  - fedora
  - linux
  - formatação
categories:
  - linux
author: Gabriel Maggioni
date: 2026-04-26T12:03:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

> **O que você vai aprender neste guia:** Criar um pendrive bootável inteligente (que aceita várias ISOs de uma vez), configurar a BIOS/UEFI para dar boot pelo pendrive, instalar o Fedora Linux e dar os primeiros passos no novo sistema. Do zero ao desktop funcional.

***

## Requisitos

Antes de colocar a mão na massa, separe o seguinte:

- **Pendrive** com pelo menos **16 GB**
- **Conexão com internet** para baixar a ISO do Fedora
- **Computador com Windows, Linux ou macOS** para preparar o pendrive
- **Cerca de 30–60 minutos** de tempo livre
- **Backup dos seus dados** - se for instalar no seu HD principal, faça backup antes!

***

## Parte 1 - Baixando a ISO do Fedora

### O que é uma ISO?

Uma ISO é um arquivo de imagem de disco - basicamente, uma cópia exata de um CD/DVD de instalação, comprimida num único arquivo. Você vai gravar essa imagem no seu pendrive para criar a mídia de instalação.

### Qual versão do Fedora baixar?

O Fedora tem várias "edições". Eu vou recomendar que você baixe a versão com KDE Plasma, que está bastante interessante na minha opinião.

### Baixando a ISO

1. Acesse: **{{< link href="https://fedoraproject.org/kde/download/">}}**
2. Clique em **"Download For Intel and AMD x86x64 systems"**
3. Escolha a arquitetura **x86_64** para a maioria dos computadores modernos
4. Aguarde o download - o arquivo tem cerca de **3 GB**

O arquivo vai se chamar algo parecido com `Fedora KDE Plasma Desktop 43.iso`.

> 💡 **Verifique a integridade:** Após o download, você pode (opcionalmente, mas recomendado) verificar se o arquivo não foi corrompido durante o download comparando o hash SHA256. Na página de download do Fedora, clique em "verify your download" para ver as instruções.

```bash
# No Linux/macOS, verificar o hash:
sha256sum caminho-do-arquivo/nome-do-arquivo.iso
```

***

## Parte 2 - Criando o Pendrive Bootável com Ventoy

Aqui está o diferencial deste guia: em vez de usar ferramentas comuns que **apagam o pendrive inteiro** para gravar apenas uma ISO, vamos usar o **Ventoy** - uma solução muito mais inteligente.

### Por Que o Ventoy é Superior?

Ferramentas como Rufus, Balena Etcher e dd sobrescrevem todo o pendrive com uma única ISO. Se você quiser testar outra distro, precisa regravá-lo do zero.

O **Ventoy** funciona de forma diferente:

- **Instala uma vez** no pendrive
- Depois é só **copiar e colar arquivos ISO** como se fosse um pendrive normal
- O pendrive continua **usável para arquivos comuns** na outra partição
- Na hora do boot, aparece um **menu** para você escolher qual ISO iniciar
- Suporta **centenas de ISOs** - Linux, Windows, ferramentas de recuperação…

### Instalando o Ventoy no Pendrive

#### No Windows:

1. Acesse [**ventoy.net**](https://www.ventoy.net/en/download.html) e baixe a versão para seu sistema operacional
2. Extraia o arquivo ZIP
3. Execute o **`Ventoy2Disk`** como **Administrador**
4. Em **"Device"**, selecione o seu pendrive (cuidado para não selecionar o HD errado!)
5. Clique em **"Install"**
6. Confirme o aviso - **o pendrive será formatado**
7. Aguarde a conclusão

> ⚠️ **ATENÇÃO:** Instalar no HD errado vai apagar seus dados! Tenha certeza que está selecionando o pendrive no menu

### Copiando as ISOs para o Pendrive

Após instalar o Ventoy, o pendrive vai aparecer com uma partição chamada **"Ventoy"**. É simples:

1. Abra o pendrive no explorador de arquivos
2. **Copie e cole** o arquivo `.iso` do Fedora diretamente para o pendrive
3. Pronto! Você pode copiar quantas ISOs quiser (desde que caibam no espaço disponível! Inclusive isos do windows)

```plain
📁 Pendrive (Ventoy)
├── Fedora43.iso
├── ubuntu-24.04.2-desktop-amd64.iso        ← pode ter várias!
├── linuxmint-22-cinnamon-64bit.iso
└── SystemRescue-11.00-amd64.iso
```

***

## Parte 3 - Configurando a BIOS/UEFI para Dar Boot pelo Pendrive

Esta é a etapa que mais assusta os iniciantes, mas é mais simples do que parece.

### O Que é a BIOS e a UEFI?

**BIOS** (Basic Input/Output System) é o firmware do computador - o software gravado diretamente na placa-mãe que roda antes de qualquer sistema operacional. Ele inicializa o hardware e define **de onde o computador vai dar boot** (HD, pendrive, CD, rede…).

**UEFI** (Unified Extensible Firmware Interface) é a evolução moderna da BIOS. A maioria dos computadores fabricados após 2012 usa UEFI. A diferença prática para o usuário é pequena, mas é importante saber que existem os dois.

### Como Entrar na BIOS/UEFI

Conecte o pendrive e **reinicie o computador**. Logo no início, antes do sistema carregar, você precisa pressionar uma tecla específica. A tecla varia conforme o fabricante:

| Fabricante / Produto | Tecla para BIOS | Tecla para Boot Menu |
| --- | --- | --- |
| **Dell** | F2 | F12 |
| **HP** | F10 ou Esc | F9 |
| **Lenovo** | F1 ou F2 | F12 |
| **Asus** | Del ou F2 | F8 |
| **Acer** | Del ou F2 | F12 |
| **MSI** | Del | F11 |
| **Gigabyte** | Del | F12 |
| **Samsung** | F2 | Esc |
| **Apple (Boot Camp)** | - | Option (Alt) |

> 💡 **Dica rápida:** Se você não sabe a tecla, preste atenção na primeira tela que aparece ao ligar o computador - geralmente está escrito algo como "Press F2 to enter Setup" ou "Press Del for BIOS". Você tem apenas alguns segundos!

### Atalho no Windows 10/11 (para UEFI)

Se o Windows estiver instalado e você não conseguir entrar na BIOS pela tecla (boot muito rápido), faça isso:

1. Vá em **Configurações → Sistema → Recuperação**
2. Clique em **"Reiniciar agora"** (em "Inicialização Avançada")
3. Na tela azul: **Solução de Problemas → Opções Avançadas → Configurações de Firmware UEFI**
4. Clique em **Reiniciar** - você vai direto para a UEFI

### Configurando a Ordem de Boot

Uma vez dentro da BIOS/UEFI:

**Opção 1 - Boot Menu (mais fácil):**
Em vez de entrar na BIOS, use a tecla do **Boot Menu** (tabela acima). Vai aparecer uma lista de dispositivos. Selecione seu pendrive com as setas do teclado e pressione Enter. Nenhuma configuração permanente é necessária!

**Opção 2 - Configurar na BIOS:**

1. Procure a aba/seção chamada **"Boot"**, **"Boot Order"** ou **"Boot Priority"**
2. Mova o **USB** ou **USB Flash Drive** para o **primeiro lugar** da lista (geralmente usando F5/F6 ou arrastando)
3. Salve e saia: pressione **F10** (na maioria das BIOS) ou vá em **"Save & Exit"**

### Configurações Importantes na UEFI (se disponíveis)

Algumas configurações podem impedir o boot pelo pendrive. Verifique:

**Secure Boot:**

- Localização: geralmente em "Security" ou "Boot"
- Para o Fedora, pode **deixar ativado** - o Fedora suporta Secure Boot nativamente
- Se tiver problemas, desative temporariamente para instalar

**Fast Boot / Fast Startup:**

- Localização: "Boot" ou "Advanced"
- **Desative** durante a instalação - pode interferir na detecção do pendrive

**CSM (Compatibility Support Module):**

- Se aparecer, mantenha **desativado** para PCs modernos (UEFI puro)
- Ative apenas se o seu PC for muito antigo e precisar de suporte BIOS legado

***

## Parte 4 - Iniciando pelo Pendrive e o Menu do Ventoy

Após configurar a BIOS e reiniciar com o pendrive conectado, você verá a **tela do Ventoy**:

```plain
┌─────────────────────────────────────────────┐
│                   VENTOY                    │
├─────────────────────────────────────────────┤
│  > Fedora    │
│    ubuntu-24.04.2-desktop-amd64             │
│    linuxmint-22-cinnamon-64bit              │
├─────────────────────────────────────────────┤
│  Use ↑↓ to navigate, Enter to boot         │
└─────────────────────────────────────────────┘
```

1. Use as **setas do teclado** para selecionar a ISO do Fedora
2. Pressione **Enter**
3. Se aparecer outra tela perguntando o modo de boot, escolha **"Boot in normal mode"**

Aguarde alguns segundos enquanto o sistema carrega da ISO…

***

## Parte 5 - Instalando o Fedora

### A Tela de Boas-vindas (Live Environment)

O Fedora vai iniciar em modo **Live** - ou seja, você está rodando o sistema diretamente do pendrive, sem instalar nada no HD. Isso é ótimo para:

- Testar se o Fedora funciona bem no seu hardware
- Verificar se Wi-Fi, áudio e vídeo estão funcionando
- Explorar o sistema antes de decidir instalar

Você verá uma janela de boas-vindas com duas opções:

- **"Try Fedora"** - usa o sistema sem instalar
- **"Install to Hard Drive"** - inicia a instalação

Clique em **"Install to Hard Drive"** para começar.

***

### O Instalador Anaconda

O instalador do Fedora se chama **Anaconda**. É visual, intuitivo e organizado numa tela central chamada **"Installation Summary"** (Resumo da Instalação), onde você configura tudo antes de confirmar.

#### Passo 1 - Idioma

Na primeira tela, selecione seu idioma:

1. Na lista da esquerda, escolha **"Português (Brasil)"**
2. Na lista da direita, escolha **"Português (Brasil)"**
3. Clique em **"Continuar"**

***

#### Passo 2 - Resumo da Instalação

Você verá a tela principal com vários itens para configurar. Vamos por partes:

***

#### 🌍 Localização - Hora e Data

1. Clique em **"Hora e Data"**
2. No mapa, clique em **Brasil** (ou selecione a região manualmente)
3. Selecione a cidade mais próxima: **"América/São Paulo"** para a maioria do Brasil
4. Ative **"Horário de Rede"** se estiver conectado - o sistema vai sincronizar automaticamente
5. Clique em **"Concluído"** (canto superior esquerdo)

***

#### ⌨️ Localização - Teclado

1. Clique em **"Teclado"**
2. Verifique se **"Português (Brasil)"** já está na lista
3. Se não estiver, clique no **"+"**, busque "Português (Brasil)" e adicione
4. Use o campo de teste na parte inferior para verificar se as teclas batem (teste o ç, ã, etc.)
5. Clique em **"Concluído"**

***

#### 💾 Sistema - Destino de Instalação (A Parte Mais Importante!)

Esta é a etapa mais crítica. Aqui você define **onde** o Fedora será instalado.

1. Clique em **"Destino de Instalação"**
2. Você verá os discos disponíveis no seu computador. Clique no disco onde quer instalar o Fedora
3. Em **"Configuração de Armazenamento"**, escolha:

**Opção A - Automático (recomendado para iniciantes):**

- Selecione **"Automático"**
- O instalador vai criar as partições necessárias sozinho
- ⚠️ Selecione se você quer manter o sistema antigo ou manter instalado junto com o novo. Se o disco tiver outros sistemas, leia com atenção o que será apagado

> 💡 **Btrfs vs Ext4:** O Fedora usa **btrfs** por padrão. Ele suporta snapshots (como pontos de restauração), compressão automática e é excelente para uso geral. Para quem está começando, deixe o padrão.

4. Clique em **"Concluído"** e confirme as mudanças

***

#### 🔒 Configuração de Usuário

**Root (Administrador do Sistema):**

1. Clique em **"Senha de Root"**
2. Você pode criar uma senha de root, ou deixar **desativado** (recomendado - use sudo em vez disso)
3. Clique em **"Concluído"**

**Criar sua conta de usuário:**

1. Clique em **"Criação de Usuário"**
2. Preencha:

- **Nome completo:** Seu nome
- **Nome de usuário:** nome sem espaços e em minúsculas (ex: `joao`)
- **Senha:** Use uma senha forte!

3. Marque a opção **"Tornar este usuário administrador"** (permite usar sudo)
4. Clique em **"Concluído"**

***

#### ✅ Iniciando a Instalação

Com tudo configurado (sem nenhum ícone de aviso ⚠️ na tela de resumo), clique em:

**"Iniciar Instalação"**

O processo vai começar. Você verá a barra de progresso realizando várias etapas:

- Preparar partições
- Instalar o sistema base
- Instalar pacotes e aplicativos
- Configurar o bootloader (GRUB)
- Finalizar configurações

**Tempo estimado: 10 a 25 minutos** (depende da velocidade do pendrive e do HD).

***

#### 🔄 Reiniciando

Quando a instalação terminar:

1. Clique em **"Concluir a Instalação"**
2. Será exibida uma tela pedindo para reiniciar
3. Clique em **"Reiniciar o Sistema"**
4. **Retire o pendrive** quando o sistema começar a desligar

***

## Parte 6 - Primeira Inicialização do Fedora

### O GRUB - Escolhendo o Sistema

Se você tem **dual boot** (Fedora + Windows), vai aparecer o menu do GRUB com as opções. Use as setas do teclado para selecionar e Enter para confirmar.

Se só tem o Fedora, o GRUB vai aparecer por alguns segundos e iniciar automaticamente.

### O Assistente de Configuração Inicial 

Na primeira vez, o Fedora vai te guiar por um assistente de configuração:

É bem básico, basta ler e continuar;

- **⚠️ Cuidado! Vai aparecer um botão bem discreto chamado "habilitar pacotes de terceiros" ou algo parecido! Marque essa opção, se não ficará meio limitado usar o sistema sem isso**

> Se você saiu da interface e não viu ou não habilitou, pode executar isso no terminal (nome Konsole):

```bash
sudo dnf install \
https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm

# e depois

sudo dnf install \
https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

***

## Parte 7 - Primeiras Coisas a Fazer no Fedora

Parabéns, você está no seu novo Fedora! Aqui estão os primeiros passos recomendados:

### 1. Atualizar o Sistema

Antes de qualquer coisa, atualize tudo. Abra o terminal (**Super** → busque "Terminal"):

```bash
sudo dnf update -y
```

Isso pode demorar alguns minutos na primeira vez. Reinicie após as atualizações:

```bash
sudo reboot
```

Eu gosto de rodar alguns scripts para deixar meu sistema top, com um único clique, agiliza bastante. Basta copiar o código, e criar um arquivo `.sh`. Em seguida dê permissão para execução: `chmod +x caminho/do/arquivo.sh`:

> ⚠️ Esse script é para usuários de AMD, se você tem NVIDIA veja a seção abaixo antes de rodar — está tudo separado por comentários

{{< details summary="Clique para ver o script (AMD)" >}}

```sh
# 1. Otimizar o DNF (Downloads mais rápidos)
echo "⚡ Otimizando o DNF..."

if ! grep -q "max_parallel_downloads" /etc/dnf/dnf.conf; then
  echo "max_parallel_downloads=10" | sudo tee -a /etc/dnf/dnf.conf > /dev/null
fi

if ! grep -q "fastestmirror" /etc/dnf/dnf.conf; then
  echo "fastestmirror=True" | sudo tee -a /etc/dnf/dnf.conf > /dev/null
fi

# 2. Atualizar o sistema
echo "🔄 Atualizando o sistema..."
sudo dnf upgrade --refresh -y

# 3. Habilitar Repositórios RPM Fusion (Free e Non-Free)
echo "📦 Habilitando RPM Fusion..."
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y

# 4. Instalar Codecs e Aceleração de Hardware para AMD (RX 6600)
echo "🎥 Instalando Codecs e Drivers Mesa (VA-API)..."
sudo dnf swap ffmpeg-free ffmpeg --allowerasing -y
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin -y
sudo dnf groupupdate sound-and-video -y
sudo dnf install mesa-va-drivers-freeworld mesa-vdpau-drivers-freeworld rocm-opencl -y

# 5. Configurar Flatpak e Flathub
echo "🌩️ Configurando Flathub..."
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

# 6. Ferramentas Essenciais (Gaming e Dev)
echo "🛠️ Instalando ferramentas base (Git, Gamemode)..."
sudo dnf install git gamemode jetbrains-mono-fonts-all -y

echo "Instalando o Steam"
sudo dnf install steam -y

# 7. Limpeza Final
echo "🧹 Limpando o sistema..."
sudo dnf autoremove -y

echo "✅ Tudo pronto! Reinicie o PC para aplicar todas as mudanças de Kernel e Drivers."
```

{{< /details >}}

***

### 2. Usuários de NVIDIA — Drivers Proprietários

Se você tem uma placa NVIDIA, essa etapa é **essencial**. O Fedora vem com o driver `nouveau` (open source), que funciona para uso básico mas tem desempenho limitado — sem suporte a CUDA, sem aceleração de hardware decente para vídeo, e com possíveis artefatos visuais.

> ⚠️ **Antes de instalar os drivers NVIDIA, certifique-se que o RPM Fusion Non-Free já está ativado** (o assistente inicial ou os comandos da seção anterior cuidam disso).

#### Instalando o driver NVIDIA

```bash
# Instala o driver proprietário + módulo do kernel (akmod compila automaticamente)
sudo dnf install akmod-nvidia -y

# Suporte a CUDA (necessário para IA, edição de vídeo acelerada, etc.)
sudo dnf install xorg-x11-drv-nvidia-cuda -y
```

> ⏳ **Importante:** O `akmod-nvidia` precisa compilar o módulo do kernel. Isso leva de **3 a 5 minutos** após a instalação. **Não reinicie imediatamente** — espere a compilação terminar. Você pode acompanhar com:

```bash
# Aguarda a conclusão da compilação (rode antes de reiniciar)
modinfo -F version nvidia
```

Quando o comando acima retornar um número de versão (ex: `550.144.03`), a compilação terminou. Aí sim reinicie.

#### Codecs com aceleração de hardware (NVIDIA)

Após reiniciar com o driver instalado:

```bash
# Codecs e VA-API para decodificação acelerada por GPU NVIDIA
sudo dnf swap ffmpeg-free ffmpeg --allowerasing -y
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin -y
sudo dnf install nvidia-vaapi-driver -y
```

#### Script completo para NVIDIA

Prefere fazer tudo de uma vez? Mesmo esquema do script AMD — salve como `.sh` e execute:

{{< details summary="Clique para ver o script (NVIDIA)" >}}

```sh
# 1. Otimizar o DNF
echo "⚡ Otimizando o DNF..."

if ! grep -q "max_parallel_downloads" /etc/dnf/dnf.conf; then
  echo "max_parallel_downloads=10" | sudo tee -a /etc/dnf/dnf.conf > /dev/null
fi

if ! grep -q "fastestmirror" /etc/dnf/dnf.conf; then
  echo "fastestmirror=True" | sudo tee -a /etc/dnf/dnf.conf > /dev/null
fi

# 2. Atualizar o sistema
echo "🔄 Atualizando o sistema..."
sudo dnf upgrade --refresh -y

# 3. Habilitar RPM Fusion
echo "📦 Habilitando RPM Fusion..."
sudo dnf install \
  https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y

# 4. Instalar driver NVIDIA proprietário
echo "🟢 Instalando driver NVIDIA..."
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda -y

echo "⏳ Aguardando compilação do módulo do kernel NVIDIA (pode levar alguns minutos)..."
# Espera o módulo estar disponível antes de continuar
until modinfo -F version nvidia &>/dev/null; do sleep 5; done
echo "✅ Driver NVIDIA compilado com sucesso!"

# 5. Codecs e VA-API para NVIDIA
echo "🎥 Instalando codecs e aceleração de vídeo..."
sudo dnf swap ffmpeg-free ffmpeg --allowerasing -y
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin -y
sudo dnf groupupdate sound-and-video -y
sudo dnf install nvidia-vaapi-driver -y

# 6. Configurar Flatpak e Flathub
echo "🌩️ Configurando Flathub..."
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

# 7. Ferramentas Essenciais
echo "🛠️ Instalando ferramentas base..."
sudo dnf install git gamemode jetbrains-mono-fonts-all -y

echo "Instalando o Steam"
sudo dnf install steam -y

# 8. Limpeza Final
echo "🧹 Limpando o sistema..."
sudo dnf autoremove -y

echo "✅ Tudo pronto! Reinicie o PC para usar o driver NVIDIA."
```

{{< /details >}}

#### Verificando se o driver está ativo após o reboot

```bash
# Deve retornar informações da sua placa com o driver "nvidia"
nvidia-smi

# Confirma qual driver está em uso
lspci -k | grep -A 3 "VGA"
```

Se `nvidia-smi` mostrar o modelo da sua placa e a versão do driver, está tudo certo. 🎉

***

Reinicie o sistema para aplicar todas as alterações.

Basicamente, é isso;
O que você precisa fazer agora é personalizar o seu sistema, e instalar seus apps. Isso é algo meio pessoal, cada pessoa tem seu gosto, no entanto planejo fazer um [post futuro sobre personalização no Fedora](/posts/linux/customizing-fedora)

***

## Resolução de Problemas Comuns

### O computador não deu boot pelo pendrive

- Verifique se o pendrive foi identificado na BIOS;
- Tente outra porta USB (prefira USB 2.0 se disponível para compatibilidade)
- Desative o Secure Boot temporariamente.
- Verifique se a ISO foi copiada corretamente para o pendrive Ventoy

### A tela ficou preta após selecionar a ISO

Pode ser problema com driver de vídeo. 

- Na tela do Ventoy, pressione `e` para editar os parâmetros de boot.
- Adicione `nomodeset` ao final da linha que começa com linux
- Pressione F10 para continuar o boot

> 💡 Isso é especialmente comum em placas NVIDIA — o `nomodeset` desativa a aceleração de vídeo temporariamente para você conseguir chegar ao instalador. Instale normalmente e depois instale o driver proprietário conforme a seção acima.

### Wi-Fi não aparece durante a instalação

- Conecte via cabo Ethernet para a instalação
- Após instalar, os drivers podem ser obtidos via RPM Fusion

### Sem som após a instalação

- Clique no ícone de som, na barra de tarefas e selecione a saída de áudio.
- Se não funcionar:

```bash
# Reinicie o serviço de áudio
systemctl --user restart pipewire pipewire-pulse

# Verifique se não está mutado
alsamixer
```

### Tela preta após instalar driver NVIDIA

Se o sistema não inicializar corretamente após instalar o driver NVIDIA:

1. Na tela do GRUB, pressione `e` para editar
2. Na linha que começa com `linux`, adicione `nomodeset` ao final
3. Pressione F10 para iniciar
4. Dentro do sistema, verifique se o módulo compilou:

```bash
# Se não retornar versão, o módulo ainda não compilou — aguarde e tente recompilar:
sudo akmods --force
sudo dracut --force
sudo reboot
```

***

## Resumo visual do processo:

```shell
[1] Baixar ISO do Fedora
         ↓
[2] Instalar Ventoy no pendrive
         ↓
[3] Copiar a ISO para o pendrive
         ↓
[4] Conectar pendrive → reiniciar
         ↓
[5] Entrar na BIOS → Boot pelo pendrive
         ↓
[6] Selecionar ISO no menu do Ventoy
         ↓
[7] Ambiente Live do Fedora carrega
         ↓
[8] Clicar em "Install to Hard Drive"
         ↓
[9] Configurar idioma, teclado, partições, usuário
         ↓
[10] Aguardar instalação (10–25 min)
         ↓
[11] Retirar pendrive → reiniciar
         ↓
[12] Configuração inicial do KDE
         ↓
[13] ✅ Fedora instalado e pronto!
         ↓
[14] Atualizar sistema + RPM Fusion + drivers (AMD ou NVIDIA)
```

***

## Conteúdos relevantes

- [Deixando o Fedora Bonito! (personalização)](/posts/linux/customizing-fedora)
- [Apps essenciais para Linux](/posts/linux/apps-essentials)

***

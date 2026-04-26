+++

title = 'Como ter papéis de parede animados no Fedora 43 com KDE Plasma'
description = "Aprenda a como ter papéis de parede animados no KDE Plasma do Fedora"
summary = "Aprenda a como ter papéis de parede animados no KDE Plasma do Fedora"
tags = ["linux", "fedora", "kde plasma", "wayland"]
categories = ["linux"]
keywords = ["linux", "kde", "kde plasma", "fedora", "wayland"]

author = "Gabriel Maggioni"
date = '2026-04-25T15:15:26-03:00'
#lastmod = '2026-04-25T15:15:26-03:00'
#publishDate = '2026-04-25T15:15:26-03:00'
#expiryDate = '2026-04-25T15:15:26-03:00'

showToc = true
TocOpen = false

draft = false

+++

## Introdução: O Desafio do Wallpaper Animado no Linux

Até o momento, não existe uma solução nativa do **Wallpaper Engine** para Linux. Os desenvolvedores já sinalizaram que não pretendem portar o software devido à fragmentação dos ambientes de desktop (GNOME, KDE, XFCE, etc.).

Embora existam projetos não oficiais que tentam integrar o conteúdo do Workshop da Steam, muitos são instáveis ou complexos de configurar. No meu caso, utilizando **Fedora 42 (KDE Plasma + Wayland)**, o popular *Hidamari* apresentou falhas críticas: o vídeo ficava sobreposto aos ícones, impedindo qualquer interação com a área de trabalho.

A solução definitiva que encontrei - estável e leve - é o plugin **Smart Video Wallpaper Reborn**.

---

## Pré-requisitos: Configurando os Codecs

Antes de instalar o plugin, precisamos garantir que o sistema tenha os codecs necessários para reproduzir vídeos (especialmente H.264) via hardware. O Fedora, por padrão, não os inclui.

### 1. Habilitando o RPM Fusion
Execute os comandos abaixo para adicionar os repositórios necessários:

```bash
sudo dnf install \
https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### 2. Trocando para o FFmpeg Completo
O Fedora vem com uma versão "free" do ffmpeg. Vamos substituir pela versão completa do RPM Fusion:

```bash
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
```

### 3. Verificando a Instalação
Certifique-se de que o suporte ao codec H.264 está ativo:

```bash
ffmpeg -codecs | grep h264
```
> **Dica:** Se o retorno mostrar `EV` ou `D` seguido de `h264`, a aceleração e decodificação estão prontas.

---

## Instalando e Configurando o Plugin

Com os codecs instalados, o processo no KDE Plasma é simples:

1.  **Acesse as Configurações:** Clique com o botão direito na área de trabalho e selecione **"Configurar área de trabalho e papel de parede..."**.
2.  **Baixe o Plugin:** Em "Tipo de papel de parede", clique no botão **Obter novos plugins...** > **Baixar novos papéis de parede de área de trabalho**.
3.  **Busque por Reborn:** Procure por **Smart Video Wallpaper Reborn** e clique em instalar.
4.  **Ative o Plugin:** Feche a janela de download e, na lista de tipos de papel de parede, selecione o plugin recém-instalado.
5.  **Selecione o Vídeo:** Clique em **"Escolher arquivo" (Pick a file)** e selecione o seu vídeo (recomendo `.mp4`).
6.  **Finalize:** Clique em **Aplicar** e pronto!

### Por que essa é a melhor opção?

Diferente de apps externos, esse plugin se integra diretamente ao gerenciador de janelas do KDE, o que garante que ele fique sempre atrás dos seus ícones.

---
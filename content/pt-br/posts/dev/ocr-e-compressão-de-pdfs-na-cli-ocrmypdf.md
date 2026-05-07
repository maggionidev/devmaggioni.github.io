---
title: OCR e compressão de PDFs na CLI (OCRmyPDF)
slug: Transforme PDFs escaneados pesados em arquivos leves e pesquisáveis, aplicando OCR, compressão e correções automáticas
description: ''
summary: Transforme PDFs escaneados pesados em arquivos leves e pesquisáveis, aplicando OCR, compressão e correções automáticas
tags:
  - arch
  - cachyos
  - linux
  - ocr
  - pdf
  - ocrmypdf
categories:
  - dev
keywords:
  - arch
  - cachyos
  - linux
  - ocr
  - pdf
  - ocrmypdf
author: Gabriel Maggioni
date: 2026-05-06T19:46:00
lastmod: ''
showToc: true
TocOpen: false
draft: true
---

# Introdução

Você recebe um PDF escaneado de 300 MB. Não tem texto selecionável, não tem busca, não dá pra mandar por e-mail, e abrir no celular é uma aventura. Esse é o cenário clássico de quem trabalha com arquivos que precisam ser digitalizados- qualidade duvidosa, tamanho absurdo, pouca utilidade prática além do papel original.

O `ocrmypdf` resolve isso. Ele aplica OCR nas páginas, gera uma camada de texto invisível por baixo da imagem (então o PDF continua parecendo igual), comprime tudo com bastante eficiência e ainda corrige distorções e rotações no processo. O resultado é um arquivo menor, pesquisável e indexável.

> Este post cobre a instalação no CachyOS (ou qualquer Arch-based).

***

## Instalando o yay

Se você ainda não tem o `yay`, a instalação é rápida. O `yay` é um AUR helper escrito em Go que envolve o `pacman` e adiciona suporte ao AUR com resolução automática de dependências, exibição do PKGBUILD antes do build, e integração transparente com os repositórios oficiais.

> O AUR é mantido pela comunidade. Não tem o mesmo nível de auditoria que os repositórios oficiais. Tenha atenção ao usar.

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

Se por obséquio o `yay` já vim instalado por padrão na sua distro, ignore.

***

## Instalando as dependências

O `ocrmypdf` não é um monólito - ele orquestra várias ferramentas por baixo dos panos. Você precisa de cada uma delas, e é útil entender o papel de cada componente antes de sair instalando às cegas.

```plain
yay -S ocrmypdf tesseract tesseract-data-por ghostscript
```

O **Tesseract** é o motor de OCR em si. É o que realmente lê as imagens e tenta identificar os caracteres. Sem ele, o `ocrmypdf` não faz nada além de empacotar o PDF de outro jeito. O `tesseract-data-por` é o modelo de linguagem para português - sem esse pacote, o Tesseract não sabe que os caracteres que está vendo formam palavras em português, e a qualidade do reconhecimento cai significativamente. Para documentos em inglês, você usaria `tesseract-data-eng`, que geralmente vem instalado por padrão.

O **Ghostscript** é o responsável pela parte de compressão e renderização do PDF final. É uma dependência antiga, estável, e presente em praticamente qualquer fluxo de trabalho com PDFs no Linux.

Depois de instalar, vale confirmar que o Tesseract está reconhecendo o idioma:

```plain
tesseract --list-langs
```

Se `por` aparecer na lista, está tudo certo. Se não aparecer, o pacote de dados não foi instalado corretamente - tente `yay -S tesseract-data-por` novamente.

Há também um pacote meta com todos os idiomas disponíveis, útil se você trabalha com documentos em vários idiomas:

```plain
yay -S tesseract-data
```

São vários gigabytes, então instale só se realmente precisar.

Por fim, duas ferramentas auxiliares que melhoram bastante o resultado em documentos escaneados:

```plain
yay -S unpaper pngquant
```

O **unpaper** é um pré-processador de imagem para documentos escaneados. Ele remove manchas, corrige perspectiva e melhora a qualidade geral da imagem antes do OCR - o que aumenta a precisão do reconhecimento. O **pngquant** é um compressor de imagens PNG com perdas controladas, usado pelo `ocrmypdf` quando a otimização de imagens está ativada. Sem ele, o nível de otimização 3 não funciona completamente.

***

## Primeiros testes com OCR

Com tudo instalado, o primeiro teste natural é simplesmente rodar o `ocrmypdf` no PDF problemático:

```plain
ocrmypdf --force-ocr -l por ./my.pdf ./pdf-ocred.pdf
```

A flag `--force-ocr` serve para forçar o OCR mesmo que o PDF já tenha uma camada de texto. Sem ela, se o `ocrmypdf` detectar texto existente nas páginas, ele pula o OCR e mantém o que está lá. Isso é o comportamento padrão e faz sentido - mas em PDFs escaneados que têm uma camada de texto ruim ou gerada automaticamente pelo scanner com qualidade baixa, você quer refazer do zero.

O custo de usar `--force-ocr` é tempo de processamento: cada página é re-renderizada e o Tesseract roda novamente em tudo. Em documentos grandes, isso pode demorar bastante. Para um PDF de 300 páginas numa máquina razoável, espere algo entre 10 e 30 minutos dependendo do DPI e da complexidade das páginas.

***

## O comando funcional

Depois de testar com o básico, o comando que realmente entrega resultado combinando OCR e compressão é este:

```plain
ocrmypdf --optimize 3 --clean --deskew --rotate-pages --oversample 300 -l por input.pdf output.pdf
```

Ou na forma mais legível, que é como você vai querer salvar nos seus scripts:

```plain
ocrmypdf \
  --optimize 3 \
  --deskew \
  --rotate-pages \
  --clean \
  --language por \
  ./a.pdf ./a.optimized.pdf
```

Cada flag tem um propósito concreto:

`--optimize 3` ativa o nível máximo de otimização de imagens. O `ocrmypdf` tem quatro níveis (0 a 3). No nível 3, ele usa o `pngquant` para recomprimir imagens com perdas controladas e o `jbig2` para imagens em preto e branco (se disponível). A diferença entre o nível 0 e o nível 3 em documentos escaneados pode ser enorme - facilmente 50% ou mais de redução adicional.

`--deskew` corrige o ângulo de páginas que foram colocadas tortas no scanner. É surpreendente como a maioria dos documentos escaneados tem algum grau de inclinação. Além de melhorar a aparência, isso também melhora a precisão do OCR porque o Tesseract funciona melhor com texto alinhado horizontalmente.

`--rotate-pages` detecta e corrige páginas em orientação errada - páginas de cabeça para baixo ou rotacionadas 90 graus. Documentos digitalizados por funcionários com pressa costumam ter pelo menos algumas páginas assim.

`--clean` usa o `unpaper` para limpar as imagens antes do OCR: remove ruído, manchas de toner, bordas de scanner. Não afeta o arquivo final diretamente, só melhora a qualidade do reconhecimento de texto.

`--oversample 300` define o DPI interno usado durante o processamento. O valor padrão é 300, que é adequado para a maioria dos documentos de texto. Se o seu PDF foi escaneado em resolução muito baixa (150 DPI ou menos), aumentar esse valor não vai recuperar detalhes que não existem. Se foi escaneado em resolução muito alta (600 DPI+), você pode reduzir para 150 e economizar bastante tempo de processamento e espaço no arquivo final - mais sobre isso adiante.

`-l por` (ou `--language por`) diz ao Tesseract para usar o modelo de linguagem português. Isso importa: o Tesseract usa o idioma para melhorar a precisão do reconhecimento, tratando sequências de caracteres como palavras plausíveis naquele idioma. Um documento em português reconhecido com o modelo inglês terá mais erros, especialmente em palavras com acentos.

***

## Diagnóstico e verificação de versão

Antes de sair processando documentos, vale verificar a versão instalada e confirmar que o ambiente está sano:

```plain
ocrmypdf --version
```

A saída mostra não só a versão do `ocrmypdf`, mas também as versões do Tesseract, Ghostscript e outras dependências detectadas. É a primeira coisa a checar quando algo dá errado - saber exatamente o que está instalado elimina metade das dúvidas durante o debug.

***

## Problemas reais e como resolver

Aqui é onde as coisas ficam interessantes. Em sistemas rolling release como o CachyOS, é comum que bibliotecas sejam atualizadas de forma assíncrona com as ferramentas que as usam. O `ocrmypdf` envolve várias dependências, e conflitos de versão acontecem.

### Arquivo não encontrado

O erro mais bobo, mas que acontece: você roda o comando e o `ocrmypdf` reclama que não encontra o arquivo de entrada. Em 90% dos casos é caminho errado ou o arquivo está em outro diretório. Verifique com `ls -lh` antes de rodar qualquer coisa, e se puder, use caminhos absolutos ou `./arquivo.pdf` explícito.

### Aviso sobre jbig2

Durante o processamento com `--optimize 3`, você pode ver um aviso parecido com:

```plain
WARNING - jbig2 is not available, some optimizations will be skipped
```

O `jbig2` é um codec de compressão muito eficiente para imagens binárias (preto e branco puro), exatamente o tipo gerado por scanners de documentos. Sem ele, o `ocrmypdf` não consegue aplicar a compressão mais agressiva em imagens monocromáticas. O impacto é real: documentos que poderiam chegar a 20 MB ficam em 60-80 MB.

Para instalar:

```plain
yay -S jbig2enc
```

Esse pacote está no AUR e compila do source, então leva alguns minutos. Depois de instalar, rode o `ocrmypdf` novamente - ele detecta automaticamente a presença do `jbig2enc` e passa a usá-lo.

### Erro de incompatibilidade de biblioteca: harfbuzz / uharfbuzz

Esse é o tipo de erro que aparece após uma atualização parcial do sistema. A mensagem típica é algo como:

```plain
ImportError: /usr/lib/python3.x/site-packages/uharfbuzz/...so: undefined symbol: hb_something
```

O `uharfbuzz` é um binding Python para a biblioteca `harfbuzz`, usada para renderização de texto. Quando o `harfbuzz` é atualizado pelo pacman mas o `uharfbuzz` (instalado via AUR) não é recompilado contra a nova versão, os símbolos não batem e tudo quebra.

A solução é atualizar o sistema todo primeiro e depois reconstruir o pacote afetado:

```plain
yay -Syu
yay -S harfbuzz uharfbuzz --rebuild
```

O `--rebuild` força o `yay` a recompilar o pacote mesmo que a versão no AUR seja a mesma - o que é exatamente o que você precisa quando a dependência nativa mudou. Esse tipo de problema é um bom argumento para sempre rodar `yay -Syu` antes de instalar qualquer pacote novo, especialmente em AUR helpers que têm dependências de bibliotecas nativas.

***

## Compressão avançada para PDFs que já têm texto

Aqui tem uma nuance importante que a maioria dos tutoriais ignora. Se o seu PDF já tem uma camada de texto - seja porque foi gerado digitalmente ou porque o scanner embutiu OCR automático - o `ocrmypdf` por padrão recusa o arquivo com um erro dizendo que já há texto e você deve usar `--force-ocr` ou `--skip-text`.

Usar `--force-ocr` refaz o OCR do zero e sobrescreve o texto existente. Isso faz sentido quando o texto existente é lixo. Mas quando o texto está bom e você só quer comprimir as imagens, use `--skip-text`:

```markdown
ocrmypdf \
  --optimize 3 \
  --clean \
  --remove-background \
  --deskew \
  --rotate-pages \
  --skip-text \
  --oversample 150 \
  -l por \
  b.pdf b.compress.pdf
```

`--skip-text` diz ao `ocrmypdf`: "se a página já tem texto, não processa o OCR, mas ainda aplica tudo o mais". Isso significa que as imagens ainda são recomprimidas, as páginas ainda são corrigidas, mas o texto existente é preservado. É o modo correto para PDFs digitais que estão apenas pesados demais.

`--remove-background` tenta remover fundos coloridos ou com gradiente das páginas, convertendo-os para branco puro. Isso parece cosmético, mas tem impacto direto no tamanho do arquivo: uma página com fundo levemente amarelado ou cinza armazena muito mais dados de cor do que uma página branca. Em PDFs escaneados de papel antigo ou com fundo sujo, essa flag pode reduzir bastante o tamanho.

`--oversample 150` reduz o DPI interno de processamento de 300 para 150. Se o documento já está bem legível e você não precisa de resolução máxima no texto reconhecido, 150 DPI é suficiente para OCR e gera imagens menores no arquivo final. A diferença em tamanho é proporcional ao quadrado da resolução: 150 DPI gera aproximadamente 1/4 dos dados de imagem comparado com 300 DPI. Não use isso em documentos com fontes muito pequenas ou detalhes finos.

A combinação de `--skip-text`, `--remove-background` e `--oversample 150` é o que permite reduções dramáticas em PDFs que já têm texto mas estão inchados. Um PDF de 300 MB gerado por scanner de alta resolução pode chegar facilmente a 30-50 MB com essas flags, dependendo do conteúdo.

***

## Uma observação sobre o --jbig2-lossy

Em versões mais antigas do `ocrmypdf`, existia a flag `--jbig2-lossy` que ativava compressão JBIG2 com perdas. O motivo pelo qual foi removida é sério: a compressão JBIG2 lossy pode, em casos raros mas documentados, alterar caracteres visualmente similares - um "8" vira um "0", um "rn" vira um "m". Em documentos jurídicos, financeiros ou qualquer coisa onde os caracteres exatos importam, isso é inaceitável.

A compressão JBIG2 sem perdas (que é o que o `jbig2enc` faz por padrão) ainda oferece uma compressão significativamente melhor que CCITT Fax Group 4 (o padrão anterior), mas sem o risco de corrupção de caracteres. A remoção do modo lossy foi a decisão certa, mesmo que signifique arquivos um pouco maiores que o máximo teórico possível.

Se alguém te sugerir ativar JBIG2 lossy via alguma gambiarra ou flag não documentada em documentos que têm valor legal ou financeiro, não faça.

***

## Resultado final

Com o pipeline completo funcionando - `jbig2enc` instalado, flags corretas, idioma certo - é realista esperar as seguintes reduções:

Um PDF de 300 MB escaneado em alta resolução, sem OCR, com páginas em preto e branco, comprime para algo entre 15 e 50 MB dependendo da densidade de conteúdo por página e da resolução original. PDFs com muitas imagens fotográficas comprimem menos. PDFs de documentos texto puro comprimem muito mais.

O que o `ocrmypdf` não vai fazer é milagre em PDFs que já estão comprimidos eficientemente, ou em PDFs com muitas imagens coloridas de alta qualidade onde a compressão com perdas não é aceitável. Nesses casos, a redução é menor - talvez 20 a 40% - e isso já é um ganho considerável.

O ponto mais importante: depois do processo, você tem um PDF menor, pesquisável, com texto selecionável, corrigido e pronto para indexação. O tamanho é consequência, não o objetivo principal. O objetivo é ter um documento utilizável, e o `ocrmypdf` entrega isso de forma consistente quando configurado direito.

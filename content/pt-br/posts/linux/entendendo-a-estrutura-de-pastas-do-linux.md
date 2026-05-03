---
title: Entendendo a estrutura de pastas do Linux
slug: understanding-the-linux-folder-structure
description: Entenda de uma vez por todas como funciona o sistema de pastas e arquivos do linux
summary: Entenda de uma vez por todas como funciona o sistema de pastas e arquivos do linux
tags:
  - linux
  - aprendendo
  - sistema de arquivos
categories:
  - linux
keywords: []
author: Gabriel Maggioni
date: 2026-05-02T20:00:00
lastmod: ''
showToc: true
TocOpen: false
draft: false
---

## Resumo do post

Nesse post iremos entender como funciona o sistema de arquivos do linux, como ele usa as pastas e etc.
Também tem conteúdos extras no final: **lost+found**, **fstab** e **trash**.

***

## Introdução

Se você cresceu usando Windows, é bem provável que a ideia de "disco C:" esteja quase automática no seu cérebro. Programas em um lugar, arquivos pessoais em outro, tudo meio segmentado por letras que organizam o caos de forma bem intuitiva.

Aí você abre o Linux pela primeira vez e… nada de C:, D:, E:. Em vez disso, você encontra uma única árvore de diretórios, começando pela raiz, o famoso "/". E isso causa um pequeno curto no início, porque o jeito de pensar muda completamente.

No Linux, tudo faz parte do mesmo sistema de arquivos. Discos, pendrives, pastas do sistema, configurações, usuários, tudo vive dentro dessa estrutura hierárquica. Entender isso não é só um detalhe técnico, é praticamente a chave para começar a se sentir confortável dentro do sistema.

E quando essa virada de mentalidade acontece, o Linux deixa de parecer estranho e começa a fazer sentido de um jeito bem mais profundo do que parece à primeira vista.
  \*\*\*

## O sistema de arquivos do Linux

O linux geralmente é composto por essa estrutura de pastas básicas:

```plain
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
└── usr
└── var
```

vamos entender como cada pasta funciona e suas responsabilidades:
  \*\*\*

## A pasta `ROOT` (ou `/`)

No Windows, as coisas costumam ficar geralmente em `C:\`. No Linux, a pasta principal do sistema fica em /.

`/` (ou "root") é a raiz do sistema, onde todas as pastas vivem. Basicamente, no root você vê todo o sistema aberto, e tudo oque tem nele.

![Print Sistema de Arquivos do Linux](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_200719.png "Print Sistema de Arquivos do Linux")

Perceba que, algumas pastas tem um ícone de "link", alguns tem um ícone de "cadeado" ou de "xis".

### As pastas com o ícone de link...

... são "links simbólicos" (symlinks) criados pelo sistema. Isso quer dizer que você pode ver a pasta `bin` dentro de `/`, mas ela não está realmente ali. Na prática, ela aponta para outro local do sistema, como se fosse um atalho, só que em nível de filesystem.

Se você olhar no print,  pode ver que o link está em `/`, mas aponta pra `usr/bin`.

![print dolphin](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_202608.png)

### As pastas com o ícone de xis ou cadeado...

...são pastas cujo seu usuário atual não possui acesso, ou privilégios para acessar (para acessá-las pode ser necessário digitar a senha do admin pra fazer alterações).

Note que, essas pastas são protegidas por um motivo: assegurar que você não vai estragar o seu sistema; Não é recomendado mecher nelas, a menos que você entenda o que está fazendo. O próprio sistema lhe avisa sobre os riscos:

![pasta admin](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_203527.png)

  \*\*\*

## Entendo as pastas 

### - `BIN`

O nome `bin` deriva de "binaries" (binários).

Ela é a pasta que contém os executáveis dos aplicativos que você usa.
Se você passear por esta pasta, verá vários nomes comuns no seu dia: 

Comandos como `ls`, `cp`, `mv`, `rm`, `cat`, `mkdir` aparecem por aqui, e são justamente esses pequenos executáveis que você usa o tempo todo sem nem pensar muito nisso.

Você pode, inclusive, jogar um executável seu aqui dentro (de alguma cli que você baixou ou criou) e poderá chamá-lo de qualquer lugar, apenas digitando o seu nome. 

### - `BOOT`

Como o nome sugere, esta é a pasta responsável pelo boot do seu sistema.
Ela armazena todos os arquivos e configurações responsáveis pela inicialização do seu linux.

Aqui você encontra a pasta do `GRUB`. Ele é o bootloader que entra em ação logo após o firmware da máquina iniciar o processo de boot. É ele quem decide qual sistema será carregado, especialmente em cenários de dual boot, permitindo escolher entre Linux, Windows ou qualquer outro sistema instalado antes de iniciar de fato o sistema operacional.

Aqui você encontra também, curiosamente, o próprio kernel do linux, no meu caso como uso o cachyos, ele usa sua própria versão customizada: "vmlinuz-linux-cachyos".

### - `DEV`

A pasta `dev` me enganou ao pensar ser "developer". Na real, ela é abreviação de "devices" (dispositivos).

Essa pasta é, muito interessante, e conceitualmente distinta do windows;

Ela abriga arquivos que, incrivelmente, representam o seu hardware. No linux, todo e qualquer dispositivo que você tem no seu pc é, olha que loucura, representado por um arquivo. Ou seja, seu teclado, mouse, hd, ssd, estão todos aqui.

![pasta dev](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_210543.png)

### - `ETC` 

Etcétara? Não sei oque significa, e há divergências sobre o real significado da palavra. No entanto isso não é relevante para nós.

Essa pasta costuma abrigar arquivos de configurações dos softwares do seu sistema. Ou seja, você pode configurar algo pela interface gráfica, e as configurações podem vir parar em algum lugar por aqui.

### - `HOME`

Essa é a pasta que nos interessa!

É aqui que ficam as pastas dos usuários, e seus respectivos arquivos. Essa é a pasta mais semelhante ao que temos no windows:

![print pasta home](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_211804.png)

Aqui é onde criamos os nossos arquivos, e trabalhamos no dia a dia.

### - `LIB`, `LIB64` e etc

Sâo pastas utilizadas geralmente por devs.

Essas pastas armazenam **bibliotecas compartilhadas**, ou seja, pedaços de código que programas usam para funcionar sem precisar "recriar tudo do zero" toda vez.

Pensa nelas como uma espécie de base de peças reutilizáveis que o sistema inteiro compartilha.

#### outras variações: `/usr/lib`, `/usr/local/lib`

Aqui começa a parte mais "organizada por camadas".

* `/usr/lib` → bibliotecas de programas instalados pelo sistema ou gerenciador de pacotes
* `/usr/local/lib` → bibliotecas de programas instalados manualmente (fora do gerenciador de pacotes)

Essa separação ajuda o sistema a não misturar o que é do sistema base com o que você instalou por fora.

### - `MEDIA`

A pasta `media` funciona como um ponto de montagem, voltada a dispositivos removíveis, como pendrives, cartões de memória, HDs e SSDs externos.

No Linux, quando um dispositivo é conectado, o sistema pode montá lo automaticamente em algum ponto do sistema de arquivos. Em algumas distribuições isso acontece dentro de `/media`, criando subpastas por usuário e nome do dispositivo.

Essa pasta é dinâmica, então só aparece algo útil quando existe um dispositivo montado. Caso contrário, ela pode estar vazia ou até nem existir no sistema, dependendo da distribuição.

Em sistemas mais modernos, esse comportamento pode variar. Em muitos casos, especialmente com systemd (meu caso no cachyos), as montagens automáticas acabam indo para caminhos como `/run/media/usuario`, enquanto o `/media` fica apenas como um padrão tradicional que nem sempre é utilizado diretamente.

Na prática, ela continua sendo um ponto de referência importante na estrutura do Linux, mas hoje o uso dela depende bastante de como o sistema está configurado e do ambiente desktop em uso.

### - `MNT`

`mnt` é uma pasta "prima" de `media`. O nome vem de "mount", que significa montagem.

Ela funciona como um ponto de montagem mais genérico. A diferença principal é que, enquanto o /media costuma ser usado por sistemas e ambientes desktop para montar dispositivos automaticamente, o /mnt tradicionalmente é reservado para montagens feitas manualmente pelo usuário ou pelo administrador do sistema.

Na prática, você pode usar o /mnt quando quiser montar qualquer coisa de forma controlada, como um segundo disco, uma partição específica ou até um sistema remoto. Por exemplo, um comando comum seria montar um disco ali temporariamente para manutenção ou transferência de arquivos.

Hoje em dia, muitas distribuições não impõem regras rígidas sobre isso, então tanto /media quanto /mnt podem ser usados de forma flexível. 

Um ponto importante dentro desse assunto é o arquivo /etc/fstab. Ele é basicamente a lista de montagens fixas do sistema. Nele você define quais dispositivos devem ser montados automaticamente no boot, em quais pontos de montagem e com quais opções.

### - `OPT`

`OPT` deriva de "Optional" (Opcional). 

Essa pasta é usada para instalação de softwares adicionais que não fazem parte do sistema base nem seguem totalmente a estrutura padrão de pacotes da distribuição. A ideia é manter esses programas isolados, com todos os arquivos deles organizados em um único lugar, sem misturar com o restante do sistema.

Na prática, ela costuma ser usada por softwares distribuídos "prontos", que vêm com tudo embutido. Em vez de espalhar executáveis, bibliotecas e recursos por várias pastas do sistema, tudo fica concentrado dentro de /opt/nome-do-programa.

O uso dela depende bastante de como o desenvolvedor empacota o software. Algumas aplicações escolhem esse modelo para evitar conflitos com bibliotecas do sistema ou para facilitar atualizações independentes.

No meu caso, por exemplo, o Brave aparece aqui isolado.

Já outros programas podem não seguir esse padrão e acabam sendo instalados pelo gerenciador de pacotes da distro, ficando espalhados pelas pastas tradicionais como /usr/bin, /usr/lib e outras partes do sistema.

### - `PROC`

 `PROC` deriva de "processes" (processos), e é um diretório especial do Linux conhecido como filesystem virtual.

Na prática, o /proc funciona como uma janela direta para o kernel. Ele expõe informações sobre processos, uso de memória, CPU, dispositivos e várias configurações internas que normalmente não seriam acessíveis de forma simples. Essas informações não estão gravadas no seu HD, ou no SSD. Elas estão na memória RAM do seu PC, e são apagados e recriados toda vez que você inicia o sistema, quando um processo é criado ou encerrado.

Por exemplo, cada processo em execução tem uma pasta dentro de /proc, identificada pelo seu PID. Dentro dela você encontra dados como status, uso de recursos e até links para arquivos abertos pelo processo.

É basicamente a mesma idéia da pasta `dev`, só que aplicado aos processos.

Sim, isso são os processos que estão rodando na sua máquina representados por arquivos:

![pasta proc](https://assets.maggioni.dev/posts/linux/Captura_de_tela_20260502_215542.png)

### - `ROOT`

Assim como a pasta `home`, o `/root` é um diretório pessoal, mas do usuário administrador do sistema.

Ele não deve ser confundido com a raiz `/` do Linux. O `/root` é apenas a "home" do superusuário.

Na prática, é onde ficam arquivos e configurações usadas quando você está operando com permissões administrativas, separado dos usuários comuns que ficam em `/home`.

Geralmente está vazia, não usamos ela diretamente.

### - `RUN` 

A pasta `run`, de "runtime", é outro diretório virtual, assim como o `proc`.

Os arquivos ali não ficam no disco, mas em memória, e representam o estado atual do sistema desde o último boot.

Diferente do `/proc`, que expõe informações direto do kernel, o `/run` guarda dados mais práticos de execução: coisas como sessões de usuários, sockets, arquivos de controle de serviços e pontos de montagem temporários.

É ali, por exemplo, que aparecem caminhos como `/run/media/usuario`, usados para montar dispositivos automaticamente.

No fim, o `/run` funciona como um espaço temporário onde o sistema e os serviços trocam informações enquanto estão rodando.

### - `SBIN` 

Se você entendeu a função da pasta `bin`, essa aqui segue a mesma ideia.

A diferença é que o `sbin` reúne executáveis voltados para administração do sistema. São ferramentas mais "sensíveis", usadas para manutenção, configuração de rede, discos, boot e coisas do tipo.

Nem sempre significa que só o root pode executar, mas, na prática, a maioria desses comandos faz sentido apenas com permissões elevadas. Por isso eles ficam separados dos binários comuns.

Enquanto o `/bin` tem comandos do dia a dia, o `/sbin` concentra utilitários mais críticos do sistema.

### - `SRV`

Pouco usada no dia a dia.

A pasta `srv` vem de "service" e foi pensada para armazenar dados servidos por serviços do sistema, como um servidor web ou FTP. Por exemplo, arquivos de um site poderiam ficar em `/srv/http`.

Na prática, muitas distribuições e aplicações ignoram esse padrão e usam outros caminhos como `/var/www` ou diretórios próprios, então ela acaba ficando vazia na maioria dos sistemas desktop.

### - `SYS`

Abreviação de “system”. Assim como `proc` e `run`, também é um diretório virtual, criado em tempo real pelo sistema.

O `/sys` expõe informações do kernel de forma mais estruturada, principalmente sobre hardware, dispositivos e drivers. Ele faz parte do sysfs, uma interface que permite não só ler informações, mas em alguns casos até alterar o comportamento de componentes do sistema.

Diferente do que parece, os módulos (drivers) e firmwares não ficam armazenados ali. Eles estão no disco, geralmente em `/lib/modules` e `/lib/firmware`. O `/sys` apenas mostra o estado atual desses componentes já carregados no sistema.

Na prática, é um ponto de interação direta com o kernel, mais técnico e raramente usado no dia a dia.

### - `TMP`

Abreviação de "temporary". Como o próprio nome sugere, arquivos e programas podem usar essa pasta como armazenamento temporário, que vão ser usados pelo usuário ou pelo próprio programa. Todos os dados aqui são apagados na reinicialização do sistema.

### - `USR`

Muitas vezes interpretado como "user", mas o significado mais aceito é "Unix System Resources".

O `/usr` concentra a maior parte dos programas e recursos do sistema que não são críticos para o boot. É ali que ficam executáveis, bibliotecas, documentação e outros arquivos usados no dia a dia.

Na prática, muita coisa que você instala vai parar dentro de `/usr`, principalmente em subpastas como `/usr/bin`, `/usr/lib` e `/usr/share`.

> se lembra que o bin, link é um link que vem pra cá?

### - `VAR` 

Estou ficando cansado de escrever esse post 🫠

Bom, estamos quase acabando! \~ grub grub grub café;

O nome var vem de "variable". É uma pasta onde ficam arquivos que mudam com o tempo, tanto em conteúdo quanto em tamanho.

Aqui entram coisas como logs do sistema, cache, arquivos temporários mais persistentes e dados gerados por serviços enquanto estão rodando. Por exemplo, logs costumam ficar em /var/log, e alguns serviços armazenam estado ou dados em /var/lib.

Diferente de outras partes mais "estáticas" do sistema, o /var está sempre sendo atualizado. 
  \*\*\*

## - extra #1: `LOST+FOUND`

`lost+found` pode ser traduzida como "achados e perdidos".

Essa pasta aparece principalmente em sistemas de arquivos do tipo ext, como ext4, e ela é criada automaticamente pelo sistema. Em condições normais, você quase nunca interage com ela.

A função dela é bem específica: armazenar arquivos que foram recuperados após algum tipo de corrupção no sistema de arquivos. Isso pode acontecer depois de um desligamento inesperado, queda de energia ou falhas no disco. Quando o sistema roda uma verificação com ferramentas como `fsck`, ele tenta restaurar o que for possível e coloca esses dados dentro do `lost+found`.

Na prática, o que vai parar ali geralmente são fragmentos de arquivos ou arquivos sem nome original, já que o sistema não conseguiu reconstruir completamente sua estrutura. Por isso ela costuma parecer vazia ou confusa quando acessada.

Em muitas distribuições ela fica oculta por padrão e aparece apenas com permissões elevadas, justamente porque não é um diretório para uso manual no dia a dia.
  \*\*\*

## - extra #2: Como funciona a `Lixeira`

A lixeira é virtual.

Quando você abre a lixeira no gerenciador de arquivos, o endereço exibido é trash:// — um protocolo virtual que unifica as lixeiras de todos os dispositivos conectados em uma interface única. Por baixo dos panos, ela aponta para pastas físicas reais no sistema de arquivos.

### Onde os arquivos ficam?

Para o disco principal do sistema, a lixeira fica na pasta oculta dentro da sua home:

```plain
# ~/.local/share/Trash/
   ├── files/      ← os arquivos apagados ficam aqui
   ├── info/       ← metadados: caminho original + data de exclusão
   └── expunged/   ← limbo antes do kernel liberar o espaço
```

A pasta info/ guarda um arquivo .trashinfo para cada item apagado — é ele que permite restaurar o arquivo para o lugar exato de onde veio.

###  .Trash-1000?

Quando você apaga um arquivo de um dispositivo externo (HD externo, pendrive), o Linux não manda para a lixeira do sistema. Ele cria uma lixeira dentro do próprio dispositivo, com o número de identificação do usuário no nome.

  Nome da pasta : .Trash-1000
                  O número 1000 é o UID do primeiro usuário criado na instalação

  Localização   : /media/user/DISK/
                  Fica na raiz do dispositivo externo, oculta (começa com ponto)

### O que acontece ao esvaziar?

Ao excluir da lixeira, o sistema operacional não apaga os dados fisicamente — ele apenas marca os inodes e o espaço em disco como disponíveis. Os bits continuam lá até serem sobrescritos por novos dados. É por isso que ferramentas de recuperação funcionam: enquanto o espaço não foi reutilizado, os dados podem ser recuperados.

### Verificar seu UID

Para confirmar seu número de usuário (e entender o nome da pasta):

```bash
id -u
```

 > 1000

Se o root apagar algo de um pendrive, a lixeira se chamará .Trash-0 — porque o UID do root é sempre 0.

***

Ficou bem sólido já. Dá pra lapidar alguns pontos, acrescentar uns detalhes que normalmente só aparecem quando algo quebra 😅 e deixar mais “pé no chão” pra quem vai realmente editar isso pela primeira vez.

Vou continuar no mesmo tom:

***

## - extra #3: `FSTAB`

`/etc/fstab` o mapa de montagem do seu sistema

### O que é?

O `/etc/fstab` (filesystem table) é uma tabela de configuração que o sistema lê durante o boot para saber:

* quais dispositivos montar
* onde montar
* com quais opções

Tecnicamente, quem executa a montagem hoje em dia costuma ser o systemd (via `systemd-mount`), não diretamente o kernel, mas a ideia continua a mesma.

Sem o fstab, você teria que rodar `mount` manualmente toda vez que ligasse o computador. Funciona, mas ninguém quer viver assim.

***

### Como ele se parece?

```plain
# <dispositivo>              <ponto>    <fs>     <opções>           <dump> <pass>

UUID=a1b2-c3d4              /          ext4     defaults           0      1
UUID=e5f6-g7h8              /boot/efi  vfat     umask=0077         0      2
UUID=i9j0-k1l2              /home      ext4     defaults           0      2
/dev/sdb1                   /dados     ntfs     uid=1000,gid=1000  0      0
tmpfs                       /tmp       tmpfs    size=2G,noexec     0      0
```

Pequeno detalhe que muita gente ignora: a ordem das linhas não é totalmente irrelevante, especialmente quando há dependências entre mounts.

***

### Os 6 campos de cada linha

**Campo 1 — dispositivo**
Pode ser:

* UUID
* LABEL
* `/dev/sdX`

UUID é o mais confiável. `/dev/sdb1` pode virar `/dev/sdc1` se você plugar outro disco antes.

***

**Campo 2 — ponto de montagem**
É onde o sistema “encaixa” o disco na árvore.

Exemplo:
se você monta em `/dados`, tudo daquele disco aparece ali.

E sim, a pasta precisa existir antes. Se não existir, o mount falha silenciosamente em alguns casos… e aí começa a confusão.

***

**Campo 3 — tipo de sistema de arquivos**

Alguns comuns:

* `ext4` → padrão Linux
* `btrfs` → snapshots, compressão
* `vfat` → EFI, pendrives
* `ntfs` → Windows
* `tmpfs` → memória RAM

***

**Campo 4 — opções**

Aqui mora o poder… e os bugs.

`defaults` já cobre bastante coisa, mas no mundo real você acaba usando combinações.

Um exemplo mais “realista” pra NTFS:

```plain
uid=1000,gid=1000,umask=022,nofail
```

Sem isso, você monta o disco e não consegue escrever nele como usuário normal. Clássico.

***

**Campo 5 — dump**

Quase morto hoje.
Deixa `0` e segue a vida.

***

**Campo 6 — pass (fsck)**

Define a ordem de verificação no boot:

* `1` → raiz (`/`)
* `2` → outras partições
* `0` → não verifica

Se você colocar errado aqui, o sistema pode demorar MUITO pra iniciar ou até travar esperando checagem.

***

### Como usar na prática

**1. Descubra o UUID**

```bash
lsblk -f
# ou
blkid /dev/sdb1
```

Dica meio prática: o `lsblk -f` é mais legível, o `blkid` é mais direto.

***

**2. Crie o ponto de montagem**

```bash
sudo mkdir -p /mnt/dados
```

***

**3. Edite com cuidado**

```bash
sudo cp /etc/fstab /etc/fstab.bak #backup!
sudo nano /etc/fstab # <= isso vai abrir o arquivo para você mecher
```

Sempre realize backups para evitar quebrar o boot...

exemplo do que colar para que monte um disco sozinho:

```plain
UUID=XXXXXXXXX   /mnt/dados   ext4   defaults,nofail   0   2
```

***

**4. Teste sem reiniciar**

```bash
sudo mount -a
```

Se der erro aqui, você ainda está seguro. Se ignorar isso e reiniciar… aí vira aventura.

***

### DICAS

Use `nofail` para:

* HD externo
* SSD secundário
* qualquer coisa não essencial

Sem isso, se o disco não estiver presente, o sistema pode parar no boot esperando algo que nunca vai aparecer.

***

### AVISO

Um `fstab` mal configurado pode:

* travar o boot
* cair em emergency mode
* ou simplesmente não montar nada e você nem perceber

Se acontecer, você pode corrigir via modo recovery ou live USB. Mas é melhor não chegar lá.

***

### Opções mais usadas

```plain
defaults            → rw, suid, dev, exec, auto, nouser, async
noauto              → não monta no boot
ro                  → somente leitura
nofail              → ignora erro se o dispositivo não existir
uid=1000            → define dono (útil pra ntfs/vfat)
noexec              → bloqueia execução (bom pra /tmp)
x-systemd.automount → monta só quando acessado
```

***

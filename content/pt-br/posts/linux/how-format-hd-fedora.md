
+++

title = 'Como formatar e montar automaticamente um HD no Fedora KDE"
summary = "Guia prático para formatar um HD e configurar montagem automática no Fedora KDE usando ext4 e fstab" 
tags = ["linux", "fedora", "kde plasma", "mount"] 
categories = ["linux"]
keywords = ["linux", "kde", "kde plasma", "fedora", "wayland"]

author = "Gabriel Maggioni" 
date = '2026-04-27T16:10:26-03:00'
#lastmod = '2026-04-25T15:15:26-03:00'
#publishDate = '2026-04-25T15:15:26-03:00'
#expiryDate = '2026-04-25T15:15:26-03:00'

showToc = true
TocOpen = false

draft = false

+++

## Introduçaõ - Eu estou insano?

Recentemente eu esbarrei num bug bem estranho no Fedora. Daqueles que fazem você duvidar da própria sanidade. Acabei formatando o sistema umas duas vezes até entender o que estava acontecendo 😅 (e sim, isso provavelmente não teria rolado se eu já soubesse usar snapshots direito… mas enfim, fica pra outro post).

Meu setup é simples: um SSD NVMe de 500GB com o sistema e um HD de 1TB pra backup e alguns jogos da Steam. Até aí, tudo normal. O comportamento estranho vinha do nada: com o HD desmontado, os jogos rodavam lisos, 100 FPS tranquilo. Mas era só montar o HD que o desempenho despencava pra coisa de 10 FPS. Detalhe importante, o jogo nem estava instalado no HD.

Depois de quebrar um pouco a cabeça, descobri que o problema estava no sistema de arquivos. O HD ainda estava em NTFS, padrão do Windows. Em teoria isso não deveria causar um impacto tão absurdo, mas na prática… causou. Só resolveu de verdade quando formatei o HD para ext4, que é o padrão do Linux.

Foi daí que surgiu a ideia desse post. Vou mostrar exatamente como formatar um HD no Linux e deixar ele montando automaticamente, sem dor de cabeça no dia a dia.

---

## O que iremos fazer

* apagar tudo do disco
* criar partição nova
* formatar em ext4
* montar automaticamente no sistema

---

## 1. Identificar o disco correto

Antes de qualquer coisa:

```bash
lsblk
```

Saída típica:

```
sda      931.5G
└─sda1
nvme0n1  465.8G
```

O sistema geralmente está no NVMe. O HD secundário costuma ser `sda`.

Confirma isso com calma. Esse passo evita desastre.

---

## 2. Desmontar o disco

Se ele estiver montado automaticamente:

```bash
sudo umount /dev/sda1
```

Se reclamar que está em uso:

```bash
sudo umount -l /dev/sda1
```

---

## 3. Apagar tudo (tabela de partição)

```bash
sudo parted /dev/sda mklabel gpt
```

Isso limpa completamente o disco.

---

## 4. Criar nova partição

```bash
sudo parted -a opt /dev/sda mkpart primary ext4 0% 100%
```

Agora você terá uma partição nova ocupando todo o HD.

---

## 5. Formatar em ext4

```bash
sudo mkfs.ext4 -L DADOS /dev/sda1
```

Aqui você pode definir um nome (label). Não é obrigatório, mas ajuda a identificar depois.

---

## 6. Montar manualmente para teste

```bash
sudo mkdir /mnt/hd
sudo mount /dev/sda1 /mnt/hd
```

Teste rápido:

```bash
touch /mnt/hd/teste.txt
```

Se der permissão negada:

```bash
sudo chown -R $USER:$USER /mnt/hd
```

---

## 7. Descobrir o UUID

```bash
lsblk -f
```

Você vai ver algo assim:

```
sda1 ext4 UUID=0ad14adb-dd8a-4c93-875b-bde24e4f3892
```

Guarde esse valor.

---

## 8. Montagem automática no boot

Edite o arquivo:

```bash
sudo nano /etc/fstab
```

Adicione no final:

```
UUID=SEU_UUID_AQUI /mnt/hd ext4 defaults,nofail 0 2
```

Exemplo real:

```
UUID=0ad14adb-dd8a-4c93-875b-bde24e4f3892 /mnt/hd ext4 defaults,nofail 0 2
```

---

## 9. Testar sem reiniciar

```bash
sudo mount -a
```

Se não aparecer erro, está funcionando.

---

## Sobre permissões

Por padrão, o Linux usa dono e grupo. Se quiser acesso total para seu usuário:

```bash
sudo chown -R $USER:$USER /mnt/hd
```

Se quiser algo compartilhado entre usuários, o ideal é usar grupos ao invés de liberar tudo.

---

## Resultado final

Depois disso:

* o HD monta sozinho ao ligar o sistema
* não pede senha
* funciona como parte nativa do Fedora
* pronto para Steam, backups ou projetos

---

Se algo não montar no boot, geralmente é erro de digitação no fstab. Vale revisar com calma.

---

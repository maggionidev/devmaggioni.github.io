+++

title = 'Como formatar seu PC + Deixando seu Windows menos ruim!'
description = "Seu PC merece um recomeço. Aprenda a formatar do zero e transformar seu Windows num sistema que presta"
summary = "Aprenda a como fazer uma instalação limpa do windows, usando pendrive bootável, e tirando todo o lixo da microsoft"
author = "Gabriel Maggioni"
date = '2026-04-23T20:29:00-03:00'

tags = ["windows", "boot", "formatação"]
categories = ["windows"]
keywords = ["windows", "pendrive", "pendrive bootável", "formatar"]

draft = false
showToc = true
TocOpen = false

+++

O Windows 11 é, definitivamente, um erro. Feio, inchado, cheio de lixo, propaganda, IA. Pelo menos enquanto escrevo esse post. Não sei se irão concertar isso no futuro.

Eu já estou com um pé no Linux, uso a certo tempo, mas o Windows sempre é bem vindo e familiar, e as vezes não queremos complicar muito, apenas usar nosso sistema sem preocupação.

Não se preocupe, não irei evangelizar a palavra do Linux nesse post (vou deixar pra fazer isso nos próximos).

Embora esse tutorial seja focado 100% no Windows, haverão próximos que estarão abordando o Linux.

Sem mais rodeios,
Vamos aprender a como formatar seu PC de forma ninja e fazer uma instalação seca seca seca do Windows 11.
(Inspiração pra esse post foi o {{< link href="https://www.youtube.com/watch?v=kQM-iv7TQz0" text="vídeo do ET">}})

---

## Requisitos

- Ter um PC (obrigatório)
- Ter um pendrive bootável (te ensinei nesse post => [aqui](/posts/windows/bootable-pendrive-windows))

---

## Capítulo 1: Autounattend

O arquivo `autounattend.xml` é um arquivo XML utilizado para automatizar e personalizar a instalação do Windows (como Windows 10 e 11) sem a necessidade de interação manual do usuário.  Ele permite definir configurações específicas, como idioma, partição de disco, scripts de pós-instalação, garantindo uma implantação consistente e rápida de sistemas operacionais. 

Iremos usar o autounattend para automatizar nossa instalação do windows;
Isso significa que você pode deixar o PC formatando e ir dormir, e acordar com ele já configurado. Fan-tás-ti-co.

Você nunca mais vai querer formatar o seu windows sem isso, sério.

---

Para gerar um `autounattend`, você pode usar esse site => {{< link href="https://schneegans.de/windows/unattend-generator/">}};

Eu não irei abordar todas as configs desse site. São muuuuitas. Seria melhor você mesmo dar uma explorada.

Eu vou disponibilizar o **meu autounattend** para você baixar, e você pode inclusive personalizar ele a seu gosto, inclusive.

{{< button 
  text="Baixar autounattend" 
  link="https://assets.maggioni.dev/posts/windows/how-to-format-your-pc-with-windows/autounattend.rar" 
  target="_blank"
  color="#16a34a" 
>}}

Pra personalizar o autounattend que você acabou de baixar, extraia o arquivo, acesse o site do schneegans, clique em `escolher arquivo` e em `importar`. Pronto, já estamos falando a mesma língua;

Vou fazer um resumo de todas as configs que fiz nesse autounattend, se você não gostou de alguma, basta mudar no site. Se tudo está ok para você, pode usar esse mesmo.

```
- Idioma: PT-BR
- Keyboard: ABNT2
- Arquitetura: 64-bit
- Coloquei para bypassar os requerimentos do Windows 11 (TPM, secure boot, etc)
- O particionamento de disco deixei manual. Gosto de fazer com atenção essa parte.
- partition layout GPT
- versão instalada do windows 11: Home
- vai ser criada duas contas offlines: Admin e User. As senhas são 0000
- menu de contexto clássico invés do novo
- Widgets desabilitados
- Não mostrar resultados do Bing no menu Iniciar
- Sem apps fixados no menu iniciar
- Windows Update sempre pausado - O windows update não inicia sozinho. Eu gosto disso, particulamente. Para atualizar basta ir na configuração e clicar no botão de atualizar.
- Desabilitar o Edge de ficar minerando o seu pc em segundo plano
- Bitlocker desabilitado (eu particulamente odeio me deu muita dor de cabeça pra recuperar pc bloqueado no bitlocker no passado)
- Desabilitar aceleração do mouse (isso aqui é horrivel! Melhor desabilitado)
- Apps inúteis são desinstalados automaticamente na primeira instalação. Só vem o básico: edge, ms store, camera, paint, powershell, galeria, calculadora, snipping tool, notepad, etc. Lixo como Copilot, Solitaire, One Drive, etc etc vem desinstalado;
```

Além dessas settings, eu criei alguns scripts que serão executados para deixar ainda mais automatizado:

- script que coloca wallpaper automaticamente
- script que muda foto de perfil automaticamente
- script que instala drivers automaticamente
- script que instala seus aplicativos favoritos automaticamente.

Irei ensinar adiante como usar;

Agora você precisa clicar em `exportar como XML`. Isso vai exportar o arquivo xml com suas settings, se você mecheu em algo, claro. Garanta que o arquivo de chame `autounattend.xml`. Não mude o nome, é exatamente assim;

Você precisa **colar esse arquivo na pasta raiz do seu pendrive bootável.**

---

## Capítulo 2: Preparação

Antes de formatar nosso pc, precisamos nos preparar;
Prepare uns 2 pendrive bootáveis. Nunca se sabe né? Melhor pecar pelo excesso. Assim você tem um pendrive backup se fizer algo errado. Mas é bem tranquilo, relaxe.

Primeiro, precisamos **fazer backup dos nossos drivers**. Bom, isso é opcional. Você pode querer instalar os drivers do 0, indo nos sites das marcas, procurando o software, instalado um por um... Isso não é errado, é até bom.
No entanto, eu estou ficando velho e impaciente. Não quero perder meu tempo. Eu costumo usar um script que faz backup de TODOS os meus drivers pra mim;

Você pode copiar esse código, colar e salvar como "backup-drivers.ps1":

{{< details summary="Clique para ver o script" >}}
```ps1
# ================================
# CONFIG
# ================================
$ErrorActionPreference = "Stop"

$scriptPath = Split-Path -Parent $MyInvocation.MyCommand.Path
$backupFolder = Join-Path $scriptPath "_drivers"
$logFile = Join-Path $scriptPath "backup-drivers.log"

# inicia log completo (captura TUDO, atÃ© erro bruto)
Start-Transcript -Path $logFile -Append

function Write-Log {
    param ($msg)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Write-Host "[$timestamp] $msg"
}

function Is-Admin {
    $currentUser = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())
    return $currentUser.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
}

try {
    Write-Log "===== INICIANDO BACKUP ====="

  # ADMIN
if (-not (Is-Admin)) {
    Write-Log "Não está como admin. Reabrindo com elevação..."

    Start-Process powershell `
        -ArgumentList "-NoProfile -ExecutionPolicy Bypass -File `"$PSCommandPath`"" `
        -Verb RunAs

    exit
}

    # PASTA
    if (!(Test-Path $backupFolder)) {
        New-Item -ItemType Directory -Path $backupFolder | Out-Null
        Write-Log "Pasta criada: $backupFolder"
    } else {
        Write-Log "Pasta já existe"
    }

    # DISM
    Write-Log "Executando DISM..."

    $process = Start-Process dism.exe `
        -ArgumentList "/online /export-driver /destination:`"$backupFolder`"" `
        -Wait -PassThru -NoNewWindow

    if ($process.ExitCode -ne 0) {
        throw "DISM falhou com cÃ³digo $($process.ExitCode)"
    }

    Write-Log "Backup concluí­do com sucesso!"

}
catch {
    Write-Log "ERRO FATAL:"
    Write-Log $_
}
finally {
    Write-Log "===== FIM ====="

    Stop-Transcript

    Write-Host ""
    Write-Host "Pressione ENTER para sair..."
    Read-Host
}

```
{{< /details >}}


Ok, após salvar como um arquivo `.ps1`, você precisa executar esse script no powershell. Ele vai pedir permissão de adm, conceda. Aguarde finalizar. Ele vai criar uma pasta chamada `_drivers/`, na mesma pasta onde você executou o script;

Pegue essa pasta (vai ser relativamente pesada, talvez 1GB) e faça um backup;

Também faça uma cópia dessa pasta pro seu pendrive bootável do windows 11, na raiz mesmo, **nada de mudar de nome nem colocar dentro de outras pastas!** Ela precisa ficar intacta!

Certo. Faça backup das outras coisas que você tem n oseu pc, sei lá, fotos, projetos, jogos, qualquer coisa. Pois tudo será apagado.

---

## Mais personalização

**Lembrando que essa etapa é opcional**

Nesse `autounattend` que eu disponibilizei pra download, ele tem alguns scripts muito úteis que eu criei. Um deles muda a foto de fundo da área de trabalho, e da tela de bloqueio, além da sua foto de perfil;

Isso é opcional claro, mas é muito legal!
Tudo oque você precisa fazer pra funcionar, é criar uma pasta chamada `_pics` na raiz do seu pendrive bootável. 
Dentro de `_pics`, você deve colocar três imagens (*as três precisam ser jpg!*): `homescreen.jpg` para ser seu plano de fundo principal, `lockscreen.jpg` para ser sua foto de bloqueio, e `perfil.jpg` para ser sua miniatura de perfil.

{{< asset file="windows/how-to-format-your-pc-with-windows/2026-04-23_21-30213014.jpeg" alt="Minhas fotos personalizadas" caption="Minhas fotos personalizadas" >}}

---

Podemos personalizar quais apps queremos instalar também;
Podemos combinar dois métodos: Ninite e instaladores próprios;

Para criar seu ninite, acesse {{< link href="https://ninite.com/">}}, marque todos os apps que você quer instalar no seu pc, e clique no botão em `Get Your Ninite`;

Isso vai baixar um instalador do Ninite, que vai instalar tudo oque você marcou;
Cole esse instalador na pasta raiz do seu pendrive bootável; Isso vai ser executado automaticamente no primeiro login;

Mas o ninite não instala tudo oque a gente quer. Talvez você tenha sua própria lista de apps favoritos, assim como eu:

{{< asset file="windows/how-to-format-your-pc-with-windows/2026-04-23_21-39213942.jpeg" alt="Meus apps que sempre uso" caption="Meus apps que sempre uso" >}}

Aqui eu coloco apps obrigatórios, como o amd adrenalin e o ryzen master para que eu não precise os baixar manualmente um por um depois. Muito útil.

Para isso, crie uma pasta chamada `_apps` na raiz do seu pendrive;
dentro da pasta `_apps` crie outra pasta chamada `instaladores`;
Ainda na pasta `_apps`, crie um script `auto-install.bat` com o seguinte código:

{{< details summary="Clique para ver o script" >}}
```bat
@echo off
setlocal enabledelayedexpansion

echo ===================================
echo Iniciando instalacoes sequenciais
echo ===================================
echo.

echo [1/1] Iniciando instalacao dos executaveis locais...
set "APP_DIR=%~dp0instaladores"

if not exist "%APP_DIR%\" (
    echo ERRO: Pasta "instaladores" nao encontrada em: "%APP_DIR%"
    goto :fim
)

for %%F in ("%APP_DIR%\*.exe") do (
    
    echo --------------------------------
    echo Instalando: %%~nxF
    echo Aguarde a conclusao...
    echo --------------------------------

    :: start /wait garante que espera terminar (mais seguro)
    start "" /wait "%%F"

    if !errorlevel! neq 0 (
        echo ❌ ERRO durante instalacao de %%~nxF
    ) else (
        echo ✅ Sucesso: %%~nxF
    )

    echo.
)

echo ===================================
echo Todas as instalacoes foram concluidas!
echo ===================================

:fim
pause
exit
```
{{</ details >}}

Esse script vai ser responsável por instalar todos os apps que estiverem dentro de `instaladores`;

Jogue dentro de `instaladores` todos os apps que você deseja instalar. (Obviamente você vai precisar procurar todos eles no google e ir baixando, e jogando na pasta)


 {{< warning 
  title="Esse script não vai executar sozinho! Após concluir a formatação, você vai navegar até essa pasta, e clicar com o botão direito no `.bat` para instalar esses apps" 
  text="Você pode alterar o autounattend pra executar esse código automaticamente, fica a seu critério. Se quiser fazer isso, vai precisar dar uma leve alterada, e colar nos scripts de ´firstLogon scripts´"
>}}

Vale a pena você rodar esse script agora, só pra testar se tá dando tudo certo, só ir fechando as janelas dos instaladores que irem aparecendo.

Estamos prontos pra formatar!
Certifique-se que fez tudo certo!

Seu pendrive bootável deve se parecer com isso:

{{< asset file="windows/how-to-format-your-pc-with-windows/2026-04-23_21-55215523.jpeg" >}}

---

## Capítulo 4: Enfim, a formatação!

Reinicie o computador e entre na BIOS. Na maioria das placas, é só pressionar uma tecla assim que a tela aparecer: **F2** nas placas ASUS, **Del**, **F10** ou **Esc** em outras marcas. Se não souber qual é a da sua, uma busca rápida no Google resolve.

Dentro da BIOS, o que você precisa fazer é mudar a **ordem de boot**, ou seja, dizer pro computador que ele deve iniciar pelo pendrive antes de qualquer coisa. Isso geralmente aparece como *Boot Priority*, *Boot Option #1* ou algo parecido, dependendo do firmware.

Coloque o pendrive (normalmente listado como *UEFI: nome_do_dispositivo*) na primeira posição, acima do seu SSD ou HD. Depois salve com **F10** e o computador vai reiniciar automaticamente.

Se tudo estiver certo, o instalador do Windows vai abrir sozinho.

---

**Atenção antes de continuar:** se o instalador começar pedindo idioma e layout de teclado, o `autounattend.xml` não foi carregado corretamente. Ele foi configurado pra pular essa parte. Se isso acontecer, aborte a instalação e verifique o arquivo antes de continuar.

Caso o processo seja automático mesmo, ótimo. Mas em algum momento pode aparecer uma tela perguntando o que você quer fazer. Se aparecer, escolha **Personalizada: Instalar apenas o Windows (avançado)**. É essa opção que te dá controle sobre o disco.

### Particionando o disco

Aqui é onde você precisa prestar atenção de verdade. Vai aparecer uma lista com todos os discos e partições do seu computador.

{{< warning 
  title="Cuidado pra não apagar o pendrive nem o disco errado!"
  text="Apague apenas as partições do disco onde o Windows vai ser instalado. Se nunca formatou um PC antes e tá inseguro, vale ver um vídeo rápido sobre como identificar e apagar partições corretamente antes de continuar."
>}}

Dois cenários possíveis:

1. **Instalação limpa total** — quer zerar o disco completamente? Selecione e exclua todas as partições do disco alvo uma por uma, até restar só **Espaço não alocado**.

2. **Tem mais de um disco?** — identifique qual é o certo pela capacidade e tipo. Não mexa nas partições dos outros discos.

Com o espaço não alocado selecionado, clique em **Avançar**. O instalador cria tudo que é necessário automaticamente:

* EFI System Partition (bootloader)
* MSR (Microsoft Reserved)
* Partição principal do Windows
* Partição de recuperação

Não precisa criar nada manualmente.

---

A partir daqui o `autounattend` assume o processo. Ele configura idioma, fuso horário, bloqueia coleta de dados da Microsoft e mais um monte de coisa sem você precisar fazer nada.

**SE APARECER, NÃO FECHE NENHUMA JANELA DO TERMINAL OU POWERSHELL! É O AUTOUNATTEND TRABALHANDO!**

Se o Windows começar a fazer perguntas da instalação, o arquivo não foi carregado. Nesse caso, refaça o pendrive, garanta que o `autounattend.xml` está na raiz e tente de novo.

---

### Se tudo deu certo...

Se a instalação rolou bem, sem você precisar confirmar nada...
parabéns, esse tutorial foi um sucesso!

provalvemente vai aparecer umas janelas de powershell, apenas minimize;

Se você criou a pasta `_apps`, não esquece de rodar o `.bat` para instalar todos eles de forma automática;

A partir daqui, não tem muito mais o que explicar, o computador é seu, e basta personálizá-lo a seu critério.

---
---
title: Proactieve GRUB-configuratie voor Azure Serial console | Microsoft Docs
description: Configureer GRUB in verschillende distributies, zodat toegang tot één gebruiker en herstel modus mogelijk is op virtuele machines van Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186927"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proactief garanderen dat u toegang hebt tot GRUB en SYSRQ u veel tijd bespaart

Als u toegang hebt tot de seriële console en GRUB, worden de herstel tijden van uw virtuele IaaS Linux-machine in de meeste gevallen verbeterd. GRUB biedt herstel opties die anders langer duren om uw virtuele machine te herstellen. 


De redenen om een VM-herstel uit te voeren, zijn veel en kunnen worden toegeschreven aan scenario's zoals:

   - Beschadigde bestands systemen/kernel/MBR (Master Boot Record)
   - Mislukte kernel-upgrades
   - Onjuiste GRUB-kernel-para meters
   - Onjuiste fstab-configuraties
   - Firewall configuraties
   - Verloren wacht woord
   - Vervormde sshd-configuratie bestanden
   - Netwerk configuraties

 Veel andere scenario's die [hier](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console) worden beschreven

Controleer of u toegang hebt tot GRUB en de Seriële console op uw Vm's die in azure zijn geïmplementeerd. 

Als u geen ervaring hebt met de seriële console, raadpleegt u [deze koppeling](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Zorg ervoor dat u back-ups van bestanden maakt voordat u wijzigingen aanbrengt

Bekijk deze video hieronder om te zien hoe u uw Linux-VM snel kunt herstellen wanneer u toegang hebt tot GRUB

[Video van Linux VM herstellen](https://youtu.be/KevOc3d_SG4)

Er zijn een aantal methoden om de virtuele Linux-machines te herstellen. In een cloud omgeving is dit proces lastig.
De voortgang wordt voortdurend doorgevoerd in hulp middelen en functies om ervoor te zorgen dat de services snel worden hersteld.

Met de seriële console van Azure kunt u met uw virtuele Linux-machine communiceren alsof u zich op de console van een systeem bevindt.

U kunt veel configuratie bestanden bewerken, inclusief de manier waarop de kernel opstart. 

De meer ervaren Linux/Unix sys-beheerders stellen de **afzonderlijke gebruikers** -en **nood herstel modi** die toegankelijk zijn via de Azure seriële console, in de tussen komst van het wisselen van schijven en het verwijderen van virtuele machines voor een groot aantal Recovery scenario's.

De herstel methode is afhankelijk van het probleem dat zich voordoet, bijvoorbeeld wanneer een verloren of verkeerd geplaatst wacht woord opnieuw kan worden ingesteld via Azure Portal opties-> **wacht woord opnieuw instellen**. De functie **wacht woord opnieuw instellen** wordt een uitbrei ding genoemd en communiceert met de Linux-gast agent.

Andere uitbrei dingen, zoals een aangepast script, zijn beschikbaar. deze opties vereisen echter dat de Linux- **waagent** in een goede staat zijn en niet altijd het geval is.

![agent status](./media/virtual-machines-serial-console/agent-status.png)


Zorg ervoor dat u toegang hebt tot de seriële Azure-console en GRUB betekent dat het wijzigen van een wacht woord of een onjuiste configuratie binnen enkele minuten in plaats van uren kan worden verholpen. U kunt zelfs voor komen dat de virtuele machine wordt opgestart vanuit een andere kernel als u meerdere kernels op schijf hebt in het scenario waarin uw primaire kernel beschadigd raakt.

![meerdere kernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Voorgestelde volg orde van herstel methoden:

- Azure Serial Console

- Schijf swap: kan worden geautomatiseerd met behulp van:

   - [Power shell-herstel scripts](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash-herstel scripts](https://github.com/sribs/azure-support-scripts)

- Verouderde methode

## <a name="disk-swap-video"></a>Video over het wisselen van schijven:

Als u geen toegang hebt tot GRUB [deze](https://youtu.be/m5t0GZ5oGAc) video te bekijken en te zien, kunt u eenvoudig de schijf swap-procedure automatiseren om uw VM te herstellen

## <a name="challenges"></a>Geconfronteerd

Niet alle virtuele Linux-machines zijn standaard geconfigureerd voor GRUB-toegang en geen van beide zijn geconfigureerd om te worden onderbroken met de SYSRQ-opdrachten. Sommige oudere distributies, zoals SLES 11, zijn niet geconfigureerd voor het weer geven van de aanmeldings prompt in de Azure-seriële console

In dit artikel gaan we verschillende Linux-distributies en document configuraties bekijken om GRUB beschikbaar te maken.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Linux-VM configureren voor het accepteren van SysRq-sleutels
De SYSRQ-sleutel is standaard ingeschakeld op sommige nieuwere Linux-distributies, hoewel deze mogelijk worden geconfigureerd voor het accepteren van waarden voor bepaalde SysRq-functies.
Op oudere distributies is het mogelijk volledig uitgeschakeld.

De functie SysRq is handig voor het opnieuw opstarten van een vastgelopen of vastgelopen virtuele machine rechtstreeks vanuit de Azure-seriële console, ook handig voor het verkrijgen van toegang tot het menu GRUB, het opnieuw starten van een VM vanuit een ander portal venster of met een SSH-sessie mogelijk de huidige console verbinding te verwijderen verlopen GRUB-time-outs waarmee het menu GRUB wordt weer gegeven.
De virtuele machine moet worden geconfigureerd om een waarde van 1 te accepteren voor de para meter kernel, waarmee alle functies van SYSRQ of 128 kunnen worden gestart/uitgeschakeld.


[SYSRQ-video inschakelen](https://youtu.be/0doqFRrHz_Mc)


Als u de virtuele machine wilt configureren voor het accepteren van een herstart via SysRq-opdrachten op de Azure Portal, moet u een waarde van 1 instellen voor de kernel-para meter kernel. SYSRQ

Als u deze configuratie wilt behouden, moet u een vermelding toevoegen aan het bestand **sysctl. conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

De kernel-para meter dynamisch configureren

`sysctl -w kernel.sysrq=1`

Als u geen toegang hebt tot de **hoofdmap** of sudo is verbroken, is het niet mogelijk om SYSRQ te configureren vanaf een shell-prompt.

U kunt SYSRQ in dit scenario inschakelen met behulp van de Azure Portal. Deze methode kan nuttig zijn als het bestand **sudo. d/waagent** beschadigd is of is verwijderd.

Met de Azure Portal bewerkingen-> uitvoeren opdracht > RunShellScript-functie moet het waagent-proces in orde zijn, kunt u deze opdracht vervolgens injecteren om SYSRQ in te scha kelen

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Zoals hier wordt weer gegeven: ![inschakelen sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Zodra het is voltooid, kunt u proberen om toegang te krijgen tot **SYSRQ** en ziet u dat de computer mogelijk opnieuw moet worden opgestart.

![sysrq3 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

De opdracht **opnieuw opstarten** en **SYSRQ verzenden** selecteren

![sysrq4 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Het systeem moet een opnieuw ingesteld bericht registreren, zoals dit

![sysrq5 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configuratie van Ubuntu GRUB

U hebt standaard toegang tot GRUB door de **ESC** -toets ingedrukt te houden tijdens het opstarten van de virtuele machine. als het menu grub niet wordt weer gegeven, kunt u het grub-menu op het scherm in de Azure seriële console afdwingen en houden door een van deze opties te gebruiken.

**Optie 1** : GEFORCEERD dat grub wordt weer gegeven op het scherm 

Werk het bestand/etc/default/grub.d/50-cloudimg-Settings.cfg bij om het menu GRUB op het scherm te laten staan voor de opgegeven time-out.
U hoeft niet op **ESC** te drukken omdat grub onmiddellijk wordt weer gegeven

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Optie 2** : Hiermee staat u toe dat **ESC** wordt ingedrukt voordat de computer wordt opgestart

Hetzelfde gedrag kan worden ervaren door wijzigingen aan te brengen in het bestand/etc/default/grub en een time-out van drie seconden te zien om op **ESC te drukken**


Opmerkingen bij deze twee regels:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
en voeg deze regel toe:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Met Ubuntu 12,04 krijgt u toegang tot de seriële console, maar biedt de mogelijkheid om niet te communiceren. Een **aanmelding:** prompt wordt niet weer gegeven


Voor 12,04 om een aanmelding te verkrijgen **:** prompt:
1. Maak een bestand met de naam/etc/init/ttyS0.conf dat de volgende tekst bevat:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. U kunt het beste beginnen met de Getty     
    ```
    sudo start ttyS0
    ```
 
De instellingen die nodig zijn voor het configureren van de seriële console voor Ubuntu-versies, vindt u [hier](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Ubuntu-herstel modus

Aanvullende herstel-en opschoon opties zijn beschikbaar voor Ubuntu via GRUB. deze instellingen zijn echter alleen toegankelijk als u kernel-para meters dienovereenkomstig configureert.
Als u deze kernel boot para meter niet configureert, wordt het herstel menu geforceerd verzonden naar de Azure Diagnostics en niet naar de seriële Azure-console.
U kunt toegang krijgen tot het Ubuntu-herstel menu door de volgende stappen uit te voeren:

Het GRUB-menu voor opstart processen en toegang onderbreken

Selecteer Geavanceerde opties voor Ubuntu en druk op ENTER

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selecteer de regel die wordt weer gegeven *(herstel modus)* en druk op ENTER. Druk op ' e '

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Zoek de regel die de kernel laadt en vervang de laatste para meter in de **modus** met bestemming als **console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Druk op **CTRL-x** om de kernel te starten en te laden.
Als alles goed gaat, worden deze extra opties weer geven die u kunnen helpen andere herstel opties uit te voeren

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configuratie van Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB-configuratie
De standaard/etc/default/grub-configuratie op deze versies is voldoende geconfigureerd

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

De SysRq-sleutel inschakelen

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 en 7\.3 GRUB-configuratie
Het bestand dat moet worden gewijzigd, is/etc/default/grub: een standaard configuratie ziet er als volgt uit:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Wijzig de volgende regels in/etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Voeg deze regel ook toe:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub moet er nu ongeveer als volgt uitzien:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Volt ooien en de grub-configuratie bijwerken met

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Stel de SysRq-kernel-para meter in:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

U kunt GRUB en SysRq ook configureren met behulp van één regel in de shell of via de opdracht uitvoeren. Back-up van uw bestanden maken voordat u deze opdracht uitvoert:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB-configuratie
Het bestand dat moet worden gewijzigd, is/boot/grub/grub.conf. Met de `timeout` waarde wordt bepaald hoe lang GRUB wordt weer gegeven.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


De laatste regel *Terminal –-timeout = 5 seriële console* verhoogt de **grub** -time-out door een prompt van vijf seconden toe te voegen, **Druk op een toets om door te gaan.**

![RH6-1](./media/virtual-machines-serial-console/rh6-1.png)

Het menu GRUB moet op het scherm worden weer gegeven voor de geconfigureerde time-out = 15 zonder te hoeven drukken op ESC. Zorg ervoor dat u in de browser op de console klikt om het menu actief te maken en de vereiste kernel te selecteren

![RH6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Gebruik de YaST-bootloader volgens de officiële [docs](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Of Voeg/Wijzig toe aan/etc/default/grub de volgende para meters:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Controleer of ttyS0 wordt gebruikt in de GRUB_CMDLINE_LINUX of GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

De grub. cfg opnieuw maken

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
De seriële console wordt weer gegeven en er worden opstart berichten weer gegeven, maar er wordt geen aanmelding weer gegeven **:** prompt

Open een SSH-sessie in de virtuele machine en werk de bestands **/etc/inittab** bij door deze regel op te heffen:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Voer de volgende opdracht uit 

`telinit q`

Om GRUB in te scha kelen, moeten de volgende wijzigingen worden aangebracht in/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Met deze configuratie wordt het bericht **op een wille keurige toets geklikt om** gedurende vijf seconden op de console te worden weer gegeven 

Vervolgens wordt het GRUB-menu voor vijf seconden weer gegeven: als u op de pijl-omlaag drukt, wordt het item onderbroken en wordt een kernel geselecteerd die u wilt opstarten. Voeg het sleutel woord **Single** toe voor de modus voor één gebruiker waarvoor een basis wachtwoord moet worden ingesteld.

Het toevoegen van de opdracht **init =/bin/bash** laadt de kernel, maar zorgt ervoor dat het init-programma wordt vervangen door een bash-shell.

U krijgt toegang tot een shell zonder dat u een wacht woord hoeft in te voeren. U kunt vervolgens door gaan met het bijwerken van het wacht woord voor Linux-accounts of andere configuratie wijzigingen aanbrengen.


## <a name="force-the-kernel-to-a-bash-prompt"></a>De kernel forceren naar een bash-prompt
Als u toegang hebt tot GRUB, kunt u het initialisatie proces onderbreken. deze interactie is handig voor veel herstel procedures.
Als u geen Hoofdwacht woord hebt en voor één gebruiker een Hoofdwacht woord vereist is, kunt u de kernel opstarten met het programma init vervangen door een bash prompt. deze interrupt kan worden bereikt door init =/bin/bash toe te voegen aan de kernel-opstart regel

![bash1](./media/virtual-machines-serial-console/bash1.png)

Uw/(root) bestandssysteem-RW opnieuw koppelen met behulp van de opdracht

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


U kunt nu hoofdwachtwoord wijzigingen of veel andere configuratie wijzigingen voor Linux uitvoeren

![bash3](./media/virtual-machines-serial-console/bash3.png)

Start de VM opnieuw met 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modus voor één gebruiker

U moet mogelijk ook toegang hebben tot de virtuele machine in één gebruiker of in de nood herstel modus. Selecteer de kernel die u wilt opstarten of onderbreken met behulp van de pijl toetsen.
Voer de gewenste modus in door het sleutel woord **Single** of **1** toe te voegen aan de kernel-opstart regel. Op RHEL-systemen kunt u ook **rd. Unlock**toevoegen.

Zie [dit document](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) voor meer informatie over het openen van de modus voor één gebruiker. 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure Serial console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
---
title: Proactieve GRUB-configuratie van Azure Serial Console| Microsoft Documenten
description: Configureer GRUB voor verschillende distributies die toegang tot één gebruiker en herstelmodus mogelijk maken in virtuele Azure-machines.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262890"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proactief zorgen dat u toegang hebt tot GRUB en sysrq kan u veel down time besparen

Toegang hebben tot de Serial Console en GRUB zal in de meeste gevallen de hersteltijden van uw IaaS Linux Virtual Machine verbeteren. GRUB biedt herstelopties die anders langer zouden duren om uw VM te herstellen. 


De redenen om een VM-herstel uit te voeren zijn talrijk en kunnen worden toegeschreven aan scenario's zoals:

   - Corrupte bestandssystemen/kernel/MBR (Master Boot Record)
   - Mislukte kernelupgrades
   - Onjuiste GRUB-kernelparameters
   - Onjuiste fstab-configuraties
   - Firewallconfiguraties
   - Wachtwoord verloren
   - Verminkte sshd-configuraties
   - Netwerkconfiguraties

 Veel andere scenario's zoals [hier](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console) beschreven

Controleer of u toegang hebt tot GRUB en de seriële console op uw VM's die in Azure zijn geïmplementeerd. 

Als u nieuw bent bij Serial Console, raadpleegt u [deze link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Zorg ervoor dat u back-ups van bestanden maakt voordat u wijzigingen aanbrengt

Bekijk deze video hieronder om te zien hoe u uw Linux VM snel herstellen zodra u toegang hebt tot GRUB

[Linux VM-video herstellen](https://youtu.be/KevOc3d_SG4)

Er zijn een aantal methoden om te helpen bij het herstel van Linux VM's. In een cloudomgeving is dit proces een uitdaging geweest.
Er wordt voortdurend vooruitgang geboekt met het toolen en functies om ervoor te zorgen dat services snel worden hersteld.

Met de Azure Serial Console u communiceren met uw Linux-vm alsof u zich op de console van een systeem bevindt.

U veel configuratiebestanden manipuleren, waaronder hoe de kernel wordt opgestart. 

De meer ervaren Linux / Unix sys admins zullen waarderen de **single user** en **noodmodi** die toegankelijk zijn via de Azure Serial Console waardoor Disk Swap en VM verwijdering voor vele herstelscenario's overbodig.

De methode van herstel is afhankelijk van het probleem dat wordt ervaren, bijvoorbeeld een verloren of misplaatst wachtwoord kan worden gereset via Azure portal opties -> **Reset Password**. De **functie Wachtwoord opnieuw instellen** staat bekend als een extensie en communiceert met de Linux Guest-agent.

Andere extensies zoals Custom Script zijn beschikbaar, maar deze opties vereisen dat de Linux **waagent** worden en in een gezonde staat die niet altijd het geval is.

![agentstatus](./media/virtual-machines-serial-console/agent-status.png)


Als u toegang hebt tot de Azure Serial Console en GRUB, u een wachtwoordwijziging of een onjuiste configuratie in een kwestie van minuten in plaats van uren rechtzetten. U de VM zelfs dwingen om op te starten vanuit een alternatieve kernel als u meerdere kernels op schijf hebt in het scenario waarin uw primaire kernel beschadigd raakt.

![multikernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Voorgestelde volgorde van herstelmethoden:

- Azure Serial Console

- Disk Swap – kan worden geautomatiseerd met behulp van:

   - [Power Shell-herstelscripts](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash Herstelscripts](https://github.com/sribs/azure-support-scripts)

- Verouderde methode

## <a name="disk-swap-video"></a>Disk Swap Video:

Als u geen toegang hebt tot GRUB bekijk [deze](https://youtu.be/m5t0GZ5oGAc) video en zie, hoe u gemakkelijk automatiseren van de disk swap procedure om uw VM te herstellen

## <a name="challenges"></a>Uitdagingen:

Niet alle Linux Azure VM's zijn standaard geconfigureerd voor GRUB-toegang en ze zijn ook niet allemaal geconfigureerd om te worden onderbroken met de sysrq-opdrachten. Sommige oudere distro's zoals SLES 11 zijn niet geconfigureerd om aanmeldingsprompt weer te geven in de Azure Serial Console

In dit artikel bekijken we verschillende Linux-distributies en documentconfiguraties over hoe GRUB beschikbaar te maken.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Linux VM configureren om SysRq-sleutels te accepteren
De sysrq-toets is standaard ingeschakeld op sommige nieuwere Linux-distro's, hoewel deze op andere apparaten alleen kan worden geconfigureerd voor het accepteren van waarden voor bepaalde SysRq-functies.
Op oudere distro's, kan het volledig worden uitgeschakeld.

De SysRq-functie is handig voor het opnieuw opstarten van een gecrashte of opgehangen VM rechtstreeks van de Azure Serial Console, ook handig bij het verkrijgen van toegang tot het GRUB-menu, als alternatief het opnieuw opstarten van een VM vanuit een ander portalvenster of ssh-sessie kan uw huidige consoleverbinding laten vallen, waardoor GRUB Timeouts verlopen die worden gebruikt om het GRUB-menu weer te geven.
De VM moet zijn geconfigureerd om een waarde van 1 voor de kernelparameter te accepteren, waardoor alle functies van sysrq of 128 kunnen worden geactiveerd, waardoor opnieuw opstarten/uitschakelen mogelijk is


[Sysrq-video inschakelen](https://youtu.be/0doqFRrHz_Mc)


Als u de VM wilt configureren om een reboot via SysRq-opdrachten op de Azure-portal te accepteren, moet u een waarde van 1 instellen voor de kernelparameter kernel.sysrq

Als u deze configuratie wilt aanhouden als u een reboot wilt starten, voegt u een vermelding toe aan het bestand **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

De kernelparameter dynamisch configureren

`sysctl -w kernel.sysrq=1`

Als u geen **root-toegang** hebt of sudo is verbroken, is sysrq niet mogelijk configureren vanuit een shell prompt.

U sysrq in dit scenario inschakelen met behulp van de Azure-portal. Deze methode kan nuttig zijn als de **sudoers.d / waagent** bestand is gebroken of is verwijderd.

Met behulp van de Azure portal Operations -> Run Command -> RunShellScript-functie, moet het waagent-proces in orde zijn, waarna u deze opdracht injecteren om sysrq in te schakelen

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Zoals hier ![getoond: sysrq2 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Eenmaal voltooid, u vervolgens proberen toegang te krijgen tot **sysrq** en moet u zien dat een reboot mogelijk is.

![sysrq3 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selecteer **SysRq** **opnieuw opstarten** en verzenden

![sysrq4 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Het systeem moet een resetbericht zoals dit

![sysrq5 inschakelen](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB-configuratie

Standaard moet u toegang hebben tot GRUB door de **Esc-toets** ingedrukt te houden tijdens de VM-opstart, als het GRUB-menu niet wordt gepresenteerd, u het GRUB-menu in de Azure Serial Console op het scherm forceren en behouden met behulp van een van deze opties.

**Optie 1** - Dwingt GRUB om op het scherm te worden weergegeven 

Werk het bestand /etc/default/grub.d/50-cloudimg-settings.cfg bij om het GRUB-menu op het scherm te houden voor de opgegeven TIMEOUT.
U bent niet verplicht om **Esc** te raken als GRUB zal onmiddellijk worden weergegeven

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Optie 2** - Hiermee kan **Esc** worden ingedrukt voordat u opstart

Vergelijkbaar gedrag kan worden ervaren door het aanbrengen van wijzigingen in het bestand / etc / standaard / grub en observeren een 3-seconden time-out te raken **Esc**


Reageer uit deze twee lijnen:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
en voeg deze regel toe:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 geeft toegang tot seriële console, maar biedt niet de mogelijkheid om te communiceren. Een **login:** prompt wordt niet gezien


Voor 12.04 om een login te **verkrijgen:** prompt:
1. Maak een bestand met de naam /etc/init/ttyS0.conf met de volgende tekst:

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

2. Vraag upstart om de getty te starten     
    ```
    sudo start ttyS0
    ```
 
De instellingen die nodig zijn om seriële console voor Ubuntu-versies te configureren, zijn [hier](https://help.ubuntu.com/community/SerialConsoleHowto) te vinden

## <a name="ubuntu-recovery-mode"></a>Ubuntu Herstelmodus

Extra herstel- en opruimopties zijn beschikbaar voor Ubuntu via GRUB, maar deze instellingen zijn alleen toegankelijk als u kernelparameters dienovereenkomstig configureert.
Als u deze parameter kernelboot niet configureert, moet het menu Herstel worden verzonden naar de Azure Diagnostics en niet naar de Azure Serial Console.
U toegang krijgen tot het Ubuntu Recovery Menu door de volgende stappen te volgen:

Het BOOT-proces onderbreken en het grub-menu openen

Selecteer Geavanceerde opties voor Ubuntu en druk op enter

![ubunturec1 ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selecteer de lijn die wordt weergegeven *(herstelmodus)* druk niet op enter, maar druk op "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Zoek de lijn die de kernel laadt en vervang de laatste parameter **nomodeset** door bestemming als **console=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Druk op **Ctrl-x** om de kernel te starten en te laden.
Als alles goed gaat ziet u deze extra opties, die kunnen helpen bij het uitvoeren van andere herstelopties

![ubunturec4 ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB-configuratie

## <a name="red-hat-74-grub-configuration"></a>Red Hat\.\+ 7 4 GRUB configuratie
De standaard /etc/default/grub configuratie op deze versies is voldoende geconfigureerd

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

De SysRq-toets inschakelen

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat\.7\.2 en 7 3 GRUB configuratie
Het bestand te wijzigen is / etc / standaard / grub - een standaard config ziet eruit als volgt uit dit voorbeeld:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

De volgende regels wijzigen in /etc/default/grub

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

Voeg ook deze regel toe:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub moet er nu uitzien op dit voorbeeld:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
De grubconfiguratie voltooien en bijwerken met behulp van

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Stel de parameter SysRq-kernel in:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

U GRUB en SysRq ook configureren met één regel in de shell of via de Run Command. Maak een back-up van uw bestanden voordat u deze opdracht uitvoert:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat\.6 x GRUB configuratie
Het bestand te wijzigen is / boot / grub / grub.conf. De `timeout` waarde bepaalt hoe lang GRUB wordt weergegeven.

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


De laatste lijn *terminal --timeout=5 seriële console* zal grub **time-out** verder verhogen door het toevoegen van een prompt van 5 seconden weergeven Druk op een toets om door te **gaan.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Grub menu moet op het scherm verschijnen voor de geconfigureerde time-out=15 zonder dat u op Esc hoeft te drukken. Klik in de console in de browser om actief te worden in het menu en selecteer de vereiste kernel

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Ofwel gebruik yast bootloader volgens de officiële [documenten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Of voeg/verander de volgende parameters toe aan /etc/default/grub:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Controleer of ttys0 wordt gebruikt in de GRUB_CMDLINE_LINUX of GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Recreëren de grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
De seriële console wordt weergegeven en geeft opstartberichten weer, maar geeft geen **aanmelding weer:** prompt

Open een ssh-sessie in de VM en werk het bestand **/etc/inittab bij** door deze regel niet te ontzeggen:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Volgende uitvoeren van de opdracht 

`telinit q`

Om GRUB in te schakelen, moeten de volgende wijzigingen worden aangebracht in /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Met deze configuratie kan het bericht **op elke toets** worden ingedrukt om gedurende 5 seconden op de console te blijven verschijnen 

Het zal dan het menu GRUB voor een extra 5 seconden - door op de pijl-omlaag te drukken u de teller zal onderbreken en selecteer een kernel die u wilt opstarten, hetzij het trefwoord **enkele** voor single gebruiker modus die root wachtwoord vereist worden ingesteld.

Als u de opdracht **init=/bin/bash toebrengt,** wordt de kernel geladen, maar zorgt ervoor dat het init-programma wordt vervangen door een bashshell.

U krijgt toegang tot een shell zonder een wachtwoord in te voeren. U vervolgens overgaan tot het bijwerken van het wachtwoord voor Linux-accounts of andere configuratiewijzigingen aanbrengen.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forceer de kernel tot een bash prompt
Met toegang tot GRUB u het initialisatieproces onderbreken, deze interactie is nuttig voor veel herstelprocedures.
Als u geen hoofdwachtwoord hebt en één gebruiker vereist dat u een hoofdwachtwoord hebt, u de kernel opstarten die het init-programma vervangt door een bashprompt - deze onderbreking kan worden bereikt door init=/bin/bash toe te nemen aan de kernel boot-lijn

![bash1 bash1](./media/virtual-machines-serial-console/bash1.png)

Uw /(root)bestandssysteem RW opnieuw monteren met de opdracht

`mount -o remount,rw /`

![bash2 bash2](./media/virtual-machines-serial-console/bash2.png)


Nu u root wachtwoord wijzigen of vele andere Linux configuratie wijzigingen uit te voeren

![bash3 bash3](./media/virtual-machines-serial-console/bash3.png)

Start de VM opnieuw op met 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modus Voor één gebruiker

U ook toegang krijgen tot de VM in de modus voor één gebruiker of in noodgevallen. Selecteer de kernel die u wilt opstarten of onderbreken met pijltoetsen.
Voer de gewenste modus in door het trefwoord **single** of **1** toe te koppelen aan de kernel bootlijn. Op RHEL-systemen u ook **rd.break toevoegen.**

Zie [dit document](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) voor meer informatie over het openen van de modus voor één gebruiker 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Volgende stappen
Meer informatie over [Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

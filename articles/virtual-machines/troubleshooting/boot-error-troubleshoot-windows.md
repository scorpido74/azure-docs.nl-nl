---
title: Het afsluiten van Azure Virtual Machines is vastgelopen bij het opnieuw starten, afsluiten of stoppen van services | Microsoft Docs
description: Dit artikel helpt u bij het oplossen van problemen met service fouten in azure Windows Virtual Machines.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: f7e2b70b111cd195f688e236bf8f05b077acb000
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678763"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Het afsluiten van Azure Windows VM is vastgelopen op het opnieuw starten, afsluiten of stoppen van services

In dit artikel worden de stappen beschreven voor het oplossen van de problemen met het opnieuw starten, afsluiten of stoppen van services die kunnen optreden wanneer u een virtuele Windows-machine (VM) opnieuw opstart in Microsoft Azure.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u mogelijk dat de scherm opname het bericht ' opnieuw opstarten ', ' afsluiten ' of ' Services stoppen ' weergeeft.

![Schermen van services opnieuw starten, afsluiten en stoppen](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Oorzaak

Windows gebruikt het afsluit proces om systeem onderhouds bewerkingen uit te voeren en wijzigingen, zoals updates, functies en onderdelen, te verwerken. Het wordt niet aanbevolen dit kritieke proces te onderbreken totdat het is voltooid. Afhankelijk van het aantal updates/wijzigingen en de grootte van de virtuele machine kan het proces enige tijd in beslag nemen. Als het proces wordt gestopt, is het mogelijk dat het besturings systeem beschadigd raakt. Onderbreek alleen het proces als het erg lang duurt.

## <a name="solution"></a>Oplossing

### <a name="collect-a-process-memory-dump"></a>Een geheugen dump van het proces verzamelen

1. Down load het [Procdump-hulp programma](http://download.sysinternals.com/files/Procdump.zip) naar een nieuwe of bestaande gegevens schijf die is gekoppeld aan een werkende VM uit dezelfde regio.

2. Ontkoppel de schijf met de benodigde bestanden van de werkende VM en koppel de schijf aan de beschadigde virtuele machine. De schijf van het **hulp programma**wordt aangeroepen.

Gebruik de [seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) om de volgende stappen uit te voeren:

1. Open een beheer-Power shell en controleer de service die niet meer reageert bij het stoppen.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Haal de PID van de niet-reagerende service op in een beheer opdracht.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Een voor beeld van een geheugen dump ophalen van het proces dat niet reageert <STOPPING SERVICE> .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Beëindig nu het niet-reagerende proces om het afsluit proces te ontgrendelen.

   ``
   taskkill /PID <PID> /t /f
   ``

Als het besturings systeem opnieuw wordt opgestart, moet u er zeker van zijn dat de besturingssysteem consistentie OK is. Als beschadiging wordt gerapporteerd, voert u de volgende opdracht uit totdat de schijf vrij is:

``
dism /online /cleanup-image /restorehealth
``

Als u geen dump van het proces geheugen kunt verzamelen, of dit probleem recursief is en u een analyse van de hoofd oorzaak hebt, kunt u door gaan met het verzamelen van een OS-geheugen dump hieronder, de door Voer om een ondersteunings aanvraag te openen.

### <a name="collect-an-os-memory-dump"></a>Een dump van het besturings systeem verzamelen

Als het probleem niet wordt opgelost nadat u hebt gewacht tot de wijzigingen zijn verwerkt, moet u een geheugen dump bestand verzamelen en contact opnemen met de ondersteuning. Voer de volgende stappen uit om het dump bestand te verzamelen:

**De besturingssysteem schijf koppelen aan een herstel-VM**

1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)(Engelstalig) voor meer informatie.

2. [Koppel de besturingssysteem schijf aan een herstel-VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Extern bureau blad naar de herstel-VM.

4. Als de besturingssysteem schijf is versleuteld, moet u de versleuteling uitschakelen voordat u verdergaat met de volgende stap. Zie [de versleutelde besturingssysteem schijf ontsleutelen in de virtuele machine die niet kan worden opgestart](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)voor meer informatie.

**Dump bestand zoeken en een ondersteunings ticket verzenden**

1. Ga op de herstel-VM naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf F is, moet u naar F:\Windows.

2. Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het dump bestand.

Als u het dump bestand niet kunt vinden, gaat u naar de volgende stap om dump logboek en seriële console in te scha kelen.

**Dump logboek en seriële console inschakelen**

Voer het volgende script uit om dump logboek en seriële console in te scha kelen.

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (als administrator uitvoeren).

2. Voer het volgende script uit:

   In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf F. Vervang deze door de juiste waarde in uw VM.

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
   
   REM Enable Serial Console
   bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
   bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
   bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
   bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
   bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   
   REM Suggested configuration to enable OS Dump
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Controleer of er voldoende ruimte op de schijf is om zoveel geheugen toe te wijzen als het RAM-geheugen, afhankelijk van de grootte die u voor deze VM selecteert.

4. Als er onvoldoende ruimte is of de virtuele machine groot is (G, GS of E-serie), kunt u de locatie wijzigen waar dit bestand wordt gemaakt en naar elke andere gegevens schijf verwijzen die aan de VM is gekoppeld. Als u de locatie wilt wijzigen, moet u de volgende sleutel wijzigen:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Ontkoppel de besturingssysteem schijf en koppel de besturingssysteem schijf opnieuw aan de betreffende VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Start de VM en open de seriële console.

7. Selecteer niet-maskeer bare interrupt (NMI) verzenden om de geheugen dump te activeren.

   ![Niet-maskeer bare interrupt verzenden](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Koppel de besturingssysteem schijf weer aan een herstel-VM en probeer het dump bestand te verzamelen.

## <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Nadat u het dump bestand hebt verzameld, neemt u contact op met micro soft ondersteuning om de hoofd oorzaak te bepalen.

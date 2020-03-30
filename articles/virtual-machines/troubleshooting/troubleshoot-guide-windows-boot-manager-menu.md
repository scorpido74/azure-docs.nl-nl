---
title: Windows virtuele machine kan niet opstarten als gevolg van Windows Boot Manager
description: In dit artikel worden stappen gezet om problemen op te lossen waarbij Windows Boot Manager het opstarten van een Azure Virtual Machine voorkomt.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373347"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows VM kan niet worden opgestart vanwege Windows Bootbeheer

In dit artikel worden stappen gezet om problemen op te lossen waarbij Windows Boot Manager het opstarten van een Azure Virtual Machine (VM) voorkomt.

## <a name="symptom"></a>Symptoom

De VM zit vast te wachten op een gebruiker prompt en niet opstarten, tenzij handmatig geïnstrueerd.

Wanneer u [Opstartdiagnose](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de schermafbeelding van de VM weer te geven, ziet u dat in de schermafbeelding de Schermafbeelding windows opstartbeheer wordt weergegeven met het bericht *Kies een besturingssysteem om te starten of druk u op TAB om een gereedschap te selecteren:*.

Afbeelding 1
 
![Windows Boot Manager met de vermelding 'Kies een besturingssysteem om te starten of druk op TAB om een gereedschap te selecteren:'](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Oorzaak

De fout is te wijten aan een *BCD-vlagdisplaybootmenu* in Windows Boot Manager. Wanneer de vlag is ingeschakeld, vraagt Windows Boot Manager de gebruiker tijdens het opstartproces om te selecteren welke lader hij of zij wil uitvoeren, waardoor een opstartvertraging ontstaat. In Azure kan deze functie toevoegen aan de tijd die nodig is om een vm op te starten.

## <a name="solution"></a>Oplossing

Procesoverzicht:

1. Configureren voor snellere opstarttijd met seriële console.
2. Een reparatievm maken en openen.
3. Configureren voor snellere opstarttijd op een reparatie-vm.
4. **Aanbevolen:** Voordat u de VM opnieuw opbouwt, schakelt u de verzameling seriële console en geheugendump in.
5. Herbouw de VM.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configureren voor snellere opstarttijd met seriële console

Als u toegang hebt tot seriële console, zijn er twee manieren waarop u snellere opstarttijden bereiken. Verlaag de wachttijd *voor het displaybootmenu* of verwijder de vlag helemaal.

1. Volg aanwijzingen om toegang te krijgen tot [Azure Serial Console voor Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) om toegang te krijgen tot de op tekst gebaseerde console.

   > [!NOTE]
   > Als u geen toegang hebt tot de seriële console, gaat u verder naar [Een reparatievm maken en openen.](#create-and-access-a-repair-vm)

2. **Optie A:** Wachttijd verkorten

   a. De wachttijd is standaard ingesteld op 30 seconden, maar kan worden gewijzigd in een snellere tijd (bijv. 5 seconden).

   b. Gebruik de volgende opdracht in de seriële console om de time-outwaarde aan te passen:

      `bcdedit /set {bootmgr} timeout 5`

3. **Optie B**: Verwijder de BCD-vlag

   a. Voer de volgende opdracht in om te voorkomen dat de prompt van het opstartmenu van het scherm helemaal wordt weergegeven:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Als u de seriële console niet gebruiken om een snellere opstarttijd in de bovenstaande stappen te configureren, u in plaats daarvan doorgaan met de volgende stappen. U lost nu problemen op in de offlinemodus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een reparatievm maken en openen

1. Gebruik [stap 1-3 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) om een reparatie-vm voor te bereiden.
2. Verbinding met extern bureaublad gebruiken verbinding maken met de VM Repareren.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configureren voor snellere opstarttijd op een reparatie-vm

1. Open een opdrachtprompt met verhoogde bevoegdheid.
2. Voer het volgende in om DisplayBootMenu in te schakelen:

   Gebruik deze opdracht voor **Generatie 1 VM's:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Gebruik deze opdracht voor **Generatie 2 VM's:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Vervang groter dan of minder dan symbolen, evenals de tekst erin, bijvoorbeeld "< tekst hier >".

3. Wijzig de time-outwaarde in 5 seconden:

   Gebruik deze opdracht voor **Generatie 1 VM's:**

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Gebruik deze opdracht voor **Generatie 2 VM's:**

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Vervang groter dan of minder dan symbolen, evenals de tekst erin, bijvoorbeeld "< tekst hier >".

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: Voordat u de VM opnieuw opbouwt, schakelt u de verzameling seriële console en geheugendump in

Voer het volgende script uit om geheugendumpverzameling en seriële console in te schakelen:

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid (Uitvoeren als beheerder).
2. Voer de volgende opdrachten uit:

   Seriële console inschakelen

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Vervang groter dan of minder dan symbolen, evenals de tekst erin, bijvoorbeeld "< tekst hier >".

3. Controleer of de vrije ruimte op de OS-schijf evenveel is als de geheugengrootte (RAM) op de VM.

   Als er niet genoeg ruimte op de OS-schijf is, moet u de locatie wijzigen waar het geheugendumpbestand wordt gemaakt en verwijzen naar een gegevensschijf die is gekoppeld aan de VM die voldoende vrije ruimte heeft. Als u de locatie wilt wijzigen, vervangt u "%SystemRoot%" door de stationsletter (bijvoorbeeld 'F:') van de gegevensschijf in de onderstaande opdrachten.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Voorgestelde configuratie om OS Dump in te schakelen

**Kapotte OS-schijf laden:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Inschakelen op ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Inschakelen op ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Kapotte OS-schijf uitladen:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>De oorspronkelijke vm opnieuw opbouwen

Gebruik [stap 5 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de VM opnieuw in elkaar te zetten.
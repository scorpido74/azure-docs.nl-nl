---
title: Windows-stopfout -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373360"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows Stop Error - #0x000000EF 'Kritieke proces overleden'

In dit artikel worden stappen gezet om problemen op te lossen waarbij een kritiek proces overlijdt tijdens het opstarten in een Azure VM.

## <a name="symptom"></a>Symptoom

Wanneer u [Opstartdiagnose](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de schermafbeelding van de vm weer te geven, ziet u dat de schermafbeelding de fout *weergeeft #0x000000EF* met het bericht *Kritieke proces is overleden.*

!["Uw pc liep in een probleem en moet opnieuw opstarten. We verzamelen alleen wat foutinfo en dan kun je opnieuw starten. (##% compleet) Als u meer wilt weten, u later online zoeken naar deze fout: 0x00000EF](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Oorzaak

Meestal is dit te wijten aan een kritisch systeemproces niet tijdens het opstarten. U meer lezen over kritieke procesproblemen bij[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died).

## <a name="solution"></a>Oplossing

### <a name="process-overview"></a>Procesoverzicht:

1. Een reparatievm maken en openen.
2. Los elke OS-corruptie op.
3. **Aanbevolen:** Voordat u de VM opnieuw opbouwt, schakelt u de verzameling seriële console en geheugendump in.
4. Herbouw de VM.

> [!NOTE]
> Bij het tegenkomen van deze opstartfout is het besturingssysteem van de gast niet operationeel. U lost problemen op in de offlinemodus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een reparatievm maken en openen

1. Gebruik [stap 1-3 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) om een reparatie-vm voor te bereiden.
2. Verbinding met extern bureaublad gebruiken verbinding maken met de VM Repareren.

### <a name="fix-any-os-corruption"></a>Los elke beschadiging van het besturingssysteem op

1. Open een opdrachtprompt met verhoogde bevoegdheid.
2. Voer de volgende opdracht System File Checker (SFC) uit:

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Waar < OPSTARTSCHIJF > is, is het opstartvolume van de Reparatie-VM (meestal "C:") en < BROKEN DISK DRIVE > de schijfletter voor de aangesloten schijf van de kapotte VM. Vervang de meer dan / minder dan symbolen, evenals de tekst daarin, bijvoorbeeld "< tekst hier >", door de juiste letter.

3. Gebruik vervolgens [stap 5 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de VM opnieuw in elkaar te zetten en te zien of deze wordt opgestart.
4. Als de VM nog steeds niet wordt opgestart, gaat u verder met het verzamelen van het geheugendumpbestand.

### <a name="collect-the-memory-dump-file"></a>Het geheugendumpbestand verzamelen

Als het probleem blijft bestaan na het uitvoeren van SFC, is analyse van een geheugendumpbestand vereist om de oorzaak van het probleem te bepalen. Voer de volgende stappen uit om het geheugendumpbestand te verzamelen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De OS-schijf koppelen aan een nieuwe reparatie-vm

1. Gebruik [stap 1-3 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) om een nieuwe reparatie-vm voor te bereiden.
2. Verbinding met extern bureaublad gebruiken verbinding maken met de VM Repareren.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Zoek het dumpbestand en stuur een ondersteuningsticket in

3. Ga op de reparatie-vm naar de Windows-map in de bijgevoegde OS-schijf. Als de stuurprogrammaletter die is toegewezen aan de gekoppelde osschijf *F*is, moet u naar *F:\Windows gaan.*
4. Zoek het *memory.dmp-bestand* en [dien vervolgens een ondersteuningsticket in](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bij het geheugendumpbestand.

   > [!NOTE]
   > Als u het dumpbestand niet vinden, voert u de onderstaande stappen uit om geheugendumpverzameling en seriële console in te schakelen, gaat u terug naar deze sectie en herhaalt u de stappen in de bovenstaande taak om het geheugendumpbestand te verzamelen.

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

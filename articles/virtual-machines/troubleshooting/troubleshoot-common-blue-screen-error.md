---
title: Fouten op blauw scherm bij het opstarten van een Azure VM| Microsoft Documenten
description: Meer informatie over het oplossen van het probleem dat de blauwe schermfout wordt ontvangen bij het opstarten| Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266934"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows toont blauwe schermfout bij het opstarten van een Azure VM
In dit artikel worden fouten op blauw scherm beschreven die u tegenkomen wanneer u een Virtuele Machine (VM) van Windows opstart in Microsoft Azure. Het biedt stappen om u te helpen gegevens te verzamelen voor een ondersteuningsticket. 


## <a name="symptom"></a>Symptoom 

Een Windows-vm wordt niet gestart. Wanneer u de opstartschermafbeeldingen in [Opstartdiagnose](./boot-diagnostics.md)controleert, ziet u een van de volgende foutmeldingen in een blauw scherm:

- onze pc liep in een probleem en moet opnieuw opstarten. We verzamelen alleen wat foutinfo en dan kun je opnieuw starten.
- Uw pc liep in een probleem en moet opnieuw opstarten.

In deze sectie worden de veelvoorkomende foutmeldingen weergegeven die u tegenkomen bij het beheren van VM's:

## <a name="cause"></a>Oorzaak

Er kunnen meerdere redenen zijn waarom je een stopfout zou krijgen. De meest voorkomende oorzaken zijn:

- Probleem met een bestuurder
- Beschadigd systeembestand of geheugen
- Een applicatie toegang tot een verboden sector van het geheugen

## <a name="collect-memory-dump-file"></a>Geheugendumpbestand verzamelen

Om dit probleem op te lossen, moet u eerst dumpbestand verzamelen voor de crash en contact opnemen met ondersteuning met het dumpbestand. Voer de volgende stappen uit om het bestand Dump te verzamelen:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. Maak een momentopname van de OS-schijf van de getroffen VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .
2. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md). 
3. Extern bureaublad naar de herstel-VM.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Dumpbestand zoeken en een ondersteuningsticket indienen

1. Ga op de herstel-vm naar de Windows-map in de bijgevoegde osschijf. Als de stuurprogrammaletter die is toegewezen aan de gekoppelde osschijf F is, moet u naar F:\Windows gaan.
2. Zoek het memory.dmp-bestand en [dien vervolgens een ondersteuningsticket in](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bij het dumpbestand. 

Als u het dumpbestand niet vinden, verplaatst u de volgende stap om dumplogboek en seriële console in te schakelen.

### <a name="enable-dump-log-and-serial-console"></a>Dumplog en Seriële console inschakelen

Voer het volgende script uit om dumplog en Seriële console in te schakelen.

1. Open de sessie Prompt met verhoogde opdracht (Uitvoeren als beheerder).
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is.  Vervang deze door de juiste waarde in uw VM.

    ```powershell
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
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Zorg ervoor dat er voldoende ruimte op de schijf is om evenveel geheugen toe te wijzen als het RAM-geheugen, dat afhankelijk is van de grootte die u voor deze virtuele machine selecteert.
    2. Als er niet genoeg ruimte is of als dit een VM(G-, GS- of E-reeks) is, u de locatie wijzigen waar dit bestand wordt gemaakt en dat doorverwijzen naar een andere gegevensschijf die aan de VM is gekoppeld. Hiervoor moet u de volgende toets wijzigen:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Maak de osschijf los en koppel de osschijf opnieuw aan de betreffende VM](../windows/troubleshoot-recovery-disks-portal.md).
4. Start de VM om het probleem te reproduceren, waarna een dumpbestand wordt gegenereerd.
5. Koppel de OS-schijf aan een herstel-vm, verzamel dumpbestand en verzend vervolgens [een ondersteuningsticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) bij het dumpbestand.




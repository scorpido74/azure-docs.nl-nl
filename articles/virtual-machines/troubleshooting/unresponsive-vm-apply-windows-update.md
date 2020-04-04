---
title: Azure VM reageert niet met C01A001D-fout bij het toepassen van Windows Update
description: In dit artikel worden stappen weergegeven om problemen op te lossen waarbij Windows-update een fout genereert en niet meer reageert in een Azure VM.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633955"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>VM reageert niet met "C01A001D" fout bij het toepassen van Windows Update

In dit artikel worden stappen weergegeven om problemen op te lossen waarbij Windows Update (KB) een fout genereert en niet meer reageert in een Azure-vm.

## <a name="symptoms"></a>Symptomen

Wanneer u [Opstartdiagnostiek](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de schermafbeelding van de VM weer te geven, wordt de in uitvoering van Windows Update (KB) weergegeven, maar mislukt met foutcode: 'C01A001D'.

![niet reagerenop Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Oorzaak

Er kan geen kernbestand worden gemaakt in het bestandssysteem. Het besturingssysteem kan geen bestanden naar de schijf schrijven.

## <a name="resolution"></a>Oplossing

### <a name="process-overview"></a>Procesoverzicht

1. [Een reparatie-vm maken en openen.](#create-and-access-a-repair-vm)
2. [Maak ruimte vrij op de harde schijf.](#free-up-space-on-the-hard-disk)
3. [Aanbevolen: Schakel voor het opnieuw opbouwen van de VM de verzameling seriële console en geheugendump in.](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)
4. [De VM opnieuw opbouwen](#rebuild-the-vm).

> [!NOTE]
> Wanneer deze fout optreedt, is het besturingssysteem van de gast niet operationeel. U moet problemen oplossen in de offlinemodus om dit probleem op te lossen.

### <a name="create-and-access-a-repair-vm"></a>Een reparatie-vm maken en openen

1. Volg [stappen 1-3 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) om een reparatie-vm voor te bereiden.
2. Maak verbinding met de VM herstellen via Verbinding met extern bureaublad.

### <a name="free-up-space-on-the-hard-disk"></a>Ruimte vrijmaken op de harde schijf

Als de schijf nog geen 1 Tb is, moet u het formaat ervan wijzigen. Zodra de schijf 1 TB is, voert u een schijfopruiming en een defragmentatie van het station uit.

1. Controleer of de schijf vol is. Als de schijf lager is dan 1 Tb, [vouw t/ uit tot maximaal 1 Tb met PowerShell.](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
2. Zodra de schijf 1 Tb is, voert u een schijfopruiming uit.
    - [Maak de gegevensschijf los van de kapotte VM](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Koppel de gegevensschijf aan een werkende vm](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Gebruik het [gereedschap Schijfopruiming](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) om ruimte vrij te maken.
3. Na het aanpassen en opruimen, defragmenteren van de drive:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    Afhankelijk van de mate van fragmentatie kan dit uren duren.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Aanbevolen: Schakel voor het opnieuw opbouwen van de VM de verzameling seriële console en geheugendump in

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid (Uitvoeren als beheerder).
2. Voer de volgende opdrachten uit:

    Seriële console inschakelen:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Zorg ervoor dat de vrije ruimte op de OS-schijf ten minste gelijk is aan de grootte van het VM-geheugen (RAM).

    Als er niet genoeg ruimte op de OS-schijf is, wijzigt u de locatie waar het geheugendumpbestand wordt gemaakt en verwijst u deze door naar een gegevensschijf die is gekoppeld aan de VM en met voldoende vrije ruimte. Als u de `%SystemRoot%` locatie wilt wijzigen, vervangt u de stationsletter (bijvoorbeeld 'F:') van de gegevensschijf in de onderstaande opdrachten:

    **Voorgestelde configuratie voor het dumpen van besturingssysteem inschakelen:**

    Kapotte OS-schijf laden:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Inschakelen op ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Inschakelen op ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Kapotte OS-schijf uitladen:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>De VM opnieuw opbouwen

Gebruik [stap 5 van de VM-reparatieopdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) om de VM opnieuw in elkaar te zetten.

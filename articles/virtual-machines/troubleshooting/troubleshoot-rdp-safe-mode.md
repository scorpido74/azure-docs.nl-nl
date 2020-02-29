---
title: Kan geen verbinding maken naar Azure Virtual Machines op afstand omdat de virtuele machine wordt opgestart in de veilige modus | Microsoft Docs
description: Meer informatie over het oplossen van problemen waarbij niet RDP-verbinding met een virtuele machine omdat de virtuele machine wordt opgestart in de veilige modus. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918203"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Niet van RDP-verbinding met een virtuele machine omdat de virtuele machine wordt opgestart in de veilige modus

In dit artikel laat zien hoe het oplossen van een probleem waarbij u geen verbinding naar Azure Windows Virtual Machines (VM's maken) omdat de virtuele machine is geconfigureerd om op te starten in de veilige modus.


## <a name="symptoms"></a>Symptomen

Kunt u niet een RDP-verbinding of andere verbindingen (zoals HTTP) aan een virtuele machine in Azure omdat de virtuele machine is geconfigureerd om op te starten in de veilige modus. Wanneer u de scherm opname in de Azure Portal [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) controleert, ziet u mogelijk dat de VM normaal wordt opgestart, maar de netwerk interface is niet beschikbaar:

![Afbeelding van netwerk inferce in de veilige modus](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Oorzaak

De RDP-service is niet beschikbaar in de veilige modus. Alleen zijn essentiële systeem programma's en services geladen wanneer de virtuele machine wordt opgestart in de veilige modus. Dit geldt voor de twee verschillende versies van de veilige modus zijn "Opstarten in veilige modus minimale" en "Veilig opstarten met de connectiviteit".


## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

U kunt dit probleem oplossen door seriële controle te gebruiken om de virtuele machine te configureren om op te starten in de normale modus of [de virtuele machine offline te herstellen](#repair-the-vm-offline) met behulp van een herstel-VM.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u [de virtuele machine offline herstellen](#repair-the-vm-offline).
2. Controleer de opstartconfiguratiegegevens:

        bcdedit /enum

    Als de virtuele machine is geconfigureerd om te worden opgestart in de veilige modus, ziet u een extra vlag onder het gedeelte **Windows boot loader** met de naam **safeboot**. Als u de vlag **safeboot** niet ziet, is de virtuele machine niet in de veilige modus. In dit artikel geldt niet voor uw scenario.

    De vlag **safeboot** kan worden weer gegeven met de volgende waarden:
   - Minimaal
   - Netwerk

     RDP wordt in een van deze twee modi, niet worden gestart. De oplossing blijft daarom ongewijzigd.

     ![Afbeelding van de markering voor de veilige modus](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Verwijder de vlag **safemoade** , zodat de virtuele machine in de normale modus wordt opgestart:

        bcdedit /deletevalue {current} safeboot

4. Controleer de opstart configuratie gegevens om er zeker van te zijn dat de vlag **safeboot** wordt verwijderd:

        bcdedit /enum

5. Start de VM opnieuw en controleer vervolgens of het probleem opgelost is.

### <a name="repair-the-vm-offline"></a>Herstel de virtuele machine offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteemschijf koppelen aan een virtuele machine voor herstel

1. [Koppel de besturingssysteem schijf aan een herstel-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een externe bureaubladverbinding met de virtuele machine voor herstel.
3. Zorg ervoor dat de schijf is gemarkeerd als **online** in de schijf beheer-console. Houd er rekening mee de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Inschakelen van de dump logboek- en seriële Console (optioneel)

De dump logboek- en seriële Console kunnen we doen problemen op te lossen als het probleem niet kan worden omgezet door de oplossing in dit artikel.

Om in te schakelen dump logboek- en seriële Console, voer het volgende script.

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (**als administrator uitvoeren**).
2. Voer het volgende script uit:

    In dit script, we gaan ervan uit dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf F. vervangen deze stationsletter door de juiste waarde voor uw virtuele machine.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Windows configureren om op te starten in de normale modus

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (**als administrator uitvoeren**).
2. Controleer de opstart configuratie gegevens. In de volgende opdrachten wordt ervan uitgegaan dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf F is. Vervang deze stationsletter door de juiste waarde voor de virtuele machine.

        bcdedit /store F:\boot\bcd /enum
    Noteer de id-naam van de partitie die de map **\Windows** heeft. De id-naam is standaard ingesteld op standaard.

    Als de virtuele machine is geconfigureerd om te worden opgestart in de veilige modus, ziet u een extra vlag onder het gedeelte **Windows boot loader** met de naam **safeboot**. Als u de vlag **safeboot** niet ziet, is dit artikel niet van toepassing op uw scenario.

    ![De installatie kopie over de opstart-id](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Verwijder de vlag **safeboot** , zodat de virtuele machine in de normale modus wordt opgestart:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Controleer de opstart configuratie gegevens om er zeker van te zijn dat de vlag **safeboot** wordt verwijderd:

        bcdedit /store F:\boot\bcd /enum
5. [Ontkoppel de besturingssysteem schijf en maak de virtuele machine opnieuw](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

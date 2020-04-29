---
title: Er kan op afstand geen verbinding worden gemaakt met Azure Virtual Machines omdat de VM wordt opgestart in de veilige modus | Microsoft Docs
description: Meer informatie over het oplossen van een probleem waarbij geen RDP naar een virtuele machine kan worden uitgevoerd, omdat de VM wordt opgestart in de veilige modus. | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918203"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Kan geen RDP-verbinding met een virtuele machine tot stand brengen omdat de VM wordt opgestart in de veilige modus

In dit artikel wordt beschreven hoe u een probleem oplost waarmee u geen verbinding kunt maken met Azure Windows Virtual Machines (Vm's), omdat de VM is geconfigureerd om te worden opgestart in de veilige modus.


## <a name="symptoms"></a>Symptomen

U kunt geen RDP-verbinding of andere verbindingen (zoals HTTP) maken naar een virtuele machine in azure omdat de virtuele machine is geconfigureerd om op te starten in de veilige modus. Wanneer u de scherm opname in de Azure Portal [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) controleert, ziet u mogelijk dat de VM normaal wordt opgestart, maar de netwerk interface is niet beschikbaar:

![Installatie kopie over netwerk inferce in de veilige modus](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Oorzaak

De RDP-service is niet beschikbaar in de veilige modus. Alleen essentiële systeem Programma's en-services worden geladen wanneer de virtuele machine wordt opgestart in de veilige modus. Dit geldt voor de twee verschillende versies van de veilige modus die ' veilig opstarten mini maal ' en ' veilig opstarten met connectiviteit ' zijn.


## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u een moment opname maken van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

U kunt dit probleem oplossen door seriële controle te gebruiken om de virtuele machine te configureren om op te starten in de normale modus of [de virtuele machine offline te herstellen](#repair-the-vm-offline) met behulp van een herstel-VM.

### <a name="use-serial-control"></a>Serieel besturings element gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u [de virtuele machine offline herstellen](#repair-the-vm-offline).
2. Controleer de opstart configuratie gegevens:

        bcdedit /enum

    Als de virtuele machine is geconfigureerd om te worden opgestart in de veilige modus, ziet u een extra vlag onder het gedeelte **Windows boot loader** met de naam **safeboot**. Als u de vlag **safeboot** niet ziet, is de virtuele machine niet in de veilige modus. Dit artikel is niet van toepassing op uw scenario.

    De vlag **safeboot** kan worden weer gegeven met de volgende waarden:
   - Minimaal
   - Netwerk

     In een van deze twee modi wordt RDP niet gestart. Daarom blijft de correctie hetzelfde.

     ![Afbeelding over de vlag voor de veilige modus](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Verwijder de vlag **safemoade** , zodat de virtuele machine in de normale modus wordt opgestart:

        bcdedit /deletevalue {current} safeboot

4. Controleer de opstart configuratie gegevens om er zeker van te zijn dat de vlag **safeboot** wordt verwijderd:

        bcdedit /enum

5. Start de virtuele machine opnieuw op en controleer of het probleem is opgelost.

### <a name="repair-the-vm-offline"></a>De virtuele machine offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De besturingssysteem schijf koppelen aan een herstel-VM

1. [Koppel de besturingssysteem schijf aan een herstel-VM](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een Extern bureaublad verbinding met de virtuele machine voor herstel.
3. Zorg ervoor dat de schijf is gemarkeerd als **online** in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Dump logboek en seriële console inschakelen (optioneel)

Het dump logboek en de seriële console helpen ons bij het oplossen van problemen als het probleem niet kan worden opgelost door de oplossing in dit artikel.

Voer het volgende script uit om dump logboek en seriële console in te scha kelen.

1. Open een opdracht prompt sessie met verhoogde bevoegdheden (**als administrator uitvoeren**).
2. Voer het volgende script uit:

    In dit script wordt ervan uitgegaan dat de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf F is. Vervang deze stationsletter door de juiste waarde voor uw VM.

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

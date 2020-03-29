---
title: Kan niet op afstand verbinding maken met Azure Virtual Machines omdat de VM wordt gestart in de veilige modus | Microsoft Documenten
description: Meer informatie over het oplossen van een probleem waarbij RDP niet aan een VM kan worden gebruikt omdat de VM wordt opgestart in de veilige modus.| Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918203"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Kan RDP niet naar een VM omdat de VM wordt opgestart in de veilige modus

In dit artikel ziet u hoe u een probleem oplossen waarbij u geen verbinding maken met Azure Windows Virtual Machines (VM's) omdat de VM is geconfigureerd om op te starten in de veilige modus.


## <a name="symptoms"></a>Symptomen

U geen RDP-verbinding of andere verbindingen (zoals HTTP) maken met een VM in Azure omdat de VM is geconfigureerd om op te starten in de veilige modus. Wanneer u de schermafbeelding in de [opstartdiagnose](../troubleshooting/boot-diagnostics.md) in de Azure-portal controleert, ziet u mogelijk dat de VM normaal wordt gestart, maar dat de netwerkinterface niet beschikbaar is:

![Afbeelding over netwerkinferce in veilige modus](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Oorzaak

De RDP-service is niet beschikbaar in de veilige modus. Alleen essentiële systeemprogramma's en -services worden geladen wanneer de VM in de veilige modus wordt opgestart. Dit geldt voor de twee verschillende versies van de veilige modus die "Safe Boot minimal" en "Safe Boot with connectivity" zijn.


## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de OS-schijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u dit probleem wilt oplossen, gebruikt u Seriële besturingselementom de VM te configureren om op te starten in de normale modus of [de VM offline te herstellen](#repair-the-vm-offline) met behulp van een herstel-vm.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Maak verbinding [met seriële console en open CMD-instantie](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Zie De [VM offline herstellen](#repair-the-vm-offline)als de seriële console niet is ingeschakeld op uw vm.
2. Controleer de opstartconfiguratiegegevens:

        bcdedit /enum

    Als de VM is geconfigureerd om op te starten in de veilige modus, ziet u een extra vlag onder de sectie **Windows Boot Loader** genaamd **safeboot**. Als u de **safebootvlag** niet ziet, bevindt de VM zich niet in de veilige modus. Dit artikel is niet van toepassing op uw scenario.

    De **safebootvlag** kan worden weergegeven met de volgende waarden:
   - Minimaal
   - Netwerk

     In een van deze twee modi wordt RDP niet gestart. Daarom blijft de oplossing hetzelfde.

     ![Afbeelding over de vlag Veilige modus](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Verwijder de **safemoadevlag,** zodat de VM in de normale modus wordt opgestart:

        bcdedit /deletevalue {current} safeboot

4. Controleer de configuratiegegevens voor het opstarten om ervoor te zorgen dat de **safebootvlag** wordt verwijderd:

        bcdedit /enum

5. Start de VM opnieuw en controleer of het probleem is opgelost.

### <a name="repair-the-vm-offline"></a>De VM offline herstellen

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>De OS-schijf koppelen aan een herstel-vm

1. [Koppel de OS-schijf aan een herstelvm](../windows/troubleshoot-recovery-disks-portal.md).
2. Start een verbinding met Extern bureaublad met de herstel-vm.
3. Controleer of de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Dumplog en Seriële console inschakelen (optioneel)

Het dumplogboek en de seriële console helpen ons om verdere problemen op te lossen als het probleem niet kan worden opgelost door de oplossing in dit artikel.

Voer het volgende script uit om dumplog en Seriële console in te schakelen.

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid **(Uitvoeren als beheerder).**
2. Voer het volgende script uit:

    In dit script gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. Vervang deze stationsletter met de juiste waarde voor uw VM.

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>De Windows configureren om op te starten in de normale modus

1. Open een opdrachtpromptsessie met verhoogde bevoegdheid **(Uitvoeren als beheerder).**
2. Controleer de opstartconfiguratiegegevens. In de volgende opdrachten gaan we ervan uit dat de stationsletter die is toegewezen aan de gekoppelde OS-schijf F is. Vervang deze stationsletter door de juiste waarde voor uw vm.

        bcdedit /store F:\boot\bcd /enum
    Let op de naam van de id van de partitie met de **map \windows.** Standaard is de naam van de id 'Standaard'.

    Als de VM is geconfigureerd om op te starten in de veilige modus, ziet u een extra vlag onder de sectie **Windows Boot Loader** genaamd **safeboot**. Als u de **safebootvlag** niet ziet, is dit artikel niet van toepassing op uw scenario.

    ![De afbeelding over boot-id](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Verwijder de **safebootvlag,** zodat de VM in de normale modus wordt opgestart:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Controleer de configuratiegegevens voor het opstarten om ervoor te zorgen dat de **safebootvlag** wordt verwijderd:

        bcdedit /store F:\boot\bcd /enum
5. [Maak de OS-schijf los en maak de VM opnieuw](../windows/troubleshoot-recovery-disks-portal.md). Controleer vervolgens of het probleem is opgelost.

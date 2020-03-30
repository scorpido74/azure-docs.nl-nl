---
title: Azure VM Guest OS-firewall blokkeert binnenkomend verkeer | Microsoft Documenten
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 0cbd1a24f5c460e248d55777735da6809befba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72028789"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM Guest OS-firewall blokkeert binnenkomend verkeer

In dit artikel wordt besproken hoe u het RDP-probleem (Remote Desktop Portal) oplossen dat optreedt als de firewall van het gastbesturingssysteem binnenkomend verkeer blokkeert.

## <a name="symptoms"></a>Symptomen

U geen RDP-verbinding gebruiken om verbinding te maken met een virtuele Azure-machine (VM). Van Opstartdiagnostiek -> Screenshot, het laat zien dat het besturingssysteem volledig is geladen op het welkomstscherm (Ctrl+Alt+Del).

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

De RDP-regel is niet ingesteld om het RDP-verkeer toe te staan.

### <a name="cause-2"></a>Oorzaak 2

De firewallprofielen van het gastsysteem zijn ingesteld om alle binnenkomende verbindingen te blokkeren, inclusief het RDP-verkeer.

![Firewall-instelling](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de systeemschijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u het probleem wilt oplossen, gebruikt u een van de methoden in [Hoe externe hulpprogramma's te gebruiken om Azure VM-problemen op](remote-tools-troubleshoot-azure-vm-issues.md) te lossen om op afstand verbinding te maken met de VM en bewerkt u vervolgens de firewallregels voor gastbesturingssystemen om RDP-verkeer toe te **staan.**

### <a name="online-troubleshooting"></a>Online probleemoplossing

Maak verbinding met de [seriële console en open vervolgens een PowerShell-exemplaar](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Als de seriële console niet is ingeschakeld op de vm, gaat u naar["De VM offline herstellen](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Mitigatie 1

1.  Als Azure Agent is geïnstalleerd en correct werkt op de VM, u de optie Alleen configuratie opnieuw instellen gebruiken onder **Ondersteuning + het wachtwoord opnieuw** > **instellen** in het VM-menu.

2.  Als u deze hersteloptie uitvoert, gaat u als volgt te werk:

    *   Hiermee schakelt u een RDP-component in als deze is uitgeschakeld.

    *   Hiermee schakelt u alle Windows-firewallprofielen in.

    *   Controleer of de RDP-regel is ingeschakeld in Windows Firewall.

    *   Als de vorige stappen niet werken, stelt u de firewallregel handmatig opnieuw in. Om dit te doen, query alle regels die de naam "Extern bureaublad" bevatten door het uitvoeren van de volgende opdracht:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Als de RDP-poort is ingesteld op een andere poort dan 3389, moet u een aangepaste regel vinden die mogelijk is gemaakt en ingesteld op deze poort. Als u wilt zoeken naar alle binnenkomende regels met een aangepaste poort, voert u de volgende opdracht uit:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Als u ziet dat de regel is uitgeschakeld, schakelt u deze in. Als u een hele groep wilt openen, zoals de ingebouwde groep Extern bureaublad, voert u de volgende opdracht uit:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Voer anders de volgende opdracht uit om de specifieke TCP-In-regel voor Extern bureaublad te openen:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Voor het oplossen van problemen u de firewallprofielen uitschakelen in UIT:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Nadat u het oplossen van problemen hebt voltooid en de firewall correct hebt ingesteld, schakelt u de firewall opnieuw in.

    > [!Note]
    > U hoeft de VM niet opnieuw op te starten om deze wijzigingen toe te passen.

5.  Probeer een RDP-verbinding te maken om toegang te krijgen tot de VM.

#### <a name="mitigation-2"></a>Mitigatie 2

1.  Vraag de firewallprofielen op om te bepalen of het inkomende firewallbeleid is ingesteld op *BlockInboundAlways:*

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Alle profielen](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > De volgende richtlijnen zijn van toepassing op het firewallbeleid, afhankelijk van hoe het is ingesteld:
    >    * *BlockInbound*: Al het binnenkomende verkeer wordt geblokkeerd, tenzij u een regel hebt die van kracht is om dat verkeer toe te staan.
    >    * *BlockInboundAlways*: Alle firewallregels worden genegeerd en al het verkeer wordt geblokkeerd.

2.  Bewerk de *DefaultInboundAction* om deze profielen in te stellen **op Verkeer toestaan.** Voer hiervoor de volgende opdracht uit:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Bevraag de profielen opnieuw om ervoor te zorgen dat de wijziging is doorgevoerd. Voer hiervoor de volgende opdracht uit:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > U hoeft de VM niet opnieuw op te starten om de wijzigingen toe te passen.

4.  Probeer opnieuw toegang te krijgen tot uw VM via RDP.

### <a name="offline-mitigations"></a>Offline mitigaties

1.  [Koppel de systeemschijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een verbinding met Extern bureaublad met de herstel-vm.

3.  Controleer of de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde systeemschijf.

#### <a name="mitigation-1"></a>Mitigatie 1

Zie [Een firewallregel inschakelen op een gastbesturingssysteem](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Mitigatie 2

1.  [Koppel de systeemschijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een verbinding met Extern bureaublad met de herstel-vm.

3.  Nadat de systeemschijf is gekoppeld aan de herstel-VM, moet u ervoor zorgen dat de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde osschijf.

4.  Open een verhoogde CMD-instantie en voer het volgende script uit:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Maak de systeemschijf los en maak de VM opnieuw.](troubleshoot-recovery-disks-portal-windows.md)

6.  Controleer of het probleem is opgelost.

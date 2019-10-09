---
title: Binnenkomend verkeer wordt geblokkeerd door firewall van Azure VM-Gast-OS | Microsoft Docs
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
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028789"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Binnenkomend verkeer wordt geblokkeerd door firewall van Azure VM-Gastbesturingssysteem

In dit artikel wordt beschreven hoe u de Portal van Remote Desktop (RDP)-fout die optreedt als de Gast-besturingssysteem firewall geblokkeerd binnenkomend verkeer op te lossen.

## <a name="symptoms"></a>Symptomen

U kunt een RDP-verbinding niet gebruiken voor het verbinding maken met een Azure-machine (VM). Bij het opstarten vanuit Screenshot van diagnostische gegevens over ->, het laat zien dat het besturingssysteem volledig is geladen op het welkomstscherm wordt weergegeven (Ctrl + Alt + Del).

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

De RDP-regel is niet ingesteld om toe te staan de RDP-verkeer.

### <a name="cause-2"></a>Oorzaak 2

De Gast-firewallprofielen zijn geconfigureerd dat alle binnenkomende verbindingen blokkeren, inclusief het RDP-verkeer.

![Firewall-instelling](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de schijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

Los het probleem, gebruikt u een van de methoden in [externe hulpprogramma's gebruiken om problemen met virtuele Azure-machine te](remote-tools-troubleshoot-azure-vm-issues.md) op afstand verbinding maken met de virtuele machine en bewerk vervolgens de firewallregels van Gast-besturingssysteem voor **toestaan** RDP-verkeer .

### <a name="online-troubleshooting"></a>Online oplossen

Verbinding maken met de [seriële Console en open vervolgens een PowerShell-sessie](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Als de seriële Console is niet ingeschakeld op de virtuele machine, gaat u naar '[herstellen van de virtuele machine Offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Risicobeperking 1

1.  Als Azure-Agent is geïnstalleerd en correct werkt op de virtuele machine, kunt u de optie 'Alleen-configuratie opnieuw instellen' onder **ondersteuning en probleemoplossing** > **wachtwoord opnieuw instellen** in het menu van de virtuele machine.

2.  Met deze optie recovery doet het volgende:

    *   Hiermee kan een RDP-onderdeel als deze uitgeschakeld.

    *   Hiermee kunt alle Windows firewall-profielen.

    *   Zorg ervoor dat de regel voor RDP in Windows Firewall is ingeschakeld.

    *   Als de vorige stappen niet werken, moet u handmatig opnieuw instellen met de firewallregel. U doet dit door een query in de regels die de naam 'Extern bureaublad' bevatten de volgende opdracht uit:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Als de RDP-poort is ingesteld op een andere poort dan 3389, hebt u een aangepaste regel die mogelijk zijn gemaakt en ingesteld op deze poort te vinden. Om te vragen voor de binnenkomende regels waarvoor een aangepaste poort, voer de volgende opdracht:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Als u ziet dat de regel is uitgeschakeld, inschakelen. Voer de volgende opdracht om te openen van een hele groep, zoals de ingebouwde extern bureaublad-groep:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Anders wilt openen de specifieke extern bureaublad (TCP-In) de regel, voert u de volgende opdracht uit:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  U kunt de firewallprofielen op OFF inschakelen voor het oplossen van problemen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Nadat u klaar bent met het oplossen van problemen en het instellen van de firewall correct is opnieuw inschakelen van de firewall.

    > [!Note]
    > U hebt geen opnieuw opstarten van de virtuele machine als deze wijzigingen wilt toepassen.

5.  Probeer een RDP-verbinding voor toegang tot de virtuele machine te maken.

#### <a name="mitigation-2"></a>Risicobeperking 2

1.  Query's uitvoeren op de Firewall profielen om te bepalen of het beleid voor binnenkomende firewall is ingesteld op *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > De volgende richtlijnen gelden voor de firewall-beleid, afhankelijk van hoe deze ingesteld:
    >    * *BlockInbound*: Alle binnenkomend verkeer wordt geblokkeerd, tenzij u een regel van kracht hebt om dat verkeer toe te staan.
    >    * *BlockInboundAlways*: Alle firewall regels worden genegeerd en alle verkeer wordt geblokkeerd.

2.  Bewerk de *DefaultInboundAction* om deze profielen in te stellen voor het **toestaan** van verkeer. U doet dit door de volgende opdracht uitvoeren:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Query uitvoeren op de profielen opnieuw om het ervoor te zorgen dat de wijziging is gemaakt. U doet dit door de volgende opdracht uitvoeren:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > U hebt geen opnieuw opstarten van de virtuele machine om de wijzigingen te laten.

4.  Probeer het opnieuw voor toegang tot uw virtuele machine via RDP.

### <a name="offline-mitigations"></a>Offline oplossingen

1.  [De schijf koppelen aan een virtuele machine voor herstel](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een externe bureaubladverbinding met de virtuele machine voor herstel.

3.  Zorg ervoor dat de schijf is gemarkeerd als **Online** in de Schijfbeheer-console. Houd er rekening mee de stationsletter die is toegewezen aan de gekoppelde schijf.

#### <a name="mitigation-1"></a>Risicobeperking 1

Lees [hoe u een firewall regel in een gast besturingssysteem inschakelt](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Risicobeperking 2

1.  [De schijf koppelen aan een virtuele machine voor herstel](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een externe bureaubladverbinding met de virtuele machine voor herstel.

3.  Nadat de systeem schijf is gekoppeld aan de herstel-VM, controleert u of de schijf is gemarkeerd als **online** in de schijf beheer-console. Houd er rekening mee de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf.

4.  Open een verhoogde CMD-exemplaar en voer het volgende script:

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

5.  [De schijf loskoppelen en opnieuw maken van de virtuele machine](troubleshoot-recovery-disks-portal-windows.md).

6.  Controleer of het probleem opgelost is.

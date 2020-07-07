---
title: Het inkomend verkeer wordt geblokkeerd door de firewall van de Azure VM-gast besturingssysteem | Microsoft Docs
description: Meer informatie over het oplossen van het verbindings probleem met de Extern bureaublad Portal (RDP) dat het gast besturingssysteem firewall inkomend verkeer blokkeert.
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
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422561"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>De firewall van het Azure VM-gast besturingssysteem blokkeert het inkomende verkeer

In dit artikel wordt beschreven hoe u het Extern bureaublad Portal-probleem (RDP) kunt oplossen dat zich voordoet als de firewall van het gast besturingssysteem inkomend verkeer blokkeert.

## <a name="symptoms"></a>Symptomen

U kunt geen RDP-verbinding gebruiken om verbinding te maken met een virtuele Azure-machine (VM). Vanuit diagnostische gegevens over opstarten: > scherm afbeelding, ziet u dat het besturings systeem volledig is geladen in het welkomst scherm (CTRL + ALT + DEL).

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

De RDP-regel is niet ingesteld om het RDP-verkeer toe te staan.

### <a name="cause-2"></a>Oorzaak 2

De profielen van het gast systeem firewall zijn ingesteld om alle binnenkomende verbindingen, inclusief het RDP-verkeer, te blok keren.

![Firewall instelling](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u een moment opname maken van de systeem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

Om het probleem op te lossen, gebruikt u een van de methoden in [het gebruik van externe hulpprogram ma's voor het oplossen](remote-tools-troubleshoot-azure-vm-issues.md) van problemen met virtuele Azure-machines om extern verbinding te maken met de VM en vervolgens de firewall regels voor het gast besturingssysteem te bewerken om RDP-verkeer **toe te staan** .

### <a name="online-troubleshooting"></a>Online problemen oplossen

Maak verbinding met de [seriële console en open vervolgens een Power shell-exemplaar](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Als de seriële console niet is ingeschakeld op de virtuele machine, gaat u naar[de virtuele machine offline herstellen](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Risico beperking 1

1.  Als Azure agent is geïnstalleerd en correct werkt op de virtuele machine, kunt u de optie ' configuratie alleen opnieuw instellen ' onder **ondersteuning en probleem oplossing**  >  voor het**opnieuw instellen van het wacht woord** in het VM-menu gebruiken.

2.  Als u deze herstel optie uitvoert, gebeurt het volgende:

    *   Hiermee schakelt u een RDP-onderdeel in als dit is uitgeschakeld.

    *   Hiermee worden alle Windows Firewall-profielen ingeschakeld.

    *   Zorg ervoor dat de RDP-regel is ingeschakeld in Windows Firewall.

    *   Als de vorige stappen niet werken, stelt u de firewall regel hand matig opnieuw in. Als u dit wilt doen, voert u een query uit op alle regels die de naam ' Extern bureaublad ' bevatten door de volgende opdracht uit te voeren:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Als de RDP-poort is ingesteld op een andere poort dan 3389, moet u een aangepaste regel vinden die mogelijk is gemaakt en ingesteld op deze poort. Voer de volgende opdracht uit om een query uit te voeren voor alle regels voor binnenkomende verbindingen met een aangepaste poort:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Als u ziet dat de regel is uitgeschakeld, schakelt u deze in. Als u een hele groep wilt openen, zoals de ingebouwde Extern bureaublad groep, voert u de volgende opdracht uit:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Als u de specifieke regel voor de Extern bureaublad (TCP-in) wilt openen, voert u de volgende opdracht uit:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Voor het oplossen van problemen kunt u de Firewall profielen uitschakelen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Nadat u klaar bent met het oplossen van problemen en de firewall correct hebt ingesteld, schakelt u de firewall opnieuw in.

    > [!Note]
    > U hoeft de virtuele machine niet opnieuw op te starten om deze wijzigingen toe te passen.

5.  Probeer een RDP-verbinding te maken voor toegang tot de virtuele machine.

#### <a name="mitigation-2"></a>Risico beperking 2

1.  Query's uitvoeren op de Firewall profielen om te bepalen of het beleid voor binnenkomende firewall is ingesteld op *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > De volgende richt lijnen zijn van toepassing op het firewall beleid, afhankelijk van de instelling:
    >    * *BlockInbound*: alle binnenkomend verkeer wordt geblokkeerd, tenzij u een regel van kracht hebt om dat verkeer toe te staan.
    >    * *BlockInboundAlways*: alle firewall regels worden genegeerd en alle verkeer wordt geblokkeerd.

2.  Bewerk de *DefaultInboundAction* om deze profielen in te stellen voor het **toestaan** van verkeer. Voer hiervoor de volgende opdracht uit:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Zoek de profielen opnieuw op om ervoor te zorgen dat de wijziging is doorgevoerd. Voer hiervoor de volgende opdracht uit:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > U hoeft de virtuele machine niet opnieuw op te starten om de wijzigingen toe te passen.

4.  Probeer opnieuw toegang te krijgen tot uw VM via RDP.

### <a name="offline-mitigations"></a>Offline oplossingen

1.  [Koppel de systeem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een Extern bureaublad verbinding met de virtuele machine voor herstel.

3.  Zorg ervoor dat de schijf is gemarkeerd als **online** in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde systeem schijf.

#### <a name="mitigation-1"></a>Risico beperking 1

Lees [hoe u een firewall regel in een gast besturingssysteem inschakelt](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Risico beperking 2

1.  [Koppel de systeem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een Extern bureaublad verbinding met de virtuele machine voor herstel.

3.  Nadat de systeem schijf is gekoppeld aan de herstel-VM, controleert u of de schijf is gemarkeerd als **online** in de schijf beheer-console. Noteer de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf.

4.  Open een met verhoogde bevoegdheden een CMD-exemplaar en voer het volgende script uit:

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

5.  [Ontkoppel de systeem schijf en maak de virtuele machine opnieuw](troubleshoot-recovery-disks-portal-windows.md).

6.  Controleer of het probleem is opgelost.

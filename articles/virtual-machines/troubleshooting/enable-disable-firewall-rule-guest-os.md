---
title: Een firewall regel in-of uitschakelen op een gast besturingssysteem in azure VM | Microsoft Docs
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
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086439"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Een firewall regel op een Azure VM-gast besturingssysteem in-of uitschakelen

Dit artikel bevat een Naslag informatie voor het oplossen van een situatie waarin u vermoedt dat de firewall van het gast besturingssysteem gedeeltelijk verkeer op een virtuele machine (VM) filtert. Dit kan handig zijn om de volgende redenen:

*   Als er een wijziging is doorgevoerd in de firewall waardoor RDP-verbindingen zijn mislukt, kunt u het probleem oplossen met behulp van de functie voor aangepaste script extensies.

*   Uitschakelen van alle Firewall profielen is een Foolproof manier om problemen op te lossen dan het instellen van de RDP-specifieke firewall regel.

## <a name="solution"></a>Oplossing

Hoe u de firewall regels configureert, is afhankelijk van het niveau van toegang tot de vereiste VM. De volgende voor beelden gebruiken RDP-regels. Dezelfde methoden kunnen echter worden toegepast op elk ander type verkeer door de juiste register sleutel te wijzen.

### <a name="online-troubleshooting"></a>Online oplossen 

#### <a name="mitigation-1-custom-script-extension"></a>Risico beperking 1: Aangepaste scriptextensie

1.  Maak uw script met behulp van de volgende sjabloon.

    *   Een regel inschakelen:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Een regel uitschakelen:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Upload dit script in de Azure Portal met de functie [aangepaste script extensie](../extensions/custom-script-windows.md) . 

#### <a name="mitigation-2-remote-powershell"></a>Beperking 2: Externe Power shell

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u de Volg oplossingen volgen met behulp van de andere VM.

1.  Open een Power shell-console venster op de virtuele machine voor probleem oplossing.

2.  Voer de volgende opdrachten uit, indien van toepassing.

    *   Een regel inschakelen:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Een regel uitschakelen:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Risico beperking 3: PSTools-opdrachten

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u de Volg oplossingen volgen met behulp van de andere VM.

1.  Down load [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)op de virtuele machine voor probleem oplossing.

2.  Open een CMD-exemplaar en toegang tot de virtuele machine via het interne IP-adres (DIP). 

    * Een regel inschakelen:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Een regel uitschakelen:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Beperking 4: Extern REGI ster

Als de virtuele machine online is en toegankelijk is op een andere VM in hetzelfde virtuele netwerk, kunt u [extern REGI ster](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) gebruiken op de andere VM.

1.  Start de REGI ster-editor (Regedit. exe) op de virtuele machine voor probleem oplossing en selecteer vervolgens **bestand** > **Connect Network Registry**.

2.  Open de *doel machine*\System vertakking en geef vervolgens de volgende waarden op:

    * Als u een regel wilt inschakelen, opent u de volgende register waarde:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Wijzig vervolgens **actief = False** in **actief = True** in de teken reeks:

        **v 2.22 | Actie = toestaan | Actief = waar | Dir = in | Protocol = 6 | Profiel = domein | Profiel = privé | Profiel = openbaar | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = term | Name =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Als u een regel wilt uitschakelen, opent u de volgende register waarde:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Wijzig vervolgens **actief = True** in **actief = onwaar**:
        
        **v 2.22 | Actie = toestaan | Actief = onwaar | Dir = in | Protocol = 6 | Profiel = domein | Profiel = privé | Profiel = openbaar | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = term | Name =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  Start de virtuele machine opnieuw op om de wijzigingen toe te passen.

### <a name="offline-troubleshooting"></a>Offline problemen oplossen 

Als u geen toegang hebt tot de virtuele machine met behulp van een wille keurige methode, mislukt de aangepaste script extensie en moet u in de OFFLINE modus werken door rechtstreeks via de systeem schijf te werken.

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de schijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

1.  [De schijf koppelen aan een virtuele machine voor herstel](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een externe bureaubladverbinding met de virtuele machine voor herstel.

3.  Zorg ervoor dat de schijf is gemarkeerd als **Online** in de Schijfbeheer-console. Houd er rekening mee dat de stationsletter die is toegewezen aan de gekoppelde systeem schijf.

4.  Voordat u wijzigingen aanbrengt, maakt u een kopie van de map \Windows\System32\Config als u de wijzigingen ongedaan wilt maken.

5.  Start de REGI ster-editor (Regedit. exe) op de virtuele machine voor probleem oplossing.

6.  Markeer de sleutel **HKEY_LOCAL_MACHINE** en selecteer vervolgens component **bestand** > **laden** in het menu.

    ![Bregedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Zoek en open het \windows\system32\config\SYSTEM-bestand. 

    > [!Note]
    > U wordt gevraagd een naam op te vragen. Voer **BROKENSYSTEM**in en vouw **HKEY_LOCAL_MACHINE**. Er wordt nu een extra sleutel met de naam **BROKENSYSTEM**weer geven. Voor deze probleem oplossing worden deze probleem componenten als **BROKENSYSTEM**gekoppeld.

8.  Breng de volgende wijzigingen aan op de BROKENSYSTEM-vertakking:

    1.  Controleer de register sleutel van de **ControlSet** waarvan de VM begint. Het sleutel nummer wordt weer geven in HKLM\BROKENSYSTEM\Select\Current.

    2.  Als u een regel wilt inschakelen, opent u de volgende register waarde:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Wijzig vervolgens **actief = False** in **actief = True**.
        
        **v 2.22 | Actie = toestaan | Actief = waar | Dir = in | Protocol = 6 | Profiel = domein | Profiel = privé | Profiel = openbaar | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = term | Name =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Als u een regel wilt uitschakelen, opent u de volgende register sleutel:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Wijzig vervolgens **actief = True** in **actief = onwaar**.
        
        **v 2.22 | Actie = toestaan | Actief = onwaar | Dir = in | Protocol = 6 | Profiel = domein | Profiel = privé | Profiel = openbaar | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = term | Name =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Markeer **BROKENSYSTEM**en selecteer vervolgens component **bestand** > **verwijderen** in het menu.

10. [De schijf loskoppelen en opnieuw maken van de virtuele machine](troubleshoot-recovery-disks-portal-windows.md).

11. Controleer of het probleem opgelost is.

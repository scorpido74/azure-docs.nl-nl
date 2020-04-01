---
title: Een firewallregel in- of uitschakelen op een gastbesturingssysteem in Azure VM | Microsoft Documenten
description: Meer informatie over het gebruik van online of offline externe hulpprogramma's of registerinstellingen om firewallregels voor gastenbesturingssysteem in te schakelen of uit te schakelen op een externe Azure VM.
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
ms.openlocfilehash: e93dbd085ce99b8d555d6b9bb04e7eb6f60de0ee
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422895"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Een firewallregel in een gastbesturingssysteem in- of uitschakelen

In dit artikel vindt u een verwijzing naar het oplossen van problemen met een situatie waarin u vermoedt dat de firewall van het gastbesturingssysteem gedeeltelijk verkeer filtert op een virtuele machine (VM). Dit kan om de volgende redenen nuttig zijn:

*   Als er bewust een wijziging is aangebracht in de firewall waardoor RDP-verbindingen zijn mislukt, kan het probleem worden opgelost door de functie Aangepaste scriptextensie te gebruiken.

*   Het uitschakelen van alle firewallprofielen is een meer waterdichte manier van oplossen van problemen dan het instellen van de RDP-specifieke firewallregel.

## <a name="solution"></a>Oplossing

Hoe u de firewallregels configureert, is afhankelijk van het vereiste toegangsniveau tot de VM. In de volgende voorbeelden worden RDP-regels gebruikt. Echter, dezelfde methoden kunnen worden toegepast op elke andere vorm van verkeer door te wijzen op de juiste registersleutel.

### <a name="online-troubleshooting"></a>Online probleemoplossing 

#### <a name="mitigation-1-custom-script-extension"></a>Mitigatie 1: Aangepaste scriptextensie

1.  Maak uw script met behulp van de volgende sjabloon.

    *   Ga als lid van de regel naar een regel:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Een regel uitschakelen:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Upload dit script in de Azure-portal met de functie [Aangepaste scriptextensie.](../extensions/custom-script-windows.md) 

#### <a name="mitigation-2-remote-powershell"></a>Mitigatie 2: PowerShell op afstand

If the VM is online and can be accessed on another VM on the same virtual network, you can make the follow mitigations by using the other VM.

1.  Open op de VM voor het oplossen van problemen een PowerShell-consolevenster.

2.  Voer de volgende opdrachten uit, naar gelang van het geval.

    *   Ga als lid van de regel naar een regel:
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

#### <a name="mitigation-3-pstools-commands"></a>Mitigatie 3: PSTools-opdrachten

If the VM is online and can be accessed on another VM on the same virtual network, you can make the follow mitigations by using the other VM.

1.  Download [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)op de VM voor het oplossen van problemen.

2.  Open een CMD-exemplaar en krijg toegang tot de VM via het interne IP (DIP). 

    * Ga als lid van de regel naar een regel:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Een regel uitschakelen:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Mitigatie 4: Extern register

If the VM is online and can be accessed on another VM on the same virtual network, you can use [Remote Registry](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) on the other VM.

1.  Start registereditor (regedit.exe) op de VM voor het oplossen van problemen en selecteer **Vervolgens Netwerkregister Bestand** > **verbinden**.

2.  Open de *doelmachine*\SYSTEEMvertakking en geef de volgende waarden op:

    * Als u een regel wilt inschakelen, opent u de volgende registerwaarde:
    
        *DOELMACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\Firewallrules\RemoteDesktop-UserMode-In-TCP
    
        Wijzig **vervolgens Active=FALSE** in **Active=TRUE** in de tekenreeks:

        **v2.22| Actie=Toestaan| Actief=WAAR| Dir=In| Protocol=6| Profiel=Domein| Profiel=Privé| Profiel=Openbaar| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Naam=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**
    
    * Als u een regel wilt uitschakelen, opent u de volgende registerwaarde:
    
        *DOELMACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\Firewallrules\RemoteDesktop-UserMode-In-TCP

        Wijzig vervolgens **Actief =WAAR** in **Actief=ONWAAR:**
        
        **v2.22| Actie=Toestaan| Actief=FALSE| Dir=In| Protocol=6| Profiel=Domein| Profiel=Privé| Profiel=Openbaar| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Naam=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

3.  Start de VM opnieuw om de wijzigingen toe te passen.

### <a name="offline-troubleshooting"></a>Offline probleemoplossing 

Als u geen toegang hebt tot de VM op een manier, mislukt het gebruik van Custom Script Extension en moet u in de OFFLINE-modus werken door rechtstreeks via de systeemschijf te werken.

Voordat u deze stappen volgt, maakt u een momentopname van de systeemschijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

1.  [Koppel de systeemschijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een verbinding met Extern bureaublad met de herstel-vm.

3.  Controleer of de schijf is gemarkeerd als **Online** in de schijfbeheerconsole. Houd er rekening mee dat de stationsletter die is toegewezen aan de gekoppelde systeemschijf.

4.  Maak voordat u wijzigingen aanbrengt een kopie van de map \windows\system32\config voor het geval een terugdraaiing van de wijzigingen noodzakelijk is.

5.  Start registereditor (regedit.exe) op de VM voor het oplossen van problemen.

6.  Markeer de **HKEY_LOCAL_MACHINE** toets en selecteer **Vervolgens Bijenkorf voor bestand** > **laden** in het menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Zoek en open vervolgens het \windows\system32\config\SYSTEM-bestand. 

    > [!Note]
    > U wordt gevraagd om een naam. Voer **BROKENSYSTEM**in en vouw **HKEY_LOCAL_MACHINE**uit . U ziet nu een extra sleutel die **BROKENSYSTEM**heet. Voor dit probleemoplossing, zijn we de montage van deze probleem bijenkasten als **BROKENSYSTEM**.

8.  Breng de volgende wijzigingen aan op de BROKENSYSTEM-vertakking:

    1.  Controleer vanaf welke **Registersleutel ControlSet** de VM begint. U ziet het sleutelnummer in HKLM\BROKENSYSTEM\Select\Current.

    2.  Als u een regel wilt inschakelen, opent u de volgende registerwaarde:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\Firewallrules\RemoteDesktop-UserMode-In-TCP
        
        Wijzig vervolgens **Active=FALSE** in **Actief=Waar**.
        
        **v2.22| Actie=Toestaan| Actief=WAAR| Dir=In| Protocol=6| Profiel=Domein| Profiel=Privé| Profiel=Openbaar| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Naam=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

    3.  Als u een regel wilt uitschakelen, opent u de volgende registersleutel:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\Firewallrules\RemoteDesktop-UserMode-In-TCP

        Wijzig vervolgens **Actief=Waar** in **Actief=FALSE**.
        
        **v2.22| Actie=Toestaan| Actief=FALSE| Dir=In| Protocol=6| Profiel=Domein| Profiel=Privé| Profiel=Openbaar| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Naam=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

9.  Markeer **BROKENSYSTEM**en selecteer**Hive voor het uitladen van** **bestanden** > in het menu.

10. [Maak de systeemschijf los en maak de VM opnieuw.](troubleshoot-recovery-disks-portal-windows.md)

11. Controleer of het probleem is opgelost.

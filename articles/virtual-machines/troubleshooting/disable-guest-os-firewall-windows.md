---
title: De gastOS Firewall uitschakelen in Azure VM | Microsoft Documenten
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
ms.openlocfilehash: dbb9b0f865c7ec5d9d29e2310ae41abbec287bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464959"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>De firewall van het gastbesturingssysteem uitschakelen in Azure VM

In dit artikel wordt een verwijzing gemaakt naar situaties waarin u vermoedt dat de firewall van het gastbesturingssysteem gedeeltelijk of volledig verkeer naar een virtuele machine (VM) filtert. Dit kan gebeuren als er opzettelijk wijzigingen zijn aangebracht in de firewall waardoor RDP-verbindingen zijn mislukt.

## <a name="solution"></a>Oplossing

Het proces dat in dit artikel wordt beschreven, is bedoeld om te worden gebruikt als tijdelijke oplossing, zodat u zich concentreren op het oplossen van uw echte probleem, dat is hoe u de firewallregels correct instellen. Het is een Aanbevolen Praktijk van Microsoft om de Windows Firewall-component ingeschakeld te hebben. Hoe u de firewallregels configureert, is afhankelijk van het vereiste toegangsniveau tot de VM.

### <a name="online-solutions"></a>Online oplossingen 

If the VM is online and can be accessed on another VM on the same virtual network, you can make these mitigations by using the other VM.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Mitigatie 1: Functie Aangepaste scriptextensie of opdracht uitvoeren

Als u een werkende Azure-agent hebt, u [aangepaste scriptextensie](../extensions/custom-script-windows.md) of de functie [Opdrachten uitvoeren](../windows/run-command.md) (alleen Resource Manager VM's) gebruiken om op afstand de volgende scripts uit te voeren.

> [!Note]
> * Als de firewall lokaal is ingesteld, voert u het volgende script uit:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Als de firewall is ingesteld via een Active Directory-beleid, u het volgende script gebruiken voor tijdelijke toegang. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Zodra het beleid echter opnieuw wordt toegepast, wordt u uit de externe sessie gezet. De permanente oplossing voor dit probleem is het wijzigen van het beleid dat op deze computer wordt toegepast.

#### <a name="mitigation-2-remote-powershell"></a>Mitigatie 2: PowerShell op afstand

1.  Maak verbinding met een vm die zich op hetzelfde virtuele netwerk bevindt als de VM die u niet bereiken met behulp van rdp-verbinding.

2.  Open een PowerShell-consolevenster.

3.  Voer de volgende opdrachten uit:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Als de firewall is ingesteld via een groepsbeleidsobject, werkt deze methode mogelijk niet omdat deze opdracht alleen de lokale registervermeldingen wijzigt. Als er een beleid is ingevoerd, wordt deze wijziging overschreven. 

#### <a name="mitigation-3-pstools-commands"></a>Mitigatie 3: PSTools-opdrachten

1.  Download [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)op de VM voor het oplossen van problemen.

2.  Open een CMD-exemplaar en open de VM via de DIP.

3.  Voer de volgende opdrachten uit:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Mitigatie 4: Extern register 

Volg deze stappen om [Extern register](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)te gebruiken.

1.  Start registereditor op de VM voor probleemoplossing en ga vervolgens naar **Netwerkregister Bestand** > **verbinden.**

2.  Open de *doelmachine*\SYSTEEMvertakking en geef de volgende waarden op:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Start de service opnieuw. Omdat u dat niet doen met behulp van het externe register, moet u Remote Service Console gebruiken.

4.  Open een exemplaar van **Services.msc**.

5.  Klik **op Services (lokaal)**.

6.  Selecteer **Verbinding maken met een andere computer**.

7.  Voer het **privé-IP-adres (DIP)** van de probleem-VM in.

8.  Start het lokale firewallbeleid opnieuw.

9.  Probeer opnieuw verbinding te maken met de VM via RDP vanaf uw lokale computer.

### <a name="offline-solutions"></a>Offline oplossingen 

Als u een situatie hebt waarin u de VM op geen enkele manier bereiken, mislukt de Aangepaste Script-extensie en moet u in de OFFLINE-modus werken door rechtstreeks via de systeemschijf te werken. Voer hiervoor de volgende stappen uit:

1.  [Koppel de systeemschijf aan een herstelvm](troubleshoot-recovery-disks-portal-windows.md).

2.  Start een verbinding met Extern bureaublad met de herstel-vm.

3.  Controleer of de schijf is gemarkeerd als Online in de schijfbeheerconsole. Let op de stationsletter die is toegewezen aan de gekoppelde systeemschijf.

4.  Maak voordat u wijzigingen aanbrengt een kopie van de map \windows\system32\config voor het geval een terugdraaiing van de wijzigingen noodzakelijk is.

5.  Start de registereditor (regedit.exe) op de VM voor het oplossen van problemen. 

6.  Voor deze probleemoplossingsprocedure monteren we de bijenkasten als BROKENSYSTEM en BROKENSOFTWARE.

7.  Markeer de HKEY_LOCAL_MACHINE-toets en selecteer Vervolgens > Bijenkast bestand in het menu.

8.  Zoek het \windows\system32\config\SYSTEM-bestand op de bijgevoegde systeemschijf.

9.  Open een verhoogde PowerShell-instantie en voer de volgende opdrachten uit:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Maak de systeemschijf los en maak de VM opnieuw.](troubleshoot-recovery-disks-portal-windows.md)

11. Controleer of het probleem is opgelost.

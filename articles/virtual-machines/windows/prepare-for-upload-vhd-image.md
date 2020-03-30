---
title: Een Windows VHD voorbereiden om te uploaden naar Azure
description: Meer informatie over het voorbereiden van een Windows VHD of VHDX om deze te uploaden naar Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250190"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure

Voordat u een Virtuele Windows-machine (VM) uploadt van on-premises naar Azure, moet u de virtuele harde schijf (VHD of VHDX) voorbereiden. Azure ondersteunt zowel generatie 1- als generatie2 VM's die zich in VHD-bestandsindeling bevinden en die een schijf met een vaste grootte hebben. De maximaal toegestane grootte voor de VHD is 1.023 GB. 

In een generatie 1 VM u een VHDX-bestandssysteem converteren naar VHD. U ook een dynamisch uitbreidende schijf converteren naar een schijf met een vaste grootte. Maar je de generatie van een VM niet veranderen. Zie [Moet ik een generatie 1 of 2 VM maken in Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) en [Azure-ondersteuning voor generatie 2 VM's (preview).](generation-2.md)

Zie [Microsoft-serversoftwareondersteuning voor Azure VM's voor](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)informatie over het ondersteuningsbeleid voor Azure VM's.

> [!NOTE]
> De instructies in dit artikel zijn van toepassing op:
>1. De 64-bits versie van Windows Server 2008 R2 en later Windows Server-besturingssystemen. Zie [Ondersteuning voor 32-bits besturingssystemen in Azure voor](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)informatie over het uitvoeren van een 32-bits besturingssysteem in Azure.
>2. Als er een hulpprogramma voor herstel na noodgevallen wordt gebruikt om de werkbelasting te migreren, zoals Azure Site Recovery of Azure Migrate, moet dit proces nog steeds worden uitgevoerd en gevolgd op het besturingssysteem van de gast om de afbeelding voorafgaand aan de migratie voor te bereiden.

## <a name="system-file-checker-sfc-command"></a>SFC (System File Checker), opdracht

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Windows System File Checker-hulpprogramma uitvoeren (sfc /scannow uitvoeren) op het besturingssysteem voordat de generalisatiestap van het maken van een afbeelding van het besturingssysteem van de klant wordt uitgevoerd

De opdracht System File Checker (SFC) wordt gebruikt om Windows-systeembestanden te verifiëren en te vervangen.

Ga als u de opdracht SFC uitvoert:

1. Open een verhoogde CMD-prompt als administrator.
1. Typ `sfc /scannow` en selecteer **Enter**.

    ![ Systeembestandscontrole](media/prepare-for-upload-vhd-image/system-file-checker.png)


Nadat de SFC-scan is voltooid, probeert u Windows-updates te installeren en de computer opnieuw op te starten.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>De virtuele schijf converteren naar een vaste grootte en naar VHD

Als u uw virtuele schijf wilt converteren naar de vereiste indeling voor Azure, gebruikt u een van de methoden in deze sectie:

1. Maak een back-up van de vm voordat u het virtuele schijfconversieproces uitvoert.

1. Zorg ervoor dat de Windows VHD correct werkt op de lokale server. Los eventuele fouten binnen de VM zelf op voordat u deze probeert te converteren of uploaden naar Azure.

1. Met betrekking tot de grootte van de VHD:

   1. Alle VHD's op Azure moeten een virtuele grootte hebben die is uitgelijnd op 1 MB. Bij het converteren van een ruwe schijf naar VHD moet u ervoor zorgen dat de ruwe schijfgrootte een veelvoud van 1 MB is voor conversie. Fracties van een megabyte zullen fouten veroorzaken bij het maken van afbeeldingen van de geüploade VHD.

   2. De maximale grootte voor de OS VHD is 2TB.


Nadat u de schijf hebt geconverteerd, maakt u een VM die de schijf gebruikt. Start en meld u aan bij de VM om de voorbereiding voor het uploaden af te ronden.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V-beheer gebruiken om de schijf om te zetten 
1. Open Hyper-V Manager en selecteer uw lokale computer aan de linkerkant. Selecteer In het menu boven de computerlijst de optie **Actieschijf** > **bewerken**.
2. Selecteer **op** de pagina Virtuele harde schijf zoeken uw virtuele schijf.
3. Selecteer Op de pagina **Actie kiezen** de optie**Volgende** **converteren** > .
4. Als u wilt converteren vanuit VHDX, selecteert u **VHD** > **Next**.
5. Als u wilt converteren vanaf een dynamisch uitdijende schijf, selecteert u **Volgende vaste grootte** > **.**
6. Zoek en selecteer een pad om het nieuwe VHD-bestand op te slaan.
7. Selecteer **Finish**.

> [!NOTE]
> Gebruik een verhoogde PowerShell-sessie om de opdrachten in dit artikel uit te voeren.

### <a name="use-powershell-to-convert-the-disk"></a>PowerShell gebruiken om de schijf om te zetten 
U een virtuele schijf converteren met de opdracht [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) in Windows PowerShell. Selecteer **Uitvoeren als beheerder** wanneer u PowerShell start. 

Met de volgende opdracht wordt de schijf geconverteerd van VHDX naar VHD. De opdracht converteert de schijf ook van een dynamisch uitdijende schijf naar een schijf met een vaste grootte.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Vervang in deze opdracht `-Path` de waarde voor het pad naar de virtuele harde schijf die u wilt converteren. Vervang de `-DestinationPath` waarde voor het nieuwe pad en de naam van de geconverteerde schijf.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converteren vanuit VMware VMDK-schijfindeling
Als u een Windows VM-afbeelding in de [VMDK-bestandsindeling hebt,](https://en.wikipedia.org/wiki/VMDK)gebruikt u de [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) om deze om te zetten naar VHD-indeling. Zie [Een VMware VMDK converteren naar Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)voor meer informatie.

## <a name="set-windows-configurations-for-azure"></a>Windows-configuraties instellen voor Azure

> [!NOTE]
> Azure-platform monteert een ISO-bestand aan de DVD-ROM wanneer een Windows-VM wordt gemaakt op basis van een algemene afbeelding.
> Om deze reden moet de DVD-ROM zijn ingeschakeld in het besturingssysteem in de algemene afbeelding. Als deze is uitgeschakeld, blijft de Windows VM steken op OOBE.

Voer op de VM die u wilt uploaden naar Azure de volgende opdrachten uit vanuit een [snelopdrachtpromptvenster:](https://technet.microsoft.com/library/cc947813.aspx)

1. Verwijder een statische permanente route in de routeringstabel:
   
   * Als u de routetabel wilt bekijken, voert u `route print` de opdrachtprompt uit.
   * Controleer `Persistence Routes` de secties. Als er een permanente route `route delete` is, gebruikt u de opdracht om deze te verwijderen.
2. Verwijder de winhttp-proxy:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Als de VM met een specifieke proxy moet werken, voegt u een proxyuitzondering toe aan het Azure IP-adres ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), zodat de VM verbinding kan maken met Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Stel het SAN-beleid voor schijven in op: [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)
   
    ```PowerShell
    diskpart 
    ```
    Typ in het venster Opdrachtprompt openen de volgende opdrachten:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Stel de UTC-tijd (Coordinated Universal Time) in voor Windows. Stel ook het opstarttype van`w32time`de `Automatic`Windows-tijdservice ( ) in op:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Stel het energieprofiel in op hoge prestaties:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Controleer of de `TEMP` omgevingsvariabelen zijn ingesteld op `TMP` hun standaardwaarden:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>De Windows-services controleren
Controleer of elk van de volgende Windows-services is ingesteld op de standaardwaarden van Windows. Deze services zijn het minimum dat moet worden ingesteld om VM-connectiviteit te garanderen. Voer de volgende opdrachten uit om de opstartinstellingen opnieuw in te stellen:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Instellingen voor extern bureaublad-register bijwerken
Controleer of de volgende instellingen correct zijn geconfigureerd voor externe toegang:

>[!NOTE] 
>Mogelijk ontvangt u een foutbericht wanneer u . `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>` U kunt dit bericht veilig negeren. Het betekent alleen dat het domein die configuratie niet door een groepsbeleidsobject duwt.

1. Rdp (Remote Desktop Protocol) is ingeschakeld:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. De RDP-poort is correct ingesteld. De standaardpoort is 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Wanneer u een vm implementeert, worden de standaardregels gemaakt tegen poort 3389. Als u het poortnummer wilt wijzigen, doet u dat nadat de VM is geïmplementeerd in Azure.

3. De luisteraar luistert in elke netwerkinterface:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Configureer de NLA-modus (Network-level authentication) voor de RDP-verbindingen:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Stel de waarde in leven:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Sluit:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Beperk het aantal gelijktijdige verbindingen:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Verwijder alle zelfondertekende certificaten die zijn gekoppeld aan de RDP-listener:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Deze code zorgt ervoor dat u aan het begin verbinding maken wanneer u de VM implementeert. Als u dit later moet controleren, u dit doen nadat de VM is geïmplementeerd in Azure.

9. Als de vm deel uitmaakt van een domein, controleert u het volgende beleid om te controleren of de oude instellingen niet worden teruggezet. 
    
    | Doel                                     | Beleid                                                                                                                                                       | Waarde                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP is ingeschakeld                           | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Componenten\Extern bureaublad-services\Extern bureaublad-sessiehost\Verbindingen         | Gebruikers toestaan op afstand verbinding te maken met Extern bureaublad                                  |
    | NLA-groepsbeleid                         | Instellingen\Beheersjablonen\Componenten\Extern bureaublad-services\Extern bureaublad-sessiehost\Beveiliging                                                    | Gebruikersverificatie vereisen voor externe toegang met NLA |
    | Keep-alive instellingen                      | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Windows-onderdelen\Extern bureaublad-services\Extern bureaublad-sessiehost\Verbindingen | Verbindingsinterval in leven houden configureren                                                 |
    | Instellingen opnieuw verbinden                       | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Windows-onderdelen\Extern bureaublad-services\Extern bureaublad-sessiehost\Verbindingen | Automatisch opnieuw verbinding maken                                                                   |
    | Beperkt aantal verbindingsinstellingen | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Windows-onderdelen\Extern bureaublad-services\Extern bureaublad-sessiehost\Verbindingen | Aantal verbindingen beperken                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows Firewall-regels configureren
1. Windows Firewall inschakelen op de drie profielen (domein, standaard en openbaar):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Voer de volgende opdracht uit in PowerShell om WinRM toe te staan via de drie firewallprofielen (domein, privé en openbaar) en schakel de externe PowerShell-service in:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Schakel de volgende firewallregels in om het RDP-verkeer toe te staan:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Schakel de regel in voor het delen van bestanden en printers, zodat de VM kan reageren op een ping-opdracht in het virtuele netwerk:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Maak een regel voor het Azure-platformnetwerk:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Als de vm deel uitmaakt van een domein, controleert u het volgende Azure AD-beleid om te controleren of de eerdere instellingen niet worden teruggezet. 

    | Doel                                 | Beleid                                                                                                                                                  | Waarde                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | De Windows Firewall-profielen inschakelen | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Netwerk\Netwerkverbinding\Windows Firewall\Domeinprofiel\Windows Firewall   | Alle netwerkverbindingen beveiligen         |
    | RDP inschakelen                           | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Netwerk\Netwerkverbinding\Windows Firewall\Domeinprofiel\Windows Firewall   | Uitzonderingen op binnenkomend extern bureaublad toestaan |
    |                                      | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Netwerk\Netwerkverbinding\Windows Firewall\Standaardprofiel\Windows Firewall | Uitzonderingen op binnenkomend extern bureaublad toestaan |
    | ICMP-V4 inschakelen                       | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Netwerk\Netwerkverbinding\Windows Firewall\Domeinprofiel\Windows Firewall   | ICMP-uitzonderingen toestaan                   |
    |                                      | Computerconfiguratie\Beleid\Windows-instellingen\Beheersjablonen\Netwerk\Netwerkverbinding\Windows Firewall\Standaardprofiel\Windows Firewall | ICMP-uitzonderingen toestaan                   |

## <a name="verify-the-vm"></a>De VM verifiëren 

Controleer of de VM gezond, veilig en RDP-toegankelijk is: 

1. Controleer de schijf bij de volgende virtuele machineopnieuw start:

    ```PowerShell
    Chkdsk /f
    ```
    Zorg ervoor dat in het rapport een schone en gezonde schijf wordt weergegeven.

2. Stel de BCD-instellingen (Boot Configuration Data) in. 

    > [!NOTE]
    > Gebruik een verhoogd PowerShell-venster om deze opdrachten uit te voeren.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Het dumplogboek kan handig zijn bij het oplossen van problemen met Windows-crash. Schakel de dumplogboekverzameling in:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Controleer of de WMI-opslagplaats (Windows Management Instrumentation) consistent is:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Als de repository beschadigd is, raadpleegt u [WMI: Corruptie in opslagplaats of niet](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Zorg ervoor dat geen enkele andere toepassing poort 3389 gebruikt. Deze poort wordt gebruikt voor de RDP-service in Azure. Voer het op om te zien `netstat -anob`welke poorten op de VM worden gebruikt:

    ```PowerShell
    netstat -anob
    ```

6. Ga als lid van het uploaden van een Windows VHD dat een domeincontroller is:

   * Volg [deze extra stappen](https://support.microsoft.com/kb/2904015) om de schijf voor te bereiden.

   * Zorg ervoor dat u het DSRM-wachtwoord (Directory Services Restore Mode) kent voor het geval u de VM op een bepaald moment in DSRM moet starten. Zie [Een DSRM-wachtwoord instellen](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)voor meer informatie.

7. Zorg ervoor dat u het ingebouwde beheerdersaccount en wachtwoord kent. Misschien wilt u het huidige wachtwoord voor lokale beheerders opnieuw instellen en ervoor zorgen dat u dit account gebruiken om u aan te melden bij Windows via de RDP-verbinding. Deze toegangsmachtiging wordt beheerd door het groepsbeleidsobject 'Aanmelden toestaan via Extern bureaublad-services'. Bekijk dit object in de lokale groepsbeleidseditor hier:

    Computerconfiguratie\Windows-instellingen\Beveiligingsinstellingen\Lokaal beleid\Toewijzing gebruikersrechten

8. Controleer het volgende Azure AD-beleid om ervoor te zorgen dat u uw RDP-toegang niet blokkeert via RDP of vanuit het netwerk:

    - Computerconfiguratie\Windows-instellingen\Beveiligingsinstellingen\Lokaal beleid\Toewijzing voor gebruikersrechten\Toegang tot deze computer weigeren vanuit het netwerk

    - Computerconfiguratie\Windows-instellingen\Beveiligingsinstellingen\Lokaal beleid\Toewijzing voor gebruikersrechten\Aanmelden weigeren via Extern bureaublad-services


9. Controleer het volgende Azure AD-beleid om ervoor te zorgen dat u geen van de vereiste toegangsaccounts verwijdert:

   - Computerconfiguratie\Windows-instellingen\Beveiligingsinstellingen\Lokaal beleid\Toewijzing voor gebruikersrechten\Toegang tot deze computer vanuit het netwerk

   In het beleid moeten de volgende groepen worden vermeld:

   - Beheerders

   - Back-upoperators

   - Iedereen

   - Gebruikers

10. Start de VM opnieuw op om ervoor te zorgen dat Windows nog steeds gezond is en bereikbaar is via de RDP-verbinding. Op dit punt u een VM maken in uw lokale Hyper-V om ervoor te zorgen dat de VM volledig wordt gestart. Test vervolgens om te zien of u de VM via RDP bereiken.

11. Verwijder eventuele extra Transport Driver Interface (TDI) filters. Verwijder bijvoorbeeld software die TCP-pakketten of extra firewalls analyseert. Als u dit later moet controleren, u dit doen nadat de VM is geïmplementeerd in Azure.

12. Verwijder andere software of stuurprogramma's van derden die gerelateerd zijn aan fysieke componenten of andere virtualisatietechnologie.

### <a name="install-windows-updates"></a>Windows-updates installeren
Idealiter moet u de machine op *het patchniveau up-to-date*houden. Als dit niet mogelijk is, moet u ervoor zorgen dat de volgende updates zijn geïnstalleerd. Zie de pagina's windows updategeschiedenis: [Windows 10 en Windows Server 2019,](https://support.microsoft.com/help/4000825) [Windows 8.1 en Windows Server 2012 R2](https://support.microsoft.com/help/4009470) en [Windows 7 SP1 en Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469)voor de laatste updates.

| Onderdeel               | Binair         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Netwerk                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Kern                    | Ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Externe bureaubladservices | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Beveiliging                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Om een onbedoelde herstart tijdens vm-provisioning te voorkomen, raden we u aan ervoor te zorgen dat alle Windows Update-installaties zijn voltooid en dat er geen updates in behandeling zijn. Een manier om dit te doen is om alle mogelijke Windows-updates te installeren en opnieuw op te starten voordat u de opdracht Sysprep uitvoert.

### <a name="determine-when-to-use-sysprep"></a>Bepalen wanneer Sysprep moet worden gebruikt<a id="step23"></a>    

System Preparation Tool (Sysprep) is een proces dat u uitvoeren om een Windows-installatie opnieuw in te stellen. Sysprep biedt een "out of the box" ervaring door het verwijderen van alle persoonlijke gegevens en het resetten van verschillende componenten. 

U voert sysprep meestal uit om een sjabloon te maken waaruit u verschillende andere VM's implementeren die een specifieke configuratie hebben. De sjabloon wordt een *algemene afbeelding*genoemd.

Als u slechts één vm vanaf één schijf wilt maken, hoeft u Sysprep niet te gebruiken. In plaats daarvan u de VM maken op basis van een *gespecialiseerde afbeelding.* Zie voor informatie over het maken van een vm vanaf een gespecialiseerde schijf:

- [Een VM maken van een gespecialiseerde schijf](create-vm-specialized.md)
- [Een VM maken op basis van een gespecialiseerde VHD-schijf](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Als u een algemene afbeelding wilt maken, moet u Sysprep uitvoeren. Zie [Hoe sysprep: een inleiding te gebruiken](https://technet.microsoft.com/library/bb457073.aspx)voor meer informatie. 

Niet elke rol of toepassing die op een Windows-computer is geïnstalleerd, ondersteunt algemene afbeeldingen. Dus voordat u deze procedure uitvoert, moet u ervoor zorgen dat Sysprep de rol van de computer ondersteunt. Zie [Sysprep-ondersteuning voor serverrollen voor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)meer informatie.

### <a name="generalize-a-vhd"></a>Een VHD generaliseren

>[!NOTE]
> Nadat u `sysprep.exe` de volgende stappen hebt uitgevoerd, schakelt u de vm uit. Schakel deze pas weer in als u er een afbeelding van maakt in Azure.

1. Meld u aan bij de Windows VM.
1. **Opdrachtprompt uitvoeren** als beheerder. 
1. De map `%windir%\system32\sysprep`wijzigen in . Voer vervolgens `sysprep.exe` uit.
1. In het dialoogvenster **Hulpprogramma voor systeemvoorbereiding** selecteert u **OOBE (Out-of-Box Experience) van systeem starten** en zorgt u dat het selectievakje **Generaliseren** is ingeschakeld.

    ![Tool voor systeemvoorbereiding](media/prepare-for-upload-vhd-image/syspre.png)
1. Selecteer **Afsluiten**opties in **Afsluiten opties**.
1. Selecteer **OK**.
1. Wanneer Sysprep klaar is, sluit u de VM af. Gebruik Opnieuw **starten** niet om de vm af te sluiten.

Nu is de VHD klaar om geüpload te worden. Zie [Een gegeneraliseerde VHD uploaden en gebruiken om een nieuwe vm in Azure te maken voor](sa-upload-generalized.md)meer informatie over het maken van een vm vanaf een gegeneraliseerde schijf.


>[!NOTE]
> Een aangepast *bestand unattend.xml* wordt niet ondersteund. Hoewel we de `additionalUnattendContent` eigenschap wel ondersteunen, biedt dat slechts beperkte ondersteuning voor het toevoegen van opties voor het instellen van [Microsoft-Windows-Shell](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) in het *bestand unattend.xml* dat de Azure-inrichtingsagent gebruikt. U bijvoorbeeld [extraUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) gebruiken om FirstLogonCommands en LogonCommands toe te voegen. Zie voor meer informatie [het voorbeeld van UnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>De aanbevolen configuraties voltooien
De volgende instellingen hebben geen invloed op het uploaden van VHD. We raden u echter ten zeerste aan ze te configureren.

* Installeer de [Azure Virtual Machine Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vervolgens u VM-extensies inschakelen. De VM-extensies implementeren de meeste kritieke functionaliteit die u mogelijk met uw VM's wilt gebruiken. U hebt de extensies nodig om bijvoorbeeld wachtwoorden opnieuw in te stellen of RDP te configureren. Zie overzicht [van Azure Virtual Machine Agent](../extensions/agent-windows.md)voor meer informatie.
* Nadat u de VM in Azure hebt gemaakt, raden we u aan het paginabestand op het *temporele schijfvolume* te plaatsen om de prestaties te verbeteren. U de plaatsing van het bestand als volgt instellen:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Als een gegevensschijf aan de VM is gekoppeld, is de letter van het tijdelijke stationvolume doorgaans *D*. Deze aanduiding kan anders zijn, afhankelijk van uw instellingen en het aantal beschikbare stations.
  * We raden u aan scriptblokkers uit te schakelen die mogelijk worden geleverd door antivirussoftware. Ze kunnen de Windows Provisioning Agent-scripts blokkeren die zijn uitgevoerd wanneer u een nieuwe virtuele machine uit uw afbeelding implementeert.
  
## <a name="next-steps"></a>Volgende stappen
* [Een Windows VM-afbeelding uploaden naar Azure voor hulpprogramma-implementaties](upload-generalized-managed.md)
* [Problemen met azure Windows VM-activering oplossen](troubleshoot-activation-problems.md)


---
title: Een Windows-VHD voorbereiden om te uploaden naar Azure | Microsoft Docs
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
ms.openlocfilehash: d2922f79c0b2ef7098e0f51e0c3bf6ab18a1b0e3
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200293"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Een VHD of VHDX van Windows voorbereiden om te uploaden naar Azure

Voordat u een virtuele Windows-machine (VM) van on-premises naar Azure uploadt, moet u de virtuele harde schijf (VHD of VHDX) voorbereiden. Azure biedt ondersteuning voor virtuele machines van de eerste en tweede generatie die in VHD-bestands indeling zijn en die een schijf met een vaste grootte hebben. De maximale grootte die is toegestaan voor de VHD is 1.023 GB. 

In een virtuele machine van de eerste generatie kunt u een VHDX-bestands systeem converteren naar VHD. U kunt ook een dynamisch uitbreid bare schijf converteren naar een schijf met een vaste grootte. Maar u kunt de generatie van een virtuele machine niet wijzigen. Zie [moet ik een generatie 1 of 2 virtuele machine in Hyper-V maken](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) en [Azure-ondersteuning voor vm's van de tweede generatie (preview)](generation-2.md)voor meer informatie.

Zie [micro soft-server software ondersteuning voor Azure-vm's](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)voor meer informatie over het ondersteunings beleid voor virtuele Azure-machines.

> [!NOTE]
> De instructies in dit artikel zijn van toepassing op:
>1. De 64-bits versie van Windows Server 2008 R2 en latere Windows Server-besturings systemen. Voor informatie over het uitvoeren van een 32-bits besturings systeem in azure, Zie [ondersteuning voor 32-bits besturings systemen in azure vm's](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Als een hulp programma voor herstel na nood gevallen wordt gebruikt voor het migreren van de werk belasting, zoals Azure Site Recovery of Azure Migrate, moet dit proces nog steeds worden uitgevoerd en worden gevolgd op het gast besturingssysteem om de installatie kopie voor te bereiden vóór de migratie.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>De virtuele schijf converteren naar een vaste grootte en naar een VHD

Als u de virtuele schijf moet converteren naar de vereiste indeling voor Azure, gebruikt u een van de methoden in deze sectie:

1. Maak een back-up van de VM voordat u het conversie proces voor de virtuele schijf uitvoert.

1. Zorg ervoor dat de VHD met Windows correct werkt op de lokale server. Los eventuele fouten op in de virtuele machine zelf voordat u deze converteert of uploadt naar Azure.

1. Met betrekking tot de grootte van de VHD:

   1. Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1 MB is vóór de conversie. Fracties van een Mega byte veroorzaken fouten bij het maken van installatie kopieën van de geüploade VHD.

   2. De maximale grootte die is toegestaan voor de besturingssysteem-VHD is 2 TB.


Nadat u de schijf hebt geconverteerd, maakt u een virtuele machine die gebruikmaakt van de schijf. Start en meld u aan bij de virtuele machine om deze voor te bereiden voor het uploaden.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V-beheer gebruiken om de schijf te converteren 
1. Open Hyper-V-beheer en selecteer uw lokale computer aan de linkerkant. Selecteer in het menu boven de computer lijst **actie** > **bewerken schijf**.
2. Selecteer uw virtuele schijf op de pagina **virtuele harde schijf zoeken** .
3. Selecteer op de pagina **actie kiezen** de optie**volgende** **omzetten** > .
4. Als u wilt converteren van VHDX, selecteert u **VHD** > **volgende**.
5. Als u wilt converteren van een dynamisch uitbreid bare schijf, selecteert u de optie **vaste grootte** > **volgende**.
6. Zoek en selecteer een pad om het nieuwe VHD-bestand op te slaan.
7. Selecteer **Finish**.

> [!NOTE]
> Gebruik een Power shell-sessie met verhoogde bevoegdheden om de opdrachten in dit artikel uit te voeren.

### <a name="use-powershell-to-convert-the-disk"></a>Power shell gebruiken om de schijf te converteren 
U kunt een virtuele schijf converteren met behulp van de [Convert-VHD-](https://technet.microsoft.com/library/hh848454.aspx) opdracht in Windows Power shell. Selecteer als **administrator uitvoeren** wanneer u Power shell start. 

Met de volgende voorbeeld opdracht wordt de schijf geconverteerd van VHDX naar VHD. Met deze opdracht wordt de schijf ook geconverteerd van een dynamisch uitbreid bare schijf naar een schijf met een vaste grootte.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Vervang in deze opdracht de waarde voor `-Path` door het pad naar de virtuele harde schijf die u wilt converteren. Vervang de waarde voor `-DestinationPath` door het nieuwe pad en de naam van de geconverteerde schijf.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converteren van VMware VMDK-schijf indeling
Als u een Windows VM-installatie kopie in de [VMDK-bestands indeling](https://en.wikipedia.org/wiki/VMDK)hebt, gebruikt u het [conversie programma van micro soft virtual machine](https://www.microsoft.com/download/details.aspx?id=42497) om het te converteren naar de VHD-indeling. Zie [How to convert a VMware VMDK to Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)(Engelstalig) voor meer informatie.

## <a name="set-windows-configurations-for-azure"></a>Windows-configuraties voor Azure instellen

Voer de volgende opdrachten uit vanaf een [opdracht prompt venster met verhoogde bevoegdheden](https://technet.microsoft.com/library/cc947813.aspx)op de virtuele machine die u wilt uploaden naar Azure:

1. Alle statische permanente routes in de routerings tabel verwijderen:
   
   * Als u de route tabel wilt weer `route print` geven, voert u uit vanaf de opdracht prompt.
   * Controleer de `Persistence Routes` secties. Als er een permanente route is, gebruikt u `route delete` de opdracht om deze te verwijderen.
2. De WinHTTP-proxy verwijderen:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Als de virtuele machine moet werken met een specifieke proxy, voegt u een proxy uitzondering toe aan het Azure IP[-](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)adres (168.63.129.16) zodat de virtuele machine verbinding kan maken met Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Stel het SAN-beleid van [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)de schijf in op:
   
    ```PowerShell
    diskpart 
    ```
    Typ de volgende opdrachten in het venster opdracht prompt openen:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. UTC-tijd (Coordinated Universal Time) instellen voor Windows. Stel ook het opstart type van de Windows Time-service`w32time`() `Automatic`in op:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Stel het energie profiel in op hoge prestaties:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Zorg ervoor dat de omgevings `TMP` variabelen `TEMP` en zijn ingesteld op de standaard waarden:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Controleer de Windows-Services
Zorg ervoor dat elk van de volgende Windows-Services is ingesteld op de Windows-standaard waarden. Deze services zijn het minimale aantal dat moet worden ingesteld om verbinding te maken met de virtuele machine. Voer de volgende opdrachten uit om de opstart instellingen opnieuw in te stellen:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Register instellingen voor extern bureau blad bijwerken
Controleer of de volgende instellingen juist zijn geconfigureerd voor externe toegang:

>[!NOTE] 
>Er wordt mogelijk een fout bericht weer gegeven wanneer `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name <object name> -value <value>`u uitvoert. U kunt dit bericht veilig negeren. Dit betekent alleen dat het domein de configuratie niet pusht via een groepsbeleid-object.

1. Remote Desktop Protocol (RDP) is ingeschakeld:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. De RDP-poort is correct ingesteld. De standaard poort is 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Wanneer u een virtuele machine implementeert, worden de standaard regels gemaakt op basis van poort 3389. Als u het poort nummer wilt wijzigen, doet u dat nadat de virtuele machine in Azure is geïmplementeerd.

3. De listener luistert in elke netwerk interface:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Configureer de modus voor verificatie op netwerk niveau (NLA) voor de RDP-verbindingen:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Keep-Alive-waarde instellen:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Opnieuw verbinding
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Het aantal gelijktijdige verbindingen beperken:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Alle zelfondertekende certificaten die zijn gekoppeld aan de RDP-listener verwijderen:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Deze code zorgt ervoor dat u verbinding kunt maken met het begin wanneer u de virtuele machine implementeert. Als u dit later wilt controleren, kunt u dit doen nadat de virtuele machine in Azure is geïmplementeerd.

9. Als de virtuele machine deel uitmaakt van een domein, controleert u het volgende beleid om er zeker van te zijn dat de oorspronkelijke instellingen niet worden teruggedraaid. 
    
    | Doel                                     | Beleid                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP is ingeschakeld                           | Computer Ga Settings\Administrative Templates\Components\Remote Desktop, bureaublad-services\Extern Desktop Session Host\Connections         | Gebruikers toestaan om extern verbinding te maken met behulp van Extern bureaublad                                  |
    | NLA-groeps beleid                         | Settings\Administrative Templates\Components\Remote Desktop Host\Security                                                    | Gebruikers verificatie vereisen voor externe toegang met behulp van NLA |
    | Keep-Alive instellingen                      | Computer Ga Settings\Administrativee bureaublad-services\Extern bureau blad-sessie Host\Connections | Keep-Alive-verbindings interval configureren                                                 |
    | Instellingen opnieuw verbinden                       | Computer Ga Settings\Administrativee bureaublad-services\Extern bureau blad-sessie Host\Connections | Automatisch opnieuw verbinding maken                                                                   |
    | Beperkt aantal verbindings instellingen | Computer Ga Settings\Administrativee bureaublad-services\Extern bureau blad-sessie Host\Connections | Aantal verbindingen beperken                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows Firewall-regels configureren
1. Schakel Windows Firewall in voor de drie profielen (domein, standaard en openbaar):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Voer de volgende opdracht uit in Power shell om WinRM toe te staan via de drie Firewall profielen (domein, privé en openbaar) en de externe Power shell-service in te scha kelen:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Schakel de volgende firewall regels in om het RDP-verkeer toe te staan:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Schakel de regel voor bestands-en printer deling in zodat de virtuele machine kan reageren op een ping-opdracht in het virtuele netwerk:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Als de virtuele machine deel uitmaakt van een domein, controleert u het volgende Azure AD-beleid om er zeker van te zijn dat de oorspronkelijke instellingen niet worden teruggedraaid. 

    | Doel                                 | Beleid                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | De Windows Firewall profielen inschakelen | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Alle netwerk verbindingen beveiligen         |
    | RDP inschakelen                           | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Uitzonde ringen voor binnenkomende Extern bureaublad toestaan |
    |                                      | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Uitzonde ringen voor binnenkomende Extern bureaublad toestaan |
    | Enable ICMP-v4                       | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | ICMP-uitzonde ringen toestaan                   |
    |                                      | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | ICMP-uitzonde ringen toestaan                   |

## <a name="verify-the-vm"></a>De VM controleren 

Zorg ervoor dat de VM in orde, veilig en RDP toegankelijk is: 

1. Controleer de schijf bij de volgende VM opnieuw opstarten om te controleren of de schijf in orde en consistent is.

    ```PowerShell
    Chkdsk /f
    ```
    Zorg ervoor dat het rapport een schone en gezonde schijf bevat.

2. Stel de instellingen voor de Boot Configuration Data (BCD) in. 

    > [!NOTE]
    > Een Power shell-venster met verhoogde bevoegdheid gebruiken om deze opdrachten uit te voeren.
   
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
3. Het dump logboek kan nuttig zijn bij het oplossen van problemen met Windows-crashes. De logboek verzameling dump inschakelen:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Controleer of de opslag plaats van de Windows Management Instrumentation (WMI) consistent is:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Als de opslag plaats is beschadigd, [raadpleegt u WMI: De opslag plaats is](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)beschadigd of niet.

5. Zorg ervoor dat er geen andere toepassing gebruikmaakt van poort 3389. Deze poort wordt gebruikt voor de RDP-service in Azure. Voer het volgende uit `netstat -anob`om te zien welke poorten op de VM worden gebruikt:

    ```PowerShell
    netstat -anob
    ```

6. Een Windows-VHD uploaden die een domein controller is:

   * Volg [deze extra stappen](https://support.microsoft.com/kb/2904015) om de schijf voor te bereiden.

   * Zorg ervoor dat u het wacht woord van Directory Services Restore Mode (DSRM) weet voor het geval u de virtuele machine op een bepaald moment in DSRM moet starten. Zie [een DSRM-wacht woord instellen](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)voor meer informatie.

7. Zorg ervoor dat u het ingebouwde Administrator-account en-wacht woord kent. Mogelijk wilt u het huidige lokale beheerders wachtwoord opnieuw instellen en ervoor zorgen dat u dit account kunt gebruiken om u aan te melden bij Windows via de RDP-verbinding. Deze toegangs machtiging wordt bepaald door het groepsbeleid-object ' Aanmelden via Extern bureaublad-services toestaan '. Bekijk dit object in het Lokale groepsbeleidsobjecteditor hier:

    Beleid\toewijzing voor de toewijzing van de rechten van de computer-Webtaak

8. Controleer het volgende Azure AD-beleid om ervoor te zorgen dat u uw RDP-toegang niet blokkeert via RDP of het netwerk:

    - Beleid\toewijzing-rechten Gebruikersrechten\aanmelden toegang tot deze computer vanaf het netwerk

    - Beleid\toewijzing-rechten Gebruikersrechten\aanmelden meldt u zich aan met Extern bureaublad-services


9. Controleer het volgende Azure AD-beleid om ervoor te zorgen dat u niet de vereiste toegangs accounts verwijdert:

   - Beleid\toewijzing-rechten Assignment\Access deze computer via het netwerk

   Het beleid moet de volgende groepen vermelden:

   - Beheerders

   - Back-upoperators

   - Iedereen

   - Gebruikers

10. Start de VM opnieuw op om er zeker van te zijn dat Windows nog steeds in orde is en kan worden bereikt via de RDP-verbinding. Op dit moment kunt u een virtuele machine maken in de lokale Hyper-V om te controleren of de VM volledig wordt gestart. Test vervolgens om te controleren of u de virtuele machine via RDP kunt bereiken.

11. Verwijder eventuele filters voor extra Transport Driver Interface (TDI). Verwijder bijvoorbeeld software waarmee TCP-pakketten of extra firewalls worden geanalyseerd. Als u dit later wilt controleren, kunt u dit doen nadat de virtuele machine in Azure is geïmplementeerd.

12. Verwijder alle software of stuur Programma's van derden die betrekking hebben op fysieke onderdelen of andere virtualisatiesoftware.

### <a name="install-windows-updates"></a>Windows-updates installeren
In het ideale geval moet u de computer bijwerken op het niveau van de *patch*. Als dit niet mogelijk is, controleert u of de volgende updates zijn geïnstalleerd:

| Onderdeel               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | schijf. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg. dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl. sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase. dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Netwerk                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Externe bureaubladservices | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Beveiliging                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Bepalen wanneer u Sysprep wilt gebruiken<a id="step23"></a>    

Het hulp programma voor systeem voorbereiding (Sysprep) is een proces dat u kunt uitvoeren om een Windows-installatie opnieuw in te stellen. Sysprep biedt een "out-of-Box"-ervaring door alle persoonlijke gegevens te verwijderen en verschillende onderdelen opnieuw in te stellen. 

Normaal gesp roken voert u Sysprep uit om een sjabloon te maken van waaruit u verschillende virtuele machines kunt implementeren die een specifieke configuratie hebben. De sjabloon wordt een gegeneraliseerde *installatie kopie*genoemd.

Als u slechts één VM van één schijf wilt maken, hoeft u geen Sysprep te gebruiken. In plaats daarvan kunt u de virtuele machine maken op basis van een *gespecialiseerde installatie kopie*. Zie voor informatie over het maken van een virtuele machine op basis van een gespecialiseerde schijf:

- [Een virtuele machine maken op basis van een gespecialiseerde schijf](create-vm-specialized.md)
- [Een virtuele machine maken op basis van een speciale VHD-schijf](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Als u een gegeneraliseerde installatie kopie wilt maken, moet u Sysprep uitvoeren. Zie [voor meer informatie Sysprep gebruiken: Een inleiding](https://technet.microsoft.com/library/bb457073.aspx). 

Niet elke rol of toepassing die is geïnstalleerd op een Windows-computer, ondersteunt gegeneraliseerde installatie kopieën. Voordat u deze procedure uitvoert, moet u ervoor zorgen dat Sysprep de rol van de computer ondersteunt. Zie [Sysprep-ondersteuning voor Server functies](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)voor meer informatie.

### <a name="generalize-a-vhd"></a>Een VHD generaliseren

>[!NOTE]
> Nadat u in `sysprep.exe` de volgende stappen hebt uitgevoerd, schakelt u de virtuele machine uit. Schakel deze optie niet weer in totdat u een installatie kopie maakt in Azure.

1. Meld u aan bij de Windows-VM.
1. Voer een **opdracht prompt** uit als Administrator. 
1. Wijzig de Directory in `%windir%\system32\sysprep`. Voer vervolgens `sysprep.exe` uit.
1. In het dialoogvenster **Hulpprogramma voor systeemvoorbereiding** selecteert u **OOBE (Out-of-Box Experience) van systeem starten** en zorgt u dat het selectievakje **Generaliseren** is ingeschakeld.

    ![Hulp programma voor systeem voorbereiding](media/prepare-for-upload-vhd-image/syspre.png)
1. Selecteer **Afsluiten**in het **afsluit opties**.
1. Selecteer **OK**.
1. Sluit de virtuele machine af wanneer Sysprep is voltooid. Gebruik niet **opnieuw opstarten** om de virtuele machine af te sluiten.

De VHD is nu klaar om te worden geüpload. Zie [een gegeneraliseerde VHD uploaden en deze gebruiken om een nieuwe virtuele machine in azure te maken](sa-upload-generalized.md)voor meer informatie over het maken van een virtuele machine op basis van een gegeneraliseerde schijf.


>[!NOTE]
> Een aangepast bestand *Unattend. XML* wordt niet ondersteund. Hoewel we de `additionalUnattendContent` eigenschap ondersteunen, biedt dit alleen beperkte ondersteuning voor het toevoegen van opties voor [micro soft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) in het bestand Unattend *. XML* dat door de inrichtings agent van Azure wordt gebruikt. U kunt bijvoorbeeld [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) gebruiken om FirstLogonCommands en LogonCommands toe te voegen. Zie [voor beeld van AdditionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407)voor meer informatie.


## <a name="complete-the-recommended-configurations"></a>De aanbevolen configuraties volt ooien
De volgende instellingen zijn niet van invloed op het uploaden van de VHD. We raden u echter ten zeerste aan om deze te configureren.

* Installeer de [agent van de virtuele Azure-machine](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vervolgens kunt u VM-extensies inschakelen. De VM-extensies implementeren de meeste essentiële functionaliteit die u mogelijk wilt gebruiken met uw virtuele machines. U hebt de uitbrei dingen nodig, bijvoorbeeld om wacht woorden opnieuw in te stellen of RDP te configureren. Zie [overzicht van Azure virtual machine agent](../extensions/agent-windows.md)voor meer informatie.
* Nadat u de virtuele machine in azure hebt gemaakt, raden we u aan het wissel bestand op het *tijdelijke schijf volume* te plaatsen om de prestaties te verbeteren. U kunt de bestands plaatsing als volgt instellen:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Als een gegevens schijf aan de virtuele machine is gekoppeld, is de stationsletter van het tijdelijke station meestal *D*. Deze aanwijzing kan verschillen, afhankelijk van de instellingen en het aantal beschik bare stations.

## <a name="next-steps"></a>Volgende stappen
* [Een Windows VM-installatie kopie uploaden naar Azure voor implementaties van Resource Manager](upload-generalized-managed.md)
* [Problemen met activering van Azure Windows VM oplossen](troubleshoot-activation-problems.md)


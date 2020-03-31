---
title: CMD en PowerShell in Azure Windows VM's | Microsoft Documenten
description: CMD- en PowerShell-opdrachten gebruiken in SAC in Azure Windows VM's
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167033"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-opdrachten - CMD en PowerShell

Deze sectie bevat voorbeeldopdrachten voor het uitvoeren van veelvoorkomende taken in scenario's waarin u SAC moet gebruiken om toegang te krijgen tot uw Windows-vm, bijvoorbeeld wanneer u rdp-verbindingsfouten moet oplossen.

SAC is sinds Windows Server 2003 in alle versies van Windows opgenomen, maar is standaard uitgeschakeld. SAC vertrouwt op `sacdrv.sys` de kernel `Special Administration Console Helper` driver, de service (`sacsvr`), en het `sacsess.exe` proces. Zie [Hulpprogramma's en instellingen voor noodbeheerservices](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))voor meer informatie.

Met SAC u verbinding maken met uw draaiende besturingssysteem via de seriële poort. Wanneer u CMD van `sacsess.exe` `cmd.exe` SAC start, wordt gestart binnen uw hardloop-besturingssysteem. Dat zie je in Taakbeheer als je rdp met je VM tegelijk bent verbonden met SAC via de seriële consolefunctie. De CMD die u via `cmd.exe` SAC benadert, is dezelfde die u gebruikt wanneer u via RDP bent aangesloten. Alle dezelfde opdrachten en tools zijn beschikbaar, met inbegrip van de mogelijkheid om PowerShell te starten vanuit dat CMD-exemplaar. Dat is een groot verschil tussen SAC en de Windows Recovery Environment (WinRE) in die SAC is laat u uw lopende OS, waar WinRE laarzen in een ander, minimaal OS te beheren. Hoewel Azure VM's geen ondersteuning bieden voor de mogelijkheid om toegang te krijgen tot WinRE, kunnen Azure VM's met de seriële consolefunctie via SAC worden beheerd.

Omdat SAC is beperkt tot een 80x24-schermbuffer zonder terugte scrollen, voegt u toe `| more` aan opdrachten om de uitvoer één pagina tegelijk weer te geven. Gebruik `<spacebar>` om de volgende `<enter>` pagina te zien of om de volgende regel te zien.

`SHIFT+INSERT`is de plaksnelkoppeling voor het seriële consolevenster.

Vanwege de beperkte schermbuffer van SAC kunnen langere opdrachten gemakkelijker worden uitgetypt in een lokale teksteditor en vervolgens in SAC worden geplakt.

## <a name="view-and-edit-windows-registry-settings"></a>Windows-registerinstellingen weergeven en bewerken
### <a name="verify-rdp-is-enabled"></a>Controleren of RDP is ingeschakeld
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

De tweede sleutel (onder \Beleid) bestaat alleen als de relevante groepsbeleidsinstelling is geconfigureerd.

### <a name="enable-rdp"></a>RDP inschakelen
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

De tweede sleutel (onder \Beleid) zou alleen nodig zijn als de relevante groepsbeleidsinstelling was geconfigureerd. Waarde wordt herschreven bij de volgende vernieuwing van het groepsbeleid als deze is geconfigureerd in groepsbeleid.

## <a name="manage-windows-services"></a>Windows Services beheren

### <a name="view-service-state"></a>Servicestatus weergeven
`sc query termservice`
###  <a name="view-service-logon-account"></a>Aanmeldingsaccount voor service weergeven
`sc qc termservice`
### <a name="set-service-logon-account"></a>Aanmeldingsaccount voor service instellen
`sc config termservice obj= "NT Authority\NetworkService"`

Een ruimte is vereist na de gelijken teken.
### <a name="set-service-start-type"></a>Begintype service instellen
`sc config termservice start= demand`

Een ruimte is vereist na de gelijken teken. Mogelijke startwaarden `boot` `system`zijn `auto` `demand`, `disabled` `delayed-auto`, , , , .
### <a name="set-service-dependencies"></a>Serviceafhankelijkheden instellen
`sc config termservice depend= RPCSS`

Een ruimte is vereist na de gelijken teken.
### <a name="start-service"></a>Service starten
`net start termservice`

of

`sc start termservice`
### <a name="stop-service"></a>Service stoppen
`net stop termservice`

of

`sc stop termservice`
## <a name="manage-networking-features"></a>Netwerkfuncties beheren
### <a name="show-nic-properties"></a>NIC-eigenschappen weergeven
`netsh interface show interface`
### <a name="show-ip-properties"></a>IP-eigenschappen weergeven
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec-configuratie weergeven
`netsh nap client show configuration`
### <a name="enable-nic"></a>NIC inschakelen
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>NIC instellen om DHCP te gebruiken
`netsh interface ip set address name="<interface name>" source=dhcp`

Voor meer `netsh`informatie over , [klik hier](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Azure VM's moeten altijd in het gastbesturingssysteem zijn geconfigureerd om DHCP te gebruiken om een IP-adres te verkrijgen. De statische IP-instelling azure gebruikt DHCP nog steeds om het statische IP aan de VM te geven.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Poortping
De telnetclient installeren

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Connectiviteit testen

`telnet bing.com 80`

De telnetclient verwijderen

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Wanneer powershell standaard beperkt is tot methoden die beschikbaar zijn in Windows, kan het een betere benadering zijn voor het testen van poortconnectiviteit. Zie de PowerShell sectie hieronder voor voorbeelden.
### <a name="test-dns-name-resolution"></a>DNS-naamomzetting testen
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows Firewall-regel weergeven
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Windows Firewall uitschakelen
`netsh advfirewall set allprofiles state off`

U deze opdracht gebruiken wanneer u problemen oplost om de Windows Firewall tijdelijk uit te sluiten. Het zal worden ingeschakeld bij de volgende herstart of wanneer u het inschakelt met behulp van de onderstaande opdracht. Stop de Windows Firewall-service (MPSSVC) of Base Filtering Engine (BFE) niet als een manier om de Windows Firewall uit te sluiten. Als u MPSSVC of BFE stopt, wordt alle connectiviteit geblokkeerd.
### <a name="enable-windows-firewall"></a>Windows Firewall inschakelen
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gebruikers en groepen beheren
### <a name="create-local-user-account"></a>Lokaal gebruikersaccount maken
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Lokale gebruiker toevoegen aan lokale groep
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Controleren of het gebruikersaccount is ingeschakeld
`net user <username> | find /i "active"`

Azure VM's die zijn gemaakt op grond van algemene afbeelding, hebben het lokale beheerdersaccount een nieuwe naam op de naam die is opgegeven tijdens de vm-inrichting. Dus het zal `Administrator`meestal niet .
### <a name="enable-user-account"></a>Gebruikersaccount inschakelen
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Eigenschappen van gebruikersaccounts weergeven
`net user <username>`

Voorbeeldlijnen van interesse van een lokaal beheerdersaccount:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Lokale groepen bekijken
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Het Windows-gebeurtenislogboek beheren
### <a name="query-event-log-errors"></a>Fouten in querygebeurtenislogboeken
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Wijzig `/c:10` in het gewenste aantal gebeurtenissen om terug te keren of verplaats deze om alle gebeurtenissen die overeenkomen met het filter te retourneren.
### <a name="query-event-log-by-event-id"></a>Querygebeurtenislogboek op gebeurtenis-id
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Querygebeurtenislogboek op gebeurtenis-id en provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Querygebeurtenislogboek door gebeurtenis-id en provider voor de afgelopen 24 uur
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Gebruik `604800000` om terug te kijken 7 dagen in plaats van 24 uur.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Querygebeurtenislogboek door Gebeurtenis-ID, Provider en EventData in de afgelopen 7 dagen
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Geïnstalleerde toepassingen weergeven of verwijderen
### <a name="list-installed-applications"></a>Geïnstalleerde toepassingen weergeven
`wmic product get Name,InstallDate | sort /r | more`

De `sort /r` soorten aflopend op installatiedatum om het gemakkelijk te maken om te zien wat onlangs werd geïnstalleerd. Met `<spacebar>` gebruiken om door te gaan `<enter>` naar de volgende pagina met uitvoer, of om een regel vooruit te gaan.
### <a name="uninstall-an-application"></a>Een toepassing verwijderen
`wmic path win32_product where name="<name>" call uninstall`

Vervang `<name>` de naam die in de bovenstaande opdracht wordt geretourneerd voor de toepassing die u wilt verwijderen.

## <a name="file-system-management"></a>Bestandssysteembeheer
### <a name="get-file-version"></a>Bestandsversie downloaden
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

In dit voorbeeld wordt de bestandsversie van het virtuele NIC-stuurprogramma geretourneerd, dat is netvsc.sys, netvsc63.sys of netvsc60.sys, afhankelijk van de Windows-versie.
### <a name="scan-for-system-file-corruption"></a>Scannen op beschadiging van systeembestanden
`sfc /scannow`

Zie ook [Een Windows-afbeelding herstellen](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Scannen op beschadiging van systeembestanden
`dism /online /cleanup-image /scanhealth`

Zie ook [Een Windows-afbeelding herstellen](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Bestandsmachtigingen exporteren naar tekstbestand
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Bestandsmachtigingen opslaan in ACL-bestand
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Bestandsmachtigingen uit ACL-bestand herstellen
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Het pad `/restore` bij gebruik moet de bovenliggende map `/save`zijn van de map die u hebt opgegeven bij het gebruik van . In dit `\RSA` voorbeeld is de `\MachineKeys` bovenliggende `/save` map die is opgegeven in het bovenstaande voorbeeld.
### <a name="take-ntfs-ownership-of-a-folder"></a>NTFS-eigendom nemen van een map
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>NTFS-machtigingen opnieuw toekennen aan een map
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Apparaten beheren
### <a name="remove-non-present-pnp-devices"></a>Niet-aanwezig PNP-apparaten verwijderen
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Groepsbeleid beheren
### <a name="force-group-policy-update"></a>Groepsbeleidsupdate forceren
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Diverse taken
### <a name="show-os-version"></a>OS-versie weergeven
`ver`

of

`wmic os get caption,version,buildnumber /format:list`

of

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Installatiedatum besturingssysteem weergeven
`systeminfo | find /i "original"`

of

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Laatste opstarttijd weergeven
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Tijdzone bekijken
`systeminfo | find /i "time zone"`

of

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Windows opnieuw starten
`shutdown /r /t 0`

Het `/f` toevoegen dwingt het uitvoeren van toepassingen om te sluiten zonder gebruikers te waarschuwen.
### <a name="detect-safe-mode-boot"></a>Boot veilige modus detecteren
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Windows-opdrachten - PowerShell

Als u PowerShell in SAC wilt uitvoeren, typt u het type:

`powershell <enter>`

> [!CAUTION]
> Verwijder de PSReadLine-module uit de PowerShell-sessie voordat u andere PowerShell-opdrachten uitvoert. Er is een bekend probleem waarbij extra tekens kunnen worden geïntroduceerd in tekst die van het klembord wordt geplakt als PSReadLine wordt uitgevoerd in een PowerShell-sessie in SAC.

Controleer eerst of PSReadLine is geladen. Het wordt standaard geladen op Windows Server 2016, Windows 10 en latere versies van Windows. Het zou alleen aanwezig zijn op eerdere Windows-versies als het handmatig was geïnstalleerd.

Als deze opdracht terugkeert naar een prompt zonder uitvoer, is de module niet geladen en u de PowerShell-sessie in SAC gewoon blijven gebruiken.

`get-module psreadline`

Als de bovenstaande opdracht de PSReadLine-moduleversie retourneert, voert u de volgende opdracht uit om deze te ontladen. Met deze opdracht wordt de module niet verwijderd of verwijderd, deze wordt alleen uit de huidige PowerShell-sessie verwijderd.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows-registerinstellingen weergeven en bewerken
### <a name="verify-rdp-is-enabled"></a>Controleren of RDP is ingeschakeld
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

De tweede sleutel (onder \Beleid) bestaat alleen als de relevante groepsbeleidsinstelling is geconfigureerd.
### <a name="enable-rdp"></a>RDP inschakelen
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

De tweede sleutel (onder \Beleid) zou alleen nodig zijn als de relevante groepsbeleidsinstelling was geconfigureerd. Waarde wordt herschreven bij de volgende vernieuwing van het groepsbeleid als deze is geconfigureerd in groepsbeleid.
## <a name="manage-windows-services"></a>Windows Services beheren
### <a name="view-service-details"></a>Servicegegevens weergeven
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`kan worden gebruikt, maar bevat niet het service-aanmeldingsaccount. `Get-WmiObject win32-service`Doet.
### <a name="set-service-logon-account"></a>Aanmeldingsaccount voor service instellen
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Wanneer u een ander `NT AUTHORITY\LocalService` `NT AUTHORITY\NetworkService`serviceaccount `LocalSystem`gebruikt dan , of het accountwachtwoord opgeeft als het laatste (achtste) argument na de accountnaam.
### <a name="set-service-startup-type"></a>Opstarttype service instellen
`set-service termservice -startuptype Manual`

`Set-service`accepteert, `Automatic` `Manual`of `Disabled` voor opstarttype.
### <a name="set-service-dependencies"></a>Serviceafhankelijkheden instellen
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Service starten
`start-service termservice`
### <a name="stop-service"></a>Service stoppen
`stop-service termservice`
## <a name="manage-networking-features"></a>Netwerkfuncties beheren
### <a name="show-nic-properties"></a>NIC-eigenschappen weergeven
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

of

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`is beschikbaar in 2012+, voor 2008R2 gebruik `Get-WmiObject`.
### <a name="show-ip-properties"></a>IP-eigenschappen weergeven
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>NIC inschakelen
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

of

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`is beschikbaar in 2012+, voor 2008R2 gebruik `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>NIC instellen om DHCP te gebruiken
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`is beschikbaar op 2012+. Voor 2008R2 `Get-WmiObject`gebruik . Azure VM's moeten altijd in het gastbesturingssysteem zijn geconfigureerd om DHCP te gebruiken om een IP-adres te verkrijgen. De statische IP-instelling azure maakt nog steeds gebruik van DHCP om het IP aan de VM te geven.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> De cmdlet Schrijfvoortgang werkt mogelijk niet met deze opdracht. Als beperking u `$ProgressPreference = "SilentlyContinue"` in PowerShell worden uitgevoerd om de voortgangsbalk uit te schakelen.

of

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`zonder enige parameters zal `internetbeacon.msedge.net`proberen te pingen . Het is beschikbaar op 2012+. Voor 2008R2 `Get-WmiObject` gebruik zoals in het tweede voorbeeld.
### <a name="port-ping"></a>Poortping
`test-netconnection -ComputerName bing.com -Port 80`

of

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`is beschikbaar op 2012+. Voor 2008R2 gebruik`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>DNS-naamomzetting testen
`resolve-dnsname bing.com`

of

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`is beschikbaar op 2012+. Voor 2008R2 `System.Net.DNS`gebruik .
### <a name="show-windows-firewall-rule-by-name"></a>Windows-firewallregel op naam weergeven
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Windows-firewallregel per poort weergeven
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

of

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`is beschikbaar op 2012+. Voor 2008R2 `hnetcfg.fwpolicy2` gebruik maken van de COM object.
### <a name="disable-windows-firewall"></a>Windows-firewall uitschakelen
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`is beschikbaar op 2012+. Voor 2008R2 `netsh advfirewall` gebruik zoals vermeld in de CMD sectie hierboven.
## <a name="manage-users-and-groups"></a>Gebruikers en groepen beheren
### <a name="create-local-user-account"></a>Lokaal gebruikersaccount maken
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Controleren of het gebruikersaccount is ingeschakeld
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

of

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`is beschikbaar op 2012+. Voor 2008R2 `Get-WmiObject`gebruik . In dit voorbeeld wordt het ingebouwde lokale `S-1-5-21-*-500`beheerdersaccount weergegeven, dat altijd SID heeft. Azure VM's die zijn gemaakt op grond van algemene afbeelding, hebben het lokale beheerdersaccount een nieuwe naam op de naam die is opgegeven tijdens de vm-inrichting. Dus het zal `Administrator`meestal niet .
### <a name="add-local-user-to-local-group"></a>Lokale gebruiker toevoegen aan lokale groep
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Lokaal gebruikersaccount inschakelen
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

In dit voorbeeld wordt het ingebouwde lokale `S-1-5-21-*-500`beheerdersaccount mogelijk, dat altijd SID heeft. Azure VM's die zijn gemaakt op grond van algemene afbeelding, hebben het lokale beheerdersaccount een nieuwe naam op de naam die is opgegeven tijdens de vm-inrichting. Dus het zal `Administrator`meestal niet .
### <a name="view-user-account-properties"></a>Eigenschappen van gebruikersaccounts weergeven
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

of

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`is beschikbaar op 2012+. Voor 2008R2 `Get-WmiObject`gebruik . In dit voorbeeld wordt het ingebouwde lokale `S-1-5-21-*-500`beheerdersaccount weergegeven, dat altijd SID heeft.
### <a name="view-local-groups"></a>Lokale groepen bekijken
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`is beschikbaar op 2012+. Voor 2008R2 `Get-WmiObject`gebruik .
## <a name="manage-the-windows-event-log"></a>Het Windows-gebeurtenislogboek beheren
### <a name="query-event-log-errors"></a>Fouten in querygebeurtenislogboeken
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Wijzig `/c:10` in het gewenste aantal gebeurtenissen om terug te keren of verplaats deze om alle gebeurtenissen die overeenkomen met het filter te retourneren.
### <a name="query-event-log-by-event-id"></a>Querygebeurtenislogboek op gebeurtenis-id
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Querygebeurtenislogboek op gebeurtenis-id en provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Querygebeurtenislogboek door gebeurtenis-id en provider voor de afgelopen 24 uur
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Gebruik `604800000` om terug te kijken 7 dagen in plaats van 24 uur. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Querygebeurtenislogboek door Gebeurtenis-ID, Provider en EventData in de afgelopen 7 dagen
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Geïnstalleerde toepassingen weergeven of verwijderen
### <a name="list-installed-software"></a>Geïnstalleerde software weergeven
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Software verwijderen
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Bestandssysteembeheer
### <a name="get-file-version"></a>Bestandsversie downloaden
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

In dit voorbeeld wordt de bestandsversie van het virtuele NIC-stuurprogramma geretourneerd, dat netvsc.sys, netvsc63.sys of netvsc60.sys heeft, afhankelijk van de Windows-versie.
### <a name="download-and-extract-file"></a>Bestand downloaden en extraheren
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

In dit `c:\bin` voorbeeld wordt een map maakt en vervolgens de `c:\bin`Sysinternals-suite met hulpprogramma's gedownload en geëxtraheerd naar .
## <a name="miscellaneous-tasks"></a>Diverse taken
### <a name="show-os-version"></a>OS-versie weergeven
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Installatiedatum besturingssysteem weergeven
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Laatste opstarttijd weergeven
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Windows-uptime weergeven
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Retourneert `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`uptime `49:16:48:00.00`als, bijvoorbeeld .
### <a name="restart-windows"></a>Windows opnieuw starten
`restart-computer`

Het `-force` toevoegen dwingt het uitvoeren van toepassingen om te sluiten zonder gebruikers te waarschuwen.
## <a name="instance-metadata"></a>Metagegevens van instantie

U Azure-instantiemetagegevens vanuit uw Azure VM opvragen om details zoals osType, Locatie, vmSize, vmId, naam, resourceGroupName, subscriptionId, privateIpAddress en publicIpAddress weer te geven.

Voor het opvragen van metagegevens van instanties is een gezonde gastnetwerkverbinding vereist, omdat er een REST-gesprek wordt gemaakt via de Azure-host naar de metagegevensservice van de instantie. Dus als u metagegevens van bijvoorbeeld opvragen, geeft dat aan dat de gast via het netwerk kan communiceren met een door Azure gehoste service.

Zie [Azure Instance Metadata-service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)voor meer informatie.

### <a name="instance-metadata"></a>Metagegevens van instantie
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>BE-type (instantiemetagegevens)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Locatie (metagegevens van instantie)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Grootte (instantiemetagegevens)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM-id (metagegevens van instantie)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM-naam (metagegevens van instantie)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Naam van resourcegroep (metagegevens van instantie)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Abonnements-id (metagegevens van instantie)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tags (metagegevens van instantie)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Groep-id voor plaatsing (metagegevens van instantie)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domein van platformfout (metagegevens van instance)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domein platformupdate (metagegevens van instance)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4-privé-IP-adres (metagegevens van instance)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4-openbaar IP-adres (metagegevens van instanties)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-subnetadres / voorvoegsel (metagegevens van instance)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6-IP-adres (metagegevens van instantie)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-adres (metagegevens van instantie)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Volgende stappen
* De belangrijkste seriële console Windows documentatie pagina is [hier](serial-console-windows.md)te vinden .
* De seriële console is ook beschikbaar voor [Linux](serial-console-linux.md) VM's.
* Meer informatie over [opstartdiagnostiek](boot-diagnostics.md).

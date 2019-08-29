---
title: CMD en Power shell in azure Windows-Vm's | Microsoft Docs
description: CMD-en Power shell-opdrachten binnen SAC gebruiken in azure Windows-Vm's
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
ms.openlocfilehash: a106984bc60d0ccfe29a1956213aec6f87ad30dd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090174"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-opdrachten-CMD en Power shell

Deze sectie bevat voor beelden van opdrachten voor het uitvoeren van veelvoorkomende taken in scenario's waarbij u SAC wellicht moet gebruiken om toegang te krijgen tot uw Windows-VM, bijvoorbeeld wanneer u problemen met de RDP-verbinding moet oplossen.

SAC is opgenomen in alle versies van Windows sinds Windows Server 2003, maar is standaard uitgeschakeld. SAC is afhankelijk van het `sacdrv.sys` kernelstuurprogramma, de `Special Administration Console Helper` service (`sacsvr`) en het `sacsess.exe` proces. Zie [Hulpprogram ma's en instellingen voor noodsituatie beheer Services](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))voor meer informatie.

Met SAC kunt u verbinding maken met uw actieve besturings systeem via seriële poort. Wanneer u cmd vanuit SAC start, `sacsess.exe` wordt `cmd.exe` gestart binnen het besturings systeem dat wordt uitgevoerd. U kunt in taak beheer zien of u RDP naar uw virtuele machine tegelijk hebt verbonden met SAC via de seriële console-functie. De cmd-verbinding die u via SAC hebt `cmd.exe` , is hetzelfde als die u gebruikt wanneer u verbinding maakt via RDP. Alle dezelfde opdrachten en hulpprogram ma's zijn beschikbaar, inclusief de mogelijkheid om Power shell vanuit dat CMD-exemplaar te starten. Dit is een belang rijk verschil tussen SAC en de Windows Recovery Environment (WinRE) in die SAC stelt u in staat om uw actieve besturings systeem te beheren, waarbij WinRE wordt opgestart in een ander, mini maal besturings systeem. Azure-Vm's bieden geen ondersteuning voor de mogelijkheid om toegang te krijgen tot WinRE, met de functie seriële console kunnen virtuele Azure-machines worden beheerd via SAC.

Omdat SAC is beperkt tot een 80x24-scherm buffer zonder terug te schuiven, `| more` voegt u toe aan opdrachten om de uitvoer één pagina tegelijk weer te geven. Gebruik `<spacebar>` om de volgende pagina weer te geven `<enter>` of om de volgende regel weer te geven.  

`SHIFT+INSERT`is de snelkoppeling plakken voor het venster van de seriële console.

Vanwege de beperkte scherm buffer van SAC kunnen langere opdrachten gemakkelijker worden getypt in een lokale tekst editor en vervolgens in SAC worden geplakt.

## <a name="view-and-edit-windows-registry-settings"></a>Windows-register instellingen weer geven en bewerken
### <a name="verify-rdp-is-enabled"></a>Controleren of RDP is ingeschakeld
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

De tweede sleutel (onder \Policies) bevindt zich alleen als de relevante groeps beleids instelling is geconfigureerd.

### <a name="enable-rdp"></a>RDP inschakelen
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

De tweede sleutel (onder \Policies) zou alleen nodig zijn als de relevante groeps beleids instelling is geconfigureerd. De waarde wordt bij volgende vernieuwing van groeps beleid herschreven als deze is geconfigureerd in groeps beleid.

## <a name="manage-windows-services"></a>Windows-Services beheren

### <a name="view-service-state"></a>Service status weer geven
`sc query termservice`
###  <a name="view-service-logon-account"></a>Aanmeldings account voor service weer geven
`sc qc termservice`
### <a name="set-service-logon-account"></a>Aanmeldings account voor service instellen 
`sc config termservice obj= "NT Authority\NetworkService"`

Er is een spatie vereist na het gelijkteken.
### <a name="set-service-start-type"></a>Start type van service instellen
`sc config termservice start= demand` 

Er is een spatie vereist na het gelijkteken. Mogelijke begin waarden zijn `boot`, `system`, `auto` `demand`, ,`disabled`, .`delayed-auto`
### <a name="set-service-dependencies"></a>Service afhankelijkheden instellen
`sc config termservice depend= RPCSS`

Er is een spatie vereist na het gelijkteken.
### <a name="start-service"></a>Service starten
`net start termservice`

of

`sc start termservice`
### <a name="stop-service"></a>Service stoppen
`net stop termservice`

of

`sc stop termservice`
## <a name="manage-networking-features"></a>Netwerk functies beheren
### <a name="show-nic-properties"></a>NIC-eigenschappen weer geven
`netsh interface show interface` 
### <a name="show-ip-properties"></a>IP-eigenschappen weer geven
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec-configuratie weer geven
`netsh nap client show configuration`  
### <a name="enable-nic"></a>NIC inschakelen
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>NIC instellen voor gebruik van DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

[Klik hier](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts)voor meer `netsh`informatie over.

Virtuele Azure-machines moeten altijd in het gast besturingssysteem worden geconfigureerd om DHCP te gebruiken voor het verkrijgen van een IP-adres. De statische IP-instelling van Azure maakt nog steeds gebruik van DHCP om het statische IP-adres aan de virtuele machine toe te wijzen.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Poort ping  
De Telnet-client installeren

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Connectiviteit testen

`telnet bing.com 80`

De Telnet-client verwijderen

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Wanneer dit is beperkt tot de methoden die beschikbaar zijn in Windows, kan Power shell een betere benadering zijn voor het testen van poort connectiviteit. Zie de sectie Power shell hieronder voor voor beelden.
### <a name="test-dns-name-resolution"></a>DNS-naam omzetting testen
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows Firewall regel weer geven
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Windows Firewall uitschakelen
`netsh advfirewall set allprofiles state off`

U kunt deze opdracht gebruiken bij het oplossen van problemen om de Windows Firewall tijdelijk uit te laten zien. Het wordt ingeschakeld bij de volgende keer opnieuw opstarten of wanneer u deze inschakelt met behulp van de onderstaande opdracht. Stop de Windows Firewall service (MPSSVC) of base filtering Engine-service (BFE) niet als een manier om de Windows Firewall uit te voeren. Als u MPSSVC of BFE stopt, wordt alle connectiviteit geblokkeerd.
### <a name="enable-windows-firewall"></a>Windows Firewall inschakelen
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gebruikers en groepen beheren
### <a name="create-local-user-account"></a>Lokaal gebruikers account maken
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Lokale gebruiker toevoegen aan lokale groep
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Controleren of gebruikers account is ingeschakeld
`net user <username> | find /i "active"`

Voor Azure-Vm's die zijn gemaakt op basis van een gegeneraliseerde installatie kopie, wordt de naam van het lokale beheerders account gewijzigd in de naam die tijdens het inrichten van de virtuele machine Het is dus meestal niet `Administrator`zo.
### <a name="enable-user-account"></a>Gebruikers account inschakelen
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Eigenschappen van gebruikers accounts weer geven
`net user <username>`

Voorbeeld regels die van belang zijn voor een lokaal beheerders account:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Lokale groepen weer geven
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Het Windows-gebeurtenis logboek beheren
### <a name="query-event-log-errors"></a>Query gebeurtenis logboek fouten
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Wijzig `/c:10` het gewenste aantal gebeurtenissen of verplaats het om alle gebeurtenissen te retour neren die overeenkomen met het filter.
### <a name="query-event-log-by-event-id"></a>Query gebeurtenis logboek op gebeurtenis-ID
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Query gebeurtenis logboek op gebeurtenis-ID en provider
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Query gebeurtenis logboek op gebeurtenis-ID en provider voor de afgelopen 24 uur
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Gebruik `604800000` dit om 7 dagen terug te kijken in plaats van 24 uur.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Query gebeurtenis logboek op gebeurtenis-ID, provider en Event data in de afgelopen 7 dagen
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Geïnstalleerde toepassingen weer geven of verwijderen
### <a name="list-installed-applications"></a>Geïnstalleerde toepassingen weer geven
`wmic product get Name,InstallDate | sort /r | more`

De `sort /r` aflopende volg orde wordt gesorteerd op installatie datum, zodat u gemakkelijk kunt zien wat onlangs is geïnstalleerd. Gebruik `<spacebar>` om door te gaan naar de volgende pagina van de `<enter>` uitvoer of om één regel te verschuiven.
### <a name="uninstall-an-application"></a>Een toepassing verwijderen
`wmic path win32_product where name="<name>" call uninstall`

Vervang `<name>` door de naam die in de bovenstaande opdracht is geretourneerd voor de toepassing die u wilt verwijderen.

## <a name="file-system-management"></a>Bestandssysteem beheer
### <a name="get-file-version"></a>Bestands versie ophalen
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

In dit voor beeld wordt de bestands versie van het virtuele NIC-stuur programma (netvsc. sys, netvsc63. sys of netvsc60. sys) als resultaat gegeven, afhankelijk van de Windows-versie.
### <a name="scan-for-system-file-corruption"></a>Controleren op beschadiging van het systeem bestand
`sfc /scannow`

Zie ook [een Windows-installatie kopie herstellen](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Controleren op beschadiging van het systeem bestand
`dism /online /cleanup-image /scanhealth`

Zie ook [een Windows-installatie kopie herstellen](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Bestands machtigingen exporteren naar tekst bestand
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Bestands machtigingen voor ACL-bestand opslaan
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Bestands machtigingen herstellen vanuit het ACL-bestand
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Het pad dat wordt `/restore` gebruikt, moet de bovenliggende map zijn van de map die u hebt `/save`opgegeven bij het gebruik van. In dit voor beeld `\RSA` is het bovenliggende item van `\MachineKeys` de map die is `/save` opgegeven in bovenstaand voor beeld.
### <a name="take-ntfs-ownership-of-a-folder"></a>NTFS-eigendom van een map maken
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>NTFS-machtigingen voor een recursief toekennen aan een map
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Apparaten beheren
### <a name="remove-non-present-pnp-devices"></a>Niet-bestaande PNP-apparaten verwijderen
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>groepsbeleid beheren
### <a name="force-group-policy-update"></a>Update van groeps beleid afdwingen
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Diverse taken
### <a name="show-os-version"></a>Versie van besturings systeem weer geven
`ver`

of 

`wmic os get caption,version,buildnumber /format:list`

of 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Installatie datum van besturings systeem weer geven
`systeminfo | find /i "original"`

of 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Laatste opstart tijd weer geven
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Tijd zone weer geven
`systeminfo | find /i "time zone"`

of

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Windows opnieuw starten
`shutdown /r /t 0`

Als `/f` u toevoegt, worden actieve toepassingen geforceerd gesloten zonder dat gebruikers worden gewaarschuwd.
### <a name="detect-safe-mode-boot"></a>Opstarten in veilige modus detecteren
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows-opdrachten-Power shell

Als u Power shell wilt uitvoeren in SAC, typt u na het bereiken van een opdracht prompt:

`powershell <enter>`

> [!CAUTION]
> Verwijder de PSReadLine-module uit de Power shell-sessie voordat u andere Power shell-opdrachten uitvoert. Er is een bekend probleem waarbij extra tekens kunnen worden geïntroduceerd in tekst die vanuit het klem bord wordt geplakt als PSReadLine wordt uitgevoerd in een Power shell-sessie in SAC.

Controleer eerst of PSReadLine is geladen. Het wordt standaard geladen op Windows Server 2016, Windows 10 en latere versies van Windows. Als deze hand matig is geïnstalleerd, is deze alleen aanwezig op eerdere Windows-versies. 

Als deze opdracht wordt geretourneerd naar een prompt zonder uitvoer, is de module niet geladen en kunt u de Power shell-sessie in SAC als normaal blijven gebruiken.

`get-module psreadline`

Als de bovenstaande opdracht de versie van de PSReadLine-module retourneert, voert u de volgende opdracht uit om het bestand te verwijderen. Met deze opdracht wordt de module niet verwijderd of verwijderd, wordt deze alleen uit de huidige Power shell-sessie verwijderd.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows-register instellingen weer geven en bewerken
### <a name="verify-rdp-is-enabled"></a>Controleren of RDP is ingeschakeld
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

De tweede sleutel (onder \Policies) bevindt zich alleen als de relevante groeps beleids instelling is geconfigureerd.
### <a name="enable-rdp"></a>RDP inschakelen
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

De tweede sleutel (onder \Policies) zou alleen nodig zijn als de relevante groeps beleids instelling is geconfigureerd. De waarde wordt bij volgende vernieuwing van groeps beleid herschreven als deze is geconfigureerd in groeps beleid.
## <a name="manage-windows-services"></a>Windows-Services beheren
### <a name="view-service-details"></a>Details van service weer geven
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`kan worden gebruikt, maar bevat niet het account voor aanmelding bij de service. `Get-WmiObject win32-service`stemt.
### <a name="set-service-logon-account"></a>Aanmeldings account voor service instellen
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Wanneer u een ander service account dan `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, of `LocalSystem`opgeeft, geeft u het wacht woord van het account op als het laatste (achtste) argument na de account naam.
### <a name="set-service-startup-type"></a>Opstart type van service instellen
`set-service termservice -startuptype Manual`

`Set-service`accepteert `Automatic`, `Manual`, of`Disabled` voor het opstart type.
### <a name="set-service-dependencies"></a>Service afhankelijkheden instellen
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Service starten
`start-service termservice`
### <a name="stop-service"></a>Service stoppen
`stop-service termservice`
## <a name="manage-networking-features"></a>Netwerk functies beheren
### <a name="show-nic-properties"></a>NIC-eigenschappen weer geven
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

of 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`is beschikbaar in 2012 +, voor 2008R2- `Get-WmiObject`gebruik.
### <a name="show-ip-properties"></a>IP-eigenschappen weer geven
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>NIC inschakelen
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

of

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`is beschikbaar in 2012 +, voor 2008R2- `Get-WmiObject`gebruik.
### <a name="set-nic-to-use-dhcp"></a>NIC instellen voor gebruik van DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`is beschikbaar op 2012 +. Voor 2008R2- `Get-WmiObject`gebruik. Virtuele Azure-machines moeten altijd in het gast besturingssysteem worden geconfigureerd om DHCP te gebruiken voor het verkrijgen van een IP-adres. De statische IP-instelling van Azure maakt nog steeds gebruik van DHCP om het IP-adres aan de virtuele machine toe te wijzen.
### <a name="ping"></a>Ping
`test-netconnection`

of

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`zonder dat er para meters worden `internetbeacon.msedge.net`geprobeerd te pingen. Deze is beschikbaar op 2012 +. Voor 2008R2 gebruiken `Get-WmiObject` zoals in het tweede voor beeld.
### <a name="port-ping"></a>Poort ping
`test-netconnection -ComputerName bing.com -Port 80`

of

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`is beschikbaar op 2012 +. Voor 2008R2 gebruik`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>DNS-naam omzetting testen
`resolve-dnsname bing.com` 

of 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`is beschikbaar op 2012 +. Voor 2008R2- `System.Net.DNS`gebruik.
### <a name="show-windows-firewall-rule-by-name"></a>Windows Firewall-regel op naam weer geven
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Windows Firewall-regel per poort weer geven
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

of

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`is beschikbaar op 2012 +. Gebruik het `hnetcfg.fwpolicy2` COM-object voor 2008R2. 
### <a name="disable-windows-firewall"></a>Windows Firewall uitschakelen
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`is beschikbaar op 2012 +. Voor 2008R2 gebruikt `netsh advfirewall` u waarnaar wordt verwezen in de sectie cmd hierboven.
## <a name="manage-users-and-groups"></a>Gebruikers en groepen beheren
### <a name="create-local-user-account"></a>Lokaal gebruikers account maken
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Controleren of gebruikers account is ingeschakeld
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

of 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`is beschikbaar op 2012 +. Voor 2008R2- `Get-WmiObject`gebruik. Dit voor beeld toont het ingebouwde lokale beheerders account, dat altijd SID `S-1-5-21-*-500`heeft. Voor Azure-Vm's die zijn gemaakt op basis van een gegeneraliseerde installatie kopie, wordt de naam van het lokale beheerders account gewijzigd in de naam die tijdens het inrichten van de virtuele machine Het is dus meestal niet `Administrator`zo.
### <a name="add-local-user-to-local-group"></a>Lokale gebruiker toevoegen aan lokale groep
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Lokale gebruikers account inschakelen
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

In dit voor beeld wordt het ingebouwde lokale beheerders account ingeschakeld, dat altijd SID `S-1-5-21-*-500`heeft. Voor Azure-Vm's die zijn gemaakt op basis van een gegeneraliseerde installatie kopie, wordt de naam van het lokale beheerders account gewijzigd in de naam die tijdens het inrichten van de virtuele machine Het is dus meestal niet `Administrator`zo.
### <a name="view-user-account-properties"></a>Eigenschappen van gebruikers accounts weer geven
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

of 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`is beschikbaar op 2012 +. Voor 2008R2- `Get-WmiObject`gebruik. Dit voor beeld toont het ingebouwde lokale beheerders account, dat altijd SID `S-1-5-21-*-500`heeft.
### <a name="view-local-groups"></a>Lokale groepen weer geven
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`is beschikbaar op 2012 +. Voor 2008R2- `Get-WmiObject`gebruik.
## <a name="manage-the-windows-event-log"></a>Het Windows-gebeurtenis logboek beheren
### <a name="query-event-log-errors"></a>Query gebeurtenis logboek fouten
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Wijzig `/c:10` het gewenste aantal gebeurtenissen of verplaats het om alle gebeurtenissen te retour neren die overeenkomen met het filter.
### <a name="query-event-log-by-event-id"></a>Query gebeurtenis logboek op gebeurtenis-ID
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Query gebeurtenis logboek op gebeurtenis-ID en provider
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Query gebeurtenis logboek op gebeurtenis-ID en provider voor de afgelopen 24 uur
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Gebruik `604800000` dit om 7 dagen terug te kijken in plaats van 24 uur. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Query gebeurtenis logboek op gebeurtenis-ID, provider en Event data in de afgelopen 7 dagen
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Geïnstalleerde toepassingen weer geven of verwijderen
### <a name="list-installed-software"></a>Geïnstalleerde software weer geven
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Software verwijderen
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Bestandssysteem beheer
### <a name="get-file-version"></a>Bestands versie ophalen
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

In dit voor beeld wordt de bestands versie van het stuur programma van de virtuele NIC met de naam netvsc. sys, netvsc63. sys of netvsc60. sys als resultaat gegeven, afhankelijk van de Windows-versie.
### <a name="download-and-extract-file"></a>Bestand downloaden en uitpakken
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

In dit voor beeld `c:\bin` wordt een map gemaakt, waarna de Sysinternals-Suite met hulpprogram ma's `c:\bin`wordt gedownload en opgehaald in.
## <a name="miscellaneous-tasks"></a>Diverse taken
### <a name="show-os-version"></a>Versie van besturings systeem weer geven
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Installatie datum van besturings systeem weer geven
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Laatste opstart tijd weer geven
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Uptime van Windows weer geven
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Retourneert de actieve `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`tijds duur als `49:16:48:00.00`bijvoorbeeld. 
### <a name="restart-windows"></a>Windows opnieuw starten
`restart-computer`

Als `-force` u toevoegt, worden actieve toepassingen geforceerd gesloten zonder dat gebruikers worden gewaarschuwd.
## <a name="instance-metadata"></a>Meta gegevens van instantie

U kunt de meta gegevens van Azure-exemplaren vanuit uw Azure-VM opvragen om de details te bekijken, zoals osType, location, vmSize, vmId, name, resourceGroupName, subscriptionId, privateIpAddress en publicIpAddress.

Voor het opvragen van meta gegevens van exemplaren is een goede gast netwerk verbinding vereist, omdat het een REST-aanroep doet via de Azure-host naar de meta gegevens service van het exemplaar. Dus als u wel de meta gegevens van het exemplaar kunt opvragen, geeft u aan dat de gast via het netwerk kan communiceren met een door Azure gehoste service.

Zie [Azure instance meta data service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)(Engelstalig) voor meer informatie.

### <a name="instance-metadata"></a>Meta gegevens van instantie
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Type besturings systeem (meta gegevens van exemplaar)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Locatie (meta gegevens van exemplaar)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Grootte (meta gegevens van exemplaar)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM-ID (meta gegevens van exemplaar)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM-naam (meta gegevens van exemplaar)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Naam van de resource groep (meta gegevens van het exemplaar)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Abonnements-ID (meta gegevens van exemplaar)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tags (meta gegevens van exemplaar)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Plaatsings groep-ID (meta gegevens van exemplaar)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Platform fout domein (meta gegevens van exemplaar)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Platform update domein (meta gegevens van exemplaar)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Privé IP-adres van IPv4 (meta gegevens van exemplaar)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Openbaar IP-adres van IPv4 (meta gegevens van exemplaar)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-subnet adres/voor voegsel (meta gegevens van exemplaar)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IP-adres van IPv6 (meta gegevens van exemplaar)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-adres (meta gegevens van exemplaar)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Volgende stappen
* De Windows-documentatie pagina van de hoofd console bevindt zich [hier](serial-console-windows.md).
* Seriële console van het is ook beschikbaar voor [Linux](serial-console-linux.md) VM's.
* Meer informatie over [diagnostische gegevens over opstarten](boot-diagnostics.md).

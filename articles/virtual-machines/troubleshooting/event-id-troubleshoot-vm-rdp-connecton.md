---
title: Problemen met RDP-verbindings problemen van Azure VM oplossen per gebeurtenis-ID | Microsoft Docs
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
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 166648402eec7f8033c090a3f7862a902bae4be6
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154196"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Problemen met Azure VM RDP-verbinding oplossen op basis van gebeurtenis-id 

In dit artikel wordt uitgelegd hoe u gebeurtenis-Id's gebruikt om problemen op te lossen die verhinderen dat een RDP-verbinding (Extern bureaublad Protocol) met een virtuele machine (VM) van Azure wordt voor komen.

## <a name="symptoms"></a>Symptomen

U probeert een Extern bureaublad protocol-sessie (RDP) te gebruiken om verbinding te maken met een virtuele machine van Azure. Nadat u uw referenties hebt ingevoerd, mislukt de verbinding en wordt het volgende fout bericht weer gegeven:

**Deze computer kan geen verbinding maken met de externe computer. Probeer opnieuw verbinding te maken. als het probleem zich blijft voordoen, neemt u contact op met de eigenaar van de externe computer of uw netwerk beheerder.**

Als u dit probleem wilt oplossen, controleert u de gebeurtenis logboeken op de virtuele machine en raadpleegt u de volgende scenario's.

## <a name="before-you-troubleshoot"></a>Voordat u problemen oplossen

### <a name="create-a-backup-snapshot"></a>Een back-upmomentopname maken

Als u een back-upmomentopname wilt maken, volgt u de stappen in [een moment opname van een schijf](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Extern verbinding maken met de virtuele machine

Als u extern verbinding wilt maken met de virtuele machine, gebruikt u een van de methoden in [het gebruik van externe hulpprogram ma's voor het oplossen van problemen met Azure-vm's](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>Scenario 1

### <a name="event-logs"></a>Gebeurtenislogboeken

Voer in een CMD-exemplaar de volgende opdrachten uit om te controleren of gebeurtenis 1058 of gebeurtenis 1057 wordt geregistreerd in het systeem logboek in de afgelopen 24 uur:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboek naam:**      Systeem <br />
**Bron**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:** *tijd* <br />
**Gebeurtenis-ID:**      1058 <br />
**Taak categorie:** Geen <br />
**Afvlakking**         Fout <br />
**Woord**      Klassiek <br />
**Gebruiker:**          N/A <br />
**Computer:** *computer* <br />
**Beschrijving:** De RD Session Host-server heeft het verlopen zelfondertekende certificaat dat wordt gebruikt voor de verificatie van de RD Session Host-server, niet vervangen door SSL-verbindingen. De relevante status code is toegang geweigerd.

**Logboek naam:**      Systeem <br />
**Bron**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:** *tijd* <br />
**Gebeurtenis-ID:**      1058 <br />
**Taak categorie:** Geen <br />
**Afvlakking**         Fout <br />
**Woord**      Klassiek <br />
**Gebruiker:**          N/A <br />
**Computer:** *computer* <br />
**Beschrijving:** Extern bureau blad-sessiehostserver kan geen nieuw zelfondertekend certificaat maken om te worden gebruikt voor de verificatie van RD Session Host-servers voor SSL-verbindingen. de relevante status code is al-object.

**Logboek naam:**      Systeem <br />
**Bron**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:** *tijd* <br />
**Gebeurtenis-ID:**      1057 <br />
**Taak categorie:** Geen <br />
**Afvlakking**         Fout <br />
**Woord**      Klassiek <br />
**Gebruiker:**          N/A <br />
**Computer:** *computer* <br />
**Beschrijving:** De extern bureau blad-sessiehostserver kan geen nieuw ondertekend certificaat maken dat kan worden gebruikt voor de verificatie van RD Session Host-servers op SSL-verbindingen. De relevante status code bevindt zich niet in het sleutel paar

U kunt ook controleren op SCHANNEL-fout gebeurtenissen 36872 en 36870 door de volgende opdrachten uit te voeren:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboek naam:**      Systeem <br />
**Bron**        Veilige <br />
**Datum:** — <br />
**Gebeurtenis-ID:**      36870 <br />
**Taak categorie:** Geen <br />
**Afvlakking**         Fout <br />
**Woord**       <br />
**Gebruiker:**          OPGEHAALD <br />
**Computer:** *computer* <br />
**Beschrijving:** Er is een onherstelbare fout opgetreden bij het openen van de persoonlijke sleutel van de SSL-server referentie. De fout code die is geretourneerd door de cryptografische module, is 0x8009030D.  <br />
De interne fout status is 10001.

### <a name="cause"></a>Oorzaak
Dit probleem doet zich voor omdat de lokale RSA-versleutelings sleutels in de map MachineKeys op de virtuele machine niet kunnen worden geopend. Dit probleem kan een van de volgende oorzaken hebben:

1. Onjuiste configuratie van machtigingen voor de map Machinekeys of de RSA-bestanden.

2. Beschadigde of ontbrekende RSA-sleutel.

### <a name="resolution"></a>Oplossing

U moet de juiste machtigingen voor het RDP-certificaat instellen met behulp van de volgende stappen om dit probleem op te lossen.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Toestemming geven voor de map MachineKeys

1. Maak een script met behulp van de volgende inhoud:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Voer dit script uit om de machtigingen van de map MachineKey opnieuw in te stellen en de RSA-bestanden opnieuw in te stellen op de standaard waarden.

3.  Probeer opnieuw toegang te krijgen tot de virtuele machine.

Nadat u het script hebt uitgevoerd, kunt u de volgende bestanden controleren die problemen met de machtigingen ondervinden:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Zelf-ondertekend RDP-certificaat vernieuwen

Als het probleem zich blijft voordoen, voert u het volgende script uit om ervoor te zorgen dat het zelf-ondertekende RDP-certificaat wordt vernieuwd:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Als u het certificaat niet kunt vernieuwen, voert u de volgende stappen uit om het certificaat te verwijderen:

1. Open het vak **uitvoeren** op een andere virtuele machine in hetzelfde VNET, typ **MMC**en druk op **OK**. 

2. Selecteer in het menu **bestand** de optie **module toevoegen/verwijderen**.

3. Selecteer in de lijst **beschik bare** modules de optie **certificaten**en selecteer vervolgens **toevoegen**.

4. Selecteer **computer account**en selecteer **volgende**.

5. Selecteer **een andere computer**en voeg vervolgens het IP-adres van de virtuele machine toe die problemen ondervindt.
   >[!Note]
   >Probeer het interne netwerk te gebruiken om te voor komen dat u een virtueel IP-adres gebruikt.

6. Selecteer **volt ooien**en selecteer vervolgens **OK**.

   ![Computer selecteren](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Vouw de certificaten uit, ga naar de map externe Desktop\Certificates, klik met de rechter muisknop op het certificaat en selecteer vervolgens **verwijderen**.

8. Start de Extern bureaublad Configuration-service opnieuw:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Als u de opslag van MMC vernieuwt, wordt het certificaat op dit moment opnieuw weer gegeven. 

Probeer opnieuw toegang te krijgen tot de virtuele machine met behulp van RDP.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Secure Sockets Layer-certificaat (SSL) bijwerken

Als u de VM zo instelt dat een SSL-certificaat wordt gebruikt, voert u de volgende opdracht uit om de vinger afdruk op te halen. Controleer vervolgens of dit hetzelfde is als de vinger afdruk van het certificaat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Als dat niet het geval is, wijzigt u de vinger afdruk:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

U kunt ook proberen om de sleutel te verwijderen, zodat de RDP het zelfondertekende certificaat voor RDP gebruikt:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenario 2

### <a name="event-log"></a>Gebeurtenis logboek

Voer in een CMD-exemplaar de volgende opdrachten uit om te controleren of SCHANNEL-fout gebeurtenis 36871 in de afgelopen 24 uur is geregistreerd in het systeem logboek:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboek naam:**      Systeem <br />
**Bron**        Veilige <br />
**Datum:** — <br />
**Gebeurtenis-ID:**      36871 <br />
**Taak categorie:** Geen <br />
**Afvlakking**         Fout <br />
**Woord**       <br />
**Gebruiker:**          OPGEHAALD <br />
**Computer:** *computer* <br />
**Beschrijving:** Er is een onherstelbare fout opgetreden tijdens het maken van een TLS-server referentie. De interne fout status is 10013.
 
### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door beveiligings beleid. Wanneer oudere versies van TLS (zoals 1,0) zijn uitgeschakeld, mislukt de RDP-toegang.

### <a name="resolution"></a>Oplossing

RDP gebruikt TLS 1,0 als het standaard protocol. Het protocol kan echter worden gewijzigd in TLS 1,1, wat de nieuwe standaard is.

Zie [problemen met verificatie fouten oplossen wanneer u RDP gebruikt om verbinding te maken met Azure VM](troubleshoot-authentication-error-rdp-vm.md#tls-version)om dit probleem op te lossen.

## <a name="scenario-3"></a>Scenario 3

Als u de functie **extern bureaublad Connection Broker** op de VM hebt geïnstalleerd, controleert u of er in de afgelopen 24 uur sprake is van gebeurtenis 2056 of gebeurtenis 1296. Voer de volgende opdrachten uit in een CMD-exemplaar: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboek naam:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Bron**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:** *tijd* <br />
**Gebeurtenis-ID:**      2056 <br />
**Taak categorie:** (109) <br />
**Afvlakking**         Fout <br />
**Woord**       <br />
**Gebruiker:**          NETWERK SERVICE <br />
**Computer:** *FQDN van computer* <br />
**Beschrijving:** De beschrijving voor gebeurtenis-ID 2056 van de bron micro soft-Windows-TerminalServices-SessionBroker is niet gevonden. Het onderdeel dat deze gebeurtenis activeert, is niet geïnstalleerd op de lokale computer of de installatie is beschadigd. U kunt het onderdeel op de lokale computer installeren of herstellen. <br />
Als de gebeurtenis afkomstig is van een andere computer, moest de weer gave-informatie worden opgeslagen met de gebeurtenis. <br />
De volgende informatie is opgenomen in de gebeurtenis: <br />
NULL <br />
NULL <br />
Aanmelden bij de data base is mislukt.

**Logboek naam:**      Micro soft-Windows-TerminalServices-SessionBroker-client/operationeel <br />
**Bron**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:** *tijd* <br />
**Gebeurtenis-ID:**      1296 <br />
**Taak categorie:** (104) <br />
**Afvlakking**         Fout <br />
**Woord**       <br />
**Gebruiker:**          NETWERK SERVICE <br />
**Computer:** *FQDN van computer* <br />
**Beschrijving:** De beschrijving voor gebeurtenis-ID 1296 van de bron micro soft-Windows-TerminalServices-SessionBroker-client is niet gevonden. Het onderdeel dat deze gebeurtenis activeert, is niet geïnstalleerd op de lokale computer of de installatie is beschadigd. U kunt het onderdeel op de lokale computer installeren of herstellen.
Als de gebeurtenis afkomstig is van een andere computer, moest de weer gave-informatie worden opgeslagen met de gebeurtenis.
De volgende informatie is opgenomen in de gebeurtenis:  <br />
*SMS* <br />
*SMS* <br />
Extern bureaublad Connection Broker is niet gereed voor RPC-communicatie.

### <a name="cause"></a>Oorzaak

Dit probleem treedt op omdat de hostnaam van de Extern bureaublad Connection Broker-server wordt gewijzigd, wat geen ondersteunde wijziging is. 

De hostnaam bevat vermeldingen en afhankelijkheden voor de interne data base van Windows, die wordt vereist door Extern bureaublad service farm om te kunnen werken. Als u de hostnaam wijzigt nadat de farm al is gebouwd, worden er veel fouten gegenereerd waardoor de Broker-server niet meer werkt.

### <a name="resolution"></a>Oplossing 

Om dit probleem op te lossen, moet de Extern bureaublad Connection Broker-rol en de interne data base van Windows opnieuw worden geïnstalleerd.

## <a name="next-steps"></a>Volgende stappen

[Schannel-gebeurtenissen](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Technisch overzicht van Schannel-SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP mislukt met gebeurtenis-ID 1058 & gebeurtenis 36870 met Extern bureaublad Session Host-certificaat & SSL-communicatie](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 of Schannel 36870 op een domein controller](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Gebeurtenis-ID 1058 — Extern bureaublad-services verificatie en versleuteling](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)


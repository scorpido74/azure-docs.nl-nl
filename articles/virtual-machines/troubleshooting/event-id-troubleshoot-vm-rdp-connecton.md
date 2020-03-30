---
title: Problemen met Azure VM RDP-verbindingsproblemen oplossen door gebeurtenis-id | Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154196"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Problemen met Azure VM RDP-verbinding oplossen op basis van gebeurtenis-id 

In dit artikel wordt uitgelegd hoe u gebeurtenis--identiteits-in-het-bed-installatie gebruiken om problemen op te lossen die een RDP-verbinding (Remote Desktop Protocol) met een VM (Azure Virtual Machine) voorkomen.

## <a name="symptoms"></a>Symptomen

U probeert een RDP-sessie (Remote Desktop Protocol) te gebruiken om verbinding te maken met een Azure VM. Nadat u uw referenties hebt ingevoerd, mislukt de verbinding en ontvangt u het volgende foutbericht:

**Deze computer kan geen verbinding maken met de externe computer. Probeer opnieuw verbinding te maken, als het probleem zich voortzet, neem dan contact op met de eigenaar van de externe computer of uw netwerkbeheerder.**

Als u dit probleem wilt oplossen, raadpleegt u de gebeurtenislogboeken op de vm en raadpleegt u de volgende scenario's.

## <a name="before-you-troubleshoot"></a>Voordat u problemen oplost

### <a name="create-a-backup-snapshot"></a>Een back-upmomentopname maken

Als u een back-upmomentopname wilt maken, volgt u de stappen in [Momentopname een schijf](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Op afstand verbinding maken met de VIRTUELE V.T.V.

Als u op afstand verbinding wilt maken met de virtuele machine, gebruikt u een van de methoden in [Hulpmiddelen op afstand gebruiken om Azure VM-problemen op te lossen.](remote-tools-troubleshoot-azure-vm-issues.md)

## <a name="scenario-1"></a>Scenario 1

### <a name="event-logs"></a>Gebeurtenislogboeken

Voer in een CMD-instantie de volgende opdrachten uit om te controleren of gebeurtenis 1058 of gebeurtenis 1057 in de afgelopen 24 uur is aangemeld bij het systeemlogboek:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboeknaam:**      Systeem <br />
**Bron:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tijd* <br />
**Gebeurtenis-id:** 1058 <br />
**Taakcategorie:** Geen <br />
**Niveau:**         Fout <br />
**Trefwoorden:**      Klassieke <br />
**Gebruiker:**          N/a <br />
**Computer:**      *computer* <br />
**Beschrijving:** De Extern bureaublad-sessiehostserver kan het verlopen zelfondertekende certificaat dat wordt gebruikt voor verificatie van extern bureaublad-sessiehostserver op SSL-verbindingen niet vervangen. De relevante statuscode is Access wordt geweigerd.

**Logboeknaam:**      Systeem <br />
**Bron:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tijd* <br />
**Gebeurtenis-id:** 1058 <br />
**Taakcategorie:** Geen <br />
**Niveau:**         Fout <br />
**Trefwoorden:**      Klassieke <br />
**Gebruiker:**          N/a <br />
**Computer:**      *computer* <br />
**Beschrijving:** Extern bureaublad-sessiehostserver heeft geen nieuw zelfondertekend certificaat gemaakt dat moet worden gebruikt voor verificatie van extern bureaublad-sessiehostserver op SSL-verbindingen, de relevante statuscode was al bestaat.

**Logboeknaam:**      Systeem <br />
**Bron:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Datum:**          *tijd* <br />
**Gebeurtenis-id:** 1057 <br />
**Taakcategorie:** Geen <br />
**Niveau:**         Fout <br />
**Trefwoorden:**      Klassieke <br />
**Gebruiker:**          N/a <br />
**Computer:**      *computer* <br />
**Beschrijving:** De Extern bureaublad-sessiehostserver kan geen nieuw zelfondertekend certificaat maken dat kan worden gebruikt voor verificatie van Extern bureaublad-sessiehostserver op SSL-verbindingen. De relevante statuscode is Keyset bestaat niet

U ook controleren op Foutgebeurtenissen 36872 en 36870 van SCHANNEL door de volgende opdrachten uit te voeren:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboeknaam:**      Systeem <br />
**Bron:**        Schannel <br />
**Datum:** — <br />
**Gebeurtenis-id:** 36870 <br />
**Taakcategorie:** Geen <br />
**Niveau:**         Fout <br />
**Zoekwoorden:**       <br />
**Gebruiker:**          Systeem <br />
**Computer:**      *computer* <br />
**Beschrijving:** Er is een fatale fout opgetreden bij een poging om toegang te krijgen tot de privésleutel van de SSL-serverreferenties. De foutcode die is geretourneerd van de cryptografische module is 0x8009030D.  <br />
De interne foutstatus is 10001.

### <a name="cause"></a>Oorzaak
Dit probleem treedt op omdat de lokale RSA-coderingssleutels in de map MachineKeys op de VM niet toegankelijk zijn. Dit probleem kan zich voordoen om een van de volgende redenen:

1. Foute machtigingenconfiguratie op de map Machinekeys of de RSA-bestanden.

2. Beschadigde of ontbrekende RSA-toets.

### <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, moet u met deze stappen de juiste machtigingen voor het RDP-certificaat instellen.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Toestemming verlenen aan de map MachineKeys

1. Maak een script met de volgende inhoud:

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

2.  Voer dit script uit om de machtigingen van de map MachineKey opnieuw in te stellen en de RSA-bestanden opnieuw in te stellen op de standaardwaarden.

3.  Probeer opnieuw toegang te krijgen tot de virtuele machine.

Nadat u het script hebt uitgevoerd, u de volgende bestanden controleren die problemen ondervinden met machtigingen:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>Rdp-certificaat verlengen

Als het probleem blijft bestaan, voert u het volgende script uit om ervoor te zorgen dat het zelfondertekende RDP-certificaat wordt verlengd:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Als u het certificaat niet verlengen, voert u de volgende stappen uit om te proberen het certificaat te verwijderen:

1. Open op een andere VM in dezelfde VNET het vak **Uitvoeren,** typ **mmc**en druk op **OK.** 

2. Selecteer **Inmaak toevoegen/verwijderen in**het menu **Bestand** .

3. Selecteer **certificaten**in de lijst **Beschikbare snap-ins** en selecteer **Vervolgens Toevoegen**.

4. Selecteer **Computeraccount**en selecteer **Volgende**.

5. Selecteer **Een andere computer**en voeg vervolgens het IP-adres van de VM toe dat problemen heeft.
   >[!Note]
   >Probeer het interne netwerk te gebruiken om te voorkomen dat u een virtueel IP-adres gebruikt.

6. Selecteer **Voltooien**en selecteer **OK**.

   ![Computer selecteren](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Vouw de certificaten uit, ga naar de map Extern bureaublad\Certificaten, klik met de rechtermuisknop op het certificaat en selecteer **Verwijderen**.

8. Start de service Extern bureaublad-configuratie opnieuw:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Als u de winkel op dit moment vernieuwt vanuit mmc, wordt het certificaat opnieuw weergegeven. 

Probeer de VM te openen door opnieuw RDP te gebruiken.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>SSL-certificaat (Secure Sockets Layer) bijwerken

Als u de VM instelt om een SSL-certificaat te gebruiken, voert u de volgende opdracht uit om de duimafdruk op te halen. Controleer vervolgens of het hetzelfde is als de duimafdruk van het certificaat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Als dit niet het is, wijzigt u de duimafdruk:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

U ook proberen de sleutel te verwijderen, zodat de RDP het zelfondertekende certificaat voor RDP gebruikt:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Scenario 2

### <a name="event-log"></a>Gebeurtenislogboek

Voer in een CMD-instantie de volgende opdrachten uit om te controleren of SCHANNEL-foutgebeurtenis 36871 in de afgelopen 24 uur is aangemeld in het systeemlogboek:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboeknaam:**      Systeem <br />
**Bron:**        Schannel <br />
**Datum:** — <br />
**Gebeurtenis-id:** 36871 <br />
**Taakcategorie:** Geen <br />
**Niveau:**         Fout <br />
**Zoekwoorden:**       <br />
**Gebruiker:**          Systeem <br />
**Computer:**      *computer* <br />
**Beschrijving:** Er is een fatale fout opgetreden tijdens het maken van een TLS-serverreferentie. De interne foutstatus is 10013.
 
### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door beveiligingsbeleid. Wanneer oudere versies van TLS (zoals 1.0) zijn uitgeschakeld, mislukt de RDP-toegang.

### <a name="resolution"></a>Oplossing

RDP gebruikt TLS 1.0 als standaardprotocol. Het protocol kan echter worden gewijzigd in TLS 1.1, de nieuwe standaard.

Zie [Verificatiefouten oplossen wanneer u RDP gebruikt om verbinding te maken met Azure VM](troubleshoot-authentication-error-rdp-vm.md#tls-version)als u dit probleem wilt oplossen.

## <a name="scenario-3"></a>Scenario 3

Als u de rol **Extern bureaublad Connection Broker** op de VM hebt geïnstalleerd, controleert u of er in de afgelopen 24 uur gebeurtenis 2056 of gebeurtenis 1296 is. Voer in een CMD-instantie de volgende opdrachten uit: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Logboeknaam:**      Microsoft-Windows-TerminalServices-SessionBroker/Operationeel <br />
**Bron:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Datum:**          *tijd* <br />
**Gebeurtenis-id:** 2056 <br />
**Taakcategorie:** (109) <br />
**Niveau:**         Fout <br />
**Zoekwoorden:**       <br />
**Gebruiker:**          NETWERKSERVICE <br />
**Computer:**      *computer fqdn* <br />
**Beschrijving:** De beschrijving voor gebeurtenis-id 2056 van de bron Microsoft-Windows-TerminalServices-SessionBroker kan niet worden gevonden. Het onderdeel dat deze gebeurtenis oproept, is niet geïnstalleerd op uw lokale computer of de installatie is beschadigd. U het onderdeel installeren of repareren op de lokale computer. <br />
Als de gebeurtenis op een andere computer is ontstaan, moest de weergave-informatie met de gebeurtenis worden opgeslagen. <br />
De volgende informatie werd bij het evenement opgenomen: <br />
NULL <br />
NULL <br />
Aanmelding bij de database is mislukt.

**Logboeknaam:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operationeel <br />
**Bron:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Datum:**          *tijd* <br />
**Gebeurtenis-id:** 1296 <br />
**Taakcategorie:** (104) <br />
**Niveau:**         Fout <br />
**Zoekwoorden:**       <br />
**Gebruiker:**          NETWERKSERVICE <br />
**Computer:**      *computer fqdn* <br />
**Beschrijving:** De beschrijving voor gebeurtenis-id 1296 van de bron Microsoft-Windows-TerminalServices-SessionBroker-Client kan niet worden gevonden. Het onderdeel dat deze gebeurtenis oproept, is niet geïnstalleerd op uw lokale computer of de installatie is beschadigd. U het onderdeel installeren of repareren op de lokale computer.
Als de gebeurtenis op een andere computer is ontstaan, moest de weergave-informatie met de gebeurtenis worden opgeslagen.
De volgende informatie werd bij het evenement opgenomen:  <br />
*Tekst* <br />
*Tekst* <br />
Remote Desktop Connection Broker is nog niet klaar voor RPC-communicatie.

### <a name="cause"></a>Oorzaak

Dit probleem treedt op omdat de hostnaam van de Extern bureaublad Connection Broker-server is gewijzigd, wat geen ondersteunde wijziging is. 

De hostnaam bevat vermeldingen en afhankelijkheden van de Windows Internal Database, die vereist is door de farm Extern bureaublad-service om te kunnen werken. Het wijzigen van de hostnaam nadat de farm al is gebouwd, veroorzaakt veel fouten en kan ertoe leiden dat de brokerserver niet meer werkt.

### <a name="resolution"></a>Oplossing 

Om dit probleem op te lossen, moeten de rol Extern bureaublad Connection Broker en de interne windowsdatabase opnieuw worden geïnstalleerd.

## <a name="next-steps"></a>Volgende stappen

[Schannel-evenementen](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Technisch overzicht van Schannel-SSP](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP mislukt met gebeurtenis-id 1058 & gebeurtenis 36870 met Extern bureaublad-sessiehostcertificaat & SSL-communicatie](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 of Schannel 36870 op een domeincontroller](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Gebeurtenis-id 1058 — Verificatie en versleuteling van Extern bureaublad-services](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)


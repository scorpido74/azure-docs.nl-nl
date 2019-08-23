---
title: Problemen met verificatie fouten oplossen wanneer u RDP gebruikt om verbinding te maken met Azure VM | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard,csscontent
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 68037ab55918a76567f2dfee7cbda1d84d0c442e
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69908006"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Problemen met verificatie fouten oplossen wanneer u RDP gebruikt om verbinding te maken met Azure VM

In dit artikel vindt u informatie over het oplossen van verificatie fouten die zich voordoen wanneer u Remote Desktop Protocol (RDP)-verbinding gebruikt om verbinding te maken met een virtuele machine van Azure (VM).

## <a name="symptoms"></a>Symptomen

U legt een scherm opname van een virtuele Azure-machine vast waarin het welkomst scherm wordt weer gegeven en geeft aan dat het besturings systeem wordt uitgevoerd. Wanneer u echter verbinding probeert te maken met de virtuele machine met behulp van Verbinding met extern bureaublad, wordt een van de volgende fout berichten weer gegeven.

### <a name="error-message-1"></a>Fout bericht 1

**Er is een verificatie fout opgetreden. Kan geen verbinding maken met de lokale beveiligings autoriteit.**

### <a name="error-message-2"></a>Fout bericht 2

**Op de externe computer waarmee u verbinding wilt maken, is verificatie op netwerkniveau (NLA) vereist, maar er kan geen contact worden gemaakt met de Windows-domein controller om NLA uit te voeren. Als u een beheerder bent op de externe computer, kunt u NLA uitschakelen met behulp van de opties op het tabblad Extern van het dialoog venster systeem eigenschappen.**

### <a name="error-message-3-generic-connection-error"></a>Fout bericht 3 (algemene verbindings fout)

**Deze computer kan geen verbinding maken met de externe computer. Probeer opnieuw verbinding te maken. als het probleem zich blijft voordoen, neemt u contact op met de eigenaar van de externe computer of uw netwerk beheerder.**

## <a name="cause"></a>Oorzaak

Er zijn verschillende redenen waarom NLA de RDP-toegang tot een virtuele machine kan blok keren.

### <a name="cause-1"></a>Oorzaak 1

De virtuele machine kan niet communiceren met de domein controller (DC). Dit probleem kan verhinderen dat een RDP-sessie toegang kan krijgen tot een virtuele machine met behulp van domein referenties. U kunt zich echter wel aanmelden met de referenties van de lokale beheerder. Dit probleem kan optreden in de volgende situaties:

1. Het Active Directory beveiligings kanaal tussen deze virtuele machine en de domein controller is verbroken.

2. De virtuele machine heeft een oude kopie van het account wachtwoord en de domein controller heeft een nieuwere kopie.

3. De domein controller waarmee deze VM verbinding maakt, is beschadigd.

### <a name="cause-2"></a>Oorzaak 2

Het versleutelings niveau van de virtuele machine is hoger dan de naam die wordt gebruikt door de client computer.

### <a name="cause-3"></a>Oorzaak 3

De protocollen TLS 1,0, 1,1 of 1,2 (Server) zijn uitgeschakeld op de virtuele machine.

### <a name="cause-4"></a>Oorzaak 4

De virtuele machine is ingesteld om het aanmelden met behulp van domein referenties uit te scha kelen en de lokale beveiligings autoriteit (LSA) is onjuist ingesteld.

### <a name="cause-5"></a>Oorzaak 5

De virtuele machine is ingesteld voor het accepteren van alleen algoritme-verbindingen die voldoen aan de Federal Information Processing Standard (FIPS). Dit gebeurt meestal met behulp van Active Directory-beleid. Dit is een zeldzame configuratie, maar FIPS kan alleen worden afgedwongen voor Extern bureaublad verbindingen.

## <a name="before-you-troubleshoot"></a>Voordat u problemen oplossen

### <a name="create-a-backup-snapshot"></a>Een back-upmomentopname maken

Als u een back-upmomentopname wilt maken, volgt u de stappen in [een moment opname van een schijf](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Extern verbinding maken met de virtuele machine

Als u extern verbinding wilt maken met de virtuele machine, gebruikt u een van de methoden in [het gebruik van externe hulpprogram ma's voor het oplossen van problemen met Azure-vm's](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Group Policy client-service

Als dit een virtuele machine in een domein is, moet u eerst de groepsbeleid-client service stoppen om te voor komen dat Active Directory beleid de wijzigingen overschrijft. U doet dit door de volgende opdracht uitvoeren:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Nadat het probleem is opgelost, herstelt u de mogelijkheid van deze VM om contact op te nemen met het domein om het meest recente groeps beleidsobject van het domein op te halen. U kunt dit doen door de volgende opdrachten uit te voeren:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Als de wijziging wordt teruggedraaid, betekent dit dat het probleem wordt veroorzaakt door een Active Directory beleid. 

### <a name="workaround"></a>Tijdelijke oplossing

U kunt dit probleem omzeilen door de volgende opdrachten uit te voeren in het opdracht venster om NLA uit te scha kelen:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Start vervolgens de VM opnieuw op.

Als u NLA opnieuw wilt inschakelen, voert u de volgende opdracht uit en start u de VM opnieuw op:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="for-domain-joined-vms"></a>Voor Vm's die lid zijn van een domein

Om dit probleem op te lossen, controleert u eerst of de virtuele machine verbinding kan maken met een domein controller en of de domein controller de status ' in orde ' heeft en aanvragen van de virtuele machine kan verwerken.

>[!Note] 
>Als u de status van de domein controller wilt testen, kunt u een andere virtuele machine gebruiken op hetzelfde VNET en subnet die dezelfde aanmeldings server delen.

Maak verbinding met de virtuele machine met behulp van Seriële console, externe CMD of externe Power shell, volgens de stappen in de sectie ' verbinding maken met de virtuele machine op afstand '.

Als u wilt bepalen met welke domein controller verbinding wordt gemaakt, voert u de volgende opdracht uit in de-console: 

```cmd
set | find /i "LOGONSERVER"
```

Controleer vervolgens de status van het beveiligde kanaal tussen de virtuele machine en de domein controller. U doet dit door de volgende opdracht uit te voeren in een Power shell-exemplaar met verhoogde bevoegdheden. Met deze opdracht wordt een Booleaanse vlag geretourneerd die aangeeft of het beveiligde kanaal actief is:

```powershell
Test-ComputerSecureChannel -verbose
```

Als het kanaal is beschadigd, voert u de volgende opdracht uit om het te herstellen:

```powershell
Test-ComputerSecureChannel -repair
```

Zorg ervoor dat het wacht woord voor het computer account in Active Directory wordt bijgewerkt op de VM en de domein controller:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Als de communicatie tussen de domein controller en de VM goed is, maar de domein controller niet in orde is om een RDP-sessie te openen, kunt u proberen om de domein controller opnieuw op te starten.

Als de voor gaande opdrachten het communicatie probleem met het domein niet hebben opgelost, kunt u deze VM opnieuw toevoegen aan het domein. Voer de volgende stappen uit om dit te doen:

1. Maak een script met de naam unjoin's. ps1 met behulp van de volgende inhoud en implementeer vervolgens het script als een aangepaste script extensie op de Azure Portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Dit script neemt de virtuele machine uit het domein geforceerd en start deze 10 seconden later opnieuw op. Vervolgens moet u het computer object op het domein opschonen.

2.  Nadat het opschonen is voltooid, wordt deze VM opnieuw lid van het domein. Als u dit wilt doen, maakt u een script met de naam JoinDomain. ps1 met behulp van de volgende inhoud en implementeert u het script als een aangepaste script extensie op de Azure Portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Hiermee wordt de virtuele machine in het domein gekoppeld met behulp van de opgegeven referenties.

Als het Active Directory kanaal in orde is, wordt het computer wachtwoord bijgewerkt en wordt de domein controller op de verwachte wijze uitgevoerd. Probeer de volgende stappen uit te voeren.

Als het probleem zich blijft voordoen, controleert u of de domein referentie is uitgeschakeld. Hiervoor opent u een opdracht prompt venster met verhoogde bevoegdheid en voert u de volgende opdracht uit om te bepalen of de virtuele machine is ingesteld om domein accounts uit te scha kelen voor aanmelding bij de virtuele machine:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Als de sleutel is ingesteld op **1**, betekent dit dat de server is ingesteld op het niet toestaan van domein referenties. Wijzig deze sleutel in **0**.

### <a name="for-standalone-vms"></a>Voor zelfstandige Vm's

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel controleren

Voer in een CMD-exemplaar de volgende opdracht uit om een query uit te voeren op de register waarde **MinEncryptionLevel** :

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Voer de volgende stappen uit op basis van de register waarde:

* 4 (FIPS): Ga naar [FIPs-compatibele algoritmes-verbindingen controleren](#fips-compliant).

* 3 (128-bits versleuteling): Stel de ernst in op **2** door de volgende opdracht uit te voeren:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (hoogste versleuteling mogelijk, zoals bepaald door de client): U kunt proberen om de versleuteling in te stellen op de minimum waarde **1** door de volgende opdracht uit te voeren:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Start de VM opnieuw op zodat de wijzigingen in het REGI ster van kracht worden.

#### <a name="tls-version"></a>TLS-versie

Afhankelijk van het systeem maakt RDP gebruik van het protocol TLS 1,0, 1,1 of 1,2 (Server). Als u wilt opvragen hoe deze protocollen zijn ingesteld op de virtuele machine, opent u een CMD-exemplaar en voert u de volgende opdrachten uit:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Als de geretourneerde waarden niet allemaal **1**zijn, betekent dit dat het protocol is uitgeschakeld. Voer de volgende opdrachten uit om deze protocollen in te scha kelen:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Voor andere protocol versies kunt u de volgende opdrachten uitvoeren:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> De SSH/TLS-versie x. x ophalen uit het gast besturingssysteem Logboeken in de SCHANNEL-fouten.

#### <a name="fips-compliant"></a>FIPs-compatibele algoritme-verbindingen controleren

Extern bureau blad kan worden afgedwongen om alleen FIPs-compatibele algoritme verbindingen te gebruiken. Dit kan worden ingesteld met behulp van een register sleutel. Hiervoor opent u een opdracht prompt venster met verhoogde bevoegdheid en voert u een query uit op de volgende sleutels:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Als de opdracht **1**retourneert, wijzigt u de register waarde in **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Controleer wat het huidige MinEncryptionLevel is op de virtuele machine:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Als de opdracht **4**retourneert, wijzigt u de register waarde in **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Start de VM opnieuw op zodat de wijzigingen in het REGI ster van kracht worden.

## <a name="next-steps"></a>Volgende stappen

[Methode SetEncryptionLevel van de klasse Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Server authenticatie-en versleutelings niveaus configureren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Klasse Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)

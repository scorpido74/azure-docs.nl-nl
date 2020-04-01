---
title: Verificatiefouten oplossen wanneer u RDP gebruikt om verbinding te maken met Azure VM | Microsoft Documenten
description: Meer informatie over het oplossen van verificatiefouten die optreden wanneer u Extern bureaublad-protocol (RDP) gebruikt om verbinding te maken met een virtuele Azure-machine (VM).
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
ms.openlocfilehash: 03356c0b4a93f4befdbc529523e58642137a8887
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420822"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Verificatiefouten oplossen als u RDP gebruikt om verbinding te maken met Azure-VM's

Met dit artikel u verificatiefouten oplossen die optreden wanneer u de RDP-verbinding (Remote Desktop Protocol) gebruikt om verbinding te maken met een virtuele Azure-machine (VM).

## <a name="symptoms"></a>Symptomen

U maakt een schermafbeelding van een Azure VM die het welkomstscherm weergeeft en aangeeft dat het besturingssysteem wordt uitgevoerd. Wanneer u echter verbinding probeert te maken met de virtuele machine met verbinding met extern bureaublad, ontvangt u een van de volgende foutmeldingen.

### <a name="error-message-1"></a>Foutbericht 1

**Er is een verificatiefout opgetreden. Er kan geen contact worden opgenomen met de lokale veiligheidsautoriteit.**

### <a name="error-message-2"></a>Foutbericht 2

**De externe computer waarmee u verbinding probeert te maken, vereist Network Level Authentication (NLA), maar er kan geen contact worden opgenomen met uw Windows-domeincontroller om NLA uit te voeren. Als u een beheerder bent op de externe computer, u NLA uitschakelen met behulp van de opties op het tabblad Afstandsbediening van het dialoogvenster Systeemeigenschappen.**

### <a name="error-message-3-generic-connection-error"></a>Foutbericht 3 (algemene verbindingsfout)

**Deze computer kan geen verbinding maken met de externe computer. Probeer opnieuw verbinding te maken, als het probleem zich voortzet, neem dan contact op met de eigenaar van de externe computer of uw netwerkbeheerder.**

## <a name="cause"></a>Oorzaak

Er zijn meerdere redenen waarom NLA de RDP-toegang tot een VM kan blokkeren.

### <a name="cause-1"></a>Oorzaak 1

De VM kan niet communiceren met de domeincontroller (DC). Dit probleem kan voorkomen dat een RDP-sessie toegang krijgt tot een VM met behulp van domeinreferenties. U zich echter nog steeds aanmelden met behulp van de referenties van de lokale beheerder. Dit probleem kan zich in de volgende situaties voordoen:

1. Het Active Directory-beveiligingskanaal tussen deze VM en de DC is verbroken.

2. De VM heeft een oude kopie van het accountwachtwoord en de DC heeft een nieuwere kopie.

3. De DC waarmee deze VM verbinding maakt, is ongezond.

### <a name="cause-2"></a>Oorzaak 2

Het versleutelingsniveau van de VM is hoger dan het niveau dat wordt gebruikt door de clientcomputer.

### <a name="cause-3"></a>Oorzaak 3

De TLS 1.0-, 1.1- of 1.2 (server)-protocollen zijn uitgeschakeld op de VM.

### <a name="cause-4"></a>Oorzaak 4

De VM is ingesteld om het aanmelden uit te schakelen met behulp van domeinreferenties en de Local Security Authority (LSA) is onjuist ingesteld.

### <a name="cause-5"></a>Oorzaak 5

De VM is ingesteld om alleen FIPS-compatibele algoritmeverbindingen (Federal Information Processing Standard) te accepteren. Dit gebeurt meestal met behulp van Active Directory-beleid. Dit is een zeldzame configuratie, maar FIPS kan alleen worden afgedwongen voor extern bureaublad-verbindingen.

## <a name="before-you-troubleshoot"></a>Voordat u problemen oplost

### <a name="create-a-backup-snapshot"></a>Een back-upmomentopname maken

Als u een back-upmomentopname wilt maken, volgt u de stappen in [Momentopname een schijf](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Op afstand verbinding maken met de VIRTUELE V.T.V.

Als u op afstand verbinding wilt maken met de virtuele machine, gebruikt u een van de methoden in [Hulpmiddelen op afstand gebruiken om Azure VM-problemen op te lossen.](remote-tools-troubleshoot-azure-vm-issues.md)

### <a name="group-policy-client-service"></a>Clientservice voor groepsbeleid

Als dit een vm met domeinis verbonden, stopt u eerst de clientservice Groepsbeleid om te voorkomen dat active directorybeleid de wijzigingen overschrijft. Voer hiervoor de volgende opdracht uit:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Nadat het probleem is opgelost, herstelt u de mogelijkheid van deze vm om contact op te nemen met het domein om de nieuwste GPO uit het domein op te halen. Voer hiervoor de volgende opdrachten uit:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Als de wijziging wordt teruggedraaid, betekent dit dat een Active Directory-beleid het probleem veroorzaakt. 

### <a name="workaround"></a>Tijdelijke oplossing

Voer de volgende opdrachten uit in het opdrachtvenster om NLA uit te schakelen om dit probleem op te lossen:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Start vervolgens de VM opnieuw.

Als u NLA opnieuw wilt inschakelen, voert u de volgende opdracht uit en start u de VM opnieuw:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="for-domain-joined-vms"></a>Vm's die zijn verbonden aan domeinen

Als u dit probleem wilt oplossen, controleert u eerst of de VM verbinding kan maken met een DC en of de DC een status van 'gezond' heeft en aanvragen van de VM kan afhandelen.

>[!Note] 
>Als u de DC-status wilt testen, u een andere VM op hetzelfde VNET en subnet gebruiken die dezelfde aanmeldingsserver delen.

Maak verbinding met de VM die het probleem heeft met behulp van seriële console, externe CMD of externe PowerShell, volgens de stappen in de sectie 'Verbinding maken met de VM op afstand'.

Voer de volgende opdracht uit in de console om te bepalen met welke DC de VM verbinding maakt: 

```cmd
set | find /i "LOGONSERVER"
```

Controleer vervolgens de status van het beveiligde kanaal tussen de VM en de DC. Voer hiervoor de volgende opdracht uit in een verhoogde PowerShell-instantie. Met deze opdracht wordt een Booleaanse vlag geretourneerd die aangeeft of het beveiligde kanaal nog leeft:

```powershell
Test-ComputerSecureChannel -verbose
```

Als het kanaal is verbroken, voert u de volgende opdracht uit om het kanaal te repareren:

```powershell
Test-ComputerSecureChannel -repair
```

Controleer of het wachtwoord van het computeraccount in Active Directory wordt bijgewerkt op de VM en de DC:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Als de communicatie tussen de DC en de VM goed is, maar de DC niet gezond genoeg is om een RDP-sessie te openen, u proberen de DC opnieuw op te starten.

Als de voorgaande opdrachten het communicatieprobleem niet naar het domein hebben opgelost, u deze VM opnieuw naar het domein voegen. Voer de volgende stappen uit om dit te doen:

1. Maak een script met de naam Unjoin.ps1 met de volgende inhoud en implementeer het script als aangepaste scriptextensie op de Azure-portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Dit script haalt de VM met geweld uit het domein en start deze 10 seconden later opnieuw op. Vervolgens moet u het computerobject aan de domeinzijde opschonen.

2.  Nadat de opschoning is gedaan, voegt u deze vm opnieuw aan in het domein. Maak hiervoor een script met de naam JoinDomain.ps1 met de volgende inhoud en implementeert het script als aangepaste scriptextensie op de Azure-portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Hiermee wordt de VM op het domein toegevoegd met behulp van de opgegeven referenties.

Als het Active Directory-kanaal in orde is, wordt het computerwachtwoord bijgewerkt en werkt de domeincontroller zoals verwacht, probeert u de volgende stappen.

Als het probleem zich blijft voordoen, controleert u of de domeinreferentie is uitgeschakeld. Open hiervoor een venster Met verhoogde opdrachtprompt en voer de volgende opdracht uit om te bepalen of de vm is ingesteld om domeinaccounts uit te schakelen om u aan te melden bij de VM:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Als de sleutel is ingesteld op **1,** betekent dit dat de server is ingesteld om geen domeinreferenties toe te staan. Wijzig deze toets in **0**.

### <a name="for-standalone-vms"></a>Voor zelfstandige VM's

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel controleren

Voer in een CMD-instantie de volgende opdracht uit om de registerwaarde **MinEncryptionLevel** op te vragen:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Volg de volgende stappen op basis van de registerwaarde:

* 4 (FIPS): Ga naar [FIPs-compatibele algoritmen controleren.](#fips-compliant)

* 3 (128-bits versleuteling): Stel de ernst in op **2** door de volgende opdracht uit te voeren:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Hoogst mogelijke versleuteling, zoals gedicteerd door de client): U proberen de versleuteling in te stellen op de minimumwaarde van **1** door de volgende opdracht uit te voeren:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Start de VM opnieuw op, zodat de wijzigingen in het register van kracht worden.

#### <a name="tls-version"></a>TLS-versie

Afhankelijk van het systeem gebruikt RDP het TLS 1.0-, 1.1- of 1.2 -protocol (server). Als u wilt opvragen hoe deze protocollen op de VM zijn ingesteld, opent u een CMD-instantie en voert u de volgende opdrachten uit:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Als de geretourneerde waarden niet alle **1**zijn, betekent dit dat het protocol is uitgeschakeld. Voer de volgende opdrachten uit om deze protocollen in te schakelen:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Voor andere protocolversies u de volgende opdrachten uitvoeren:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Download de SSH/TLS-versie x.x uit de GastOS-logboeken op de SCHANNEL-fouten.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a>Fips-compatibele algoritmenverbindingen controleren

Extern bureaublad kan worden afgedwongen om alleen FIPs-compatibele algoritmeverbindingen te gebruiken. Dit kan worden ingesteld met behulp van een registersleutel. Open hiervoor een verhoogd opdrachtpromptvenster en vraag vervolgens de volgende toetsen op:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Als de opdracht **1**retourneert, wijzigt u de registerwaarde in **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Controleer welke is de huidige MinEncryptionLevel op de VM:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Als de opdracht **4**retourneert, wijzigt u de registerwaarde in **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Start de VM opnieuw op, zodat de wijzigingen in het register van kracht worden.

## <a name="next-steps"></a>Volgende stappen

[De methode EncryptionLevel van de klasse Win32_TSGeneralSetting](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Serververificatie- en versleutelingsniveaus configureren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting klas](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)

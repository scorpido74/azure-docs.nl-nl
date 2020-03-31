---
title: Op afstand verbinding maken met uw StorSimple-apparaat
description: Hier wordt uitgelegd hoe u uw apparaat configureert voor extern beheer en hoe u verbinding maken met Windows PowerShell voor StorSimple via HTTP of HTTPS.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58d61df932da06e32bb4c8f21a3a296b185f02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80299010"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>Maak op afstand verbinding met uw StorSimple 8000-serie apparaat

## <a name="overview"></a>Overzicht

U op afstand verbinding maken met uw apparaat via Windows PowerShell. Wanneer u op deze manier verbinding maakt, ziet u geen menu. (U ziet alleen een menu als u de seriële console op het apparaat gebruikt om verbinding te maken.) Met Windows PowerShell remoting maakt u verbinding met een specifieke runspace. U ook de weergavetaal opgeven.

Ga voor meer informatie over het gebruik van Windows PowerShell-remoting om uw apparaat te beheren naar [Windows PowerShell voor StorSimple gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-windows-powershell-administration.md)

In deze zelfstudie wordt uitgelegd hoe u uw apparaat configureert voor extern beheer en hoe u verbinding maken met Windows PowerShell voor StorSimple. U HTTP of HTTPS gebruiken om op afstand verbinding te maken via Windows PowerShell. Wanneer u echter beslist hoe u verbinding maakt met Windows PowerShell voor StorSimple, moet u rekening houden met de volgende informatie:

* Rechtstreeks verbinding maken met de seriële console van het apparaat is veilig, maar verbinding maken met de seriële console via netwerkswitches is dat niet. Wees voorzichtig met het beveiligingsrisico wanneer u via netwerkswitches verbinding maakt met de seriële console van het apparaat.
* Verbinding maken via een HTTP-sessie biedt mogelijk meer beveiliging dan verbinding maken via de seriële console via het netwerk. Hoewel dit niet de meest veilige methode is, is het acceptabel op vertrouwde netwerken.
* Verbinding maken via een HTTPS-sessie met een zelfondertekend certificaat is de meest veilige en de aanbevolen optie.

U op afstand verbinding maken met de Windows PowerShell-interface. Externe toegang tot uw StorSimple-apparaat via de Windows PowerShell-interface is echter niet standaard ingeschakeld. U moet extern beheer op het apparaat eerst inschakelen en vervolgens op de client die wordt gebruikt om toegang te krijgen tot uw apparaat.

De in dit artikel beschreven stappen zijn uitgevoerd op een hostsysteem met Windows Server 2012 R2.

## <a name="connect-through-http"></a>Verbinding maken via HTTP

Verbinding maken met Windows PowerShell voor StorSimple via een HTTP-sessie biedt meer beveiliging dan verbinding maken via de seriële console van uw StorSimple-apparaat. Hoewel dit niet de meest veilige methode is, is het acceptabel op vertrouwde netwerken.

U de Azure-portal of de seriële console gebruiken om extern beheer te configureren. Kies uit de volgende procedures:

* De Azure-portal gebruiken om extern beheer via HTTP in te schakelen
* [Gebruik de seriële console om extern beheer via HTTP in te schakelen](#use-the-serial-console-to-enable-remote-management-over-http)

Nadat u extern beheer hebt ingeschakeld, gebruikt u de volgende procedure om de client voor te bereiden op een externe verbinding.

* [De client voorbereiden op verbinding op afstand](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>De Azure-portal gebruiken om extern beheer via HTTP in te schakelen

Voer de volgende stappen uit in de Azure-portal om extern beheer via HTTP in te schakelen.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Extern beheer inschakelen via de Azure-portal

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer **Apparaten** en selecteer en klik op het apparaat dat u wilt configureren voor extern beheer. Ga naar **Apparaatinstellingen > Beveiliging**.
2. Klik in het blad **Beveiligingsinstellingen** op **Extern beheer**.
3. Stel **extern beheer inschakelen** in het **beheerblad op afstand** op **Ja**.
4. U kunt nu verbinding maken via HTTP. (De standaardinstelling is om verbinding te maken via HTTPS.) Controleer of HTTP is geselecteerd.
   
   > [!NOTE]
   > Verbinding maken via HTTP is alleen acceptabel in vertrouwde netwerken.
   
5. Klik **op Opslaan** en wanneer u om bevestiging wordt gevraagd, selecteert u **Ja**.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Gebruik de seriële console om extern beheer via HTTP in te schakelen
Voer de volgende stappen uit op de seriële console van het apparaat om extern beheer in te schakelen.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Extern beheer inschakelen via de seriële console van het apparaat
1. Selecteer optie 1 in het menu van de seriële console. Ga voor meer informatie over het gebruik van de seriële console op het apparaat [naar Verbinding maken met Windows PowerShell voor StorSimple via de seriële console van het apparaat.](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
2. Typ bij de prompt:`Enable-HcsRemoteManagement –AllowHttp`
3. U wordt op de hoogte gesteld van de beveiligingsproblemen van het gebruik van HTTP om verbinding te maken met het apparaat. Bevestig, wanneer u daarom wordt gevraagd door **Y**te typen .
4. Controleer of HTTP is ingeschakeld door te typen:`Get-HcsSystem`
5. Controleer of in het veld **RemoteManagementMode** **HttpsAndHttpEnabled wordt weergegeven.** De volgende afbeelding toont deze instellingen in PuTTY.
   
     ![Seriële HTTPS en HTTP ingeschakeld](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>De client voorbereiden op verbinding op afstand
Voer de volgende stappen uit op de client om extern beheer in te schakelen.

#### <a name="to-prepare-the-client-for-remote-connection"></a>De client voorbereiden op verbinding op afstand
1. Start een Windows PowerShell-sessie als beheerder. Als u een Windows 10-client gebruikt, is de Windows Remote Management-service standaard ingesteld op handmatig. Mogelijk moet u de service starten door te typen:

    `Start-Service WinRM`
    
2. Typ de volgende opdracht om het IP-adres van het StorSimple-apparaat toe te voegen aan de lijst met vertrouwde hosts van de client:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     Vervang <*device_ip*> door het IP-adres van uw apparaat; bijvoorbeeld: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Typ de volgende opdracht om de apparaatreferenties in een variabele op te slaan: 
   
    ```
    $cred = Get-Credential
    ```
    
4. In het dialoogvenster dat wordt weergegeven:
   
   1. Typ de gebruikersnaam in deze indeling: *device_ip\SSAdmin*.
   2. Typ het wachtwoord van de apparaatbeheerder dat is ingesteld toen het apparaat is geconfigureerd met de wizard Setup. Het standaardwachtwoord is *Password1*.
5. Start een Windows PowerShell-sessie op het apparaat door deze opdracht te typen:
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > Als u een Windows PowerShell-sessie wilt maken voor gebruik `–Port` met het virtuele storSimple-apparaat, sluit u de parameter toe en geeft u de openbare poort op die u in Remoting hebt geconfigureerd voor StorSimple Virtual Appliance.
   
   
Op dit moment moet u een actieve externe Windows PowerShell-sessie op het apparaat hebben.
   
![PowerShell remoting met HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Verbinding maken via HTTPS

Verbinding maken met Windows PowerShell voor StorSimple via een HTTPS-sessie is de meest veilige en aanbevolen methode om op afstand verbinding te maken met uw Microsoft Azure StorSimple-apparaat. In de volgende procedures wordt uitgelegd hoe u de seriële console- en clientcomputers instelt, zodat u HTTPS gebruiken om verbinding te maken met Windows PowerShell voor StorSimple.

U de Azure-portal of de seriële console gebruiken om extern beheer te configureren. Kies uit de volgende procedures:

* De Azure-portal gebruiken om extern beheer via HTTPS in te schakelen
* [Gebruik de seriële console om extern beheer via HTTPS in te schakelen](#use-the-serial-console-to-enable-remote-management-over-https)

Nadat u extern beheer hebt ingeschakeld, gebruikt u de volgende procedures om de host voor te bereiden op een extern beheer en verbinding te maken met het apparaat vanaf de externe host.

* [De host voorbereiden voor extern beheer](#prepare-the-host-for-remote-management)
* [Verbinding maken met het apparaat vanaf de externe host](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>De Azure-portal gebruiken om extern beheer via HTTPS in te schakelen

Voer de volgende stappen uit in de Azure-portal om extern beheer via HTTPS in te schakelen.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Extern beheer via HTTPS inschakelen vanuit de Azure-portal

1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer **Apparaten** en selecteer en klik op het apparaat dat u wilt configureren voor extern beheer. Ga naar **Apparaatinstellingen > Beveiliging**.
2. Klik in het blad **Beveiligingsinstellingen** op **Extern beheer**.
3. Stel **Extern beheer inschakelen** in op **Ja**.
4. U er nu voor kiezen om verbinding te maken via HTTPS. (De standaardinstelling is om verbinding te maken via HTTPS.) Controleer of HTTPS is geselecteerd.
5. Klik... en klik vervolgens op **Extern beheercertificaat downloaden**. Geef een locatie op om dit bestand op te slaan. U moet dit certificaat installeren op de client- of hostcomputer die u gebruikt om verbinding te maken met het apparaat.
6. Klik **op Opslaan** en klik vervolgens op **Ja** wanneer u om bevestiging wordt gevraagd.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Gebruik de seriële console om extern beheer via HTTPS in te schakelen

Voer de volgende stappen uit op de seriële console van het apparaat om extern beheer in te schakelen.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Extern beheer inschakelen via de seriële console van het apparaat
1. Selecteer optie 1 in het menu van de seriële console. Ga voor meer informatie over het gebruik van de seriële console op het apparaat [naar Verbinding maken met Windows PowerShell voor StorSimple via de seriële console van het apparaat.](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
2. Typ bij de prompt:
   
     `Enable-HcsRemoteManagement`
   
    Dit moet HTTPS op uw apparaat inschakelen.
3. Controleer of HTTPS is ingeschakeld door te typen: 
   
     `Get-HcsSystem`
   
    Zorg ervoor dat in het veld **RemoteManagementMode** **HttpsIngeschakeld wordt weergegeven.** De volgende afbeelding toont deze instellingen in PuTTY.
   
     ![Seriële HTTPS ingeschakeld](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. Kopieer het `Get-HcsSystem`serienummer van het apparaat vanaf de uitvoer van , en bewaar het voor later gebruik.
   
   > [!NOTE]
   > Het serienummer wordt toegewezen aan de cn-naam in het certificaat.
   
5. Verkrijg een extern beheercertificaat door te typen: 
   
     `Get-HcsRemoteManagementCert`
   
    Er verschijnt een certificaat dat vergelijkbaar is met het volgende.
   
    ![Certificaat extern beheer ontvangen](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. Kopieer de informatie in het certificaat van **-----BEGINCERTIFICATE-----** naar **-----END CERTIFICATE-----** naar een teksteditor zoals Kladblok en sla deze op als een .cer-bestand. (U kopieert dit bestand naar uw externe host wanneer u de host voorbereidt.)
   
   > [!NOTE]
   > Als u een nieuw `Set-HcsRemoteManagementCert` certificaat wilt genereren, gebruikt u de cmdlet.
   
### <a name="prepare-the-host-for-remote-management"></a>De host voorbereiden voor extern beheer

Voer de volgende procedures uit om de hostcomputer voor te bereiden op een externe verbinding die een HTTPS-sessie gebruikt:

* [Importeer het .cer-bestand in de hoofdopslag van de client of externe host.](#to-import-the-certificate-on-the-remote-host)
* [Voeg de serienummers van het apparaat toe aan het hosts-bestand op uw externe host.](#to-add-device-serial-numbers-to-the-remote-host)

De bovenstaande procedures worden hieronder beschreven.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Het certificaat importeren op de externe host
1. Klik met de rechtermuisknop op het CER-bestand en selecteer **Certificaat installeren**. Hiermee start u de wizard Certificaat importeren.
   
    ![Wizard Certificaat importeren 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. Selecteer bij **Archieflocatie** de optie **Lokale computer** en klik op **Volgende**.
3. Selecteer **Alle certificaten in het onderstaande archief opslaan** en klik op **Bladeren**. Navigeer naar het basisarchief van de externe host en klik vervolgens op **Volgende**.
   
    ![Wizard Certificaat importeren 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. Klik op **Voltooien**. Er wordt een bericht weergegeven dat het importeren is geslaagd.
   
    ![Wizard Certificaat importeren 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Apparaatserienummers toevoegen aan de externe host
1. Start Kladblok als beheerder en open vervolgens het hosts-bestand op \Windows\System32\Drivers\etc.
2. Voeg de volgende drie vermeldingen toe aan uw hosts-bestand: **IP-adres MET GEGEVENS 0**, **Vast IP-adres controller 0**en vast **IP-adres voor controller 1**.
3. Voer het serienummer van het apparaat in dat u eerder hebt opgeslagen. Breng dit naar het IP-adres zoals weergegeven in de volgende afbeelding. Voor Controller 0 en Controller 1, toeteweet **Controller0** en **Controller1** aan het einde van het serienummer (CN-naam).
   
    ![CN-naam toevoegen aan hosts-bestand](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. Sla het bestand met hosts op.

### <a name="connect-to-the-device-from-the-remote-host"></a>Verbinding maken met het apparaat vanaf de externe host

Gebruik Windows PowerShell en TLS om een SSAdmin-sessie op uw apparaat in te voeren vanaf een externe host of client. De SSAdmin-sessie wordt toegewezen aan optie 1 in het [seriële consolemenu](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) van uw apparaat.

Voer de volgende procedure uit op de computer van waaruit u de externe Windows PowerShell-verbinding wilt maken.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-tls"></a>Een SSAdmin-sessie op het apparaat invoeren met Windows PowerShell en TLS
1. Start een Windows PowerShell-sessie als beheerder. Als u een Windows 10-client gebruikt, is de Windows Remote Management-service standaard ingesteld op handmatig. Mogelijk moet u de service starten door te typen:

    `Start-Service WinRM`

2. Voeg het IP-adres van het apparaat toe aan de vertrouwde hosts van de client door te typen:
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    Waar <*device_ip*> is, is het IP-adres van uw apparaat; bijvoorbeeld: 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. Als u een nieuwe referentie wilt maken, typt u het volgende:
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    Wanneer <*IP van doelapparaat*> het IP-adres van GEGEVENS 0 voor uw apparaat is; bijvoorbeeld **10.126.173.90** zoals weergegeven in de vorige afbeelding van het hosts-bestand. Geef ook het beheerderswachtwoord voor uw apparaat op.
4. Een sessie maken door te typen:
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    Geef voor de parameter -ComputerName in de cmdlet het <*serienummer van het doelapparaat*>. Dit serienummer is toegewezen aan het IP-adres van GEGEVENS 0 in het hosts-bestand op uw externe host; **BIJVOORBEELD SHX0991003G44MT** zoals weergegeven in de volgende afbeelding.
5. Type:
   
     `Enter-PSSession $session`
6. Je moet een paar minuten wachten, en dan zul je verbonden zijn met je apparaat via HTTPS via TLS. U ziet een bericht dat aangeeft dat u bent verbonden met uw apparaat.
   
    ![PowerShell remoting met HTTPS en TLS](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het gebruik van Windows PowerShell om uw StorSimple-apparaat te beheren.](storsimple-8000-windows-powershell-administration.md)
* Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)


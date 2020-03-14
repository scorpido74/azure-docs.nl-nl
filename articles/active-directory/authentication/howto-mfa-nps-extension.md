---
title: Azure MFA-mogelijkheden bieden met behulp van NPS-Azure Active Directory
description: Op de cloud gebaseerde verificatie mogelijkheden in twee stappen toevoegen aan uw bestaande verificatie-infra structuur
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3cd858653d54ae622758d218bb887d94bceb697
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263853"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Uw bestaande NPS-infra structuur integreren met Azure Multi-Factor Authentication

Met de uitbrei ding van de Network Policy Server (NPS) voor Azure MFA voegt u op de cloud gebaseerde MFA-mogelijkheden toe aan uw verificatie-infra structuur met uw bestaande servers. Met de NPS-extensie kunt u een telefoon gesprek, SMS-bericht of de verificatie van de mobiele app toevoegen aan uw bestaande verificatie stroom zonder dat u nieuwe servers hoeft te installeren, configureren en onderhouden. 

Deze uitbrei ding is gemaakt voor organisaties die VPN-verbindingen willen beveiligen zonder de Azure MFA-server te implementeren. De NPS-uitbrei ding fungeert als een adapter tussen RADIUS-en Cloud-Azure MFA om een tweede factor van verificatie te bieden voor federatieve of gesynchroniseerde gebruikers.

Wanneer u de NPS-extensie voor Azure MFA gebruikt, bevat de verificatie stroom de volgende onderdelen: 

1. **NAS/VPN-server** ontvangt aanvragen van VPN-clients en converteert deze naar RADIUS-aanvragen naar NPS-servers. 
2. **NPS-server** maakt verbinding met Active Directory om de primaire verificatie voor de RADIUS-aanvragen uit te voeren en, wanneer het is geslaagd, wordt de aanvraag door gegeven aan een geïnstalleerde uitbrei dingen.  
3. De **NPS-extensie** activeert een aanvraag voor Azure MFA voor de secundaire authenticatie. Zodra de uitbrei ding het antwoord ontvangt en de MFA-uitdaging is geslaagd, wordt de verificatie aanvraag voltooid door de NPS-server te voorzien van beveiligings tokens die een MFA-claim bevatten die is uitgegeven door Azure STS.  
4. **Azure MFA** communiceert met Azure Active Directory om de details van de gebruiker op te halen en de secundaire verificatie uit te voeren met behulp van een verificatie methode die voor de gebruiker is geconfigureerd.

In het volgende diagram ziet u de verificatie aanvraag stroom op hoog niveau: 

![Verificatie stroom diagram](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Uw implementatie plannen

De NPS-extensie verwerkt automatisch redundantie, dus u hebt geen speciale configuratie nodig.

U kunt net zoveel NPS-servers waarvoor Azure MFA is ingeschakeld maken als u wilt. Als u meerdere-servers installeert, moet u een diff-client certificaat voor elke server gebruiken. Als u een certificaat voor elke server maakt, kunt u elk certificaat afzonderlijk bijwerken en hoeft u zich geen zorgen te maken over uitval tijd op al uw servers.

VPN-servers routeren verificatie aanvragen, zodat ze op de hoogte moeten zijn van de nieuwe NPS-servers waarvoor Azure MFA is ingeschakeld.

## <a name="prerequisites"></a>Vereisten

De NPS-extensie is bedoeld om te werken met uw bestaande infra structuur. Zorg ervoor dat u aan de volgende vereisten voldoet voordat u begint.

### <a name="licenses"></a>Licenties

De NPS-extensie voor Azure MFA is beschikbaar voor klanten met [licenties voor azure multi-factor Authentication](multi-factor-authentication.md) (opgenomen in azure AD Premium, EMS of een zelfstandige licentie voor MFA). Op verbruik gebaseerde licenties voor Azure MFA, zoals per gebruiker of per verificatie-licentie, zijn niet compatibel met de NPS-extensie. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 of hoger.

### <a name="libraries"></a>Bibliotheken

Deze bibliotheken worden automatisch met de extensie geïnstalleerd.

- [Visual C++ Redistributable packages for visual studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-module voor Windows PowerShell versie 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

De Microsoft Azure Active Directory-module voor Windows PowerShell is geïnstalleerd, als deze nog niet aanwezig is, via een configuratie script dat u uitvoert als onderdeel van het installatie proces. U hoeft deze module niet vooraf te installeren als deze nog niet is geïnstalleerd.

### <a name="azure-active-directory"></a>Azure Active Directory

Iedereen die de NPS-extensie gebruikt, moet worden gesynchroniseerd met Azure Active Directory met behulp van Azure AD Connect en moeten worden geregistreerd voor MFA.

Wanneer u de uitbrei ding installeert, hebt u de map-ID en de beheerders referenties nodig voor uw Azure AD-Tenant. U kunt uw directory-ID vinden in de [Azure Portal](https://portal.azure.com). Meld u aan als beheerder. Zoek en selecteer de **Azure Active Directory**en selecteer vervolgens **Eigenschappen**. Kopieer de GUID in het vak **Directory-id** en sla deze op. U gebruikt deze GUID als Tenant-ID bij de installatie van de NPS-extensie.

![Zoek uw directory-ID onder Azure Active Directory eigenschappen](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Netwerkvereisten

De NPS-server moet kunnen communiceren met de volgende Url's via de poorten 80 en 443.

- https:\/-adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Daarnaast is connectiviteit met de volgende Url's vereist voor het volt ooien [van de installatie van de adapter met het opgegeven Power shell-script](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\/-provisioningapi.microsoftonline.com
- https:\/-aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

Voordat u de NPS-extensie installeert, wilt u de omgeving voorbereiden om het verificatie verkeer te verwerken.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>De NPS-functie inschakelen op een server die lid is van een domein

De NPS-server maakt verbinding met Azure Active Directory en verifieert de MFA-aanvragen. Kies één server voor deze rol. We raden u aan een server te kiezen die geen aanvragen van andere services verwerkt, omdat de NPS-extensie fouten genereert voor aanvragen die geen RADIUS zijn. De NPS-server moet worden ingesteld als de primaire en secundaire verificatie server voor uw omgeving. Er kunnen geen RADIUS-aanvragen naar een andere server worden geproxyeerd.

1. Open op uw server de **wizard functies en onderdelen toevoegen** in het menu Serverbeheer Snelstartgids.
2. Kies een **functie of installatie op basis** van een functie voor uw installatie type.
3. Selecteer de server functie **Services voor netwerk beleid en-toegang** . Er wordt mogelijk een venster met de melding over de vereiste functies gewaarschuwd om deze functie uit te voeren.
4. Ga door met de wizard totdat de pagina bevestiging wordt bevestigd. Selecteer **Installeren**.

Nu u een server hebt die is aangewezen voor NPS, moet u deze server ook configureren voor het afhandelen van binnenkomende RADIUS-aanvragen van de VPN-oplossing.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configureer uw VPN-oplossing om te communiceren met de NPS-server

Afhankelijk van de VPN-oplossing die u gebruikt, verschillen de stappen voor het configureren van uw RADIUS-verificatie beleid. Configureer dit beleid om naar uw RADIUS NPS-server te verwijzen.

### <a name="sync-domain-users-to-the-cloud"></a>Domein gebruikers synchroniseren met de Cloud

Deze stap is mogelijk al voltooid voor uw Tenant, maar het is een goed idee om te controleren of Azure AD Connect uw data bases onlangs hebt gesynchroniseerd.

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory** > **Azure AD Connect**
3. Controleer of de synchronisatie status is **ingeschakeld** en of de laatste synchronisatie minder dan een uur geleden is.

Als u een nieuwe ronde van synchronisatie wilt starten, kunt u de instructies in [Azure AD Connect Sync: scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bepalen welke verificatie methoden uw gebruikers kunnen gebruiken

Er zijn twee factoren die invloed hebben op de verificatie methoden die beschikbaar zijn met een implementatie van een NPS-extensie:

1. De wachtwoord versleutelings algoritme die wordt gebruikt tussen de RADIUS-client (VPN, NetScaler server of andere) en de NPS-servers.
   - **Pap** ondersteunt alle verificatie methoden van Azure MFA in de Cloud: telefoon oproep, tekst bericht in één richting, melding voor mobiele apps, OATH-hardware-tokens en verificatie code voor mobiele apps.
   - Ondersteuning voor telefonische en **EAP** -telefoon **gesprekken en mobiele** app-meldingen.

      > [!NOTE]
      > Wanneer u de NPS-uitbrei ding implementeert, moet u deze factoren gebruiken om te evalueren welke methoden beschikbaar zijn voor uw gebruikers. Als uw RADIUS-client PAP ondersteunt, maar de client UX geen invoer velden heeft voor een verificatie code, zijn de meldingen voor telefoon gesprekken en mobiele apps de twee ondersteunde opties.
      >
      > Als uw VPN-client UX het invoer veld ondersteunt en u het beleid voor netwerk toegang hebt geconfigureerd, kan de verificatie slagen, maar geen van de RADIUS-kenmerken die zijn geconfigureerd in het netwerk beleid wordt toegepast op het apparaat voor netwerk toegang. net als de RRAS-server en de VPN-client. Als gevolg hiervan kan de VPN-client meer toegang hebben dan gewenst of beperkt tot geen toegang.
      >

2. De invoer methoden die de client toepassing (VPN, NetScaler server of andere) kan verwerken. Heeft de VPN-client bijvoorbeeld een aantal manieren om de gebruiker toe te staan een verificatie code in te voeren in een tekst-of mobiele app?

U kunt [niet-ondersteunde verificatie methoden uitschakelen](howto-mfa-mfasettings.md#verification-methods) in Azure.

### <a name="register-users-for-mfa"></a>Gebruikers voor MFA registreren

Voordat u de NPS-extensie implementeert en gebruikt, moeten gebruikers die een verificatie in twee stappen moeten uitvoeren, worden geregistreerd voor MFA. Als u de uitbrei ding wilt testen terwijl u deze implementeert, moet u ten minste één test account hebben dat volledig is geregistreerd voor Multi-Factor Authentication.

Volg deze stappen om een test account te verkrijgen:

1. Meld u aan bij [https://aka.ms/mfasetup](https://aka.ms/mfasetup) met een test account.
2. Volg de aanwijzingen voor het instellen van een verificatie methode.
3. [Maak een beleid voor voorwaardelijke toegang](howto-mfa-getstarted.md#create-conditional-access-policy) om multi-factor Authentication voor het test account te vereisen.

## <a name="install-the-nps-extension"></a>De NPS-extensie installeren

> [!IMPORTANT]
> Installeer de NPS-extensie op een andere server dan het VPN-toegangs punt.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>De NPS-extensie voor Azure MFA downloaden en installeren

1. [Down load de NPS-extensie](https://aka.ms/npsmfa) vanuit het micro soft Download centrum.
2. Kopieer het binaire bestand naar de Network Policy Server die u wilt configureren.
3. Voer *Setup. exe* uit en volg de installatie-instructies. Als er fouten optreden, controleert u of de twee bibliotheken van de sectie vereisten zijn geïnstalleerd.

#### <a name="upgrade-the-nps-extension"></a>De NPS-extensie upgraden

Voer de volgende stappen uit als u een bestaande installatie van de NPS-extensie wilt bijwerken om te voor komen dat de onderliggende server opnieuw wordt opgestart:

1. De bestaande versie verwijderen
1. Het nieuwe installatie programma uitvoeren
1. De Network Policy Server-service (IAS) opnieuw opstarten

### <a name="run-the-powershell-script"></a>Het PowerShell-script uitvoeren

Het installatie programma maakt een Power shell-script op deze locatie: `C:\Program Files\Microsoft\AzureMfa\Config` (waarbij C:\ is het station van de installatie). Met dit Power shell-script worden de volgende acties uitgevoerd telkens wanneer het wordt uitgevoerd:

- Maak een zelfondertekend certificaat.
- Koppel de open bare sleutel van het certificaat aan de Service-Principal in azure AD.
- Sla het certificaat op in het certificaat archief van de lokale computer.
- Verleen toegang tot de persoonlijke sleutel van het certificaat aan de netwerk gebruiker.
- Start NPS opnieuw.

Tenzij u uw eigen certificaten wilt gebruiken (in plaats van de zelfondertekende certificaten die door het Power shell-script worden gegenereerd), voert u het Power shell-script uit om de installatie te volt ooien. Als u de uitbrei ding op meerdere servers installeert, moet elk een eigen certificaat hebben.

1. Voer Windows Power shell uit als beheerder.
2. Mappen wijzigen.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Voer het Power shell-script uit dat is gemaakt door het installatie programma.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Meld u als beheerder aan bij Azure AD.
5. Power shell vraagt om uw Tenant-ID. Gebruik de GUID van de Directory-ID die u hebt gekopieerd uit de Azure Portal in de sectie vereisten.
6. Power shell toont een geslaagd bericht wanneer het script is voltooid.  

Herhaal deze stappen voor alle extra NPS-servers die u wilt instellen voor taak verdeling.

Als uw vorige computer certificaat is verlopen en er een nieuw certificaat is gegenereerd, moet u verlopen certificaten verwijderen. Verlopen certificaten kunnen leiden tot problemen met het starten van de NPS-extensie.

> [!NOTE]
> Als u uw eigen certificaten gebruikt in plaats van certificaten te genereren met het Power shell-script, moet u ervoor zorgen dat ze worden uitgelijnd op de NPS-naamgevings Conventie. De naam van het onderwerp moet **CN =\<TenantID\>, ou = micro soft NPS extension**zijn. 

### <a name="microsoft-azure-government-additional-steps"></a>Aanvullende stappen Microsoft Azure Government

Voor klanten die Azure Government Cloud gebruiken, zijn de volgende aanvullende configuratie stappen vereist op elke NPS-server:

1. Open de **REGI ster-editor** op de NPS-server.
1. Navigeer naar `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Stel de volgende sleutel waarden in:

    | Register sleutel       | Waarde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Herhaal de vorige twee stappen om de register sleutel waarden voor elke NPS-server in te stellen.
1. Start de NPS-service voor elke NPS-server opnieuw.

    Voor een minimale impact moet u elke NPS-server uit de NLB-rotatie een voor een tegelijk halen en wachten op alle verbindingen met de afvoer.

### <a name="certificate-rollover"></a>Certificaat overschakeling

Met Release-1.0.1.32 van de NPS-extensie wordt het lezen van meerdere certificaten nu ondersteund. Met deze mogelijkheid kunnen Rolling certificaat updates worden vereenvoudigd voordat ze verlopen. Als uw organisatie een eerdere versie van de NPS-extensie uitvoert, moet u een upgrade uitvoeren naar versie 1.0.1.32 of hoger.

Certificaten die zijn gemaakt door het `AzureMfaNpsExtnConfigSetup.ps1` script zijn twee jaar geldig. IT-organisaties moeten certificaten controleren op verlopen. Certificaten voor de NPS-extensie worden onder persoonlijk in het certificaat archief van de lokale computer geplaatst en worden verleend aan de Tenant-ID die is opgegeven voor het script.

Wanneer een certificaat de verval datum nadert, moet er een nieuw certificaat worden gemaakt om het te vervangen.  Dit proces wordt uitgevoerd door de `AzureMfaNpsExtnConfigSetup.ps1` opnieuw uit te voeren en dezelfde Tenant-ID te bewaren wanneer u hierom wordt gevraagd. Dit proces moet worden herhaald op elke NPS-server in uw omgeving.

## <a name="configure-your-nps-extension"></a>Uw NPS-extensie configureren

Deze sectie bevat ontwerp overwegingen en suggesties voor geslaagde implementaties van NPS-extensies.

### <a name="configuration-limitations"></a>Configuratie beperkingen

- De NPS-extensie voor Azure MFA bevat geen hulp middelen voor het migreren van gebruikers en instellingen van MFA server naar de Cloud. Daarom wordt u aangeraden de uitbrei ding voor nieuwe implementaties te gebruiken in plaats van de bestaande implementatie. Als u de uitbrei ding gebruikt voor een bestaande implementatie, moeten uw gebruikers opnieuw een controle uitvoeren om de MFA-gegevens in de cloud te vullen.  
- De NPS-extensie gebruikt de UPN van de on-premises Active Directory om de gebruiker op Azure MFA te identificeren voor het uitvoeren van de secundaire auth. De uitbrei ding kan worden geconfigureerd voor het gebruik van een andere id, zoals een alternatieve aanmeldings-ID of een aangepast Active Directory veld dan UPN. Zie het artikel [Geavanceerde configuratie-opties voor de NPS-extensie voor multi-factor Authentication](howto-mfa-nps-extension-advanced.md)voor meer informatie.
- Niet alle versleutelings protocollen ondersteunen alle verificatie methoden.
   - **Pap** ondersteunt telefoon gesprekken, tekst berichten in één richting, meldingen over mobiele apps en verificatie code voor mobiele apps
   - Telefonische ondersteuning en melding voor mobiele apps via de service **CHAPV2** en **EAP**

### <a name="control-radius-clients-that-require-mfa"></a>Beheer RADIUS-clients waarvoor MFA is vereist

Zodra u MFA voor een RADIUS-client met de NPS-extensie hebt ingeschakeld, zijn alle verificaties voor deze client vereist om MFA uit te voeren. Als u MFA wilt inschakelen voor sommige RADIUS-clients, maar niet voor anderen, kunt u twee NPS-servers configureren en de uitbrei ding alleen op één van deze-services installeren. Configureer RADIUS-clients waarvoor u MFA wilt vereisen voor het verzenden van aanvragen naar de NPS-server die is geconfigureerd met de extensie, en andere RADIUS-clients naar de NPS-server die niet met de extensie zijn geconfigureerd.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Voorbereiden voor gebruikers die niet zijn Inge schreven voor MFA

Als u gebruikers hebt die niet zijn Inge schreven voor MFA, kunt u bepalen wat er gebeurt wanneer ze proberen te verifiëren. Gebruik de register instelling *REQUIRE_USER_MATCH* in het registerpad *HKLM\Software\Microsoft\AzureMFA* om het functie gedrag te bepalen. Deze instelling heeft één configuratie optie:

| Sleutel | Waarde | Standaard |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | WAAR/ONWAAR | Niet ingesteld (gelijk aan TRUE) |

Het doel van deze instelling is om te bepalen wat er moet gebeuren wanneer een gebruiker niet is inge schreven voor MFA. Als de sleutel niet bestaat, niet is ingesteld of is ingesteld op TRUE en de gebruiker niet is inge schreven, mislukt de extensie de MFA-Challenge. Als de sleutel is ingesteld op FALSE en de gebruiker niet is inge schreven, wordt de verificatie voortgezet zonder MFA uit te voeren. Als een gebruiker is inge schreven bij MFA, moeten ze worden geverifieerd met MFA, zelfs als REQUIRE_USER_MATCH is ingesteld op FALSE.

U kunt ervoor kiezen om deze sleutel te maken en deze in te stellen op ONWAAR wanneer uw gebruikers onboarding hebben en mogelijk nog niet allemaal zijn geregistreerd voor Azure MFA. Omdat het instellen van de sleutel echter toestaat dat gebruikers die niet zijn Inge schreven voor MFA zich kunnen aanmelden, moet u deze sleutel verwijderen voordat u naar de productie gaat.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="nps-extension-health-check-script"></a>Script voor status controle van NPS-uitbrei ding

Het volgende script is beschikbaar voor het uitvoeren van basis stappen voor de status controle bij het oplossen van problemen met de NPS-extensie.

[MFA_NPS_Troubleshooter. ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hoe kan ik controleren of het certificaat van de client is geïnstalleerd zoals verwacht?

Zoek naar het zelfondertekende certificaat dat is gemaakt door het installatie programma in het certificaat archief en controleer of de persoonlijke sleutel machtigingen heeft die zijn verleend aan de **netwerk service**van de gebruiker. Het certificaat heeft de onderwerpnaam **CN \<tenantid\>, ou = micro soft NPS extension**

Zelfondertekende certificaten die zijn gegenereerd door het script *AzureMfaNpsExtnConfigSetup. ps1* , hebben ook een levens duur van twee jaar. Wanneer u controleert of het certificaat is geïnstalleerd, moet u ook controleren of het certificaat niet is verlopen.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hoe kan ik controleren of mijn client certificaat is gekoppeld aan mijn Tenant in Azure Active Directory?

Open de Power shell-opdracht prompt en voer de volgende opdrachten uit:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Met deze opdrachten worden alle certificaten afgedrukt die aan uw Tenant zijn gekoppeld met uw exemplaar van de NPS-extensie in uw Power shell-sessie. Zoek uw certificaat door uw client certificaat te exporteren als een ' base-64 Encoded X. 509-bestand (. CER) zonder de persoonlijke sleutel, en vergelijk dit met de lijst van Power shell.

Met de volgende opdracht wordt een bestand met de naam ' npscertificate ' gemaakt op het station ' C: ' in Format. cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Wanneer u deze opdracht hebt uitgevoerd, gaat u naar uw C-station, zoekt u het bestand en dubbelklikt u erop. Ga naar details en schuif omlaag naar ' vinger afdruk ', vergelijk de vinger afdruk van het certificaat dat op de server is geïnstalleerd. De certificaat vingerafdrukken moeten overeenkomen.

De tijds tempels geldig van en geldig tot en met een lees bare vorm, kunnen worden gebruikt voor het filteren van duidelijke, niet-passende, als de opdracht meer dan één certificaat retourneert.

---

### <a name="why-cannot-i-sign-in"></a>Waarom kan ik me niet aanmelden?

Controleer of uw wacht woord niet is verlopen. De NPS-extensie biedt geen ondersteuning voor het wijzigen van wacht woorden als onderdeel van de aanmeld werk stroom. Neem contact op met de IT-afdeling van uw organisatie voor meer informatie.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Waarom mislukt mijn aanvragen met een ADAL-token fout?

Deze fout kan een van de volgende oorzaken hebben. Volg deze stappen om u te helpen bij het oplossen van problemen:

1. Start de NPS-server opnieuw op.
2. Controleer of het client certificaat is geïnstalleerd zoals verwacht.
3. Controleer of het certificaat is gekoppeld aan uw Tenant in azure AD.
4. Controleer of https://login.microsoftonline.com/ toegankelijk is vanaf de server waarop de extensie wordt uitgevoerd.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Waarom mislukt verificatie met een fout in HTTP-logboeken met de melding dat de gebruiker niet is gevonden?

Controleer of AD Connect actief is en of de gebruiker aanwezig is in zowel Windows Active Directory als Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Waarom zie ik HTTP-verbindings fouten in Logboeken met al mijn authenticaties mislukt?

Controleer of https://adnotifications.windowsazure.com bereikbaar is vanaf de server waarop de NPS-extensie wordt uitgevoerd.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Waarom werkt verificatie niet, ondanks dat er een geldig certificaat aanwezig is?

Als uw vorige computer certificaat is verlopen en er een nieuw certificaat is gegenereerd, moet u verlopen certificaten verwijderen. Verlopen certificaten kunnen leiden tot problemen met het starten van de NPS-extensie.

Als u wilt controleren of u een geldig certificaat hebt, controleert u het certificaat archief van het lokale computer account met behulp van MMC en zorgt u ervoor dat het certificaat de verval datum niet heeft door gegeven. Als u een nieuw geldig certificaat wilt genereren, voert u de stappen uit onder het gedeelte[het Power shell-script uitvoeren](#run-the-powershell-script)

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Beheer van de TLS/SSL-protocollen en coderingssuites

U wordt aangeraden oudere en zwakkere coderings suites uit te scha kelen of te verwijderen, tenzij uw organisatie dit vereist. Informatie over het uitvoeren van deze taak vindt u in het artikel [​​SSL/TLS-protocollen en coderingssuites beheren voor AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Extra probleem oplossing

Aanvullende richt lijnen voor probleem oplossing en mogelijke oplossingen vindt u in het artikel [oplossingen voor fout berichten van de NPS-extensie voor Azure multi-factor Authentication oplossen](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Volgende stappen

- [Overzicht en configuratie van Network Policy Server in Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Configureer alternatieve Id's voor aanmelding of stel een uitzonderingen lijst in voor IP-adressen die geen verificatie in twee stappen moeten uitvoeren in [Geavanceerde configuratie opties voor de NPS-extensie voor multi-factor Authentication](howto-mfa-nps-extension-advanced.md)

- Meer informatie over het integreren van [extern bureaublad-gateway](howto-mfa-nps-extension-rdg.md) [-en VPN-servers](howto-mfa-nps-extension-vpn.md) met BEhulp van de NPS-extensie

- [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Foutberichten van de NPS-extensie voor Azure Multi-Factor Authentication oplossen)

---
title: Azure MFA-mogelijkheden bieden met NPS - Azure Active Directory
description: Cloudgebaseerde verificatiemogelijkheden in twee stappen toevoegen aan uw bestaande verificatie-infrastructuur
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
ms.openlocfilehash: 2c8606f0b7ab47d624ec66c8cda539e571cec6ce
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393057"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Uw bestaande NPS-infrastructuur integreren met Azure Multi-Factor Authentication

De NPS-extensie (Network Policy Server) voor Azure MFA voegt cloudgebaseerde MFA-mogelijkheden toe aan uw verificatie-infrastructuur met behulp van uw bestaande servers. Met de NPS-extensie u telefoongesprekken, sms-berichten of verificatie van de telefoon-app toevoegen aan uw bestaande verificatiestroom zonder dat u nieuwe servers hoeft te installeren, configureren en onderhouden. 

Deze extensie is gemaakt voor organisaties die VPN-verbindingen willen beveiligen zonder de Azure MFA Server te implementeren. De NPS-extensie fungeert als een adapter tussen RADIUS en cloudgebaseerde Azure MFA om een tweede verificatiefactor te bieden voor federatieve of gesynchroniseerde gebruikers.

Wanneer u de NPS-extensie voor Azure MFA gebruikt, bevat de verificatiestroom de volgende onderdelen: 

1. **NAS/VPN Server** ontvangt verzoeken van VPN-clients en zet deze om in RADIUS-verzoeken naar NPS-servers. 
2. **NPS Server** maakt verbinding met Active Directory om de primaire verificatie voor de RADIUS-aanvragen uit te voeren en geeft het verzoek bij succes door aan geïnstalleerde extensies.  
3. **NPS Extension** activeert een aanvraag voor Azure MFA voor de secundaire verificatie. Zodra de extensie het antwoord ontvangt en als de MFA-uitdaging slaagt, voltooit deze de verificatieaanvraag door de NPS-server beveiligingstokens te bieden die een MFA-claim bevatten, uitgegeven door Azure STS.  
4. **Azure MFA** communiceert met Azure Active Directory om de gegevens van de gebruiker op te halen en voert de secundaire verificatie uit met behulp van een verificatiemethode die is geconfigureerd voor de gebruiker.

In het volgende diagram wordt deze verificatieaanvraagstroom op hoog niveau geïllustreerd: 

![Verificatiestroomdiagram](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Uw implementatie plannen

De NPS-extensie verwerkt automatisch redundantie, zodat u geen speciale configuratie nodig hebt.

U zoveel Azure MFA-enabled NPS-servers maken als u nodig hebt. Als u meerdere servers installeert, moet u voor elk van deze servers een verschilclientcertificaat gebruiken. Het maken van een cert voor elke server betekent dat u elk cert afzonderlijk bijwerken en zich geen zorgen maken over downtime op al uw servers.

VPN-servers routeauthenticatieverzoeken, dus ze moeten op de hoogte zijn van de nieuwe Azure MFA-enabled NPS-servers.

## <a name="prerequisites"></a>Vereisten

De NPS-extensie is bedoeld om te werken met uw bestaande infrastructuur. Zorg ervoor dat u de volgende voorwaarden hebt voordat u begint.

### <a name="licenses"></a>Licenties

De NPS-extensie voor Azure MFA is beschikbaar voor klanten met [licenties voor Azure Multi-Factor Authentication](multi-factor-authentication.md) (inbegrepen bij Azure AD Premium, EMS of een MFA-zelfstandige licentie). Licenties op basis van verbruik voor Azure MFA, zoals per gebruiker of per verificatielicenties, zijn niet compatibel met de NPS-extensie. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 of hoger.

### <a name="libraries"></a>Bibliotheken

Deze bibliotheken worden automatisch geïnstalleerd met de extensie.

- [Visual C++ Verredbare pakketten voor Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory Module voor Windows PowerShell versie 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

De Microsoft Azure Active Directory Module voor Windows PowerShell is geïnstalleerd, als deze nog niet aanwezig is, via een configuratiescript dat u uitvoert als onderdeel van het installatieproces. Het is niet nodig om deze module van tevoren te installeren als deze nog niet is geïnstalleerd.

### <a name="azure-active-directory"></a>Azure Active Directory

Iedereen die de NPS-extensie gebruikt, moet worden gesynchroniseerd met Azure Active Directory met Azure AD Connect en moet zijn geregistreerd voor MFA.

Wanneer u de extensie installeert, hebt u de directory-id- en beheerdersreferenties voor uw Azure AD-tenant nodig. U uw directory-id vinden in de [Azure-portal.](https://portal.azure.com) Meld u aan als beheerder. Zoek naar en selecteer de **Azure Active Directory**en selecteer vervolgens **Eigenschappen**. Kopieer de GUID in het vak **Directory-id** en sla deze op. U gebruikt deze GUID als tenant-id wanneer u de NPS-extensie installeert.

![Uw directory-id zoeken onder Azure Active Directory-eigenschappen](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Netwerkvereisten

De NPS-server moet kunnen communiceren met de volgende URL's via poorten 80 en 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https:\//credentials.azure.com

Bovendien is connectiviteit met de volgende URL's vereist om de installatie van de adapter te voltooien [met behulp van het meegeleverde PowerShell-script](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

Voordat u de NPS-extensie installeert, wilt u uw omgeving voorbereiden op het verificatieverkeer.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>De NPS-rol inschakelen op een server die is verbonden met een domein

De NPS-server maakt verbinding met Azure Active Directory en verifieert de MFA-aanvragen. Kies één server voor deze rol. We raden u aan een server te kiezen die geen aanvragen van andere services verwerkt, omdat de NPS-extensie fouten bevat voor aanvragen die geen RADIUS zijn. De NPS-server moet zijn ingesteld als de primaire en secundaire verificatieserver voor uw omgeving; het kan niet proxy RADIUS aanvragen naar een andere server.

1. Open op uw server de **wizard Rollen en onderdelen toevoegen** in het menu Snelstart van Serverbeheer.
2. Kies **Op rollen gebaseerde of op functies gebaseerde installatie** voor uw installatietype.
3. Selecteer de serverrol **Netwerkbeleid en Access Services.** Er kan een venster verschijnen om u te informeren over de vereiste functies om deze rol uit te voeren.
4. Ga door de wizard tot de bevestigingspagina. Selecteer **Installeren**.

Nu u een server hebt die is aangewezen voor NPS, moet u deze server ook configureren om binnenkomende RADIUS-aanvragen van de VPN-oplossing af te handelen.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Uw VPN-oplossing configureren om te communiceren met de NPS-server

Afhankelijk van welke VPN-oplossing u gebruikt, zijn de stappen om uw RADIUS-verificatiebeleid te configureren, afhankelijk van de vereisten. Configureer dit beleid om naar uw RADIUS NPS-server te wijzen.

### <a name="sync-domain-users-to-the-cloud"></a>Domeingebruikers synchroniseren met de cloud

Deze stap is mogelijk al voltooid op uw tenant, maar het is goed om dubbel te controleren of Azure AD Connect uw databases onlangs heeft gesynchroniseerd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. **Selecteer Azure Active Directory** > **Azure AD Connect**
3. Controleer of uw synchronisatiestatus is **ingeschakeld** en of uw laatste synchronisatie minder dan een uur geleden is geweest.

Als u een nieuwe synchronisatieronde wilt starten, worden de instructies in [Azure AD Connect-synchronisatie: Scheduler .](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bepalen welke verificatiemethoden uw gebruikers kunnen gebruiken

Er zijn twee factoren die van invloed zijn op welke verificatiemethoden beschikbaar zijn met een implementatie van nps-extensie:

1. Het wachtwoordversleutelingsalgoritme dat wordt gebruikt tussen de RADIUS-client (VPN, Netscaler-server of andere) en de NPS-servers.
   - **PAP** ondersteunt alle verificatiemethoden van Azure MFA in de cloud: telefoongesprek, eenrichtingsbericht, melding van mobiele apps, OATH-hardwaretokens en verificatiecode voor mobiele apps.
   - **CHAPV2** en **EAP** ondersteunen telefoongesprekken en meldingen van mobiele apps.

      > [!NOTE]
      > Wanneer u de NPS-extensie implementeert, gebruikt u deze factoren om te evalueren welke methoden beschikbaar zijn voor uw gebruikers. Als uw RADIUS-client PAP ondersteunt, maar de client-UX geen invoervelden heeft voor een verificatiecode, zijn telefoongesprekken en meldingen van mobiele apps de twee ondersteunde opties.
      >
      > Als uw UX met VPN-client invoerveld ondersteunt en u netwerktoegangsbeleid hebt geconfigureerd, kan de verificatie bovendien slagen, maar geen van de RADIUS-kenmerken die zijn geconfigureerd in het netwerkbeleid, wordt toegepast op noch het netwerktoegangsapparaat, zoals de RRAS-server, noch op de VPN-client. Als gevolg hiervan heeft de VPN-client mogelijk meer toegang dan gewenst of minder tot geen toegang.
      >

2. De invoermethoden die de clienttoepassing (VPN, Netscaler-server of andere) aankan. Heeft de VPN-client bijvoorbeeld een aantal middelen om de gebruiker in staat te stellen een verificatiecode in te typen vanuit een tekst of mobiele app?

U [niet-ondersteunde verificatiemethoden uitschakelen](howto-mfa-mfasettings.md#verification-methods) in Azure.

### <a name="register-users-for-mfa"></a>Gebruikers registreren voor MFA

Voordat u de NPS-extensie implementeert en gebruikt, moeten gebruikers die tweestapsverificatie moeten uitvoeren, worden geregistreerd voor MFA. Meer onmiddellijk, om de extensie te testen als u deze implementeert, hebt u ten minste één testaccount nodig dat volledig is geregistreerd voor Multi-Factor Authentication.

Gebruik deze stappen om een testaccount te starten:

1. Meld u [https://aka.ms/mfasetup](https://aka.ms/mfasetup) aan bij een testaccount.
2. Volg de aanwijzingen om een verificatiemethode in te stellen.
3. [Maak een beleid voor voorwaardelijke toegang](howto-mfa-getstarted.md#create-conditional-access-policy) om meervoudige verificatie voor het testaccount te vereisen.

## <a name="install-the-nps-extension"></a>De NPS-extensie installeren

> [!IMPORTANT]
> Installeer de NPS-extensie op een andere server dan het VPN-toegangspunt.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>De NPS-extensie voor Azure MFA downloaden en installeren

1. [Download de NPS-extensie](https://aka.ms/npsmfa) in het Microsoft Downloadcentrum.
2. Kopieer het binaire naar de netwerkbeleidsserver die u wilt configureren.
3. Voer *setup.exe uit* en volg de installatie-instructies. Als u fouten tegenkomt, controleert u of de twee bibliotheken uit de sectie vereiste zijn geïnstalleerd.

#### <a name="upgrade-the-nps-extension"></a>De NPS-extensie upgraden

Bij het upgraden van een bestaande NPS-extensie installatie, om te voorkomen dat een reboot van de onderliggende server de volgende stappen uitvoert:

1. De bestaande versie verwijderen
1. De nieuwe installatieprogramma uitvoeren
1. De IAS-service (Network Policy Server) opnieuw starten

### <a name="run-the-powershell-script"></a>Het PowerShell-script uitvoeren

Het installatieprogramma maakt op deze `C:\Program Files\Microsoft\AzureMfa\Config` locatie een PowerShell-script: (waarbij C:\ is uw installatiestation). Dit PowerShell-script voert de volgende acties uit telkens wanneer het wordt uitgevoerd:

- Maak een zelfondertekend certificaat.
- Koppel de openbare sleutel van het certificaat aan de serviceprincipal in Azure AD.
- Bewaar het cert in de lokale machine cert store.
- Geef toegang tot de privésleutel van het certificaat aan netwerkgebruiker.
- Start de NPS opnieuw.

Tenzij u uw eigen certificaten wilt gebruiken (in plaats van de zelfondertekende certificaten die het PowerShell-script genereert), voert u het PowerShell Script uit om de installatie te voltooien. Als u de extensie op meerdere servers installeert, moet elk extensiescertificaat hebben.

1. Voer Windows PowerShell uit als beheerder.
2. Verander mappen.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Voer het PowerShell-script uit dat door het installatieprogramma is gemaakt.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Meld u aan bij Azure AD als beheerder.
5. PowerShell vraagt om uw tenant-id. Gebruik de DIRECTORY ID-GUID die u hebt gekopieerd van de Azure-portal in de sectie Vereisten.
6. PowerShell toont een succesbericht wanneer het script is voltooid.  

Herhaal deze stappen op eventuele extra NPS-servers die u wilt instellen voor taakverdeling.

Als uw vorige computercertificaat is verlopen en er een nieuw certificaat is gegenereerd, moet u verlopen certificaten verwijderen. Het hebben van verlopen certificaten kan problemen veroorzaken met het starten van de NPS-extensie.

> [!NOTE]
> Als u uw eigen certificaten gebruikt in plaats van certificaten te genereren met het PowerShell-script, moet u ervoor zorgen dat ze worden uitgelijnd met de NPS-naamgevingsconventie. De onderwerpnaam moet **\<CN=\>TenantID ,OU=Microsoft NPS Extension**zijn. 

### <a name="microsoft-azure-government-additional-steps"></a>Aanvullende stappen van Microsoft Azure Government

Voor klanten die de Azure Government-cloud gebruiken, zijn de volgende aanvullende configuratiestappen vereist op elke NPS-server:

1. Open **registereditor** op de NPS-server.
1. Navigeer naar `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Stel de volgende kernwaarden in:

    | Registersleutel       | Waarde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Herhaal de vorige twee stappen om de registersleutelwaarden voor elke NPS-server in te stellen.
1. Start de NPS-service opnieuw voor elke NPS-server.

    Voor minimale impact, neem elke NPS-server uit de NLB-rotatie een voor een en wacht tot alle verbindingen leeg lopen.

### <a name="certificate-rollover"></a>Certificaatrollover

Met release 1.0.1.32 van de NPS-extensie wordt het lezen van meerdere certificaten nu ondersteund. Deze mogelijkheid zal helpen bij het vergemakkelijken van het rollen van certificaat updates voorafgaand aan hun vervaldatum. Als uw organisatie een vorige versie van de NPS-extensie uitvoert, moet u upgraden naar versie 1.0.1.32 of hoger.

Certificaten die `AzureMfaNpsExtnConfigSetup.ps1` door het script zijn gemaakt, zijn 2 jaar geldig. IT-organisaties moeten certificaten controleren op vervaldatum. Certificaten voor de NPS-extensie worden geplaatst in het certificaatarchief Lokale computer onder Persoonlijk en worden uitgegeven aan de tenant-id die aan het script wordt verstrekt.

Wanneer een certificaat de vervaldatum nadert, moet een nieuw certificaat worden gemaakt om het te vervangen.  Dit proces wordt uitgevoerd `AzureMfaNpsExtnConfigSetup.ps1` door het opnieuw uit te voeren en dezelfde tenant-id te behouden wanneer daarom wordt gevraagd. Dit proces moet worden herhaald op elke NPS-server in uw omgeving.

## <a name="configure-your-nps-extension"></a>Uw NPS-extensie configureren

Deze sectie bevat ontwerpoverwegingen en suggesties voor succesvolle NPS-extensieimplementaties.

### <a name="configuration-limitations"></a>Configuratiebeperkingen

- De NPS-extensie voor Azure MFA bevat geen hulpprogramma's om gebruikers en instellingen van MFA Server naar de cloud te migreren. Daarom raden we u aan de extensie te gebruiken voor nieuwe implementaties, in plaats van voor bestaande implementaties. Als u de extensie voor een bestaande implementatie gebruikt, moeten uw gebruikers opnieuw een proof-up uitvoeren om hun MFA-gegevens in de cloud in te vullen.  
- De NPS-extensie gebruikt de UPN van de on-premises Active directory om de gebruiker op Azure MFA te identificeren voor het uitvoeren van de Secundaire Auth. De extensie kan worden geconfigureerd om een andere id te gebruiken, zoals alternatieve inlog-id of aangepast Active Directory-veld, met andere dan UPN. Zie voor meer informatie het artikel [Geavanceerde configuratieopties voor de NPS-extensie voor multifactorauthenticatie](howto-mfa-nps-extension-advanced.md).
- Niet alle versleutelingsprotocollen ondersteunen alle verificatiemethoden.
   - **PAP** ondersteunt telefoongesprek, eenrichtingssms, melding van mobiele apps en verificatiecode voor mobiele apps
   - **CHAPV2-** en **EAP-ondersteuning** voor telefoongesprekken en meldingen van mobiele apps

### <a name="control-radius-clients-that-require-mfa"></a>RADIUS-clients beheren waarvoor MFA nodig is

Zodra u MFA inschakelt voor een RADIUS-client met behulp van de NPS-extensie, zijn alle verificaties voor deze client vereist om MFA uit te voeren. Als u MFA wilt inschakelen voor sommige RADIUS-clients, maar niet voor andere, u twee NPS-servers configureren en de extensie op slechts één van deze servers installeren. Configureer RADIUS-clients waarvan u MFA wilt vereisen dat ze aanvragen verzenden naar de NPS-server die is geconfigureerd met de extensie en andere RADIUS-clients naar de NPS-server die niet is geconfigureerd met de extensie.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Voorbereiden op gebruikers die niet zijn ingeschreven voor MFA

Als u gebruikers hebt die niet zijn ingeschreven voor MFA, u bepalen wat er gebeurt wanneer ze proberen te verifiëren. Gebruik de registerinstelling *REQUIRE_USER_MATCH* in het registerpad *HKLM\Software\Microsoft\AzureMFA* om het functiegedrag te beheren. Deze instelling heeft één configuratieoptie:

| Sleutel | Waarde | Standaard |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | WAAR/ONWAAR | Niet ingesteld (gelijk aan WAAR) |

Het doel van deze instelling is om te bepalen wat te doen wanneer een gebruiker niet is ingeschreven voor MFA. Wanneer de sleutel niet bestaat, niet is ingesteld of is ingesteld op TRUE en de gebruiker niet is ingeschreven, mislukt de extensie de MFA-uitdaging. Wanneer de sleutel is ingesteld op FALSE en de gebruiker niet is ingeschreven, gaat de verificatie verder zonder MFA uit te voeren. Als een gebruiker is ingeschreven bij MFA, moet deze persoon verifiëren met MFA, zelfs als REQUIRE_USER_MATCH is ingesteld op FALSE.

U ervoor kiezen deze sleutel te maken en deze in te stellen op FALSE terwijl uw gebruikers aan het instappen zijn en mogelijk nog niet allemaal zijn ingeschreven voor Azure MFA. Aangezien gebruikers die niet zijn ingeschreven voor MFA deze sleutel kunnen instellen, deze sleutel echter kunnen verwijderen voordat u naar de productie gaat.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="nps-extension-health-check-script"></a>Script voor statuscontrole van NPS-extensie

Het volgende script is beschikbaar om basisstappen voor statuscontrole uit te voeren bij het oplossen van problemen met de NPS-extensie.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hoe kan ik controleren of het clientcert is geïnstalleerd zoals verwacht?

Zoek naar het zelfondertekende certificaat dat is gemaakt door de installateur in de cert-winkel en controleer of de privésleutel machtigingen heeft die zijn verleend aan de **netwerkservice van**de gebruiker. Het cert heeft een onderwerpnaam van **CN \<tenantid\>, OU = Microsoft NPS Extension**

Zelfondertekende certificaten gegenereerd door het *AzureMfaNpsExtnConfigSetup.ps1-script* hebben ook een geldigheidsduur van twee jaar. Wanneer u controleert of het certificaat is geïnstalleerd, moet u ook controleren of het certificaat niet is verlopen.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hoe kan ik controleren of mijn clientcert is gekoppeld aan mijn tenant in Azure Active Directory?

Open powershell-opdrachtprompt en voer de volgende opdrachten uit:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Met deze opdrachten worden alle certificaten afgedrukt die uw tenant koppelen aan uw instantie van de NPS-extensie in uw PowerShell-sessie. Zoek uw certificaat door uw clientcert te exporteren als een bestand "Base-64-gecodeerd X.509(.cer) zonder de privésleutel en vergelijk het met de lijst van PowerShell.

Met de volgende opdracht wordt een bestand gemaakt met de naam "npscertificate" op uw "C:" station in formaat .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Zodra u deze opdracht uitvoert, gaat u naar uw C-station, zoekt u het bestand en dubbelklikt u erop. Ga naar details en scroll naar beneden naar "thumbprint", vergelijk de duimafdruk van het certificaat dat op de server is geïnstalleerd met deze. De duimafdrukken van het certificaat moeten overeenkomen.

Valid-From en Valid-Until timestamps, die in menselijke leesbare vorm zijn, kunnen worden gebruikt om voor de hand liggende buitenbeentjes uit te filteren als de opdracht meer dan één cert retourneert.

---

### <a name="why-cannot-i-sign-in"></a>Waarom kan ik me niet aanmelden?

Controleer of uw wachtwoord niet is verlopen. De NPS-extensie biedt geen ondersteuning voor het wijzigen van wachtwoorden als onderdeel van de aanmeldingsworkflow. Neem contact op met de IT-medewerkers van uw organisatie voor verdere hulp.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Waarom worden mijn aanvragen mislukt met adal-tokenfouten?

Deze fout kan te wijten zijn aan een van de verschillende redenen. Gebruik deze stappen om problemen op te lossen:

1. Start uw NPS-server opnieuw op.
2. Controleer of het clientcertificaat is geïnstalleerd zoals verwacht.
3. Controleer of het certificaat is gekoppeld aan uw tenant in Azure AD.
4. Controleer of `https://login.microsoftonline.com/` toegankelijk is vanaf de server waarop de extensie wordt uitgevoerd.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Waarom mislukt verificatie met een fout in HTTP-logboeken waarin staat dat de gebruiker niet wordt gevonden?

Controleer of AD Connect wordt uitgevoerd en of de gebruiker aanwezig is in zowel Windows Active Directory als Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Waarom zie ik HTTP-verbindingsfouten in logboeken waarbij al mijn verificaties mislukken?

Controleer of https://adnotifications.windowsazure.com bereikbaar is vanaf de server waarop de NPS-extensie wordt uitgevoerd.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Waarom werkt verificatie niet, ondanks dat er een geldig certificaat aanwezig is?

Als uw vorige computercertificaat is verlopen en er een nieuw certificaat is gegenereerd, moet u verlopen certificaten verwijderen. Het hebben van verlopen certificaten kan problemen veroorzaken met het starten van de NPS-extensie.

Als u wilt controleren of u een geldig certificaat hebt, controleert u het certificaatarchief van het lokale computeraccount met MMC en controleert u of het certificaat de vervaldatum niet heeft gehaald. Als u een nieuw geldig certificaat wilt genereren, voert u de stappen onder de sectie["Het PowerShell-script uitvoeren" opnieuw uit](#run-the-powershell-script)

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Beheer van de TLS/SSL-protocollen en coderingssuites

Het wordt aanbevolen oudere en zwakkere versleutelingssuites uit te schakelen of te verwijderen, tenzij vereist door uw organisatie. Informatie over het uitvoeren van deze taak vindt u in het artikel [​​SSL/TLS-protocollen en coderingssuites beheren voor AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Extra probleemoplossing

Aanvullende richtlijnen voor probleemoplossing en mogelijke oplossingen zijn te vinden in het artikel [Foutberichten oplossen vanuit de NPS-extensie voor Azure Multi-Factor Authentication.](howto-mfa-nps-extension-errors.md)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht en configuratie van netwerkbeleidsserver in Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Alternatieve identiteitsteksten configureren voor aanmelding of een uitzonderingslijst instellen voor IP's die geen verificatie in twee stappen mogen uitvoeren in [geavanceerde configuratieopties voor de NPS-extensie voor multifactorverificatie](howto-mfa-nps-extension-advanced.md)

- Meer informatie over het integreren van [Remote Desktop Gateway-](howto-mfa-nps-extension-rdg.md) en [VPN-servers](howto-mfa-nps-extension-vpn.md) met de NPS-extensie

- [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Foutberichten van de NPS-extensie voor Azure Multi-Factor Authentication oplossen)

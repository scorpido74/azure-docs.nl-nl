---
title: Azure Multi-Factor Authentication configureren - Azure Active Directory
description: In dit artikel wordt beschreven hoe u Azure Multi-Factor Authentication-instellingen configureert in de Azure-portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077032e4fe3886d5bf9a678dffdffca1a5802091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263801"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Azure-instellingen voor meervoudige verificatie configureren

Met dit artikel u instellingen voor meervoudige verificatie beheren in de Azure-portal. Het behandelt verschillende onderwerpen die u helpen om het meeste uit Azure Multi-Factor Authentication te halen. Niet alle functies zijn beschikbaar in elke versie van Azure Multi-Factor Authentication.

U hebt toegang tot instellingen met betrekking tot Azure Multi-Factor Authentication vanuit de Azure-portal door te bladeren naar **Azure Active Directory** > **Security** > **MFA**.

![Azure-portal - Azure AD-instellingen voor meervoudige verificatie](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Instellingen

Sommige van deze instellingen zijn van toepassing op MFA Server, Azure MFA of beide.

| Functie | Beschrijving |
| ------- | ----------- |
| Accountuitsluiting | Accounts in de multi-factor-verificatieservice tijdelijk vergrendelen als er te veel pogingen tot geweigerde verificatie op een rij zijn. Deze functie is alleen van toepassing op gebruikers die een pincode invoeren om te verifiëren. (MFA-server) |
| [Gebruikers blokkeren/deblokkeren](#block-and-unblock-users) | Wordt gebruikt om te voorkomen dat specifieke gebruikers multifactorauthenticatieaanvragen kunnen ontvangen. Verificatiepogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze zijn geblokkeerd. |
| [Fraudewaarschuwing](#fraud-alert) | Instellingen configureren met betrekking tot de mogelijkheid van gebruikers om frauduleuze verificatieverzoeken te melden |
| [Meldingen](#notifications) | Meldingen van gebeurtenissen van MFA Server inschakelen. |
| [EED-tokens](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Wordt gebruikt in cloudgebaseerde Azure MFA-omgevingen om OATH-tokens voor gebruikers te beheren. |
| [Instellingen voor bellen](#phone-call-settings) | Instellingen configureren met betrekking tot telefoongesprekken en begroetingen voor cloud- en on-premises omgevingen. |
| Providers | Hier ziet u alle bestaande verificatieproviders die u mogelijk aan uw account hebt gekoppeld. Nieuwe verificatieproviders worden mogelijk niet meer gemaakt vanaf 1 september 2018 |

## <a name="manage-mfa-server"></a>MFA-server beheren

Instellingen in deze sectie zijn alleen voor MFA Server.

| Functie | Beschrijving |
| ------- | ----------- |
| Serverinstellingen | MFA Server downloaden en activeringsreferenties genereren om uw omgeving te initialiseren |
| [Eenmalige bypass](#one-time-bypass) | Laat een gebruiker zich verifiëren zonder gedurende een beperkte tijd verificatie in twee stappen uit te voeren. |
| [Caching-regels](#caching-rules) |  Caching wordt voornamelijk gebruikt wanneer on-premises systemen, zoals VPN, meerdere verificatieverzoeken verzenden terwijl het eerste verzoek nog in uitvoering is. Met deze functie kunnen de volgende aanvragen automatisch worden uitgevoerd, nadat de gebruiker de eerste verificatie heeft uitgevoerd. |
| Serverstatus | Bekijk de status van uw on-premises MFA-servers, inclusief versie, status, IP en laatste communicatietijd en -datum. |

## <a name="activity-report"></a>Activiteitenrapport

De hier beschikbare rapportage is specifiek voor MFA Server (on-premises). Voor Azure MFA-rapporten (cloud) ziet u het aanmeldingsrapport in Azure AD.

## <a name="block-and-unblock-users"></a>Gebruikers blokkeren en deblokkeren

Gebruik de functie _Voor het blokkeren en deblokkeren van gebruikers_ om te voorkomen dat gebruikers verificatieverzoeken ontvangen. Verificatiepogingen voor geblokkeerde gebruikers worden automatisch geweigerd. Gebruikers blijven 90 dagen geblokkeerd vanaf het moment dat ze zijn geblokkeerd.

### <a name="block-a-user"></a>Een gebruiker blokkeren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Blader naar **Azure Active Directory** > **Security** > **MFA** > **Blokkeren/deblokkeren van gebruikers.**
3. Selecteer **Toevoegen** om een gebruiker te blokkeren.
4. Selecteer de **replicatiegroep**. Voer de gebruikersnaam in voor de geblokkeerde gebruiker als **gebruikersnaam\@domain.com**. Voer een opmerking in het veld **Reden** in.
5. Selecteer **Toevoegen** om de gebruiker te blokkeren.

### <a name="unblock-a-user"></a>Een gebruiker deblokkering van een gebruiker deblokkeren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Blader naar **Azure Active Directory** > **Security** > **MFA** > **Blokkeren/deblokkeren van gebruikers.**
3. Selecteer **De blokkering opheffen** in de kolom **Actie** naast de gebruiker om de blokkering te opheffen.
4. Voer een opmerking in het veld **Reden voor het deblokkeren.**
5. Selecteer **De blokkering opheffen** om de blokkering van de gebruiker te beëindigen.

## <a name="fraud-alert"></a>Fraudewaarschuwing

Configureer de _functie voor fraudewaarschuwing,_ zodat uw gebruikers frauduleuze pogingen om toegang te krijgen tot hun bronnen kunnen melden. Gebruikers kunnen fraudepogingen melden via de mobiele app of via hun telefoon.

### <a name="turn-on-fraud-alerts"></a>Fraudemeldingen inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Blader naar **azure Active Directory** > **Security** > **MFA-fraudewaarschuwing****MFA** > .
3. Stel de instelling **Voor gebruikers in om fraudemeldingen in** te dienen in op **Aan**.
4. Selecteer **Opslaan**.

### <a name="configuration-options"></a>Configuratie-opties

* **Gebruiker blokkeren wanneer fraude wordt gemeld:** Als een gebruiker fraude meldt, wordt zijn/haar account 90 dagen geblokkeerd of totdat een beheerder de blokkering van zijn account heeft gedeblokkeerd. Een beheerder kan aanmeldingen controleren met behulp van het aanmeldingsrapport en passende maatregelen nemen om toekomstige fraude te voorkomen. Een beheerder kan vervolgens het account van de gebruiker [deblokkeren.](#unblock-a-user)
* **Code om fraude te melden tijdens de eerste begroeting:** Wanneer gebruikers **#** een telefoontje ontvangen om verificatie in twee stappen uit te voeren, drukken ze normaal gesproken op hun aanmelding. Om fraude te melden, voert **#** de gebruiker een code in voordat hij op . Deze code is standaard **0,** maar u deze aanpassen.

   >[!NOTE]
   >De standaardgesproken begroetingen van Microsoft instrueren gebruikers om op **0#** te drukken om een fraudewaarschuwing in te dienen. Als u een andere code dan **0**wilt gebruiken, neemt u uw eigen aangepaste gesproken begroetingen op en uploadt u deze met de juiste instructies voor uw gebruikers.
   >

### <a name="view-fraud-reports"></a>Frauderapporten bekijken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer**aanmeldingen in** **Azure Active Directory** > . Het frauderapport maakt nu deel uit van het standaard Azure AD-aanmeldingsrapport.

## <a name="notifications"></a>Meldingen

Configureer hier e-mailadressen voor gebruikers die e-mails met fraudewaarschuwingen ontvangen.

![Voorbeeld van meldingsfraudewaarschuwing](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Instellingen voor bellen

### <a name="caller-id"></a>Beller-ID

**MFA beller ID-nummer** - Dit is het nummer dat uw gebruikers op hun telefoon zien. Alleen in de VS gevestigde nummers zijn toegestaan.

>[!NOTE]
>Wanneer multi-factor authenticatie gesprekken worden geplaatst via het openbare telefoonnetwerk, soms worden ze gerouteerd via een provider die geen ondersteuning voor beller-ID. Hierdoor is beller-ID niet gegarandeerd, hoewel het Multi-Factor Authentication-systeem het altijd verzendt.

In de Verenigde Staten, als u mfa-beller-id niet hebt geconfigureerd, komen spraakoproepen van Microsoft uit de volgende nummers: +1 (866) 539 4191, +1 (855) 330 8653 en +1 (877) 668 6536. Als u spamfilters gebruikt, moet u deze nummers uitsluiten.

### <a name="custom-voice-messages"></a>Aangepaste spraakberichten

U uw eigen opnamen of begroetingen gebruiken voor verificatie in twee stappen met de _aangepaste functie voor spraakberichten._ Deze berichten kunnen worden gebruikt in aanvulling op of ter vervanging van de Microsoft-opnamen.

Wees u bewust van de volgende beperkingen voordat u begint:

* De ondersteunde bestandsindelingen zijn .wav en .mp3.
* De limiet voor bestandsgrootte is 1 MB.
* Verificatieberichten moeten korter zijn dan 20 seconden. Berichten die langer zijn dan 20 seconden, kunnen ervoor zorgen dat de verificatie mislukt. De gebruiker reageert mogelijk niet voordat het bericht is afgelopen en de verificatie is afgelopen.

### <a name="custom-message-language-behavior"></a>Aangepast berichttaalgedrag

Wanneer een aangepast spraakbericht wordt afgespeeld voor de gebruiker, is de taal van het bericht afhankelijk van deze factoren:

* De taal van de huidige gebruiker.
  * De taal gedetecteerd door de browser van de gebruiker.
  * Andere verificatiescenario's kunnen zich anders gedragen.
* De taal van alle beschikbare aangepaste berichten.
  * Deze taal wordt gekozen door de beheerder wanneer een aangepast bericht wordt toegevoegd.

Als er bijvoorbeeld slechts één aangepast bericht is, met een taal duits:

* Een gebruiker die zich in de Duitse taal verifieert, hoort het aangepaste Duitse bericht.
* Een gebruiker die zich in het Engels verifieert, hoort het standaard Engelse bericht.

### <a name="set-up-a-custom-message"></a>Een aangepast bericht instellen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
1. Blader naar **Azure Active Directory** > **Security** > **MFA-telefoongespreksinstellingen****MFA** > .
1. Selecteer **Begroeting toevoegen**.
1. Kies het type begroeting.
1. Kies de taal.
1. Selecteer een .mp3- of .wav-geluidsbestand dat u wilt uploaden.
1. Selecteer **Toevoegen**.

### <a name="custom-voice-message-defaults"></a>Standaardinstellingen voor aangepaste gesproken berichten

Voorbeeldscripts voor het maken van aangepaste berichten.

| Berichtnaam | Script |
| --- | --- |
| Verificatie geslaagd | Uw aanmelding is geverifieerd. Dag. |
| Verlengingsprompt | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft. Druk op pond toets om door te gaan. |
| Fraudebevestiging | Er is een fraudemelding ingediend. Neem contact op met de IT-helpdesk van uw bedrijf om de blokkering van uw account te doen. |
| De groet van de fraude (Standaard) | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft. Druk op de pound-toets om uw verificatie te voltooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op nul pond om een fraude waarschuwing in te dienen. Dit zal het IT-team van uw bedrijf op de hoogte brengen en verdere verificatiepogingen blokkeren. |
| Fraude gemeld Er is een fraudemelding ingediend. | Neem contact op met de IT-helpdesk van uw bedrijf om de blokkering van uw account te doen. |
| Activering | Bedankt voor het gebruik van het aanmeldingssysteem van Microsoft. Druk op de pound-toets om uw verificatie te voltooien. |
| Verificatie geweigerd opnieuw proberen | Verificatie geweigerd. |
| Opnieuw proberen (standaard) | Bedankt voor het gebruik van het aanmeldingssysteem van Microsoft. Druk op de pound-toets om uw verificatie te voltooien. |
| Begroeting (standaard) | Bedankt voor het gebruik van het aanmeldingssysteem van Microsoft. Druk op de pound-toets om uw verificatie te voltooien. |
| Begroeting (pincode) | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft. Voer uw pincode in, gevolgd door de pound-sleutel om uw verificatie te voltooien. |
| Fraudegroet (PINCODE) | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft.  Voer uw pincode in, gevolgd door de pound-sleutel om uw verificatie te voltooien. Als u deze verificatie niet hebt gestart, probeert iemand mogelijk toegang te krijgen tot uw account. Druk op nul pond om een fraude waarschuwing in te dienen. Dit zal het IT-team van uw bedrijf op de hoogte brengen en verdere verificatiepogingen blokkeren. |
| Opnieuw proberen(pincode) | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft. Voer uw pincode in, gevolgd door de pound-sleutel om uw verificatie te voltooien. |
| Verlengingsprompt na cijfers | Als u al bij deze uitbreiding bent, drukt u op de pondtoets om door te gaan. |
| Verificatie geweigerd | Het spijt me, we kunnen je op dit moment niet aanmelden. Probeer het later opnieuw. |
| Activeringsbegroeting (standaard) | Bedankt voor het gebruik van het aanmeldingssysteem van Microsoft. Druk op de pound-toets om uw verificatie te voltooien. |
| Activeringspoging opnieuw (standaard) | Bedankt voor het gebruik van het aanmeldingssysteem van Microsoft. Druk op de pound-toets om uw verificatie te voltooien. |
| Activeringsbegroeting (pincode) | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft. Voer uw pincode in, gevolgd door de pound-sleutel om uw verificatie te voltooien. |
| Verlengingsprompt voor cijfers | Bedankt voor het gebruik van het aanmeldingsverificatiesysteem van Microsoft. Gelieve deze oproep over te dragen aan uitbreiding ... |

## <a name="one-time-bypass"></a>Eenmalige bypass

Met _de eenmalige bypass-functie_ kan een gebruiker één keer verifiëren zonder verificatie in twee stappen uit te voeren. De bypass is tijdelijk en verloopt na een bepaald aantal seconden. In situaties waarin de mobiele app of telefoon geen melding of telefoontje ontvangt, u een eenmalige bypass toestaan, zodat de gebruiker toegang heeft tot de gewenste bron.

### <a name="create-a-one-time-bypass"></a>Een eenmalige bypass maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Blader naar **Azure Active Directory** > **Security** > **MFA** > **Eenmalige bypass**.
3. Selecteer **Toevoegen**.
4. Selecteer indien nodig de replicatiegroep voor de bypass.
5. Voer de gebruikersnaam in als **gebruikersnaam\@domain.com**. Voer het aantal seconden in dat de bypass moet duren. Voer de reden voor de bypass in.
6. Selecteer **Toevoegen**. De termijn gaat onmiddellijk in. De gebruiker moet zich aanmelden voordat de eenmalige bypass verloopt.

### <a name="view-the-one-time-bypass-report"></a>Het eenmalige bypassrapport weergeven

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Blader naar **Azure Active Directory** > **Security** > **MFA** > **Eenmalige bypass**.

## <a name="caching-rules"></a>Caching-regels

U een periode instellen om verificatiepogingen toe te staan nadat een gebruiker is geverifieerd met behulp van de _caching-functie._ Latere verificatiepogingen voor de gebruiker binnen de opgegeven periode worden automatisch gestart. Caching wordt voornamelijk gebruikt wanneer on-premises systemen, zoals VPN, meerdere verificatieverzoeken verzenden terwijl het eerste verzoek nog in uitvoering is. Met deze functie kunnen de volgende aanvragen automatisch worden uitgevoerd, nadat de gebruiker de eerste verificatie heeft uitgevoerd.

>[!NOTE]
>De caching-functie is niet bedoeld voor aanmeldingen bij Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Caching instellen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Blader naar **Azure Active Directory** > **Security** > **MFA Caching-regels** > **Caching rules**.
3. Selecteer **Toevoegen**.
4. Selecteer het **cachetype** in de vervolgkeuzelijst. Voer het maximum aantal **cacheseconden in.**
5. Selecteer indien nodig een verificatietype en geef een toepassing op.
6. Selecteer **Toevoegen**.

## <a name="mfa-service-settings"></a>MFA-service-instellingen

Instellingen voor app-wachtwoorden, vertrouwde IP's, verificatieopties en multifactorauthenticatie voor Azure Multi-Factor Authentication zijn te vinden in service-instellingen. Service-instellingen kunnen worden geopend vanuit de Azure-portal door te bladeren naar **Azure Active Directory** > **Security** > **MFA** > **Aan de slag** > **Configureren** > **aanvullende cloudgebaseerde MFA-instellingen**.

![Azure Multi-Factor Authentication-serviceinstellingen](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

De vertrouwde IP-adresbereiken kunnen privé of openbaar zijn.

## <a name="app-passwords"></a>App-wachtwoorden

Sommige toepassingen, zoals Office 2010 of eerder en Apple Mail vóór iOS 11, bieden geen ondersteuning voor verificatie in twee stappen. De apps zijn niet geconfigureerd om een tweede verificatie te accepteren. Als u deze toepassingen wilt gebruiken, u profiteren van de functie _app-wachtwoorden._ U een app-wachtwoord gebruiken in plaats van uw traditionele wachtwoord, zodat een app verificatie in twee stappen kan omzeilen en blijft werken.

Moderne verificatie wordt ondersteund voor de Microsoft Office 2013-clients en hoger. Office 2013-clients, waaronder Outlook, ondersteunen moderne verificatieprotocollen en kunnen worden ingeschakeld om te werken met verificatie in twee stappen. Nadat de client is ingeschakeld, zijn app-wachtwoorden niet vereist voor de client.

>[!NOTE]
>App-wachtwoorden werken niet met op voorwaardelijke toegang gebaseerd multi-factor authenticatiebeleid en moderne verificatie.

### <a name="considerations-about-app-passwords"></a>Overwegingen over app-wachtwoorden

Houd bij het gebruik van app-wachtwoorden rekening met de volgende belangrijke punten:

* App-wachtwoorden worden slechts één keer per toepassing ingevoerd. Gebruikers hoeven de wachtwoorden niet elke keer bij te houden of in te voeren.
* Het werkelijke wachtwoord wordt automatisch gegenereerd en wordt niet geleverd door de gebruiker. Het automatisch gegenereerde wachtwoord is moeilijker te raden voor een aanvaller en is veiliger.
* Er is een limiet van 40 wachtwoorden per gebruiker.
* Toepassingen die wachtwoorden in de cache opslaan en deze gebruiken in on-premises scenario's, kunnen mislukken omdat het app-wachtwoord niet bekend is buiten het werk- of schoolaccount. Een voorbeeld van dit scenario is Exchange-e-mails die on-premises zijn, maar de gearchiveerde e-mail zich in de cloud bevindt. In dit scenario werkt hetzelfde wachtwoord niet.
* Nadat multifactorverificatie is ingeschakeld op het account van een gebruiker, kunnen app-wachtwoorden worden gebruikt bij de meeste niet-browserclients zoals Outlook en Microsoft Skype voor Bedrijven. Administratieve acties kunnen niet worden uitgevoerd met app-wachtwoorden via niet-browsertoepassingen, zoals Windows PowerShell. De acties kunnen niet worden uitgevoerd, zelfs niet wanneer de gebruiker een administratief account heeft. Als u PowerShell-scripts wilt uitvoeren, maakt u een serviceaccount met een sterk wachtwoord en schakelt u het account niet in voor verificatie in twee stappen.

>[!WARNING]
>App-wachtwoorden werken niet in hybride omgevingen waar clients communiceren met zowel on-premises als cloud automatisch ontdekken eindpunten. Domeinwachtwoorden zijn vereist om on-premises te verifiëren. App-wachtwoorden zijn vereist om te verifiëren met de cloud.

### <a name="guidance-for-app-password-names"></a>Richtlijnen voor wachtwoordnamen van apps

De wachtwoordnamen van apps moeten het apparaat weergeven waarop ze worden gebruikt. Als u een laptop hebt met niet-browsertoepassingen zoals Outlook, Word en Excel, maakt u een app-wachtwoord met de naam **Laptop** voor deze apps. Maak een ander app-wachtwoord met de naam **Bureaublad** voor dezelfde toepassingen die op uw desktopcomputer worden uitgevoerd.

>[!NOTE]
>We raden u aan één app-wachtwoord per apparaat te maken, in plaats van één app-wachtwoord per toepassing.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federatieve of enkele app-wachtwoorden

Azure AD ondersteunt federatie of enkele aanmelding (SSO) met on-premises Windows Server Active Directory Domain Services (AD DS). Als uw organisatie is gefedereerd met Azure AD en u Azure Multi-Factor Authentication gebruikt, moet u rekening houden met de volgende punten over app-wachtwoorden.

>[!NOTE]
>De volgende punten zijn alleen van toepassing op federatieve (SSO) klanten.

* App-wachtwoorden worden geverifieerd door Azure AD en omzeilen daarom de federatie. Federation wordt alleen actief gebruikt bij het instellen van app-wachtwoorden.
* De Identity Provider (IdP) wordt niet gecontacteerd voor federatieve (SSO) gebruikers, in tegenstelling tot de passieve stroom. De app-wachtwoorden worden opgeslagen in het werk- of schoolaccount. Als een gebruiker het bedrijf verlaat, stroomt de informatie van de gebruiker naar het werk- of schoolaccount met Behulp van **DirSync** in realtime. Het uitschakelen/verwijderen van het account kan tot drie uur duren om te synchroniseren, waardoor het wachtwoord van de app in Azure AD kan worden uitgeschakeld/verwijderd.
* On-premises client Access Control-instellingen worden niet gehonoreerd door de functie app-wachtwoorden.
* Er is geen on-premises verificatielogboekregistratie/controlemogelijkheid beschikbaar voor gebruik met de functie app-wachtwoorden.
* Sommige geavanceerde architecturen vereisen een combinatie van referenties voor verificatie in twee stappen met clients. Deze referenties kunnen een gebruikersnaam voor werk- of schoolaccounts en wachtwoorden en app-wachtwoorden bevatten. De vereisten zijn afhankelijk van hoe de verificatie wordt uitgevoerd. Voor clients die zich verifiëren tegen een on-premises infrastructuur, een gebruikersnaam voor werk- of schoolaccount en wachtwoord vereist. Voor clients die zich verifiëren tegen Azure AD, is een app-wachtwoord vereist.

  Stel dat u de volgende architectuur hebt:

  * Uw on-premises exemplaar van Active Directory wordt gefedereerd met Azure AD.
  * U gebruikt Exchange online.
  * U gebruikt skype voor bedrijven on-premises.
  * U gebruikt Azure Multi-Factor Authentication.

  In dit scenario gebruikt u de volgende referenties:

  * Als u zich wilt aanmelden bij Skype voor Bedrijven, gebruikt u de gebruikersnaam en het wachtwoord van uw werk- of schoolaccount.
  * Als u het adresboek wilt openen vanuit een Outlook-client die online verbinding maakt met Exchange, gebruikt u een app-wachtwoord.

### <a name="allow-users-to-create-app-passwords"></a>Gebruikers toestaan app-wachtwoorden te maken

Gebruikers kunnen standaard geen app-wachtwoorden maken. De functie app-wachtwoorden moet zijn ingeschakeld. Gebruik de volgende procedure om gebruikers de mogelijkheid te bieden app-wachtwoorden te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer azure Active**Directory-gebruikers** **Azure Active Directory** > aan de linkerkant .
3. Selecteer **Meervoudige verificatie**.
4. Selecteer **service-instellingen**onder Multi-Factor Authentication .
5. Selecteer op de pagina **Service-instellingen** de optie **Gebruikers toestaan app-wachtwoorden te maken om zich aan te melden bij apps die niet in de browser zijn** aangemeld.

### <a name="create-app-passwords"></a>App-wachtwoorden maken

Gebruikers kunnen app-wachtwoorden maken tijdens hun eerste registratie. De gebruiker heeft de mogelijkheid om app-wachtwoorden te maken aan het einde van het registratieproces.

Gebruikers kunnen ook app-wachtwoorden maken na registratie. Zie [Wat zijn app-wachtwoorden in Azure Multi-Factor Authentication voor](../user-help/multi-factor-authentication-end-user-app-passwords.md) meer informatie en gedetailleerde stappen voor uw gebruikers?

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

De _functie Vertrouwde IP's_ van Azure Multi-Factor Authentication wordt gebruikt door beheerders van een beheerde of federatieve tenant. De functie omzeilt verificatie in twee stappen voor gebruikers die zich aanmelden vanaf het intranet van het bedrijf. De functie is beschikbaar met de volledige versie van Azure Multi-Factor Authentication en niet de gratis versie voor beheerders. Zie Azure Multi-Factor Authentication voor meer informatie over het krijgen van de volledige versie van Azure [Multi-Factor Authentication.](multi-factor-authentication.md)

> [!NOTE]
> MFA vertrouwde IP's en locaties met de naam Conditional Access werken alleen met IPV4-adressen.

Als uw organisatie de NPS-extensie implementeert om MFA te bieden aan on-premises toepassingen, wordt het bron-IP-adres altijd de NPS-server weergegeven waar de verificatiepoging doorheen stroomt.

| Azure AD-tenanttype | Opties voor vertrouwde IP-functie |
|:--- |:--- |
| Beheerd |**Specifiek bereik van IP-adressen**: Beheerders geven een reeks IP-adressen op die verificatie in twee stappen kunnen omzeilen voor gebruikers die zich aanmelden vanaf het intranet van het bedrijf. Er kunnen maximaal 50 vertrouwde IP-bereiken worden geconfigureerd.|
| Federatief |**Alle federatieve gebruikers:** Alle federatieve gebruikers die zich aanmelden vanuit de binnenkant van de organisatie kunnen verificatie in twee stappen omzeilen. De gebruikers omzeilen verificatie met behulp van een claim die is uitgegeven door Ad FS (Active Directory Federation Services).<br/>**Specifiek bereik van IP-adressen**: Beheerders geven een reeks IP-adressen op die verificatie in twee stappen kunnen omzeilen voor gebruikers die zich aanmelden vanaf het intranet van het bedrijf. |

De Trusted IP's bypass werkt alleen vanuit het intranet van het bedrijf. Als u de optie **Alle federatieve gebruikers** selecteert en een gebruiker zich aanmeldt van buiten het bedrijfsintranet, moet de gebruiker zich verifiëren met behulp van verificatie in twee stappen. Het proces is hetzelfde, zelfs als de gebruiker een AD FS-claim presenteert. 

### <a name="end-user-experience-inside-of-corpnet"></a>Eindgebruikerservaring binnen in corpnet

Wanneer de functie Vertrouwde IP's is uitgeschakeld, is verificatie in twee stappen vereist voor browserstromen. App-wachtwoorden zijn vereist voor oudere uitgebreide clienttoepassingen.

Wanneer de functie Vertrouwde IP's is ingeschakeld, is verificatie in twee stappen *niet* vereist voor browserstromen. App-wachtwoorden zijn *niet* vereist voor oudere uitgebreide clienttoepassingen, op voorwaarde dat de gebruiker geen app-wachtwoord heeft gemaakt. Nadat een app-wachtwoord is gebruikt, blijft het wachtwoord vereist. 

### <a name="end-user-experience-outside-corpnet"></a>Ervaring buiten corpnet

Ongeacht of de functie Vertrouwde IP's is ingeschakeld, is verificatie in twee stappen vereist voor browserstromen. App-wachtwoorden zijn vereist voor oudere uitgebreide clienttoepassingen.

### <a name="enable-named-locations-by-using-conditional-access"></a>Benoemde locaties inschakelen met voorwaardelijke toegang

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer aan de linkerkant de**locatie locaties met**voorwaardelijke toegang met voorwaardelijke toegang voor Azure Active**Directory-beveiliging** > **Conditional Access** >  **Azure Active Directory** > .
3. Selecteer **Nieuwe locatie**.
4. Voer een naam in voor de locatie.
5. Selecteer **Markeren als vertrouwde locatie**.
6. Voer het IP-bereik in CIDR-notatie in, zoals **192.168.1.1/24**.
7. Selecteer **Maken**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>De functie Vertrouwde IP's inschakelen met voorwaardelijke toegang

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer aan de linkerkant de**locatie locaties met**voorwaardelijke toegang met voorwaardelijke toegang voor Azure Active**Directory-beveiliging** >  **Conditional Access** >  **Azure Active Directory** > .
3. Selecteer **MFA vertrouwde IP's configureren**.
4. Kies op de pagina **Service-instellingen** onder **Vertrouwde IP's**uit een van de volgende twee opties:

   * **Voor aanvragen van federatieve gebruikers die afkomstig zijn van mijn intranet:** Schakel het selectievakje in om deze optie te kiezen. Alle federatieve gebruikers die zich aanmelden via het bedrijfsnetwerk omzeilen verificatie in twee stappen met behulp van een claim die is uitgegeven door AD FS. Zorg ervoor dat AD FS een regel heeft om de intranetclaim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen uit een specifiek bereik van openbare IP-adressen**: Als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met CIDR-notatie.
      * Voor IP-adressen die zich in het bereik xxx.xxx.xxx.1 tot en met xxx.xxx.xxx.254 bevinden, gebruikt u notatie zoals **xxx.xxx.xxx.0/24**.
      * Gebruik notatie zoals **xxx.xxx.xxx.xxx/32**voor één IP-adres.
      * Voer maximaal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen omzeilen verificatie in twee stappen.

5. Selecteer **Opslaan**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>De functie Vertrouwde IP's inschakelen met service-instellingen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer azure Active**Directory-gebruikers** **Azure Active Directory** > aan de linkerkant .
3. Selecteer **Meervoudige verificatie**.
4. Selecteer **service-instellingen**onder Multi-Factor Authentication .
5. Kies op de pagina **Service-instellingen** onder **Vertrouwde IP's**een van de volgende twee opties:

   * **Voor aanvragen van federatieve gebruikers op mijn intranet**: Schakel het selectievakje in om deze optie te kiezen. Alle federatieve gebruikers die zich aanmelden via het bedrijfsnetwerk omzeilen verificatie in twee stappen met behulp van een claim die is uitgegeven door AD FS. Zorg ervoor dat AD FS een regel heeft om de intranetclaim toe te voegen aan het juiste verkeer. Als de regel niet bestaat, maakt u de volgende regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Voor aanvragen uit een bepaald bereik van IP-adressubnetten**: Als u deze optie wilt kiezen, voert u de IP-adressen in het tekstvak in met CIDR-notatie.
      * Voor IP-adressen die zich in het bereik xxx.xxx.xxx.1 tot en met xxx.xxx.xxx.254 bevinden, gebruikt u notatie zoals **xxx.xxx.xxx.0/24**.
      * Gebruik notatie zoals **xxx.xxx.xxx.xxx/32**voor één IP-adres.
      * Voer maximaal 50 IP-adresbereiken in. Gebruikers die zich aanmelden vanaf deze IP-adressen omzeilen verificatie in twee stappen.

6. Selecteer **Opslaan**.

## <a name="verification-methods"></a>Verificatiemethoden

U de verificatiemethoden kiezen die beschikbaar zijn voor uw gebruikers. De volgende tabel geeft een kort overzicht van de methoden.

Wanneer uw gebruikers hun accounts inschrijven voor Azure Multi-Factor Authentication, kiezen ze hun gewenste verificatiemethode uit de opties die u hebt ingeschakeld. Richtlijnen voor het gebruikersinschrijvingsproces worden verstrekt in [Mijn account instellen voor verificatie in twee stappen.](../user-help/multi-factor-authentication-end-user-first-time.md)

| Methode | Beschrijving |
|:--- |:--- |
| Bellen naar de telefoon |Hiermee plaatst u een geautomatiseerd telefoongesprek. De gebruiker beantwoordt het gesprek en drukt # in op de toetsenblok van de telefoon om te authenticeren. Het telefoonnummer is niet gesynchroniseerd met on-premises Active Directory. |
| Sms-bericht naar telefoon |Hiermee verzendt u een sms-bericht met een verificatiecode. De gebruiker wordt gevraagd om de verificatiecode in te voeren in de aanmeldingsinterface. Dit proces wordt one-way SMS genoemd. Sms in twee richtingen betekent dat de gebruiker een bepaalde code moet terugsms'en. Tweerichtingssms wordt afgeschaft en niet ondersteund na 14 november 2018. Beheerders moeten een andere methode inschakelen voor gebruikers die eerder sms in twee richtingen hebben gebruikt.|
| Melding via mobiele app |Hiermee stuurt u een pushmelding naar uw telefoon of geregistreerd apparaat. De gebruiker bekijkt de melding en selecteert **Verifiëren** om verificatie te voltooien. De Microsoft Authenticator-app is beschikbaar voor [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Verificatiecode van mobiele app of hardwaretoken |De Microsoft Authenticator-app genereert elke 30 seconden een nieuwe OATH-verificatiecode. De gebruiker voert de verificatiecode in op de aanmeldingsinterface. De Microsoft Authenticator-app is beschikbaar voor [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)en [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

### <a name="enable-and-disable-verification-methods"></a>Verificatiemethoden in- en uitschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer azure Active**Directory-gebruikers** **Azure Active Directory** > aan de linkerkant .
3. Selecteer **Meervoudige verificatie**.
4. Selecteer **service-instellingen**onder Multi-Factor Authentication .
5. Selecteer op de pagina **Service-instellingen** onder **verificatieopties**de methoden die u aan uw gebruikers wilt bieden.
6. Klik op **Opslaan**.

Aanvullende details over het gebruik van authenticatiemethoden vindt u in het artikel [Wat zijn verificatiemethoden.](concept-authentication-methods.md)

## <a name="remember-multi-factor-authentication"></a>Multi-Factor-verificatie onthouden

De _functie Multi-Factor Authentication onthouden_ voor apparaten en browsers die door de gebruiker worden vertrouwd, is een gratis functie voor alle gebruikers van multifactorverificatie. Gebruikers kunnen volgende controles gedurende een bepaald aantal dagen overslaan nadat ze zijn aangemeld bij een apparaat met behulp van Multi-Factor Authentication. De functie verbetert de bruikbaarheid door het minimaliseren van het aantal keren dat een gebruiker tweestapsverificatie op hetzelfde apparaat moet uitvoeren.

>[!IMPORTANT]
>Als een account of apparaat is gecompromitteerd, kan het onthouden van multi-factorauthenticatie voor vertrouwde apparaten de beveiliging beïnvloeden. Als een bedrijfsaccount wordt gecompromitteerd of een vertrouwd apparaat verloren of wordt gestolen, moet u [MFA-sessies intrekken.](howto-mfa-userdevicesettings.md)
>
>Met de herstelactie wordt de vertrouwde status van alle apparaten ingetrokken en moet de gebruiker opnieuw tweestapsverificatie uitvoeren. U uw gebruikers ook instrueren om multifactorverificatie op hun eigen apparaten te herstellen met de instructies in [Uw instellingen beheren voor verificatie in twee stappen.](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)

### <a name="how-the-feature-works"></a>Hoe de functie werkt

De functie Multi-Factor Authentication onthouden stelt een permanente cookie in op de browser wanneer een gebruiker de optie **Niet opnieuw vragen om X-dagen bij** aanmelding selecteert. De gebruiker wordt niet opnieuw gevraagd voor Multi-Factor Authenticatie van diezelfde browser totdat de cookie verloopt. Als de gebruiker een andere browser op hetzelfde apparaat opent of zijn cookies verwijdert, wordt deze opnieuw gevraagd om deze te verifiëren.

De **optie Niet opnieuw vragen voor X-dagen** wordt niet weergegeven in niet-browsertoepassingen, ongeacht of de app moderne verificatie ondersteunt. Deze apps gebruiken _refresh tokens_ die elk uur nieuwe toegangstokens bieden. Wanneer een vernieuwingstoken is gevalideerd, controleert Azure AD of de laatste verificatie in twee stappen binnen het opgegeven aantal dagen heeft plaatsgevonden.

De functie vermindert het aantal verificaties op web-apps, die normaal elke keer worden gevraagd. De functie verhoogt het aantal verificaties voor moderne verificatieclients die normaal gesproken elke 90 dagen vragen. Kan ook het aantal verificaties verhogen in combinatie met beleid voor voorwaardelijke toegang.

>[!IMPORTANT]
>De functie **Multifactorverificatie onthouden** is niet compatibel met de functie **Houd me aangemeld in** AD FS, wanneer gebruikers tweestapsverificatie uitvoeren voor AD FS via Azure Multi-Factor Authentication Server of een multifactorauthenticatieoplossing van derden.
>
>Als uw gebruikers mij laten **inloggen** op AD FS en hun apparaat ook markeren als vertrouwd voor multifactorverificatie, wordt de gebruiker niet automatisch geverifieerd nadat het aantal meervoudige **verificaties** van onthouden van dagen is verlopen. Azure AD vraagt een nieuwe verificatie in twee stappen aan, maar AD FS retourneert een token met de oorspronkelijke multifactorverificatieclaim en -datum, in plaats van opnieuw verificatie in twee stappen uit te voeren. **Met deze reactie wordt een verificatielus tussen Azure AD en AD FS ingesteld.**
>
>De functie **Multi-Factor Authentication onthouden** is niet compatibel met B2B-gebruikers en is niet zichtbaar voor B2B-gebruikers wanneer ze zich aanmelden bij de uitgenodigde tenants.
>

### <a name="enable-remember-multi-factor-authentication"></a>Multifactorverificatie onthouden inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer azure Active**Directory-gebruikers** **Azure Active Directory** > aan de linkerkant .
3. Selecteer **Meervoudige verificatie**.
4. Selecteer **service-instellingen**onder Multi-Factor Authentication .
5. Op de pagina **Service-instellingen** **beheren onthoud multi-factor authenticatie**, selecteer de optie Gebruikers toestaan om **multi-factor authenticatie te onthouden op apparaten die ze vertrouwen** optie.
6. Stel het aantal dagen in zodat vertrouwde apparaten verificatie in twee stappen kunnen omzeilen. De standaardinstelling is 14 dagen.
7. Selecteer **Opslaan**.

### <a name="mark-a-device-as-trusted"></a>Een apparaat markeren als vertrouwd

Nadat u de functie Multifactorverificatie onthouden hebt ingeschakeld, kunnen gebruikers een apparaat markeren als vertrouwd wanneer ze zich aanmelden door **Niet opnieuw vragen te selecteren.**

## <a name="next-steps"></a>Volgende stappen

[Azure AD-aanmeldingspaginabranding wijzigen](../fundamentals/customize-branding.md)

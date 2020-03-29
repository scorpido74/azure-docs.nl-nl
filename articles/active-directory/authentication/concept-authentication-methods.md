---
title: Verificatiemethoden - Azure Active Directory
description: Verificatiemethoden beschikbaar in Azure AD voor MFA en SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968774"
---
# <a name="what-are-authentication-methods"></a>Wat zijn verificatiemethoden?

Als beheerder wordt het raadzaam om verificatiemethoden te kiezen voor Azure Multi-Factor Authentication en selfservice password reset (SSPR) dat u gebruikers verplicht om meerdere verificatiemethoden te registreren. Wanneer een verificatiemethode niet beschikbaar is voor een gebruiker, kunnen ze ervoor kiezen om te verifiëren met een andere methode.

Beheerders kunnen in het beleid bepalen welke verificatiemethoden beschikbaar zijn voor gebruikers van SSPR en MFA. Sommige verificatiemethoden zijn mogelijk niet voor alle functies beschikbaar. Zie de artikelen Hoe u [selfservicewachtwoord reset](howto-sspr-deployment.md) en [het plannen van een cloudgebaseerde Azure Multi-Factor Authentication](howto-mfa-getstarted.md) met succes uitrollen voor meer informatie over het configureren van uw beleid.

Microsoft raadt beheerders ten zeerste aan om gebruikers in staat te stellen meer dan het minimaal vereiste aantal verificatiemethoden te selecteren voor het geval ze daar geen toegang toe hebben.

|Verificatiemethode|Gebruik|
| --- | --- |
| Wachtwoord | MFA en SSPR |
| Beveiligingsvragen | Alleen SSPR |
| E-mailadres | Alleen SSPR |
| Microsoft Authenticator-app | MFA en SSPR |
| OATH-hardwaretoken | Openbare preview voor MFA en SSPR |
| Sms | MFA en SSPR |
| Spraakoproep | MFA en SSPR |
| App-wachtwoorden | MFA alleen in bepaalde gevallen |

![Verificatiemethoden die worden gebruikt op het aanmeldingsscherm](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH Hardware-tokens voor MFA en SSPR zijn openbare preview-functies van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen|
|     |

## <a name="password"></a>Wachtwoord

Uw Azure AD-wachtwoord wordt beschouwd als een verificatiemethode. Het is de enige methode die **niet kan worden uitgeschakeld.**

## <a name="security-questions"></a>Beveiligingsvragen

Beveiligingsvragen zijn alleen beschikbaar **in azure AD self-service wachtwoord opnieuw instellen** voor niet-beheerdersaccounts.

Als u beveiligingsvragen gebruikt, raden we u aan deze te gebruiken in combinatie met een andere methode. Beveiligingsvragen kunnen minder veilig zijn dan andere methoden, omdat sommige mensen de antwoorden op de vragen van een andere gebruiker kennen.

> [!NOTE]
> Beveiligingsvragen worden privé en veilig opgeslagen op een gebruikersobject in de directory en kunnen alleen worden beantwoord door gebruikers tijdens de registratie. Een beheerder kan de vragen of antwoorden van een gebruiker niet lezen of wijzigen.
>

### <a name="predefined-questions"></a>Vooraf gedefinieerde vragen

* In welke stad heb je je eerste echtgenoot / partner ontmoet?
* In welke stad hebben je ouders elkaar ontmoet?
* In welke stad woont je naaste broer of zus?
* In welke stad werd je vader geboren?
* In welke stad was je eerste baan?
* In welke stad werd je moeder geboren?
* In welke stad was je op nieuwjaarsdag 2000?
* Wat is de achternaam van je favoriete leraar op de middelbare school?
* Wat is de naam van een universiteit waar je op solliciteerde, maar niet aanwezig was?
* Wat is de naam van de plaats waar u uw eerste huwelijksreceptie hield?
* Wat is de tweede naam van je vader?
* Wat is je favoriete eten?
* Wat is de voor- en achternaam van je grootmoeder?
* Wat is de tweede naam van je moeder?
* Wat is de verjaardag van je oudste broer en zus maand en jaar? (bijv. november 1985)
* Wat is de tweede naam van je oudste broer of zus?
* Wat is de voor- en achternaam van je vaderlijke grootvader?
* Wat is de tweede naam van je jongste broer of zus?
* Welke school heb je naar de zesde klas?
* Wat was de voor- en achternaam van je beste vriend uit je jeugd?
* Wat was de voor- en achternaam van je eerste belangrijke andere?
* Wat was de achternaam van je favoriete leraar?
* Wat was het merk en model van uw eerste auto of motorfiets?
* Wat was de naam van de eerste school die je bezocht?
* Wat was de naam van het ziekenhuis waar je geboren bent?
* Wat was de naam van de straat van je eerste ouderlijk huis?
* Wat was de naam van je jeugdheld?
* Wat was de naam van je favoriete knuffeldier?
* Wat was de naam van je eerste huisdier?
* Wat was je bijnaam uit je jeugd?
* Wat was je favoriete sport op de middelbare school?
* Wat was je eerste baan?
* Wat waren de laatste vier cijfers van je telefoonnummer?
* Toen je jong was, wat wilde je worden toen je opgroeide?
* Wie is de beroemdste persoon die je ooit hebt ontmoet?

Alle vooraf gedefinieerde beveiligingsvragen worden vertaald en gelokaliseerd in de volledige set Office 365-talen op basis van de browserlocale van de gebruiker.

### <a name="custom-security-questions"></a>Aangepaste beveiligingsvragen

Aangepaste beveiligingsvragen zijn niet gelokaliseerd. Alle aangepaste vragen worden weergegeven in dezelfde taal als ze worden ingevoerd in de administratieve gebruikersinterface, zelfs als de browserlocale van de gebruiker anders is. Als u gelokaliseerde vragen nodig hebt, moet u de vooraf gedefinieerde vragen gebruiken.

De maximale lengte van een aangepaste beveiligingsvraag is 200 tekens.

### <a name="security-question-requirements"></a>Vereisten voor beveiligingsvragen

* De minimale limiet voor het antwoordteken is drie tekens.
* De maximale limiet voor het antwoordteken is 40 tekens.
* Gebruikers kunnen niet dezelfde vraag meer dan één keer beantwoorden.
* Gebruikers kunnen niet hetzelfde antwoord geven op meer dan één vraag.
* Elke tekenset kan worden gebruikt om de vragen en antwoorden te definiëren, inclusief Unicode-tekens.
* Het aantal gestelde vragen moet groter zijn dan of gelijk zijn aan het aantal vragen dat moest worden geregistreerd.

## <a name="email-address"></a>E-mailadres

E-mailadres is alleen beschikbaar **in Azure AD self-service password reset**.

Microsoft raadt het gebruik aan van een e-mailaccount waarvoor het Azure AD-wachtwoord van de gebruiker niet nodig is.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-app

De Microsoft Authenticator-app biedt een extra beveiligingsniveau voor uw Azure AD-werk- of schoolaccount of uw Microsoft-account.

De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) en [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Gebruikers hebben niet de mogelijkheid om hun mobiele app te registreren wanneer ze zich registreren voor het opnieuw instellen van een selfservicewachtwoord. In plaats daarvan kunnen gebruikers [https://aka.ms/mfasetup](https://aka.ms/mfasetup) hun mobiele app registreren [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)op of in de preview van de registratie van beveiligingsgegevens op .
>

### <a name="notification-through-mobile-app"></a>Melding via mobiele app

De Microsoft Authenticator-app kan onbevoegde toegang tot accounts voorkomen en frauduleuze transacties stoppen door een melding naar uw smartphone of tablet te pushen. Gebruikers bekijken de melding en als deze legitiem is, selecteert u Verifiëren. Anders kunnen ze Weigeren selecteren.

> [!WARNING]
> Voor het opnieuw instellen van selfservicewachtwoorden wanneer slechts één methode nodig is voor het opnieuw instellen, is verificatiecode de enige optie die beschikbaar is voor gebruikers **om het hoogste beveiligingsniveau te garanderen.**
>
> Wanneer twee methoden nodig zijn, kunnen gebruikers opnieuw instellen **met behulp van een** melding **of** verificatiecode, naast andere ingeschakelde methoden.
>

Als u het gebruik van beide meldingen via de mobiele app en verificatiecode van de mobiele app inschakelt, kunnen gebruikers die de Microsoft Authenticator-app registreren met behulp van een melding, zowel een melding als code gebruiken om hun identiteit te verifiëren.

> [!NOTE]
> Als uw organisatie personeel heeft dat in Of naar China reist, werkt de **methode Melding via mobiele app** op **Android-apparaten** niet in dat land. Voor deze gebruikers moeten alternatieve methoden beschikbaar worden gesteld.

### <a name="verification-code-from-mobile-app"></a>Verificatiecode van de mobiele app

De Microsoft Authenticator-app of andere apps van derden kunnen worden gebruikt als softwaretoken om een OATH-verificatiecode te genereren. Nadat u uw gebruikersnaam en wachtwoord hebt ingevoerd, voert u de code van de app in op het aanmeldingsscherm. De verificatiecode biedt een tweede vorm van verificatie.

> [!WARNING]
> Voor selfservice wachtwoord reset wanneer slechts een methode nodig is voor reset verificatie code is de enige optie beschikbaar voor gebruikers **om het hoogste niveau van beveiliging te garanderen**.
>

Gebruikers kunnen een combinatie hebben van maximaal vijf OATH-hardwaretokens of authenticator-toepassingen, zoals de Microsoft Authenticator-app die op elk gewenst moment is geconfigureerd voor gebruik.

## <a name="oath-hardware-tokens-public-preview"></a>OATH-hardwaretokens (openbare preview)

OATH is een open standaard die aangeeft hoe otp-codes (one-time password) worden gegenereerd. Azure AD ondersteunt het gebruik van OATH-TOTP SHA-1-tokens van de variëteit van 30 seconden of 60 seconden. Klanten kunnen deze tokens verkrijgen bij de leverancier van hun keuze. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutel kan alleen de tekens *a-z* of *A-Z* en cijfers *1-7*bevatten en moet worden gecodeerd in Base32.

![Het uploaden van OATH-tokens naar het MFA OATH-tokensblad](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

OATH-hardwaretokens worden ondersteund als onderdeel van een openbare preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) meer informatie over voorvertoningen

Zodra tokens zijn verkregen, moeten ze worden geüpload in een csv-bestandsindeling (comma-separated values), inclusief de UPN, serienummer, geheime sleutel, tijdsinterval, fabrikant en model, zoals in het volgende voorbeeld wordt weergegeven:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Zorg ervoor dat u de koptekstrij opneemt in uw CSV-bestand.

Zodra een CSV-bestand goed is opgemaakt, kan een beheerder zich aanmelden bij de Azure-portal, naar **Azure Active Directory** > **Security** > **MFA** > **OATH-tokens**navigeren en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren voordat het is verwerkt. Klik **op** de knop Vernieuwen om de huidige status op te halen. Als er fouten in het bestand zijn, hebt u de mogelijkheid om een CSV-bestand te downloaden met eventuele fouten die u oplossen. De veldnamen in het gedownloade CSV-bestand zijn anders dan de geüploade versie.

Zodra eventuele fouten zijn verholpen, kan de beheerder elke sleutel activeren door op **Activeren** te klikken om het token te activeren en de OTP in te voeren die op het token wordt weergegeven.

Gebruikers kunnen een combinatie hebben van maximaal vijf OATH-hardwaretokens of authenticator-toepassingen, zoals de Microsoft Authenticator-app die op elk gewenst moment is geconfigureerd voor gebruik.

## <a name="phone-options"></a>Telefoonopties

### <a name="mobile-phone"></a>Mobiele telefoon

Er zijn twee opties beschikbaar voor gebruikers met mobiele telefoons.

Als gebruikers niet willen dat hun mobiele telefoonnummer zichtbaar is in de directory, maar ze het nog steeds willen gebruiken voor het opnieuw instellen van het wachtwoord, moeten beheerders het niet in de directory vullen. Gebruikers moeten hun **kenmerk Verificatietelefoon** invullen via de [verificatieregistratieportal](https://aka.ms/ssprsetup)voor het opnieuw instellen van wachtwoorden. Beheerders kunnen deze informatie zien in het profiel van de gebruiker, maar deze wordt niet elders gepubliceerd.

Om goed te kunnen werken, moeten telefoonnummers in het formaat *+CountryCode PhoneNumber*, bijvoorbeeld +1 4255551234.

> [!NOTE]
> Er moet een ruimte zijn tussen de landcode en het telefoonnummer.
>
> Wachtwoordreset biedt geen ondersteuning voor telefoonextensies. Zelfs in het +1 4255551234X12345-formaat worden extensies verwijderd voordat de oproep wordt geplaatst.

Microsoft garandeert geen consistente sms- of spraakgebaseerde multi-factorauthenticatie promptlevering met hetzelfde nummer. In het belang van onze gebruikers kan Microsoft op elk moment korte codes toevoegen of verwijderen als we routeaanpassingen maken om de doorleverbaarheid van sms'en te verbeteren. Microsoft ondersteunt geen korte codes voor landen/regio's naast de Verenigde Staten en Canada.

#### <a name="text-message"></a>Sms-bericht

Er wordt een sms verzonden naar het mobiele telefoonnummer met een verificatiecode. Voer de verificatiecode in die in de aanmeldingsinterface wordt opgegeven om door te gaan.

#### <a name="phone-call"></a>Telefoongesprek

Er wordt een geautomatiseerd telefoongesprek gevoerd naar het telefoonnummer dat u verstrekt. Beantwoord het gesprek en druk op # in het telefoontoetsenbord om te verifiëren

> [!IMPORTANT]
> Vanaf maart 2019 zijn de telefoongespreksopties niet beschikbaar voor MFA- en SSPR-gebruikers in gratis/proefversie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. Telefoongesprek blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging heeft alleen gevolgen voor gratis/trial Azure AD-tenants.

### <a name="office-phone"></a>Zakelijke telefoon

Er wordt een geautomatiseerd telefoongesprek gevoerd naar het telefoonnummer dat u verstrekt. Beantwoord het gesprek en drukt op # in het telefoontoetsenbord om te verifiëren.

Om goed te kunnen werken, moeten telefoonnummers in het formaat *+CountryCode PhoneNumber*, bijvoorbeeld +1 4255551234.

Het kenmerk office-telefoon wordt beheerd door uw beheerder.

> [!IMPORTANT]
> Vanaf maart 2019 zijn de telefoongespreksopties niet beschikbaar voor MFA- en SSPR-gebruikers in gratis/proefversie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. Telefoongesprek blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging heeft alleen gevolgen voor gratis/trial Azure AD-tenants.

> [!NOTE]
> Er moet een ruimte zijn tussen de landcode en het telefoonnummer.
>
> Wachtwoordreset biedt geen ondersteuning voor telefoonextensies. Zelfs in het +1 4255551234X12345-formaat worden extensies verwijderd voordat de oproep wordt geplaatst.

### <a name="troubleshooting-phone-options"></a>Problemen met telefoonopties oplossen

Veelvoorkomende problemen met betrekking tot verificatiemethoden met behulp van een telefoonnummer:

* Geblokkeerde beller-ID op één apparaat
   * Probleemoplossend apparaat
* Verkeerd telefoonnummer, onjuiste landcode, telefoonnummer thuis versus telefoonnummer
   * Problemen met gebruikersobject en geconfigureerde verificatiemethoden oplossen. Zorg ervoor dat de juiste telefoonnummers worden geregistreerd.
* Verkeerde pincode ingevoerd
   * Controleer of de gebruiker de juiste pincode heeft gebruikt die is geregistreerd in Azure MFA Server.
* Oproep doorgestuurd naar voicemail
   * Zorg ervoor dat de gebruiker de telefoon heeft ingeschakeld en dat de service beschikbaar is in zijn of een alternatieve methode gebruikt.
* Gebruiker is geblokkeerd
   * Laat de beheerder de blokkering van de gebruiker in de Azure-portal doen.
* SMS is niet geabonneerd op het apparaat
   * Laat de gebruiker methoden wijzigen of sms activeren op het apparaat.
* Defecte telecomproviders (Geen telefooninvoer gedetecteerd, problemen met DTMF-tonen, geblokkeerde beller-ID op meerdere apparaten of geblokkeerde sms op meerdere apparaten)
   * Microsoft gebruikt meerdere telecomproviders om telefoongesprekken en sms-berichten te routeren voor verificatie. Als u een van de bovenstaande problemen ziet, probeert een gebruiker de methode minstens 5 keer binnen 5 minuten te gebruiken en heeft u de informatie van die gebruiker beschikbaar wanneer u contact opneemt met Microsoft-ondersteuning.

## <a name="app-passwords"></a>App-wachtwoorden

Bepaalde niet-browserapps ondersteunen geen meervoudige verificatie, als een gebruiker is ingeschakeld voor meervoudige verificatie en probeert niet-browserapps te gebruiken, kan deze niet verifiëren. Met een app-wachtwoord kunnen gebruikers zich blijven verifiëren

Als u multifactorauthenticatie afdwingt via beleid voor voorwaardelijke toegang en niet via MFA per gebruiker, u geen app-wachtwoorden maken. Toepassingen die beleid voor voorwaardelijke toegang gebruiken om de toegang te beheren, hebben geen app-wachtwoorden nodig.

Als uw organisatie is gefedereerd voor SSO met Azure AD en u Azure MFA gaat gebruiken, moet u rekening houden met de volgende details:

* Het app-wachtwoord wordt geverifieerd door Azure AD en omzeilt daarom de federatie. Federatie wordt alleen gebruikt bij het instellen van app-wachtwoorden. Voor federatieve (SSO)-gebruikers worden wachtwoorden opgeslagen in de organisatie-ID. Als de gebruiker het bedrijf verlaat, moet die informatie via DirSync naar de organisatie-id worden geleid. Het kan tot drie uur duren voordat het account wordt uitgeschakeld/verwijderd, waardoor app-wachtwoorden in Azure AD worden uitgeschakeld/verwijderd.
* On-premises instellingen voor toegangsbeheer van client worden niet herkend door het app-wachtwoord.
* Er is geen on-premises verificatielogboekregistratie/controlemogelijkheid beschikbaar voor app-wachtwoorden.
* Voor bepaalde geavanceerde architecturale ontwerpen kan het nodig zijn een combinatie van gebruikersnamen en wachtwoorden van organisaties en app-wachtwoorden te gebruiken bij het gebruik van verificatie in twee stappen met clients, afhankelijk van waar ze zich verifiëren. Voor clients die zich verifiëren tegen een on-premises infrastructuur, gebruikt u een gebruikersgebruikersnaam en wachtwoord van de organisatie. Voor clients die zich verifiëren tegen Azure AD, gebruikt u het app-wachtwoord.
* Gebruikers kunnen standaard geen app-wachtwoorden maken. Als u gebruikers wilt toestaan app-wachtwoorden te maken, selecteert u de **optie Gebruikers toestaan app-wachtwoorden te maken om zich aan te melden bij niet-browsertoepassingen** onder service-instellingen.

## <a name="next-steps"></a>Volgende stappen

[Self service-wachtwoord opnieuw instellen voor uw organisatie inschakelen](quickstart-sspr.md)

[Azure Multi-Factor Authentication inschakelen voor uw organisatie](howto-mfa-getstarted.md)

[Gecombineerde registratie inschakelen in uw tenant](howto-registration-mfa-sspr-combined.md)

[Configuratiedocumentatie van verificatiemethode voor eindgebruikers](https://aka.ms/securityinfoguide)

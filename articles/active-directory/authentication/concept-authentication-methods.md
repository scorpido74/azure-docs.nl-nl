---
title: Verificatie methoden en-functies-Azure Active Directory
description: Meer informatie over de verschillende verificatie methoden en-functies die beschikbaar zijn in Azure Active Directory om aanmeldings gebeurtenissen te verbeteren en te beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 642f2705f54fe8f84cfde7ff039c9a723be59595
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770956"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Welke verificatie-en verificatie methoden zijn er beschikbaar in Azure Active Directory?

Als onderdeel van de aanmeldings ervaring voor accounts in Azure Active Directory (Azure AD), zijn er verschillende manieren waarop een gebruiker zichzelf kan verifiëren. Een gebruikers naam en wacht woord zijn de meest voorkomende manier waarop een gebruiker historische referenties zou opgeven. Met moderne verificatie-en beveiligings functies in azure AD kan het basis wachtwoord worden aangevuld of worden vervangen door extra verificatie methoden.

Een gebruiker in azure AD kan ervoor kiezen om te verifiëren met behulp van een van de volgende verificatie methoden:

* Traditionele gebruikers naam en wacht woord
* Aanmelding met wacht woord voor app Microsoft Authenticator
* OATH-hardware-token of FIDO2-beveiligings sleutel
* Aanmelden op basis van een wacht woord zonder wachtwoord aanmelding

Veel accounts in azure AD zijn ingeschakeld voor Self-service voor wachtwoord herstel (SSPR) of Azure Multi-Factor Authentication. Deze functies omvatten aanvullende verificatie methoden, zoals een telefoon gesprek of beveiligings vragen. Het is raadzaam dat u gebruikers verplicht om meerdere verificatie methoden te registreren. Wanneer een methode niet beschikbaar is voor een gebruiker, kunnen ze ervoor kiezen om te verifiëren met een andere methode.

De volgende tabel geeft een overzicht van de methoden die beschikbaar zijn voor primaire of secundaire verificatie:

| Methode | Primaire authenticatie | Secundaire verificatie |
| --- | --- | --- |
| [Wachtwoord](#password) | Ja | |
| [Microsoft Authenticator-app](#microsoft-authenticator-app) | Ja (preview-versie) | MFA en SSPR |
| [FIDO2-beveiligings sleutels (preview-versie)](#fido2-security-keys) | Ja | Alleen MFA |
| [OATH-software tokens](#oath-software-tokens) | Nee | MFA |
| [OATH-hardware-tokens (preview-versie)](#oath-hardware-tokens-preview) | Ja | MFA |
| [Sms](#phone-options) | Ja (preview-versie) | MFA en SSPR |
| [Spraakoproep](#phone-options) | Nee | MFA en SSPR |
| [Beveiligingsvragen](#security-questions) | Nee | Alleen SSPR |
| [E-mailadres](#email-address) | Nee | Alleen SSPR |
| [App-wachtwoorden](#app-passwords) | Nee | Alleen in bepaalde gevallen MFA |

Dit artikel bevat een overzicht van de verschillende verificatie-en verificatie methoden die beschikbaar zijn in azure AD en alle specifieke beperkingen of beperkingen.

![Verificatie methoden die in gebruik zijn op het aanmeldings scherm](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Wachtwoord

Een Azure AD-wacht woord is vaak een van de primaire verificatie methoden. U kunt de verificatie methode voor wacht woorden niet uitschakelen.

Zelfs als u een verificatie methode gebruikt, zoals een [aanmelding op basis van SMS](howto-authentication-sms-signin.md) als de gebruiker niet het wacht woord gebruikt om te ondertekenen, blijft een wacht woord als een beschik bare verificatie methode.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-app

De verificator-app biedt een extra beveiligings niveau voor uw Azure AD-werk-of school account of uw Microsoft-account en is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594), [IOS](https://go.microsoft.com/fwlink/?linkid=866594)en [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Met de Microsoft Authenticator-app kunnen gebruikers tijdens de aanmelding of als extra verificatie optie worden geverifieerd tijdens de selfservice voor het opnieuw instellen van wacht woorden (SSPR) of Azure Multi-Factor Authentication-gebeurtenissen.

Gebruikers kunnen een melding ontvangen via de mobiele app om ze goed te keuren of te weigeren, of de verificator-app gebruiken voor het genereren van een OATH-verificatie code die kan worden ingevoerd in een aanmeldings interface. Als u zowel een melding als een verificatie code inschakelt, kunnen gebruikers die de verificator-app registreren, een van beide methoden gebruiken om hun identiteit te verifiëren.

Als u de verificator-app bij een aanmeldings prompt wilt gebruiken in plaats van een combi natie van gebruikers naam en wacht woord, raadpleegt u [aanmelden zonder wacht woord inschakelen met de Microsoft Authenticator-app (preview)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Gebruikers hebben geen optie om hun mobiele app te registreren wanneer ze SSPR inschakelen. In plaats daarvan kunnen gebruikers hun mobiele app registreren op [https://aka.ms/mfasetup](https://aka.ms/mfasetup) of als onderdeel van de registratie van gecombineerde beveiligings gegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Melding via mobiele app

De verificator-app kan helpen voor komen dat onbevoegde toegang tot accounts en frauduleuze trans acties wordt afgebroken door een melding naar uw smartphone of Tablet te pushen. Gebruikers zien de melding en als deze legitiem is, selecteert u **verifiëren**. Als dat niet het geval is, kunnen ze **weigeren**selecteren.

![Scherm afbeelding van voor beeld webbrowser prompt voor verificator-app-melding voor het volt ooien van het aanmeldings proces](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Als uw organisatie mede werkers heeft in of reist naar China, werkt de *melding via de mobiele app* -methode op Android-apparaten niet in dat land/deze regio. Alternatieve verificatie methoden moeten beschikbaar worden gemaakt voor deze gebruikers.

### <a name="verification-code-from-mobile-app"></a>Verificatie code uit de mobiele app

De verificator-app kan worden gebruikt als een software token voor het genereren van een OATH-verificatie code. Nadat u uw gebruikers naam en wacht woord hebt ingevoerd, voert u de code in die is opgegeven door de verificator-app in de aanmeldings interface. De verificatiecode biedt een tweede vorm van verificatie.

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de app Microsoft Authenticator, die op elk gewenst moment worden geconfigureerd voor gebruik.

> [!WARNING]
> Een verificatie code is de enige optie die beschikbaar is voor gebruikers, om ervoor te zorgen dat het hoogste beveiligings niveau voor de selfservice voor het opnieuw instellen van wacht woorden wanneer er slechts één methode vereist is voor opnieuw instellen.
>
> Wanneer twee methoden zijn vereist, kunnen gebruikers een melding of verificatie code gebruiken naast andere ingeschakelde methoden.

## <a name="fido2-security-keys"></a>FIDO2-beveiligings sleutels

De FIDO-Alliantie (Fast IDentity online) helpt u bij het promo veren van open-verificatie standaarden en de gebruiker van wacht woorden te beperken als een vorm van verificatie. FIDO2 is de nieuwste standaard die de webverificatie (webauth)-standaard bevat.

Als u FIDO2-beveiligings sleutels wilt gebruiken bij een aanmeldings prompt in plaats van een combi natie van gebruikers naam en wacht woord, raadpleegt u [wacht woordloze FIDO2 beveiligings sleutel aanmelden inschakelen (preview)](howto-authentication-passwordless-security-key.md).

Gebruikers kunnen zich registreren en vervolgens een FIDO2-beveiligings sleutel bij de aanmeldings interface selecteren als hun belangrijkste verificatie methode. Deze FIDO2-beveiligings sleutels zijn doorgaans USB-apparaten, maar kunnen ook Bluetooth of NFC gebruiken. Met een hardwareapparaat dat de verificatie afhandelt, wordt de beveiliging van een account verhoogd omdat er geen wacht woord is dat kan worden weer gegeven of geraden.

FIDO2-beveiligings sleutels in azure AD zijn momenteel beschikbaar als preview-versie. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over Previews.

## <a name="oath-tokens"></a>OATH-tokens

OATH mobiele TOTP (tijd op basis van een tijd wacht woord) is een open standaard die aangeeft hoe OTP-codes (one-time password) worden gegenereerd. OATH mobiele TOTP kan worden geïmplementeerd met behulp van software of hardware voor het genereren van de codes. Azure AD biedt geen ondersteuning voor OATH HOTP, een andere standaard code generatie.

### <a name="oath-software-tokens"></a>OATH-software tokens

Software OATH-tokens zijn doorgaans toepassingen zoals de Microsoft Authenticator-app en andere verificator-apps. Azure AD genereert de geheime sleutel, of Seed, die wordt ingevoerd in de app en die wordt gebruikt voor het genereren van elke OTP.

De verificator-app genereert automatisch codes bij het instellen van push meldingen, zodat een gebruiker een back-up heeft, zelfs als het apparaat geen verbinding heeft. Toepassingen van derden die gebruikmaken van OATH mobiele TOTP om codes te genereren, kunnen ook worden gebruikt.

Sommige software-tokens van OATH mobiele TOTP zijn programmeerbaar, wat betekent dat ze niet worden geleverd met een geheime sleutel of een vooraf geprogrammeerd Zaai zaad. Deze Programmeer bare hardware-tokens kunnen worden ingesteld met behulp van de geheime sleutel of Seed die is verkregen van de installatie stroom van het software token. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze en de geheime sleutel of het Seed gebruiken in het installatie proces van de leverancier.

### <a name="oath-hardware-tokens-preview"></a>OATH-hardware-tokens (preview-versie)

Azure AD ondersteunt het gebruik van OATH-mobiele TOTP SHA-1-tokens waarmee codes elke 30 of 60 seconden worden vernieuwd. Klanten kunnen deze tokens aanschaffen bij de leverancier van hun keuze.

OATH mobiele TOTP-hardware-tokens worden doorgaans geleverd met een geheime sleutel, of Seed, vooraf geprogrammeerd in het token. Deze sleutels moeten worden ingevoerd in azure AD, zoals beschreven in de volgende stappen. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutel mag alleen de tekens *a-z* of *a-* z en cijfers *1-7*bevatten en moet worden gecodeerd in *Base32*.

Programmeer bare OATH-mobiele TOTP die kunnen worden geseedd, kunnen ook worden ingesteld met Azure AD in de instellings stroom van het software token.

OATH-hardware-tokens worden ondersteund als onderdeel van een open bare preview. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie over Previews

![OATH-tokens uploaden naar de Blade MFA OATH-tokens](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Wanneer de tokens zijn verkregen, moeten ze worden geüpload in een CSV-bestand (Comma-Separated Values), met inbegrip van de UPN, het serie nummer, de geheime sleutel, het tijds interval, de fabrikant en het model, zoals wordt weer gegeven in het volgende voor beeld:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Zorg ervoor dat u de rij met koppen in het CSV-bestand opneemt.

Als een beheerder zich heeft geformatteerd als een CSV-bestand, kunt u zich aanmelden bij de Azure Portal, naar **Azure Active Directory > Security > MFA > OATH-tokens**gaan en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren voordat het proces is uitgevoerd. Selecteer de knop **vernieuwen** om de huidige status op te halen. Als het bestand fouten bevat, kunt u een CSV-bestand downloaden met een lijst met fouten die u moet oplossen. De veld namen in het gedownloade CSV-bestand wijken af van de geüploade versie.

Zodra er fouten zijn opgelost, kan de beheerder elke sleutel activeren door **Activate** voor het token te selecteren en de otp op te geven die op het token wordt weer gegeven.

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de app Microsoft Authenticator, die op elk gewenst moment worden geconfigureerd voor gebruik.

## <a name="phone-options"></a>Telefoon opties

Voor directe verificatie met een tekst bericht kunt u [gebruikers configureren en inschakelen voor verificatie op basis van SMS (preview-versie)](howto-authentication-sms-signin.md). Aanmelding op basis van SMS is uitstekend voor front-line werk nemers. Met aanmelden op basis van SMS hoeven gebruikers geen gebruikers naam en wacht woord te kennen om toegang te krijgen tot toepassingen en services. De gebruiker voert het geregistreerde mobiele telefoon nummer in, ontvangt een SMS-bericht met een verificatie code en voert die in de aanmeldings interface in.

Gebruikers kunnen zich ook zelf verifiëren via een mobiele telefoon of een zakelijke telefoon als een secundaire vorm van verificatie die wordt gebruikt tijdens Azure Multi-Factor Authentication of selfservice voor wachtwoord herstel (SSPR).

Om goed te kunnen werken, moeten telefoon nummers de notatie *+ CountryCode phonenumber*hebben, bijvoorbeeld *+ 1 4251234567*.

> [!NOTE]
> Er moet een spatie zijn tussen de land-/regiocode en het telefoon nummer.
>
> Het opnieuw instellen van wacht woorden biedt geen ondersteuning voor telefoon uitbreidingen. Zelfs in de indeling van de *+ 1-4251234567X12345* worden uitbrei dingen verwijderd voordat de oproep wordt geplaatst.

### <a name="mobile-phone-verification"></a>Verificatie van mobiele telefoon

Voor Azure Multi-Factor Authentication of SSPR kunnen gebruikers ervoor kiezen om een SMS-bericht te ontvangen met een verificatie code om in te voeren in de aanmeldings interface, of om een telefoon gesprek te ontvangen met een prompt om hun gedefinieerde pincode in te voeren.

Als gebruikers niet willen dat hun mobiele telefoon nummer wordt weer gegeven in de map, maar u dit wilt gebruiken voor het opnieuw instellen van wacht woorden, moeten beheerders het telefoon nummer in de map niet vullen. In plaats daarvan moeten gebruikers hun kenmerk voor de verificatie van de **telefoon** invullen via de registratie van gecombineerde beveiligings gegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Beheerders kunnen deze informatie zien in het profiel van de gebruiker, maar ze worden niet elders gepubliceerd.

![Scherm afbeelding van de Azure Portal waarin de verificatie methoden met een ingevuld telefoon nummer worden weer gegeven](media/concept-authentication-methods/user-authentication-methods.png)

Micro soft biedt geen garantie voor consistente op SMS of spraak gebaseerde Azure Multi-Factor Authentication prompts met hetzelfde nummer. In het belang van onze gebruikers kunnen we op elk gewenst moment korte codes toevoegen of verwijderen, omdat we route aanpassingen aanbrengen om de bezorgings mogelijkheden van SMS te verbeteren. Micro soft biedt geen ondersteuning voor korte codes voor landen/regio's, naast de Verenigde Staten en Canada.

#### <a name="text-message-verification"></a>Verificatie van SMS-berichten

Met de verificatie van SMS-berichten tijdens SSPR of Azure Multi-Factor Authentication, wordt er een sm's verzonden naar het mobiele telefoon nummer met een verificatie code. Om het aanmeldings proces te volt ooien, wordt de opgegeven verificatie code ingevoerd in de aanmeldings interface.

#### <a name="phone-call-verification"></a>Verificatie via telefoon oproep

Bij een verificatie via telefoon gesprekken tijdens SSPR of Azure Multi-Factor Authentication wordt een automatische telefoon oproep uitgevoerd naar het telefoon nummer dat door de gebruiker is geregistreerd. Om het aanmeldings proces te volt ooien, wordt de gebruiker gevraagd hun pincode in te voeren, gevolgd door # op hun toetsen blok.

### <a name="office-phone-verification"></a>Verificatie op bedrijfs telefoon

Het kenmerk Office Phone wordt beheerd door de Azure AD-beheerder en kan niet worden geregistreerd door een gebruiker zelf.

Bij een verificatie via telefoon gesprekken tijdens SSPR of Azure Multi-Factor Authentication wordt een automatische telefoon oproep uitgevoerd naar het telefoon nummer dat door de gebruiker is geregistreerd. Om het aanmeldings proces te volt ooien, wordt de gebruiker gevraagd hun pincode in te voeren, gevolgd door # op hun toetsen blok.

### <a name="troubleshooting-phone-options"></a>Problemen met telefoonopties oplossen

Als u problemen ondervindt met telefoon verificatie voor Azure AD, raadpleegt u de volgende stappen voor probleem oplossing:

* De geblokkeerde beller-ID op één apparaat.
   * Bekijk alle geblokkeerde nummers die op het apparaat zijn geconfigureerd.
* Onjuist telefoon nummer of onjuiste land-/regionummer of Verwar ring tussen privé telefoon nummer en telefoon nummer van werk.
   * Problemen oplossen met het gebruikers object en de geconfigureerde verificatie methoden. Controleer of de juiste telefoon nummers zijn geregistreerd.
* Verkeerde pincode ingevoerd.
   * Bevestig dat de gebruiker de juiste pincode heeft gebruikt als geregistreerd voor hun account.
* Oproep doorgestuurd naar Voice mail.
   * Zorg ervoor dat de telefoon is ingeschakeld voor de gebruiker en dat de service beschikbaar is in hun gebied of gebruik een alternatieve methode.
* Gebruiker is geblokkeerd
   * Laat een Azure AD-beheerder de gebruiker blok keren in de Azure Portal.
* SMS is niet geabonneerd op het apparaat.
   * Laat de gebruiker methoden wijzigen of SMS activeren op het apparaat.
* Defecte telecommunicatie providers, zoals geen telefoon invoer gedetecteerd, ontbrekende DTMF Toon problemen, een geblokkeerde beller-ID op meerdere apparaten of geblokkeerde SMS-computers op meerdere apparaten.
   * Micro soft gebruikt meerdere telecom providers om telefoon gesprekken en SMS-berichten voor verificatie te routeren. Als u een van de bovenstaande problemen ziet, moet een gebruiker de methode ten minste vijf keer binnen vijf minuten gebruiken en de gegevens van die gebruiker beschikbaar hebben als u contact opneemt met micro soft ondersteuning.

## <a name="security-questions"></a>Beveiligingsvragen

Beveiligings vragen worden niet als een verificatie methode gebruikt tijdens een aanmeldings gebeurtenis. In plaats daarvan kunnen beveiligings vragen worden gebruikt tijdens het SSPR-proces (self-service voor wachtwoord herstel) om te bevestigen wie u bent. Beheerders accounts kunnen geen beveiligings vragen als verificatie methode gebruiken met SSPR.

Wanneer gebruikers zich registreren voor SSPR, wordt u gevraagd de verificatie methoden te kiezen die u wilt gebruiken. Als ze ervoor kiezen om beveiligings vragen te gebruiken, kiezen ze uit een set vragen om te vragen en ze vervolgens hun eigen antwoorden te geven.

![Scherm afbeelding van de Azure Portal waarin de verificatie methoden en-opties voor beveiligings vragen worden weer gegeven](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Beveiligings vragen worden privé opgeslagen en beveiligd op een gebruikers object in de Directory en kunnen alleen worden beantwoord door gebruikers tijdens de registratie. Het is niet mogelijk dat een beheerder de vragen of antwoorden van een gebruiker kan lezen of wijzigen.

Beveiligings vragen kunnen minder veilig zijn dan andere methoden omdat sommige mensen de antwoorden op de vragen van een andere gebruiker kunnen kennen. Als u beveiligings vragen met SSPR gebruikt, is het raadzaam deze te gebruiken in combi natie met een andere methode. Een gebruiker kan worden gevraagd om de Microsoft Authenticator app of telefoon verificatie te gebruiken om hun identiteit te verifiëren tijdens het SSPR-proces en om alleen beveiligings vragen te kiezen als ze hun telefoon of geregistreerd apparaat niet hebben.

### <a name="predefined-questions"></a>Vooraf gedefinieerde vragen

De volgende vooraf gedefinieerde beveiligings vragen zijn beschikbaar voor gebruik als een verificatie methode met SSPR. Al deze beveiligings vragen worden vertaald en gelokaliseerd in de volledige set Office 365-talen op basis van de browser land instelling van de gebruiker:

* In welke stad hebt u uw eerste partner ontmoet?
* In welke stad hebben uw ouders elkaar ontmoet?
* In welke stad woont uw dichtstbijzijnde broer of zus?
* In welke stad is uw vader geboren?
* In welke stad is uw eerste taak?
* In welke stad is uw moeder geboren?
* In welke plaats was u op 2000 van het nieuwjaarsdag?
* Wat is de achternaam van uw favoriete docent op de hoogste school?
* Wat is de naam van een college dat u hebt toegepast, maar niet?
* Wat is de naam van de plaats waar u uw eerste bruilofts receptie hebt gehouden?
* Wat is de tweede naam van uw vader?
* Wat is uw favoriete levens middelen?
* Wat zijn de voor-en achternaam van uw en achternaam?
* Wat is de tweede naam van uw moeder?
* Wat is de maand en het jaar van uw oudste broer of zus? (bijvoorbeeld november 1985)
* Wat is de tweede naam van uw oudste broer of zus?
* Wat zijn de voor-en achternaam van uw Paternal groot vader?
* Wat is de tweede naam van uw jongste broer of zus?
* Op welke school hebt u de zesde kwaliteit bijgewoond?
* Wat zijn de voor-en achternaam van de beste vriend van uw jeugd?
* Wat zijn de voor-en achternaam van uw eerste belang rijke andere?
* Wat is de achternaam van uw favoriete school docent?
* Wat was het merk en model van uw eerste auto of motor?
* Wat is de naam van de eerste school die u hebt bijgewoond?
* Wat is de naam van het zieken huis waarin u bent geboren?
* Wat is de naam van de straat van uw eerste huis land?
* Wat is de naam van uw jeugd held?
* Wat is de naam van uw favoriete dierlijke dier?
* Wat is de naam van uw eerste huis dier?
* Wat was de bijnaam van uw kinderen?
* Wat was uw favoriete sport op de hoge school?
* Wat is uw eerste taak?
* Wat zijn de laatste vier cijfers van het telefoon nummer van uw kinderen?
* Wanneer u jonger was, wat wilt u doen wanneer u ingroeit?
* Wie is de meest beroemde persoon die u ooit hebt ontmoet?

### <a name="custom-security-questions"></a>Aangepaste beveiligings vragen

Voor extra flexibiliteit kunt u uw eigen aangepaste beveiligings vragen definiëren. De maximale lengte van een aangepaste beveiligings vraag is 200 tekens.

Aangepaste beveiligings vragen worden niet automatisch gelokaliseerd zoals bij de standaard beveiligings vragen. Alle aangepaste vragen worden weer gegeven in dezelfde taal als die in de gebruikers interface voor beheer, zelfs als de browser land instelling van de gebruiker verschillend is. Als u gelokaliseerde vragen nodig hebt, moet u de vooraf gedefinieerde vragen gebruiken.

### <a name="security-question-requirements"></a>Vereisten voor beveiligings vragen

Voor zowel standaard als aangepaste beveiligings vragen gelden de volgende vereisten en beperkingen:

* De minimale antwoord teken limiet is drie tekens.
* De maximale antwoord teken limiet is 40 tekens.
* Gebruikers kunnen niet meer dan één keer dezelfde vraag beantwoorden.
* Gebruikers kunnen niet hetzelfde antwoord op meer dan één vraag opgeven.
* Elke tekenset kan worden gebruikt voor het definiëren van de vragen en de antwoorden, met inbegrip van Unicode-tekens.
* Het aantal gedefinieerde vragen moet groter zijn dan of gelijk zijn aan het aantal vragen dat moet worden geregistreerd.

## <a name="email-address"></a>E-mailadres

Een e-mail adres kan niet worden gebruikt als directe verificatie methode. E-mail adres is alleen beschikbaar als verificatie optie voor Self-service voor wachtwoord herstel (SSPR). Wanneer e-mail adres is geselecteerd tijdens SSPR, wordt er een e-mail bericht verzonden naar de gebruiker om het verificatie-en verificatie proces te volt ooien.

Tijdens de registratie voor SSPR geeft een gebruiker het e-mail adres op dat moet worden gebruikt. Het is raadzaam om een ander e-mail account dan hun bedrijfs account te gebruiken om er zeker van te zijn dat ze toegang hebben tijdens SSPR.

## <a name="app-passwords"></a>App-wachtwoorden

Bepaalde oudere, niet-browser-apps begrijpen onderbrekingen of onderbrekingen in het verificatie proces niet. Als een gebruiker is ingeschakeld voor multi-factor Authentication en probeert een van deze oudere, niet-browser-apps te gebruiken, kunnen ze meestal niet goed worden geverifieerd. Met een app-wacht woord kunnen gebruikers zonder onderbreking blijven verifiëren met oudere, niet-browser-apps.

Standaard kunnen gebruikers geen app-wacht woorden maken. Als u wilt toestaan dat gebruikers app-wacht woorden maken, selecteert u de optie **gebruikers toestaan app-wacht woorden te maken om zich aan te melden bij niet-browser-apps** onder *Service-instellingen* voor Azure multi-factor Authentication-eigenschappen van de gebruiker.

![Scherm afbeelding van de Azure Portal waarin de service-instellingen voor multi-factor Authentication worden weer gegeven om de gebruiker van app-wacht woorden toe te staan](media/concept-authentication-methods/app-password-authentication-method.png)

Als u Azure Multi-Factor Authentication afdwingt met behulp van beleid voor voorwaardelijke toegang en niet via MFA per gebruiker, kunt u geen app-wacht woorden maken. Moderne toepassingen die gebruikmaken van beleid voor voorwaardelijke toegang om toegang te beheren, hoeven geen app-wacht woorden te hebben.

Als uw organisatie federatief is voor eenmalige aanmelding (SSO) met Azure AD en u Azure Multi-Factor Authentication gebruikt, zijn de volgende overwegingen van toepassing:

* Het app-wacht woord wordt geverifieerd door Azure AD, dus heeft geen Federatie over. Federatie wordt alleen gebruikt bij het instellen van app-wacht woorden. Voor federatieve gebruikers (SSO) worden wacht woorden opgeslagen in de organisatie-ID. Als de gebruiker het bedrijf verlaat, moet dat informatie naar de organisatie-ID stromen met behulp van DirSync. Het kan tot drie uur duren voordat het account is uitgeschakeld of verwijderd, waardoor het uitschakelen/verwijderen van app-wacht woorden in azure AD wordt vertraagd.
* De on-premises Client Access Control instellingen worden niet gehonoreerd door app-wacht woorden.
* Er is geen on-premises verificatie logboek registratie of controle mogelijkheid beschikbaar voor app-wacht woorden.
* Bepaalde geavanceerde architecturale ontwerpen kunnen vereisen dat er een combi natie van gebruikers naam en wacht woord en app-wacht woorden wordt gebruikt wanneer u multi-factor Authentication gebruikt, afhankelijk van de locatie van de verificatie.
    * Voor clients die worden geverifieerd aan de hand van een on-premises infra structuur, gebruikt u een gebruikers naam en wacht woord voor de organisatie.
    * Voor clients die met Azure AD worden geverifieerd, gebruikt u het app-wacht woord.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, raadpleegt u de [zelf studie voor selfservice voor wachtwoord herstel (SSPR)][tutorial-sspr] en [Azure multi-factor Authentication][tutorial-azure-mfa].

Zie [hoe Azure AD self-service password reset werkt][concept-sspr]voor meer informatie over SSPR-concepten.

Zie [hoe Azure multi-factor Authentication werkt][concept-mfa]voor meer informatie over MFA-concepten.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
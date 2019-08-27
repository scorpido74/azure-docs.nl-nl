---
title: Verificatie methoden-Azure Active Directory
description: Verificatie methoden die beschikbaar zijn in azure AD voor MFA en SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b0c91357e5ab15b88c92b04fd0896b989e83953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051442"
---
# <a name="what-are-authentication-methods"></a>Wat zijn verificatiemethoden?

Als beheerder kiest u verificatie methoden voor Azure Multi-Factor Authentication en self-service voor wachtwoord herstel (SSPR). het wordt aanbevolen dat u gebruikers verplicht om meerdere verificatie methoden te registreren. Wanneer een verificatie methode niet beschikbaar is voor een gebruiker, kunnen ze ervoor kiezen om te verifiëren met een andere methode.

Beheerders kunnen in beleid definiëren welke authenticatie methoden beschikbaar zijn voor gebruikers van SSPR en MFA. Sommige verificatie methoden zijn mogelijk niet beschikbaar voor alle functies. Zie voor meer informatie over het configureren van uw beleid de artikelen [over het implementeren van selfservice voor wachtwoord herstel](howto-sspr-deployment.md) en het [plannen van een Azure multi-factor Authentication](howto-mfa-getstarted.md) voor de Cloud

Micro soft raadt beheerders ten zeerste aan dat gebruikers meer dan het minimale vereiste aantal authenticatie methoden kunnen selecteren voor het geval ze geen toegang tot één hebben.

|Verificatiemethode|Gebruik|
| --- | --- |
| Wachtwoord | MFA en SSPR |
| Beveiligingsvragen | Alleen SSPR |
| E-mailadres | Alleen SSPR |
| Microsoft Authenticator-app | MFA en SSPR |
| OATH-hardware-token | Open bare Preview voor MFA en SSPR |
| SMS | MFA en SSPR |
| Spraakoproep | MFA en SSPR |
| App-wachtwoorden | Alleen in bepaalde gevallen MFA |

![Verificatie methoden die in gebruik zijn op het aanmeldings scherm](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH-hardware-tokens voor MFA en SSPR zijn open bare preview-functies van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) -previews voor meer informatie over Previews|
|     |

## <a name="password"></a>Wachtwoord

Uw Azure AD-wacht woord wordt beschouwd als een verificatie methode. Het is de enige methode die **niet kan worden uitgeschakeld**.

## <a name="security-questions"></a>Beveiligingsvragen

Beveiligings vragen zijn **alleen beschikbaar in azure AD self-service voor wachtwoord herstel** naar niet-beheerders accounts.

Als u beveiligings vragen gebruikt, raden wij u aan deze te gebruiken in combi natie met een andere methode. Beveiligings vragen kunnen minder veilig zijn dan andere methoden omdat sommige mensen de antwoorden op de vragen van een andere gebruiker kunnen kennen.

> [!NOTE]
> Beveiligings vragen worden privé opgeslagen en beveiligd op een gebruikers object in de Directory en kunnen alleen worden beantwoord door gebruikers tijdens de registratie. Het is niet mogelijk dat een beheerder de vragen of antwoorden van een gebruiker kan lezen of wijzigen.
>

### <a name="predefined-questions"></a>Vooraf gedefinieerde vragen

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

Alle vooraf gedefinieerde beveiligings vragen worden vertaald en gelokaliseerd in de volledige set Office 365-talen op basis van de browser land instelling van de gebruiker.

### <a name="custom-security-questions"></a>Aangepaste beveiligingsvragen

Aangepaste beveiligings vragen zijn niet gelokaliseerd. Alle aangepaste vragen worden weer gegeven in dezelfde taal als die in de gebruikers interface voor beheer, zelfs als de browser land instelling van de gebruiker verschillend is. Als u gelokaliseerde vragen nodig hebt, moet u de vooraf gedefinieerde vragen gebruiken.

De maximale lengte van een aangepaste beveiligings vraag is 200 tekens.

### <a name="security-question-requirements"></a>Vereisten voor beveiligings vragen

* De minimale antwoord teken limiet is drie tekens.
* De maximale antwoord teken limiet is 40 tekens.
* Gebruikers kunnen niet meer dan één keer dezelfde vraag beantwoorden.
* Gebruikers kunnen niet hetzelfde antwoord op meer dan één vraag opgeven.
* Elke tekenset kan worden gebruikt voor het definiëren van de vragen en de antwoorden, met inbegrip van Unicode-tekens.
* Het aantal gedefinieerde vragen moet groter zijn dan of gelijk zijn aan het aantal vragen dat moet worden geregistreerd.

## <a name="email-address"></a>E-mailadres

E-mail adres is **alleen beschikbaar in de self-service voor wachtwoord herstel van Azure AD**.

Micro soft adviseert het gebruik van een e-mail account waarvoor het Azure AD-wacht woord van de gebruiker niet voor toegang is vereist.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-app

De Microsoft Authenticator-app biedt een extra beveiligings niveau voor uw Azure AD-werk-of school account of uw Microsoft-account.

De Microsoft Authenticator-app is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) en [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Gebruikers kunnen hun mobiele app niet registreren wanneer ze zich registreren voor selfservice voor wachtwoord herstel. In plaats daarvan kunnen gebruikers hun mobiele app registreren [https://aka.ms/mfasetup](https://aka.ms/mfasetup) op of in de preview-versie van [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)de registratie van beveiligings gegevens op.
>

### <a name="notification-through-mobile-app"></a>Melding via mobiele app

De Microsoft Authenticator-app kan helpen voor komen dat onbevoegde toegang tot accounts en frauduleuze trans acties wordt gestopt door een melding naar uw smartphone of Tablet te pushen. Gebruikers zien de melding en als deze legitiem is, selecteert u verifiëren. Als dat niet het geval is, kunnen ze weigeren selecteren.

> [!WARNING]
> Voor een self-service voor het opnieuw instellen van wacht woorden wanneer er slechts één methode vereist is voor opnieuw instellen, is verificatie code de enige optie die voor gebruikers beschikbaar is voor **het hoogste beveiligings niveau**.
>
> Wanneer twee methoden vereist zijn, kunnen gebruikers naast andere ingeschakelde methoden opnieuw worden ingesteld met behulp van **een** melding **of** verificatie code.
>

Als u het gebruik van beide meldingen via de mobiele app en de verificatie code van de mobiele app inschakelt, kunnen gebruikers die de Microsoft Authenticator-app via een melding registreren, beide meldingen en code gebruiken om hun identiteit te verifiëren.

> [!NOTE]
> Als uw organisatie mede werkers heeft in of reist naar China, werkt de **melding via de mobiele app** -methode op **Android-apparaten** niet in dat land. Alternatieve methoden moeten beschikbaar worden gemaakt voor deze gebruikers.

### <a name="verification-code-from-mobile-app"></a>Verificatiecode via mobiele app

De Microsoft Authenticator-app of andere apps van derden kunnen worden gebruikt als een software token voor het genereren van een OATH-verificatie code. Nadat u uw gebruikers naam en wacht woord hebt ingevoerd, voert u de code die door de app is opgegeven, in het aanmeldings scherm in. De verificatie code biedt een tweede vorm van verificatie.

> [!WARNING]
> Voor selfservice voor het opnieuw instellen van wacht woorden wanneer er slechts één methode vereist is voor het opnieuw instellen van de verificatie code is de enige optie beschikbaar voor gebruikers **om het hoogste beveiligings niveau te garanderen**.
>

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de Microsoft Authenticator-app die op elk gewenst moment is geconfigureerd voor gebruik.

## <a name="oath-hardware-tokens-public-preview"></a>OATH-hardware-tokens (open bare preview)

OATH is een open standaard die aangeeft hoe OTP-codes (one-time password) worden gegenereerd. Azure AD biedt ondersteuning voor het gebruik van OATH-mobiele TOTP SHA-1-tokens van de 30-seconden of 60-Second-variëteit. Klanten kunnen deze tokens van de leverancier van hun keuze aanschaffen. Geheime sleutels zijn beperkt tot 128 tekens, die mogelijk niet compatibel zijn met alle tokens. De geheime sleutels moeten worden gecodeerd in Base32.

![OATH-tokens uploaden naar de Blade van de MFA-server OATH-tokens](media/concept-authentication-methods/oath-tokens-azure-ad.png)

OATH-hardware-tokens worden ondersteund als onderdeel van een open bare preview. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) -previews voor meer informatie over Previews

Wanneer de tokens zijn verkregen, moeten ze worden geüpload in een CSV-bestand (Comma-Separated Values), met inbegrip van de UPN, het serie nummer, de geheime sleutel, het tijds interval, de fabrikant en het model, zoals in het voor beeld hieronder wordt weer gegeven.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Zorg ervoor dat u de rij met koppen in het CSV-bestand opneemt, zoals hierboven wordt weer gegeven.

Als een beheerder zich heeft geformatteerd als een CSV-bestand, kan deze zich vervolgens aanmelden bij de Azure Portal en naar **Azure Active Directory**, **MFA-server**, **OATH**-TOKENs gaan en het resulterende CSV-bestand uploaden.

Afhankelijk van de grootte van het CSV-bestand kan het enkele minuten duren voordat het proces is uitgevoerd. Klik op de knop **vernieuwen** om de huidige status op te halen. Als het bestand fouten bevat, kunt u een CSV-bestand downloaden met een lijst met fouten die u kunt oplossen.

Zodra er fouten zijn opgelost, kan de beheerder elke sleutel activeren door te klikken op **activeren** voor het token dat wordt geactiveerd en de otp op het token in te voeren.

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de Microsoft Authenticator-app die op elk gewenst moment is geconfigureerd voor gebruik.

## <a name="phone-options"></a>Telefoonopties

### <a name="mobile-phone"></a>Telefoon (mobiel)

Er zijn twee opties beschikbaar voor gebruikers met mobiele telefoons.

Als gebruikers niet willen dat hun mobiele telefoon nummer wordt weer gegeven in de adres lijst, maar ze nog steeds willen gebruiken voor het opnieuw instellen van wacht woorden, moeten beheerders deze niet in de adres lijst invullen. Gebruikers moeten hun kenmerk voor authenticatie via de [registratie portal voor het opnieuw instellen van het wacht woord](https://aka.ms/ssprsetup)invullen. Beheerders kunnen deze informatie zien in het profiel van de gebruiker, maar ze worden niet elders gepubliceerd.

Om goed te kunnen werken, moeten telefoon nummers de notatie *+ CountryCode phonenumber*hebben, bijvoorbeeld + 1 4255551234.

> [!NOTE]
> Er moet een spatie zijn tussen de land code en het telefoon nummer.
>
> Het opnieuw instellen van een wacht woord biedt geen ondersteuning voor telefoon uitbreidingen. Zelfs in de indeling van de + 1-4255551234X12345 worden uitbrei dingen verwijderd voordat de oproep wordt geplaatst.

Micro soft biedt geen garantie voor consistentie op basis van SMS-of spraak Multi-Factor Authentication op hetzelfde nummer. In het belang van onze gebruikers mag micro soft op elk gewenst moment korte codes toevoegen of verwijderen, aangezien we route aanpassingen aanbrengen om de levering van SMS te verbeteren. Micro soft biedt geen ondersteuning voor korte codes voor landen/regio's, naast de Verenigde Staten en Canada.

#### <a name="text-message"></a>Sms-bericht

Er wordt een SMS-bericht verzonden naar het mobiele telefoon nummer met een verificatie code. Voer de verificatie code in die is opgegeven in de aanmeldings interface om door te gaan.

#### <a name="phone-call"></a>Telefoonoproep

Er wordt een automatische telefoon oproep gedaan naar het telefoon nummer dat u opgeeft. Vraag de oproep af en druk op # in het telefoon blok om te verifiëren

> [!IMPORTANT]
> Vanaf maart 2019 zijn de opties voor telefoon gesprekken niet beschikbaar voor MFA-en SSPR-gebruikers in gratis/proef versie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. De telefoon oproep blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging is alleen van invloed op de Azure AD-tenants gratis en proef versie.

### <a name="office-phone"></a>Telefoon (werk)

Er wordt een automatische telefoon oproep gedaan naar het telefoon nummer dat u opgeeft. Beantwoord het gesprek en druk op # in het telefoon blok om te verifiëren.

Om goed te kunnen werken, moeten telefoon nummers de notatie *+ CountryCode phonenumber*hebben, bijvoorbeeld + 1 4255551234.

Het kenmerk Office Phone wordt beheerd door de beheerder.

> [!IMPORTANT]
> Vanaf maart 2019 zijn de opties voor telefoon gesprekken niet beschikbaar voor MFA-en SSPR-gebruikers in gratis/proef versie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. De telefoon oproep blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging is alleen van invloed op de Azure AD-tenants gratis en proef versie.

> [!NOTE]
> Er moet een spatie zijn tussen de land code en het telefoon nummer.
>
> Het opnieuw instellen van een wacht woord biedt geen ondersteuning voor telefoon uitbreidingen. Zelfs in de indeling van de + 1-4255551234X12345 worden uitbrei dingen verwijderd voordat de oproep wordt geplaatst.

### <a name="troubleshooting-phone-options"></a>Problemen met telefoon opties oplossen

Algemene problemen met betrekking tot verificatie methoden met behulp van een telefoon nummer:

* ID van de geblokkeerde beller op één apparaat
   * Problemen met apparaat oplossen
* Onjuist telefoon nummer, onjuiste land code, telefoon nummer thuis versus telefoon nummer werk
   * Problemen met het gebruikers object en de geconfigureerde verificatie methoden oplossen. Controleer of de juiste telefoon nummers zijn geregistreerd.
* Verkeerde pincode ingevoerd
   * Bevestig dat de gebruiker de juiste pincode heeft gebruikt die in de Azure MFA-server is geregistreerd.
* Oproep doorgestuurd naar Voice mail
   * Zorg ervoor dat de gebruiker telefoon is ingeschakeld en dat de service beschikbaar is op hun gebied of gebruik een alternatieve methode.
* Gebruiker is geblokkeerd
   * Laat de beheerder de gebruiker blok keren in de Azure Portal.
* SMS is niet geabonneerd op het apparaat
   * Laat de gebruiker methoden wijzigen of SMS activeren op het apparaat.
* Defecte telecommunicatie providers (geen telefoon invoer gevonden, ontbrekende DTMF-Toon problemen, geblokkeerde beller-ID op meerdere apparaten of geblokkeerde SMS-computers op meerdere apparaten)
   * Micro soft gebruikt meerdere telecom providers om telefoon gesprekken en SMS-berichten voor verificatie te routeren. Als een van de bovenstaande problemen wordt weer gegeven, kan een gebruiker de methode ten minste 5 keer binnen vijf minuten gebruiken en de gegevens van die gebruiker beschikbaar hebben als u contact opneemt met micro soft ondersteuning.

## <a name="app-passwords"></a>Appwachtwoorden

Bepaalde niet-browser-apps ondersteunen multi-factor Authentication niet, als een gebruiker is ingeschakeld voor multi-factor Authentication en probeert om niet-browser-apps te gebruiken, kunnen ze niet worden geverifieerd. Met een app-wacht woord kunnen gebruikers blijven verifiëren

Als u Multi-Factor Authentication afdwingt via beleids regels voor voorwaardelijke toegang en niet via MFA per gebruiker, kunt u geen app-wacht woorden maken. Toepassingen die gebruikmaken van beleids regels voor voorwaardelijke toegang om toegang te beheren, hoeven geen app-wacht woorden te hebben.

Als uw organisatie federatief is voor eenmalige aanmelding met Azure AD en u Azure MFA wilt gebruiken, moet u rekening houden met de volgende details:

* Het app-wacht woord wordt geverifieerd door Azure AD en daarom wordt Federatie omzeild. Federatie wordt alleen gebruikt bij het instellen van app-wacht woorden. Voor federatieve gebruikers (SSO) worden wacht woorden opgeslagen in de organisatie-ID. Als de gebruiker het bedrijf verlaat, moet dat informatie naar de organisatie-ID stromen met behulp van DirSync. Het kan tot drie uur duren voordat het account is uitgeschakeld of verwijderd, waardoor het uitschakelen/verwijderen van app-wacht woorden in azure AD wordt vertraagd.
* On-premises instellingen voor toegangsbeheer van client worden niet herkend door het app-wachtwoord.
* Er is geen on-premises verificatie-logboek registratie/controle mogelijkheid beschikbaar voor app-wacht woorden.
* Voor bepaalde geavanceerde architecturale ontwerpen moet u een combi natie van gebruikers naam en wacht woord en app-wacht woorden gebruiken wanneer verificatie in twee stappen wordt gebruikt met clients, afhankelijk van waar ze worden geverifieerd. Voor clients die worden geverifieerd aan de hand van een on-premises infra structuur, gebruikt u een gebruikers naam en wacht woord voor de organisatie. Voor clients die met Azure AD worden geverifieerd, gebruikt u het app-wacht woord.
* Standaard kunnen gebruikers geen app-wacht woorden maken. Als u wilt toestaan dat gebruikers app-wacht woorden maken, selecteert u de **optie gebruikers toestaan app-wacht woorden te maken om zich aan te melden bij niet-browser toepassingen** onder service-instellingen.

## <a name="next-steps"></a>Volgende stappen

[Selfservice voor het opnieuw instellen van wacht woorden inschakelen voor uw organisatie](quickstart-sspr.md)

[Azure Multi-Factor Authentication inschakelen voor uw organisatie](howto-mfa-getstarted.md)

[Gecombineerde registratie inschakelen in uw Tenant](howto-registration-mfa-sspr-combined.md)

[Documentatie voor de verificatie methode van de eind gebruiker](https://aka.ms/securityinfoguide)

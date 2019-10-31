---
title: Waarom bijwerken naar micro soft Identity platform (v 2.0) | Azure
description: Ken de verschillen tussen het micro soft Identity platform (v 2.0)-eind punt en het Azure Active Directory (Azure AD) v 1.0-eind punt, en ontdek de voor delen van het bijwerken naar v 2.0.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 975c7f18da9797305b0af3f81b00acca1ba14a1a
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200316"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Waarom bijwerken naar micro soft Identity platform (v 2.0)?

Wanneer u een nieuwe toepassing ontwikkelt, is het belang rijk dat u weet wat de verschillen zijn tussen de micro soft Identity platform (v 2.0) en de Azure Active Directory (v 1.0)-eind punten. In dit artikel worden de belangrijkste verschillen beschreven tussen de eind punten en enkele bestaande beperkingen van micro soft Identity platform.

> [!NOTE]
> Het micro soft Identity platform-eind punt biedt geen ondersteuning voor alle Azure AD-scenario's en-functies. Lees over [micro soft Identity platform-beperkingen](#limitations)om te bepalen of u het micro soft Identity platform-eind punt moet gebruiken.

## <a name="who-can-sign-in"></a>Wie kan zich aanmelden

![Wie kan zich aanmelden met eind punten van de v 1.0 en v 2.0](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* Met het eind punt v 1.0 kunnen alleen werk-en school accounts worden aangemeld bij uw toepassing (Azure AD)
* Met het micro soft Identity platform-eind punt kunnen werk-en school accounts van Azure AD en persoonlijke micro soft-accounts (MSA), zoals hotmail.com, outlook.com en msn.com, worden aangemeld.
* Beide eind punten accepteren ook aanmeldingen van *[gast gebruikers](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* van een Azure AD-Directory voor toepassingen die zijn geconfigureerd als *[single tenant](single-and-multi-tenant-apps.md)* of voor *multi tenant* -toepassingen die zijn geconfigureerd om naar het tenant-specifieke eind punt (`https://login.microsoftonline.com/{TenantId_or_Name}`) te verwijzen.

Met het micro soft Identity platform-eind punt kunt u apps schrijven die aanmeldingen accepteren van persoonlijke micro soft-accounts en werk-en school accounts. Dit biedt u de mogelijkheid om uw app volledig account-neutraal te schrijven. Als uw app bijvoorbeeld de [Microsoft Graph](https://graph.microsoft.io)aanroept, zijn er extra functies en gegevens beschikbaar voor werk accounts, zoals hun share point-sites of Directory gegevens. Maar voor veel acties, zoals het [lezen van de e-mail van een gebruiker](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), heeft dezelfde code toegang tot het e-mail bericht voor zowel privé-als werk-en school accounts.

Voor het micro soft Identity platform-eind punt kunt u de micro soft Authentication Library (MSAL) gebruiken om toegang te krijgen tot de consument, het onderwijs en de zakelijke wereld. Het Azure AD v 1.0-eind punt accepteert alleen aanmeldingen van werk-en school accounts.

## <a name="incremental-and-dynamic-consent"></a>Incrementele en dynamische toestemming

Apps die gebruikmaken van het Azure AD v 1.0-eind punt zijn vereist om de vereiste OAuth 2,0-machtigingen vooraf op te geven, bijvoorbeeld:

![Voor beeld van de gebruikers interface voor machtigingen registratie](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

De machtigingen die rechtstreeks op de registratie van de toepassing zijn ingesteld, zijn **statisch**. Hoewel statische machtigingen van de app die zijn gedefinieerd in de Azure Portal de code mooi en eenvoudig laten staan, worden er enkele mogelijke problemen voor ontwikkel aars weer gegeven:

* De app moet alle machtigingen aanvragen die deze ooit nodig had bij de eerste aanmelding van de gebruiker. Dit kan leiden tot een lange lijst met machtigingen waarmee eind gebruikers de toegang tot de app kunnen goed keuren bij de eerste aanmelding.

* In de app moeten alle resources worden genoteerd die voorheen eerder toegang zouden hebben. Het is lastig om apps te maken die een wille keurig aantal resources kunnen benaderen.

Met het micro soft Identity platform-eind punt kunt u de statische machtigingen die zijn gedefinieerd in de registratie gegevens van de app in de Azure Portal negeren en in plaats daarvan de machtigingen stapsgewijs aanvragen. Dit betekent dat u wordt gevraagd om een bare minimale set machtigingen vooraf en meer tijd in beslag nemen naarmate de klant extra app-functies gebruikt. Hiervoor kunt u de bereiken die uw app nodig heeft op elk gewenst moment opgeven door de nieuwe scopes in de para meter `scope` op te nemen bij het aanvragen van een toegangs token-zonder dat u deze hoeft vooraf in te stellen in de registratie gegevens van de toepassing. Als de gebruiker nog niet is gemachtigd om nieuwe scopes toe te voegen aan de aanvraag, wordt u gevraagd om alleen toestemming te geven aan de nieuwe machtigingen. Zie [machtigingen, toestemming en bereiken](v2-permissions-and-consent.md)voor meer informatie.

Als u een app toestaat om machtigingen dynamisch aan te vragen via de para meter `scope`, geeft ontwikkel aars de volledige controle over uw gebruikers ervaring. U kunt ook de voor keur geven aan de bestemmings ervaring en vragen om alle machtigingen in één initiële autorisatie aanvraag. Als uw app een groot aantal machtigingen vereist, kunt u de machtigingen van de gebruiker stapsgewijs verzamelen, omdat ze gedurende een periode proberen bepaalde functies van de app te gebruiken.

Toestemming van de beheerder die namens een organisatie wordt uitgevoerd, vereist nog steeds de statische machtigingen die voor de app zijn geregistreerd. Daarom moet u deze machtigingen voor apps in de app-registratie Portal instellen als u een beheerder nodig hebt om toestemming te geven namens de hele organisatie. Dit reduceert de cycli die de organisatie beheerder nodig heeft om de toepassing in te stellen.

## <a name="scopes-not-resources"></a>Bereiken, geen resources

Voor apps die gebruikmaken van het v 1.0-eind punt kan een app zich gedragen als een **resource**of een ontvanger van tokens. Een resource kan een aantal **bereiken** of **oAuth2Permissions** definiëren die het begrijpt, zodat client-apps tokens van die bron kunnen aanvragen voor een bepaalde reeks bereiken. Houd rekening met de Azure AD-Graph API als voor beeld van een resource:

* Resource-id of `AppID URI`: `https://graph.windows.net/`
* Bereiken of `oAuth2Permissions`: `Directory.Read`, `Directory.Write`, enzovoort.

Dit geldt voor het micro soft Identity platform-eind punt. Een app kan zich nog steeds gedragen als een resource, scopes definiëren en worden geïdentificeerd met een URI. Client-apps kunnen nog steeds toegang tot deze bereiken aanvragen. De manier waarop een client die machtigingen aanvraagt, is echter gewijzigd.

Voor het eind punt van de v 1.0 kan een OAuth 2,0-aanvraag voor het machtigen van Azure AD er als volgt uitzien:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Hier geeft de **resource** parameter op welke resource de client-app autorisatie aanvraagt. Azure AD heeft de vereiste machtigingen voor de app berekend op basis van de statische configuratie in het Azure Portal en de uitgegeven tokens dienovereenkomstig.

Voor toepassingen die gebruikmaken van het micro soft Identity platform-eind punt, ziet dezelfde OAuth 2,0-machtigings aanvraag er als volgt uit:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Hier geeft de **bereik** parameter aan welke resource en machtigingen de app autorisatie aanvraagt. De gewenste resource is nog steeds aanwezig in de aanvraag. deze is opgenomen in elk van de waarden van de bereik parameter. Als u de bereik parameter op deze manier gebruikt, is het micro soft Identity platform-eind punt beter compatibel met de OAuth 2,0-specificatie en wordt nauw keuriger afgestemd op de algemene branche praktijken. Daarnaast kunnen apps [incrementele toestemming](#incremental-and-dynamic-consent) verlenen-alleen machtigingen aanvragen wanneer de toepassing deze vereist in plaats van de voor grond.

## <a name="well-known-scopes"></a>Bekende bereiken

### <a name="offline-access"></a>Offline toegang

Apps die gebruikmaken van het micro soft Identity platform-eind punt, kunnen het gebruik van een nieuwe goed bekende machtiging voor apps vereisen-het `offline_access` bereik. Alle apps moeten deze machtiging aanvragen als ze voor een langdurige periode toegang moeten krijgen tot bronnen namens een gebruiker, zelfs wanneer de gebruiker de app niet actief mag gebruiken. Het `offline_access` bereik wordt weer gegeven aan de gebruiker in dialoog vensters met toestemming als **toegang tot uw gegevens op elk gewenst moment**, waar de gebruiker mee moet instemmen. Als u de machtiging `offline_access` hebt aangevraagd, kan uw web-app OAuth 2,0 refresh_tokens ontvangen van het micro soft Identity platform-eind punt. Vernieuwings tokens worden lang bewaard en kunnen worden uitgewisseld voor nieuwe OAuth 2,0-toegangs tokens voor langere Peri Oden van toegang.

Als uw app het `offline_access` bereik niet opvraagt, worden er geen vernieuwings tokens ontvangen. Dit betekent dat wanneer u een autorisatie code inwisselt in de OAuth 2,0-autorisatie code stroom, u alleen een toegangs token van het `/token`-eind punt terugkrijgt. Dat toegangs token blijft geldig gedurende een korte periode (meestal één uur), maar zal uiteindelijk verlopen. Op dat moment moet uw app de gebruiker terugsturen naar het eind punt van `/authorize` om een nieuwe autorisatie code op te halen. Tijdens deze omleiding is het mogelijk dat de gebruiker de referenties niet opnieuw moet invoeren of dat er toestemming moet worden gegeven voor machtigingen, afhankelijk van het type app.

Voor meer informatie over OAuth 2,0, `refresh_tokens`en `access_tokens`raadpleegt u de naslag informatie voor het [micro soft Identity platform-protocol](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID Connect, profile en e-mail

De meest eenvoudige OpenID Connect voor het verbinden van verbindingen met het micro soft Identity-platform bieden een grote hoeveelheid informatie over de gebruiker in de resulterende *id_token*. De claims in een id_token kunnen bestaan uit de naam van de gebruiker, de voorkeurs gebruikersnaam, het e-mail adres, de object-ID en meer.

Met de informatie die door het `openid` bereik wordt geboden, is de toegang tot uw app nu beperkt. Met het `openid` bereik kan uw app de gebruiker niet aanmelden en een app-specifieke id voor de gebruiker ontvangen. Als u persoonlijke gegevens over de gebruiker in uw app wilt ophalen, moet uw app aanvullende machtigingen voor de gebruiker aanvragen. Met twee nieuwe bereiken, `email` en `profile`, kunt u aanvullende machtigingen aanvragen.

* Met het `email` bereik kan uw app toegang krijgen tot het primaire e-mail adres van de gebruiker via de `email` claim in het id_token, ervan uitgaande dat de gebruiker een adresseerbaar e-mail adres heeft.
* Het `profile` bereik biedt uw app toegang tot alle andere basis informatie over de gebruiker, zoals de naam, de voorkeurs gebruikersnaam, de object-ID, enzovoort, in de id_token.

Deze bereiken bieden u de mogelijkheid om uw app in een minimale verschaffings modus in te stellen, zodat u alleen de gebruiker kunt vragen voor de set informatie die uw app nodig heeft om de taak uit te voeren. Zie voor meer informatie over deze bereiken [de referentie voor het micro soft Identity platform-bereik](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Token claims

Met het micro soft Identity platform-eind punt wordt standaard een kleinere set claims in de tokens uitgegeven om payloads klein te blijven. Als u beschikt over apps en services die afhankelijk zijn van een bepaalde claim in een v 1.0-token dat niet meer standaard wordt geleverd in een micro soft Identity platform-token, kunt u overwegen de [optionele claim](active-directory-optional-claims.md) functie te gebruiken om die claim op te nemen.

> [!IMPORTANT]
> v 1.0 en v 2.0-tokens kunnen worden uitgegeven door de eind punten v 1.0 en v 2.0. id_tokens komen *altijd* overeen met het eind punt waarvoor ze zijn aangevraagd, en toegangs tokens komen *altijd* overeen met de indeling die wordt verwacht door de Web-API die door de client wordt gebruikt.  Dus als uw app het v 2.0-eind punt gebruikt om een token op te halen voor het aanroepen van Microsoft Graph, waarbij de toegangs tokens voor de indeling van v 1.0 worden verwacht, ontvangt uw app een token in de indeling v 1.0.  

## <a name="limitations"></a>Beperkingen

Er zijn enkele beperkingen waarmee u rekening moet houden bij het gebruik van micro soft Identity platform.

Wanneer u toepassingen bouwt die worden geïntegreerd met het micro soft-identiteits platform, moet u beslissen of het micro soft Identity platform-eind punt en de verificatie protocollen voldoen aan uw behoeften. Het eind punt van de v 1.0 en het platform worden nog steeds volledig ondersteund en, in sommige opzichten, is meer functionaliteit uitgebreid dan het micro soft Identity-platform. Micro soft Identity platform [introduceert echter aanzienlijke voor delen](azure-ad-endpoint-comparison.md) voor ontwikkel aars.

Dit is nu een vereenvoudigde aanbeveling voor ontwikkel aars:

* Als u persoonlijke micro soft-accounts in uw toepassing wilt of moeten ondersteunen, of als u een nieuwe toepassing schrijft, gebruikt u micro soft Identity platform. Maar voordat u dit doet, moet u de beperkingen begrijpen die in dit artikel worden besproken.
* Als u een toepassing die afhankelijk is van SAML, migreert of bijwerkt, kunt u micro soft Identity platform niet gebruiken. In plaats daarvan raadpleegt u de [hand leiding voor Azure AD v 1.0](v1-overview.md).

Het micro soft Identity platform-eind punt gaat verder met het elimineren van de beperkingen die hier worden vermeld, zodat u het micro soft Identity platform-eind punt nooit hoeft te gebruiken. In de tussen tijd gebruikt u dit artikel om te bepalen of het micro soft Identity platform-eind punt geschikt is voor u. We blijven dit artikel bijwerken zodat dit overeenkomt met de huidige status van het micro soft Identity platform-eind punt. Ga terug om uw vereisten te evalueren op basis van de mogelijkheden van micro soft Identity platform.

### <a name="restrictions-on-app-registrations"></a>Beperkingen voor app-registraties

Voor elke app die u wilt integreren met het micro soft Identity platform-eind punt, kunt u een app-registratie maken in de nieuwe [ **app-registraties** ervaring](https://aka.ms/appregistrations) in de Azure Portal. Bestaande Microsoft-account-apps zijn niet compatibel met de portal, maar alle Azure AD-apps zijn, ongeacht waar of wanneer ze zijn geregistreerd.

App-registraties die ondersteuning bieden voor werk-en school accounts en persoonlijke accounts, hebben de volgende voor behoud:

* Er zijn slechts twee app-geheimen per toepassings-ID toegestaan.
* Een toepassing die niet is geregistreerd in een Tenant, kan alleen worden beheerd door het account waarmee het is geregistreerd. Het kan niet worden gedeeld met andere ontwikkel aars. Dit is het geval voor de meeste apps die zijn geregistreerd met behulp van een persoonlijke Microsoft-account in de app registratie-Portal. Als u de registratie van uw app met meerdere ontwikkel aars wilt delen, registreert u de toepassing in een Tenant met behulp van de nieuwe **app-registraties** sectie van de Azure Portal.
* Er zijn verschillende beperkingen voor de indeling van de omleidings-URL die is toegestaan. Zie de volgende sectie voor meer informatie over omleidings-URL.

### <a name="restrictions-on-redirect-urls"></a>Beperkingen voor omleidings-Url's

Apps die zijn geregistreerd voor het micro soft-identiteits platform, zijn beperkt tot een beperkt aantal omleidings-URL-waarden. De omleidings-URL voor web-apps en-services moet beginnen met het schema `https`, en alle omleidings-URL-waarden moeten één DNS-domein delen.  Het registratie systeem vergelijkt de volledige DNS-naam van de bestaande omleidings-URL naar de DNS-naam van de omleidings-URL die u wilt toevoegen. `http://localhost` wordt ook ondersteund als een omleidings-URL.  

De aanvraag voor het toevoegen van de DNS-naam mislukt als aan een van de volgende voorwaarden wordt voldaan:  

* De volledige DNS-naam van de nieuwe omleidings-URL komt niet overeen met de DNS-naam van de bestaande omleidings-URL.
* De volledige DNS-naam van de nieuwe omleidings-URL is geen subdomein van de bestaande omleidings-URL.

#### <a name="example-1"></a>Voor beeld 1

Als de app een omleidings-URL van `https://login.contoso.com`heeft, kunt u een omleidings-URL toevoegen waarbij de DNS-naam precies overeenkomt, zoals wordt weer gegeven in het volgende voor beeld:

`https://login.contoso.com/new`

U kunt ook verwijzen naar een DNS-subdomein van login.contoso.com, zoals wordt weer gegeven in het volgende voor beeld:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Voor beeld 2

Als u een app wilt hebben die `login-east.contoso.com` en `login-west.contoso.com` als omleidings-Url's, moet u deze omleidings-Url's in de volgende volg orde toevoegen:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

U kunt de laatste twee toevoegen omdat deze subdomeinen zijn van de eerste omleidings-URL, contoso.com.

U kunt slechts 20 antwoord-Url's voor een bepaalde toepassing hebben: deze limiet geldt voor alle app-typen die door de registratie worden ondersteund (een toepassing met één pagina, SPA), systeem eigen client, Web-app en service.  

Zie [een app registreren met de nieuwe app-registraties-ervaring](quickstart-register-app.md)voor meer informatie over het registreren van een app voor gebruik met micro soft Identity platform.

### <a name="restrictions-on-libraries-and-sdks"></a>Beperkingen voor bibliotheken en Sdk's

Momenteel is de ondersteuning van tape wisselaars voor het micro soft Identity platform-eind punt beperkt. Als u het micro soft Identity platform-eind punt in een productie toepassing wilt gebruiken, hebt u de volgende opties:

* Als u een webtoepassing bouwt, kunt u veilig de algemeen beschik bare middleware aan de server zijde gebruiken om u aan te melden en de validatie van tokens te valideren. Dit zijn onder andere de OWIN OpenID Connect Connect middleware voor ASP.NET en de node. js Pass Port-invoeg toepassing. Voor code voorbeelden die gebruikmaken van micro soft middleware, zie de sectie [micro soft Identity platform Getting Started](v2-overview.md#getting-started) .
* Als u een desktop-of mobiele toepassing bouwt, kunt u een van de micro soft-verificatie bibliotheken (MSAL) gebruiken. Deze bibliotheken zijn algemeen beschikbaar of worden ondersteund in een preview-versie, zodat u ze veilig kunt gebruiken in productie toepassingen. U vindt meer informatie over de voor waarden van de preview-versie en de beschik bare bibliotheken in [verificatie bibliotheken](reference-v2-libraries.md).
* Voor platforms die niet onder micro soft-bibliotheken vallen, kunt u integreren met het micro soft Identity platform-eind punt door rechtstreeks protocol berichten te verzenden en ontvangen in de code van uw toepassing. De OpenID Connect Connect-en OAuth-protocollen [zijn expliciet gedocumenteerd](active-directory-v2-protocols.md) om u een dergelijke integratie te bieden.
* Ten slotte kunt u open-source OpenID Connect Connect en OAuth-bibliotheken gebruiken om te integreren met het micro soft Identity platform-eind punt. Het micro soft Identity platform-eind punt moet compatibel zijn met veel open-source protocol bibliotheken zonder wijzigingen. De beschik baarheid van dit soort bibliotheken is afhankelijk van de taal en het platform. De [OpenID Connect Connect](https://openid.net/connect/) -en [OAuth 2,0](https://oauth.net/2/) -websites onderhouden een lijst met populaire implementaties. Zie voor meer informatie [micro soft Identity-platform en verificatie bibliotheken](reference-v2-libraries.md)en de lijst met open source-client bibliotheken en-voor beelden die zijn getest met het micro soft Identity platform-eind punt.
* Ter referentie is het `.well-known`-eind punt voor het micro soft Identity platform common-eind punt `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Vervang `common` door uw Tenant-ID om gegevens op te halen die specifiek zijn voor uw Tenant.  

### <a name="protocol-changes"></a>Protocol wijzigingen

Het micro soft Identity platform-eind punt biedt geen ondersteuning voor SAML of WS-Federation. OpenID Connect Connect en OAuth 2,0 worden alleen ondersteund.  De belangrijkste wijzigingen in de OAuth 2,0-protocollen van het eind punt van de v 1.0 zijn: 

* De `email` claim wordt geretourneerd als een optionele claim is geconfigureerd **of** Scope = e-mail is opgegeven in de aanvraag. 
* De para meter `scope` wordt nu ondersteund in plaats van de para meter `resource`.  
* Veel reacties zijn gewijzigd om ze beter compatibel te maken met de OAuth 2,0-specificatie, bijvoorbeeld correct retour neren `expires_in` als een geheel getal in plaats van een teken reeks.  

Zie [OpenID Connect Connect and OAuth 2,0 protocol Reference](active-directory-v2-protocols.md)(Engelstalig) voor meer informatie over het bereik van de protocol functionaliteit die wordt ondersteund in het micro soft Identity platform-eind punt.

#### <a name="saml-restrictions"></a>SAML-beperkingen

Als u Active Directory Authentication Library (ADAL) in Windows-toepassingen hebt gebruikt, hebt u mogelijk geprofiteerd van geïntegreerde Windows-authenticatie, die gebruikmaakt van de toekenning van de Security Assertion Markup Language (SAML). Met deze toekenning kunnen gebruikers van federatieve Azure AD-tenants zich op de achtergrond verifiëren met hun lokale Active Directory exemplaar zonder referenties in te voeren. De toekenning van SAML-verklaringen wordt niet ondersteund op het micro soft Identity platform-eind punt.

---
title: Waarom bijwerken naar Microsoft-identiteitsplatform (v2.0) | Azure
description: Ken de verschillen tussen het Microsoft-identiteitsplatform (v2.0) eindpunt en het Azure Ad-eindpunt (Azure Directory( Azure Directory) v1.0 en leer de voordelen van bijwerken naar v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 67a54a2cd4fa071fd47bcebb9aa53fd11fefd61e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154913"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Waarom bijwerken naar Microsoft Identity Platform (v2.0)?

Bij het ontwikkelen van een nieuwe toepassing is het belangrijk om de verschillen te kennen tussen de eindpunten van het Microsoft-identiteitsplatform (v2.0) en Azure Active Directory (v1.0). Dit artikel behandelt de belangrijkste verschillen tussen de eindpunten en enkele bestaande beperkingen voor microsoft-identiteitsplatform.

> [!NOTE]
> Het eindpunt van het Microsoft-identiteitsplatform biedt geen ondersteuning voor alle Azure AD-scenario's en -functies. Als u wilt bepalen of u het eindpunt van het Microsoft-identiteitsplatform moet gebruiken, leest u over [de beperkingen van het Microsoft-identiteitsplatform.](#limitations)

## <a name="who-can-sign-in"></a>Wie kan zich aanmelden

![Wie kan inloggen met v1.0 en v2.0 eindpunten](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* Met het v1.0-eindpunt kunnen alleen werk- en schoolaccounts worden aangemeld bij uw toepassing (Azure AD)
* Met het eindpunt van het Microsoft-identiteitsplatform kunnen werk- en schoolaccounts van Azure AD en persoonlijke Microsoft-accounts (MSA), zoals hotmail.com, outlook.com en msn.com, zich aanmelden.
* Beide eindpunten accepteren ook aanmeldingen van *[gastgebruikers](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* van een Azure *[AD-map](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* voor toepassingen die zijn geconfigureerd als één tenant`https://login.microsoftonline.com/{TenantId_or_Name}`of voor *multi-tenanttoepassingen* die zijn geconfigureerd om het tenantspecifieke eindpunt aan te wijzen ( ).

Met het eindpunt van het Microsoft-identiteitsplatform u apps schrijven die aanmeldingen van persoonlijke Microsoft-accounts en werk- en schoolaccounts accepteren. Dit geeft u de mogelijkheid om uw app volledig account-agnostisch te schrijven. Als uw app bijvoorbeeld de [Microsoft Graph](https://graph.microsoft.io)aanroept, zijn er extra functionaliteit en gegevens beschikbaar voor werkaccounts, zoals hun SharePoint-sites of directorygegevens. Maar voor veel acties, zoals [het lezen van de e-mail van een gebruiker,](https://docs.microsoft.com/graph/api/user-list-messages?view=graph-rest-1.0)heeft dezelfde code toegang tot de e-mail voor zowel persoonlijke als werk- en schoolaccounts.

Voor het eindpunt van het Microsoft-identiteitsplatform u de Microsoft Authentication Library (MSAL) gebruiken om toegang te krijgen tot de werelden van consumenten, onderwijs en ondernemingen. Het Azure AD v1.0-eindpunt accepteert alleen aanmeldingen van werk- en schoolaccounts.

## <a name="incremental-and-dynamic-consent"></a>Incrementele en dynamische toestemming

Apps die het Azure AD v1.0-eindpunt gebruiken, moeten vooraf hun vereiste OAuth 2.0-machtigingen opgeven, bijvoorbeeld:

![Voorbeeld van de gebruikersinterface voor de registratie machtigingen](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

De machtigingen die rechtstreeks op de registratie van de toepassing zijn **ingesteld,** zijn statisch . Hoewel statische machtigingen van de app die zijn gedefinieerd in de Azure-portal de code mooi en eenvoudig houden, biedt dit een aantal mogelijke problemen voor ontwikkelaars:

* De app moet alle machtigingen aanvragen die het ooit nodig zou hebben bij de eerste aanmelding van de gebruiker. Dit kan leiden tot een lange lijst met machtigingen die eindgebruikers ontmoedigt om de toegang van de app goed te keuren bij de eerste aanmelding.

* De app moet alle bronnen kennen die het ooit van tevoren zou openen. Het was moeilijk om apps te maken die toegang hadden tot een willekeurig aantal bronnen.

Met het eindpunt van het Microsoft-identiteitsplatform u de statische machtigingen negeren die zijn gedefinieerd in de app-registratiegegevens in de Azure-portal en in plaats daarvan stapsgewijs machtigingen aanvragen, wat betekent dat u vooraf om een minimumset machtigingen moet vragen en groeit meer in de tijd als de klant extra app-functies gebruikt. Om dit te doen, u op elk gewenst moment de scopes opgeven die uw app nodig heeft door de nieuwe scopes in de `scope` parameter op te nemen bij het aanvragen van een toegangstoken - zonder dat u ze vooraf hoeft te definiëren in de registratiegegevens van de toepassing. Als de gebruiker nog niet heeft ingestemd met nieuwe scopes die aan het verzoek zijn toegevoegd, wordt deze persoon gevraagd alleen in te stemmen met de nieuwe machtigingen. Zie [machtigingen, toestemming en scopes](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie.

Als een app machtigingen dynamisch kan `scope` aanvragen via de parameter, hebben ontwikkelaars volledige controle over de ervaring van uw gebruiker. U ook uw toestemmingservaring vooraf laden en alle machtigingen aanvragen in één eerste autorisatieaanvraag. Als uw app een groot aantal machtigingen vereist, u deze machtigingen stapsgewijs van de gebruiker verzamelen terwijl deze bepaalde functies van de app in de loop van de tijd proberen te gebruiken.

Beheerderstoestemming die namens een organisatie wordt verleend, vereist nog steeds de statische machtigingen die voor de app zijn geregistreerd, dus u moet deze machtigingen instellen voor apps in de app-registratieportal als u een beheerder nodig hebt om toestemming te geven namens de hele organisatie. Dit vermindert de cycli die de organisatiebeheerder nodig heeft om de toepassing in te stellen.

## <a name="scopes-not-resources"></a>Scopes, geen resources

Voor apps die het v1.0-eindpunt gebruiken, kan een app zich gedragen als een **bron**of als ontvanger van tokens. Een resource kan een aantal **scopes** of **oAuth2Permissions** definiëren die het begrijpt, waardoor client-apps tokens van die bron kunnen aanvragen voor een bepaalde set scopes. Beschouw de Microsoft Graph API als een voorbeeld van een bron:

* Resource-id, `AppID URI`of :`https://graph.microsoft.com/`
* Scopes, `oAuth2Permissions`of `Directory.Read` `Directory.Write`: , , enzovoort.

Dit geldt voor het eindpunt van het Microsoft-identiteitsplatform. Een app kan zich nog steeds gedragen als een resource, scopes definiëren en worden geïdentificeerd door een URI. Client-apps kunnen nog steeds toegang tot deze scopes aanvragen. De manier waarop een client deze machtigingen aanvraagt, is echter gewijzigd.

Voor het v1.0-eindpunt lijkt een oauth 2.0-geautoriseerde aanvraag voor Azure AD mogelijk op:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.microsoft.com/
...
```

Hier gaf de **resourceparameter** aan welke resource de client-app autorisatie aanvraagt. Azure AD berekende de machtigingen die de app vereist op basis van statische configuratie in de Azure-portal en gaf dienovereenkomstig tokens uit.

Voor toepassingen die het eindpunt van het Microsoft-identiteitsplatform gebruiken, ziet dezelfde oauth 2.0-geautoriseerde aanvraag eruit als volgt:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.microsoft.com/directory.read%20https://graph.microsoft.com/directory.write
...
```

Hier geeft de **bereikparameter** aan welke resource en machtigingen de app autorisatie aanvraagt. De gewenste resource is nog steeds aanwezig in de aanvraag - deze is verwerkt in elk van de waarden van de scopeparameter. Door de scopeparameter op deze manier te gebruiken, kan het eindpunt van het Microsoft-identiteitsplatform beter voldoen aan de OAuth 2.0-specificatie en wordt deze beter afgestemd op de gangbare industriepraktijken. Het stelt apps ook in staat om [incrementele toestemming](#incremental-and-dynamic-consent) te geven - alleen het aanvragen van machtigingen wanneer de toepassing ze vereist in tegenstelling tot vooraf.

## <a name="well-known-scopes"></a>Bekende scopes

### <a name="offline-access"></a>Offline toegang

Apps die het eindpunt van het Microsoft-identiteitsplatform gebruiken, vereisen mogelijk `offline_access` het gebruik van een nieuwe bekende toestemming voor apps - het bereik. Alle apps moeten deze toestemming aanvragen als ze gedurende een langere periode toegang moeten krijgen tot bronnen namens een gebruiker, zelfs wanneer de gebruiker de app mogelijk niet actief gebruikt. Het `offline_access` bereik wordt op elk gewenst **moment**weergegeven aan de gebruiker in toestemmingsdialogen als Toegang tot uw gegevens , waarmee de gebruiker moet instemmen. Als u `offline_access` de toestemming aanvraagt, kan uw web-app OAuth 2.0-refresh_tokens ontvangen van het eindpunt van het Microsoft-identiteitsplatform. Refresh tokens zijn van lange duur en kunnen worden ingewisseld voor nieuwe OAuth 2.0-toegangstokens voor langere tijd toegang.

Als uw app geen `offline_access` scope aanvraagt, ontvangt deze geen vernieuwingstokens. Dit betekent dat wanneer u een autorisatiecode inwisselt in de oauth 2.0-autorisatiecodestroom, u alleen een toegangstoken van het `/token` eindpunt terugontvangt. Dat toegangstoken blijft geldig voor een korte periode (meestal een uur), maar vervalt uiteindelijk. Op dat moment moet uw app de gebruiker terugleiden naar het `/authorize` eindpunt om een nieuwe autorisatiecode op te halen. Tijdens deze omleiding hoeft de gebruiker zijn of niet opnieuw in te voeren of toestemming te geven voor machtigingen, afhankelijk van het type app.

Voor meer informatie over OAuth `refresh_tokens`2.0 en `access_tokens`bekijk de referentie van het [Microsoft-identiteitsplatformprotocol](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="openid-profile-and-email"></a>OpenID, profiel en e-mail

Historisch gezien zou de meest elementaire OpenID Connect-aanmeldingsstroom met het Microsoft-identiteitsplatform veel informatie over de gebruiker bieden in de resulterende *id_token.* De claims in een id_token kunnen de naam van de gebruiker, de gebruikersnaam, het e-mailadres, de object-id en meer bevatten.

De informatie `openid` waartoe het bereik uw app toegang biedt, is nu beperkt. Met `openid` het bereik kan uw app alleen de gebruiker aanmelden en een app-specifieke id voor de gebruiker ontvangen. Als u persoonlijke gegevens over de gebruiker in uw app wilt ophalen, moet uw app aanvullende machtigingen van de gebruiker aanvragen. Met twee nieuwe `email` `profile`scopes u aanvullende machtigingen aanvragen.

* Het `email` bereik geeft uw app toegang tot het `email` primaire e-mailadres van de gebruiker via de claim in de id_token, ervan uitgaande dat de gebruiker een adresseerbaar e-mailadres heeft.
* Het `profile` bereik biedt uw app toegang tot alle andere basisinformatie over de gebruiker, zoals hun naam, voorkeursgebruikersnaam, object-ID, enzovoort, in de id_token.

Met deze scopes u uw app op een minimale manier coderen, zodat u de gebruiker alleen vragen om de set informatie die uw app nodig heeft om zijn werk te doen. Zie [de scope-referentie van het Microsoft-identiteitsplatform voor](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)meer informatie over deze scopes.

## <a name="token-claims"></a>Tokenclaims

Het eindpunt van het Microsoft-identiteitsplatform geeft standaard een kleinere set claims in zijn tokens uit om payloads klein te houden. Als u apps en services hebt die afhankelijk zijn van een bepaalde claim in een v1.0-token dat niet langer standaard wordt geleverd in een Microsoft-identiteitsplatformtoken, u overwegen de [optionele claimfunctie](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) te gebruiken om die claim op te nemen.

> [!IMPORTANT]
> v1.0 en v2.0 tokens kunnen worden uitgegeven door zowel de v1.0 als v2.0 endpoints! id_tokens *altijd* overeenkomen met het eindpunt waarvan ze worden aangevraagd en toegangstokens *komen altijd* overeen met de indeling die wordt verwacht door de Web-API die uw client aanroept met dat token.  Dus als uw app het v2.0-eindpunt gebruikt om een token te krijgen om Microsoft Graph te bellen, die toegangbiedt aan v1.0-indelingstokens, ontvangt uw app een token in de v1.0-indeling.  

## <a name="limitations"></a>Beperkingen

Er zijn een paar beperkingen om rekening mee te houden bij het gebruik van microsoft-identiteitsplatform.

Wanneer u toepassingen bouwt die integreren met het Microsoft-identiteitsplatform, moet u beslissen of het eindpunt en de verificatieprotocollen van het Microsoft-identiteitsplatform aan uw behoeften voldoen. Het v1.0-eindpunt en -platform wordt nog steeds volledig ondersteund en is in sommige opzichten rijker dan het Microsoft-identiteitsplatform. Microsoft-identiteitsplatform [biedt](azure-ad-endpoint-comparison.md) echter aanzienlijke voordelen voor ontwikkelaars.

Hier is een vereenvoudigde aanbeveling voor ontwikkelaars nu:

* Als u persoonlijke Microsoft-accounts in uw toepassing wilt of wilt ondersteunen of als u een nieuwe toepassing schrijft, gebruikt u het Microsoft-identiteitsplatform. Maar voordat je dat doet, zorg ervoor dat u de beperkingen besproken in dit artikel te begrijpen.
* Als u een toepassing migreert of bijwerkt die afhankelijk is van SAML, u het Identiteitsplatform van Microsoft niet gebruiken. Raadpleeg in plaats daarvan de [Azure AD v1.0-handleiding](v1-overview.md).

Het eindpunt van het Microsoft-identiteitsplatform evolueert om de hier genoemde beperkingen te elimineren, zodat u alleen het eindpunt van het Microsoft-identiteitsplatform hoeft te gebruiken. Gebruik in de tussentijd dit artikel om te bepalen of het eindpunt van het Microsoft-identiteitsplatform geschikt is voor u. We blijven dit artikel bijwerken om de huidige status van het eindpunt van het Microsoft-identiteitsplatform weer te geven. Controleer terug om uw vereisten opnieuw te evalueren aan de hand van de mogelijkheden van het Microsoft-identiteitsplatform.

### <a name="restrictions-on-app-registrations"></a>Beperkingen op app-registraties

Voor elke app die u wilt integreren met het eindpunt van het Microsoft-identiteitsplatform, u een app-registratie maken in de nieuwe [ **ervaring met app-registraties** ](https://aka.ms/appregistrations) in de Azure-portal. Bestaande Microsoft-accountapps zijn niet compatibel met de portal, maar alle Azure AD-apps wel, ongeacht waar of wanneer ze zijn geregistreerd.

App-registraties die werk- en schoolaccounts en persoonlijke accounts ondersteunen, hebben de volgende kanttekeningen:

* Per toepassings-ID zijn slechts twee app-geheimen toegestaan.
* Een toepassing die niet is geregistreerd in een tenant, kan alleen worden beheerd door het account dat het heeft geregistreerd. Het kan niet worden gedeeld met andere ontwikkelaars. Dit is het geval voor de meeste apps die zijn geregistreerd met een persoonlijk Microsoft-account in de App Registration Portal. Als u uw app-registratie met meerdere ontwikkelaars wilt delen, registreert u de toepassing in een tenant met behulp van de nieuwe sectie **App-registraties** van de Azure-portal.
* Er zijn verschillende beperkingen op de indeling van de omleidings-URL die is toegestaan. Zie de volgende sectie voor meer informatie over de omleidings-URL.

### <a name="restrictions-on-redirect-urls"></a>Beperkingen op omleidings-URL's

Apps die zijn geregistreerd voor het Microsoft-identiteitsplatform, zijn beperkt tot een beperkte set omleidingsURL-waarden. De omleidings-URL voor web-apps `https`en -services moet beginnen met het schema en alle URL-waarden omleiden moeten één DNS-domein delen.  Het registratiesysteem vergelijkt de hele DNS-naam van de bestaande omleidings-URL met de DNS-naam van de omleidings-URL die u toevoegt. `http://localhost`wordt ook ondersteund als een omleidings-URL.  

De aanvraag voor het toevoegen van de DNS-naam mislukt als aan een van de volgende voorwaarden wordt voldaan:  

* De hele DNS-naam van de nieuwe omleidings-URL komt niet overeen met de DNS-naam van de bestaande omleidings-URL.
* De hele DNS-naam van de nieuwe omleidings-URL is geen subdomein van de bestaande omleidings-URL.

#### <a name="example-1"></a>Voorbeeld 1

Als de app een `https://login.contoso.com`omleidings-URL heeft van, u een omleidings-URL toevoegen waar de DNS-naam exact overeenkomt, zoals in het volgende voorbeeld wordt weergegeven:

`https://login.contoso.com/new`

U ook verwijzen naar een DNS-subdomein van login.contoso.com, zoals in het volgende voorbeeld wordt weergegeven:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Voorbeeld 2

Als u een app met `login-east.contoso.com` `login-west.contoso.com` URL's en als omleidings-URL's wilt hebben, moet u deze omleidings-URL's in de volgende volgorde toevoegen:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

U de laatste twee toevoegen omdat het subdomeinen zijn van de eerste omleidings-URL, contoso.com.

U slechts 20 antwoord-URL's voor een bepaalde toepassing hebben - deze limiet geldt voor alle app-typen die de registratie ondersteunt (spa-toepassing met één pagina), native client, web-app en service).  

Zie [Een app registreren met de nieuwe ervaring voor app-registratie](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="restrictions-on-libraries-and-sdks"></a>Beperkingen op bibliotheken en SDK's

Momenteel is de ondersteuning van de bibliotheek voor het eindpunt van het Microsoft-identiteitsplatform beperkt. Als u het eindpunt van het Microsoft-identiteitsplatform in een productietoepassing wilt gebruiken, hebt u de volgende opties:

* Als u een webtoepassing bouwt, u de algemeen beschikbare middleware aan de serverzijde veilig gebruiken om aanmeldings- en tokenvalidatie uit te voeren. Deze omvatten de OWIN OpenID Connect middleware voor ASP.NET en de Node.js Passport plug-in. Zie de sectie [Microsoft-identiteitsplatform aan](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) de slag voor codevoorbeelden die microsoft middleware gebruiken.
* Als u een desktop- of mobiele toepassing bouwt, u een van de Microsoft-verificatiebibliotheken (MSAL) gebruiken. Deze bibliotheken zijn algemeen beschikbaar of in een door de productie ondersteunde preview, dus het is veilig om ze te gebruiken in productietoepassingen. U meer lezen over de voorwaarden van de preview en de beschikbare bibliotheken in [verificatiebibliotheken verwijzen.](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Voor platforms die niet onder Microsoft-bibliotheken vallen, u integreren met het eindpunt van het Microsoft-identiteitsplatform door rechtstreeks protocolberichten in uw toepassingscode te verzenden en te ontvangen. De OpenID Connect- en [OAuth-protocollen zijn expliciet gedocumenteerd](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) om u te helpen een dergelijke integratie te doen.
* Ten slotte u open-source OpenID Connect- en OAuth-bibliotheken gebruiken om te integreren met het eindpunt van het Microsoft-identiteitsplatform. Het eindpunt van het Microsoft-identiteitsplatform moet zonder wijzigingen compatibel zijn met veel open-source protocolbibliotheken. De beschikbaarheid van dit soort bibliotheken verschilt per taal en platform. De [Websites Van OpenID Connect](https://openid.net/connect/) en [OAuth 2.0](https://oauth.net/2/) houden een lijst bij van populaire implementaties. Zie [microsoft-identiteitsplatform- en verificatiebibliotheken en](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)de lijst met open-source clientbibliotheken en -voorbeelden die zijn getest met het eindpunt van het Microsoft-identiteitsplatform voor meer informatie.
* Ter referentie `.well-known` is `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`het eindpunt voor het gemeenschappelijk eindpunt van het Microsoft-identiteitsplatform . Vervang `common` door uw tenant-id om gegevens te krijgen die specifiek zijn voor uw tenant.  

### <a name="protocol-changes"></a>Protocolwijzigingen

Het eindpunt van het Microsoft-identiteitsplatform biedt geen ondersteuning voor SAML of WS-Federation; het ondersteunt alleen OpenID Connect en OAuth 2.0.  De opmerkelijke wijzigingen in de OAuth 2.0 protocollen van de v1.0 eindpunt zijn: 

* De `email` claim wordt geretourneerd als een optionele claim is geconfigureerd **of** scope=email is opgegeven in de aanvraag. 
* De `scope` parameter wordt nu ondersteund `resource` in plaats van de parameter.  
* Veel reacties zijn gewijzigd om ze meer in overeenstemming met de OAuth `expires_in` 2.0 specificatie, bijvoorbeeld, correct terug te keren als een int in plaats van een string.  

Zie [OpenID Connect en OAuth 2.0-protocolverwijzing](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)om een beter inzicht te krijgen in de reikwijdte van de protocolfunctionaliteit die wordt ondersteund in het eindpunt van het Microsoft-identiteitsplatform.

#### <a name="saml-restrictions"></a>SAML-beperkingen

Als u Active Directory Authentication Library (ADAL) hebt gebruikt in Windows-toepassingen, hebt u mogelijk gebruik gemaakt van Windows Integrated-verificatie, waarbij gebruik wordt gemaakt van de claimverlening van de claimvan de claimvan de claimvan de claimvan beveiligingsbeweringsopmerktaal (SAML). Met deze subsidie kunnen gebruikers van gefedereerde Azure AD-tenants in stilte verifiëren met hun on-premises Active Directory-exemplaar zonder referenties in te voeren. De SAML-claimsubsidie wordt niet ondersteund op het eindpunt van het Microsoft-identiteitsplatform.

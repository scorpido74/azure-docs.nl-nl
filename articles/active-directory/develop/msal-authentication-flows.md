---
title: MSAL-verificatie stromen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verificatie stromen en subsidies die worden gebruikt door de micro soft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ce81af90baeeda519f1b56d1e10a46923ebd22c2
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772128"
---
# <a name="authentication-flows"></a>Verificatie stromen

In dit artikel worden de verschillende verificatie stromen beschreven die worden ondersteund door micro soft Authentication Library (MSAL).  Deze stromen kunnen worden gebruikt in verschillende toepassings scenario's.

| Stroom | Beschrijving | Gebruikt in|  
| ---- | ----------- | ------- | 
| [SBSI](#interactive) | Hiermee wordt het token opgehaald via een interactief proces waarbij de gebruiker wordt gevraagd om referenties via een browser of pop-upvenster. | [Bureau blad-apps](scenario-desktop-overview.md), [mobiele apps](scenario-mobile-overview.md) |
| [Impliciete toekenning](#implicit-grant) | Hiermee kan de app tokens ophalen zonder dat er een back-end-server referentie-uitwisseling wordt uitgevoerd. Hiermee kan de app zich aanmelden bij de gebruiker, de sessie onderhouden en tokens ophalen voor andere web-Api's, allemaal in de Java script-code van de client.| [Toepassingen met één pagina (SPA)](scenario-spa-overview.md) |
| [Autorisatiecode](#authorization-code) | Wordt gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Zo kunt u aanmelden en API-toegang toevoegen aan uw mobiele en desktop-apps. | [Bureau blad-apps](scenario-desktop-overview.md), [mobiele apps](scenario-mobile-overview.md), [Web-apps](scenario-web-app-call-api-overview.md) | 
| [Namens-van](#on-behalf-of) | Een toepassing roept een service of Web-API aan, die op zijn beurt een andere service of Web-API moet aanroepen. Het is verstandig om de gedelegeerde gebruikers identiteit en de machtigingen via de aanvraag keten door te geven. | [Web-API's](scenario-web-api-call-api-overview.md) |
| [Clientreferenties](#client-credentials) | Hiermee krijgt u toegang tot webhoste bronnen met behulp van de identiteit van een toepassing. Wordt meestal gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker. | [Daemon-apps](scenario-daemon-overview.md) |
| [Apparaatcode](#device-code) | Hiermee kunnen gebruikers zich aanmelden op apparaten met invoer beperkingen, zoals een Smart TV, IoT-apparaat of printer. | [Desktop/mobiele apps](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Geïntegreerde Windows-verificatie](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Hiermee kunnen toepassingen die lid zijn van een domein of Azure Active Directory (Azure AD), een token op de achtergrond verkrijgen (zonder interactie van de gebruikers interface van de gebruiker).| [Desktop/mobiele apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Gebruikersnaam en wachtwoord](scenario-desktop-acquire-token.md#username-and-password) | Hiermee kan een toepassing zich aanmelden bij de gebruiker door rechtstreeks hun wacht woord af te handelen. Deze stroom wordt niet aanbevolen. | [Desktop/mobiele apps](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitstraalt
 
Afhankelijk van hoe uw client is gebouwd, kunnen er één (of meerdere) verificatie stromen worden gebruikt die worden ondersteund door het micro soft Identity-platform.  Deze stromen kunnen een aantal tokens (id_tokens, vernieuwings tokens, toegangs tokens) en autorisatie codes produceren, en vereisen verschillende tokens om ze te laten werken. Dit diagram bevat een overzicht:
 
|Stroom | Nodig | id_token | toegangs token | token vernieuwen | autorisatie code | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Autorisatie code stroom](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Impliciete stroom](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Aflossingen van token vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token vernieuwen | x | x | x| |
|[Namens-stroom](v2-oauth2-on-behalf-of-flow.md) | toegangs token| x| x| x| |
|[Toestel code stroom](v2-oauth2-device-code.md) | | x| x| x| |
|[Clientreferenties](v2-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |
 
Tokens die zijn uitgegeven via de impliciete modus, hebben een beperkte lengte omdat ze via de URL worden teruggestuurd naar de browser (waarbij `response_mode` `query` of `fragment` ).  Voor sommige browsers geldt een limiet voor de grootte van de URL die in de browser balk kan worden geplaatst en die kan worden uitgevoerd als deze te lang is.  Daarom hebben deze tokens geen `groups` `wids` claims.

## <a name="interactive"></a>Interactief

MSAL biedt ondersteuning voor de mogelijkheid om de gebruiker te vragen om zijn of haar referenties om zich aan te melden en een token te verkrijgen met behulp van deze referenties.

![Diagram van interactieve stroom](media/msal-authentication-flows/interactive.png)

Zie voor meer informatie over het gebruik van MSAL.NET voor het interactief verkrijgen van tokens op specifieke platforms:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Universeel Windows-platform](msal-net-uwp-considerations.md)

Zie voor meer informatie over interactieve aanroepen in MSAL. js [prompt gedrag in interactieve aanvragen van MSAL. js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Impliciete toekenning

MSAL ondersteunt de [OAuth 2 impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md), waarmee de app tokens kan ophalen van het micro soft-identiteits platform zonder een back-end-server referentie-uitwisseling uit te voeren. Hiermee kan de app zich aanmelden bij de gebruiker, de sessie onderhouden en tokens ophalen voor andere web-Api's, allemaal in de Java script-code van de client.

![Diagram van impliciete toekennings stroom](media/msal-authentication-flows/implicit-grant.svg)

Veel moderne webtoepassingen zijn gebouwd als client-side, single page-toepassingen, geschreven door gebruik te maken van Java script of een beveiligd-wachtwoord verificatie-Framework zoals hoek, vue. js en reageren. js. Deze toepassingen worden uitgevoerd in een webbrowser en hebben verschillende verificatie kenmerken dan traditionele webtoepassingen aan de server zijde. Met het micro soft Identity-platform kunnen toepassingen met één pagina worden aangemeld en tokens worden verkregen voor toegang tot back-end-services of Web-Api's met behulp van de impliciete toekennings stroom. Met de impliciete stroom kan de toepassing ID-tokens ophalen om de geverifieerde gebruiker te vertegenwoordigen, en kunnen ook toegangs tokens worden verkregen die nodig zijn om beveiligde Api's aan te roepen.

Deze verificatie stroom bevat geen toepassings scenario's die gebruikmaken van platformoverschrijdende java script frameworks, zoals elektroden en reageren, omdat ze verdere mogelijkheden nodig hebben voor interactie met de systeem eigen platformen.

## <a name="authorization-code"></a>Autorisatiecode

MSAL ondersteunt de [autorisatie code toekenning van OAuth 2](v2-oauth2-auth-code-flow.md). Deze toekenning kan worden gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-Api's. Zo kunt u aanmelden en API-toegang toevoegen aan uw mobiele en desktop-apps. 

Wanneer gebruikers zich aanmelden bij webtoepassingen (websites), ontvangt de webtoepassing een autorisatie code.  De autorisatie code wordt ingewisseld om een token te verkrijgen voor het aanroepen van web-Api's. In ASP.NET-en ASP.NET Core-web-apps is het enige doel van `AcquireTokenByAuthorizationCode` om een token toe te voegen aan de token cache. Het token kan vervolgens worden gebruikt door de toepassing (meestal in de controllers, waardoor alleen een token voor een API wordt opgehaald met `AcquireTokenSilent` ).

![Diagram van autorisatie code stroom](media/msal-authentication-flows/authorization-code.png)

In het voor gaande diagram wordt de toepassing:

1. Vraagt een autorisatie code op die wordt ingewisseld voor een toegangs token.
2. Maakt gebruik van het toegangs token om een web-API aan te roepen.

### <a name="considerations"></a>Overwegingen

- U kunt de autorisatie code slechts eenmaal gebruiken voor het inwisselen van een token. Probeer niet meerdere keren een token te verkrijgen met dezelfde autorisatie code (dit is expliciet verboden door de specificatie van het Protocol standaard). Als u de code meermaals inwisselt, of omdat u niet weet dat een framework dit ook voor u doet, krijgt u de volgende fout:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Als u een ASP.NET-of ASP.NET Core-toepassing schrijft, kan dit gebeuren als u het Framework dat u al hebt ingewisseld, niet op de hoogte brengt. Hiervoor moet u de-methode aanroepen `context.HandleCodeRedemption()` van de `AuthorizationCodeReceived` gebeurtenis-handler.

- Vermijd het delen van het toegangs token met ASP.NET. Dit kan ertoe leiden dat incrementele toestemming op de juiste wijze wordt voor komen. Zie [issue #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)(Engelstalig) voor meer informatie.

## <a name="on-behalf-of"></a>Namens-van

MSAL ondersteunt de [OAuth 2-verificatie stroom op naam](v2-oauth2-on-behalf-of-flow.md).  Deze stroom wordt gebruikt wanneer een toepassing een service of Web-API aanroept, die op zijn beurt een andere service of Web-API moet aanroepen. Het is verstandig om de gedelegeerde gebruikers identiteit en de machtigingen via de aanvraag keten door te geven. Voor de middelste service om geverifieerde aanvragen voor de downstream-service te maken, moet het een toegangs token van het micro soft Identity-platform worden beveiligd namens de gebruiker.

![Diagram van namens-stroom](media/msal-authentication-flows/on-behalf-of.png)

In het bovenstaande diagram:

1. De toepassing verkrijgt een toegangs token voor de Web-API.
2. Een client (Web-, Desktop-, mobiele of toepassing met één pagina) roept een beveiligde web-API aan en voegt het toegangs token toe als Bearer-token in de verificatie header van de HTTP-aanvraag. De Web-API verifieert de gebruiker.
3. Wanneer de-client de Web-API aanroept, vraagt de Web-API een andere token namens de gebruiker aan.  
4. De beveiligde web-API gebruikt dit token voor het aanroepen van een downstream Web-API namens de gebruiker.  De Web-API kan ook later tokens aanvragen voor andere downstream-Api's (maar nog steeds namens dezelfde gebruiker).

## <a name="client-credentials"></a>Clientreferenties

MSAL biedt ondersteuning voor de [gegevens stroom van de OAuth 2-client](v2-oauth2-client-creds-grant-flow.md). Met deze stroom kunt u toegang krijgen tot webtoepassingen met behulp van de identiteit van een toepassing. Dit type toekenning wordt meestal gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker. Deze typen toepassingen worden vaak daemons of service accounts genoemd. 

De overdrachts stroom van client referenties staat een webservice (een vertrouwelijke client) toe om eigen referenties te gebruiken, in plaats van een gebruiker te imiteren, om te verifiëren bij het aanroepen van een andere webservice. In dit scenario is de client doorgaans een middelste laag, een daemon-service of een website. Voor een hoger garantie niveau kan het micro soft Identity-platform ook een certificaat (in plaats van een gedeeld geheim) als referentie gebruiken.

> [!NOTE]
> De vertrouwelijke client stroom is niet beschikbaar op de mobiele platformen (UWP, Xamarin. iOS en Xamarin. Android), omdat deze alleen open bare client toepassingen ondersteunen. Voor open bare client toepassingen weet u niet hoe u de identiteit van de toepassing kunt bewijzen aan de ID-provider. Een beveiligde verbinding kan worden bereikt voor web-app of Web-API-back-ends door een certificaat te implementeren.

MSAL.NET ondersteunt twee typen client referenties. Deze client referenties moeten worden geregistreerd bij Azure AD. De referenties worden door gegeven aan de constructors van de vertrouwelijke client toepassing in uw code.

### <a name="application-secrets"></a>Toepassings geheimen

![Diagram van vertrouwelijke client met wacht woord](media/msal-authentication-flows/confidential-client-password.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token met behulp van toepassings geheim of wachtwoord referenties.
2. Gebruikt het token om aanvragen van de resource te maken.

### <a name="certificates"></a>Certificaten

![Diagram van vertrouwelijke client met certificaat](media/msal-authentication-flows/confidential-client-certificate.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token met behulp van certificaat referenties.
2. Gebruikt het token om aanvragen van de resource te maken.

Deze client referenties moeten:
- Geregistreerd bij Azure AD.
- Door gegeven in bij de constructie van de vertrouwelijke client toepassing in uw code.

## <a name="device-code"></a>Apparaatcode

MSAL ondersteunt de [code stroom van het OAuth 2-apparaat](v2-oauth2-device-code.md), waarmee gebruikers zich kunnen aanmelden bij apparaten met invoer beperkingen, zoals een Smart TV, IOT-apparaat of printer. Interactieve verificatie met Azure AD vereist een webbrowser. Met de apparaatcode stroom kan de gebruiker een ander apparaat (bijvoorbeeld een andere computer of een mobiele telefoon) gebruiken om zich interactief aan te melden, waarbij het apparaat of besturings systeem geen webbrowser heeft.

Door de apparaatcode stroom te gebruiken, haalt de toepassing tokens op via een proces met twee stappen dat speciaal is ontworpen voor deze apparaten of besturings systemen. Voor beelden van dergelijke toepassingen zijn computers die worden uitgevoerd op IoT-apparaten of opdracht regel programma's (CLI). 

![Diagram van de code stroom van het apparaat](media/msal-authentication-flows/device-code.png)

In het bovenstaande diagram:

1. Wanneer gebruikers verificatie is vereist, biedt de app een code en wordt de gebruiker gevraagd een ander apparaat (zoals een smartphone met Internet verbinding) te gebruiken om naar een URL te gaan (bijvoorbeeld `https://microsoft.com/devicelogin` ). De gebruiker wordt vervolgens gevraagd de code in te voeren en gaat verder met een normale verificatie-ervaring, inclusief toestemming prompts en [multi-factor Authentication](../authentication/concept-mfa-howitworks.md) , indien nodig.

2. Wanneer de verificatie is geslaagd, ontvangt de opdracht regel-app de vereiste tokens via een back-upkanaal en worden ze gebruikt om de Web-API te laten aanroepen.

### <a name="constraints"></a>Beperkingen

- De code stroom van het apparaat is alleen beschikbaar voor open bare client toepassingen.
- De door gegeven instantie bij het maken van de open bare client toepassing moet een van de volgende zijn:
  - Tenanted (van het formulier `https://login.microsoftonline.com/{tenant}/` waarbij de `{tenant}` GUID staat voor de Tenant-id of een domein dat is gekoppeld aan de Tenant).
  - Voor elke werk-en school account ( `https://login.microsoftonline.com/organizations/` ).
- Persoonlijke micro soft-accounts worden nog niet ondersteund door het Azure AD v 2.0-eind punt (u kunt de `/common` of `/consumers` tenants niet gebruiken).

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

MSAL biedt ondersteuning voor geïntegreerde Windows-verificatie (IWA) voor desktop-of mobiele toepassingen die worden uitgevoerd op een lid van een domein of een Windows-computer die is toegevoegd aan Azure AD. Met behulp van IWA kunnen deze toepassingen een token op de achtergrond verkrijgen (zonder interactie van de gebruikers interface van de gebruiker). 

![Diagram van geïntegreerde Windows-verificatie](media/msal-authentication-flows/integrated-windows-authentication.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token met behulp van geïntegreerde Windows-verificatie.
2. Gebruikt het token om aanvragen van de resource te maken.

### <a name="constraints"></a>Beperkingen

IWA biedt alleen ondersteuning voor federatieve gebruikers, wat betekent dat gebruikers die zijn gemaakt in Active Directory en worden ondersteund door Azure AD. Gebruikers die rechtstreeks zijn gemaakt in azure AD, zonder Active Directory-back-ups (Managed users) kunnen deze verificatie stroom niet gebruiken. Deze beperking heeft geen invloed op de [gebruikers naam en het wacht woord](#usernamepassword).

IWA is bedoeld voor apps die zijn geschreven voor .NET Framework-, .NET core-en Universeel Windows-platform-platforms.

IWA niet overs Laan multi-factor Authentication. Als multi-factor Authentication is geconfigureerd, kan IWA mislukken als een multi-factor Authentication-Challenge vereist is. Multi-factor Authentication vereist gebruikers interactie.

U kunt niet bepalen wanneer de identiteits provider twee ledige verificatie aanvragen moet uitvoeren. De Tenant beheerder doet dat. Normaal gesp roken is twee ledige verificatie vereist wanneer u zich aanmeldt vanuit een ander land of een andere regio, wanneer u niet verbonden bent via VPN met een bedrijfs netwerk, en soms zelfs wanneer u verbinding hebt via VPN. Azure AD maakt gebruik van AI om voortdurend te leren wanneer twee ledige verificatie is vereist. Als IWA mislukt, moet u terugvallen op een [interactieve gebruikers prompt] (#interactive).

De door gegeven instantie bij het maken van de open bare client toepassing moet een van de volgende zijn:
- Tenanted (van het formulier `https://login.microsoftonline.com/{tenant}/` waarbij de `tenant` GUID staat voor de Tenant-id of een domein dat is gekoppeld aan de Tenant).
- Voor elke werk-en school account ( `https://login.microsoftonline.com/organizations/` ). Persoonlijke micro soft-accounts worden niet ondersteund (u kunt geen `/common` `/consumers` tenants gebruiken).

Omdat IWA een stille stroom is, moet een van de volgende voor waarden worden voldaan:
- De gebruiker van uw toepassing moet eerder hebben ingestemd om de toepassing te gebruiken. 
- De Tenant beheerder moet eerder toestemming hebben gegeven voor alle gebruikers in de Tenant om de toepassing te gebruiken.

Dit betekent dat een van de volgende voor waarden waar is:
- Als ontwikkelaar hebt u voor uzelf een **toekenning** geselecteerd voor de Azure Portal.
- Een Tenant beheerder heeft de **toestemming beheerder verlenen/intrekken voor {Tenant domein}** geselecteerd op het tabblad **API-machtigingen** van de registratie voor de toepassing (Zie [machtigingen toevoegen voor toegang tot Web-api's](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- U hebt een manier gegeven waarop gebruikers toestemming kunnen geven voor de toepassing (Zie [toestemming van individuele gebruiker aanvragen](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- U hebt een manier voor de Tenant beheerder om toestemming te geven voor de toepassing (Zie toestemming van de [beheerder](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

De IWA-stroom is ingeschakeld voor .NET Desktop-, .NET core-en Windows Universal platform-apps. In .NET Core moet u de gebruikers naam opgeven voor IWA, omdat .NET core geen gebruikers namen kan ophalen uit het besturings systeem.
  
Zie [v 2.0-machtigingen en-toestemming](v2-permissions-and-consent.md)voor meer informatie over toestemming.

## <a name="usernamepassword"></a>Gebruikersnaam en wachtwoord

MSAL ondersteunt de [toewijzing van het wacht woord voor de OAuth 2-resource-eigenaar](v2-oauth-ropc.md), waarmee een toepassing zich kan aanmelden bij de gebruiker door rechtstreeks hun wacht woord af te handelen. In uw bureaublad toepassing kunt u de stroom gebruikers naam/wacht woord gebruiken om een token op de achtergrond te verkrijgen. Er is geen gebruikers interface vereist bij het gebruik van de toepassing.

![Diagram van de gebruikers naam/wacht woord stroom](media/msal-authentication-flows/username-password.png)

In het voor gaande diagram wordt de toepassing:

1. Verkrijgt een token door de gebruikers naam en het wacht woord naar de ID-provider te verzenden.
2. Hiermee wordt een web-API aangeroepen met behulp van het token.

> [!WARNING]
> Deze stroom wordt niet aanbevolen. Hiervoor is een hoge mate van vertrouwen en gebruikers belichting vereist.  U moet deze stroom alleen gebruiken wanneer andere, veiligere stromen niet kunnen worden gebruikt. Zie [Wat is de oplossing voor het groeiende probleem van wacht woorden?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)voor meer informatie. 

De voorkeurs stroom voor het op de achtergrond verkrijgen van een token op computers die lid zijn van een Windows-domein is [geïntegreerde Windows-verificatie](#integrated-windows-authentication). Als dat niet het geval is, kunt u ook de [code stroom](#device-code)van het apparaat gebruiken.

Hoewel dit in sommige gevallen nuttig is (DevOps-scenario's), als u gebruikers naam/wacht woord wilt gebruiken in interactieve scenario's waarin u uw eigen gebruikers interface opgeeft, probeert u het te vermijden. Met gebruikers naam/wacht woord:
- Gebruikers die multi-factor Authentication moeten uitvoeren, kunnen zich niet aanmelden (omdat er geen interactie is).
- Gebruikers kunnen eenmalige aanmelding niet uitvoeren.

### <a name="constraints"></a>Beperkingen

Naast de [beperkingen voor geïntegreerde Windows-verificatie](#integrated-windows-authentication)gelden de volgende beperkingen ook:

- De gebruikers naam/wacht woord stroom is niet compatibel met voorwaardelijke toegang en multi-factor Authentication. Als uw app wordt uitgevoerd in een Azure AD-Tenant waarvoor de Tenant beheerder multi-factor Authentication vereist, kunt u deze stroom niet gebruiken. Veel organisaties doen dat.
- Het werkt alleen voor werk-en school accounts (geen micro soft-accounts).
- De stroom is beschikbaar op .NET Desktop en .NET core, maar niet op Universeel Windows-platform.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C Details

Zie [using ROPC with Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)(Engelstalig) voor meer informatie over het gebruik van ROPC in MSAL.NET en Azure AD B2C.

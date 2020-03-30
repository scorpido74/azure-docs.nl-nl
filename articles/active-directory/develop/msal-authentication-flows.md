---
title: MSAL-authenticatiestromen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verificatiestromen en -subsidies die worden gebruikt door de Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 25c219bedbbbec9fbc0c5617c7bd9fc482faf49a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050513"
---
# <a name="authentication-flows"></a>Verificatiestromen

In dit artikel worden de verschillende verificatiestromen beschreven die worden geleverd door De Verificatiebibliotheek (MSAL) van Microsoft Authentication.  Deze stromen kunnen worden gebruikt in verschillende toepassingsscenario's.

| Stroom | Beschrijving | Gebruikt in|  
| ---- | ----------- | ------- | 
| [Interactieve](#interactive) | Hiermee wordt het token via een interactief proces dat de gebruiker om referenties vraagt via een browser of pop-upvenster. | [Bureaublad-apps,](scenario-desktop-overview.md) [mobiele apps](scenario-mobile-overview.md) |
| [Impliciete subsidie](#implicit-grant) | Hiermee kan de app tokens ontvangen zonder een back-end server-referentie-uitwisseling uit te voeren. Hierdoor kan de app zich aanmelden bij de gebruiker, de sessie onderhouden en tokens naar andere web-API's krijgen, allemaal binnen de JavaScript-code van de client.| [Toepassingen met één pagina (SPA)](scenario-spa-overview.md) |
| [Autorisatiecode](#authorization-code) | Wordt gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-API's. Hiermee u aanmeldings- en API-toegang toevoegen aan uw mobiele en desktop-apps. | [Bureaublad-apps,](scenario-desktop-overview.md) [mobiele apps,](scenario-mobile-overview.md) [web-apps](scenario-web-app-call-api-overview.md) | 
| [Namens](#on-behalf-of) | Een toepassing beroept zich op een service of web-API, die op zijn beurt een andere service of web-API moet aanroepen. Het idee is om de gedelegeerde gebruikersidentiteit en machtigingen te verspreiden via de aanvraagketen. | [Web-API's](scenario-web-api-call-api-overview.md) |
| [Clientreferenties](#client-credentials) | Hiermee u toegang krijgen tot webgehoste bronnen met behulp van de identiteit van een toepassing. Vaak gebruikt voor server-naar-server interacties die op de achtergrond moeten worden uitgevoerd, zonder onmiddellijke interactie met een gebruiker. | [Daemon-apps](scenario-daemon-overview.md) |
| [Apparaatcode](#device-code) | Hiermee kunnen gebruikers zich aanmelden bij apparaten met beperkte invoer, zoals een smart-tv, IoT-apparaat of printer. | [Desktop-/mobiele apps](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Geïntegreerde Windows-verificatie](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Hiermee kunnen toepassingen op domein- of Azure Active Directory (Azure AD) verbonden computers een token in stilte aanschaffen (zonder enige ui-interactie van de gebruiker).| [Desktop-/mobiele apps](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Gebruikersnaam en wachtwoord](scenario-desktop-acquire-token.md#username-and-password) | Hiermee kan een toepassing zich aanmelden bij de gebruiker door hun wachtwoord rechtstreeks te gebruiken. Deze stroom wordt niet aanbevolen. | [Desktop-/mobiele apps](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitzendt
 
Afhankelijk van hoe uw client is gebouwd, kan deze één (of meerdere) van de verificatiestromen gebruiken die worden ondersteund door het Microsoft-identiteitsplatform.  Deze stromen kunnen een verscheidenheid aan tokens produceren (id_tokens, tokens vernieuwen, toegangstokens) en autorisatiecodes en vereisen verschillende tokens om ze te laten werken. Deze grafiek geeft een overzicht:
 
|Stroom | Vereist | id_token | toegangstoken | token vernieuwen | autorisatiecode | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Machtigingscodestroom](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Impliciete stroom](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Tokeninwisseling vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token vernieuwen | x | x | x| |
|[Namens-stroom](v2-oauth2-on-behalf-of-flow.md) | toegangstoken| x| x| x| |
|[Apparaatcodestroom](v2-oauth2-device-code.md) | | x| x| x| |
|[Clientreferenties](v2-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |
 
Tokens uitgegeven via de impliciete modus hebben een lengtebeperking als gevolg `response_mode` van `query` `fragment`wordt doorgegeven terug naar de browser via de URL (waar is of ).  Sommige browsers hebben een limiet op de grootte van de URL die in de browserbalk kan worden geplaatst en mislukken wanneer deze te lang is.  Dus, deze tokens `groups` hebben `wids` geen of claims.

## <a name="interactive"></a>Interactief

MSAL ondersteunt de mogelijkheid om de gebruiker interactief te vragen om zich aan te melden en een token te verkrijgen met behulp van deze referenties.

![Diagram van interactieve stroom](media/msal-authentication-flows/interactive.png)

Zie voor meer informatie over het gebruik van MSAL.NET om op interactieve wijze tokens op specifieke platforms te verwerven:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Universeel Windows-platform](msal-net-uwp-considerations.md)

Zie Snel gedrag in interactieve verzoeken van MSAL.js voor meer informatie over interactieve oproepen in [MSAL.js.](msal-js-prompt-behavior.md)

## <a name="implicit-grant"></a>Impliciete subsidie

MSAL ondersteunt de [impliciete subsidiestroom van OAuth 2](v2-oauth2-implicit-grant-flow.md), waarmee de app tokens van het Microsoft-identiteitsplatform kan krijgen zonder een back-end server-referentie-uitwisseling uit te voeren. Hierdoor kan de app zich aanmelden bij de gebruiker, de sessie onderhouden en tokens naar andere web-API's krijgen, allemaal binnen de JavaScript-code van de client.

![Diagram van impliciete subsidiestroom](media/msal-authentication-flows/implicit-grant.svg)

Veel moderne webapplicaties zijn gebouwd als client-side, single page applicaties, geschreven met behulp van JavaScript of een SPA framework zoals Angular, Vue.js en React.js. Deze toepassingen worden uitgevoerd in een webbrowser en hebben andere verificatiekenmerken dan traditionele webtoepassingen aan de serverzijde. Het Microsoft-identiteitsplatform maakt toepassingen met één pagina in staat om gebruikers aan te melden en tokens toegang te krijgen tot back-endservices of web-API's, met behulp van de impliciete subsidiestroom. Met de impliciete stroom kan de toepassing ID-tokens krijgen om de geverifieerde gebruiker te vertegenwoordigen, en ook toegang tot tokens die nodig zijn om beveiligde API's te bellen.

Deze verificatiestroom bevat geen toepassingsscenario's die gebruikmaken van JavaScript-frameworks met meerdere platforms, zoals Electron en React-Native, omdat ze verdere mogelijkheden vereisen voor interactie met de native platforms.

## <a name="authorization-code"></a>Autorisatiecode

MSAL ondersteunt de [OAuth 2 autorisatiecode subsidie](v2-oauth2-auth-code-flow.md). Deze subsidie kan worden gebruikt in apps die op een apparaat zijn geïnstalleerd om toegang te krijgen tot beveiligde bronnen, zoals web-API's. Hiermee u aanmeldings- en API-toegang toevoegen aan uw mobiele en desktop-apps. 

Wanneer gebruikers zich aanmelden bij webtoepassingen (websites), ontvangt de webtoepassing een autorisatiecode.  De autorisatiecode wordt ingewisseld om een token te verkrijgen om web-API's aan te roepen. In ASP.NET en ASP.NET Core-web-apps is het enige doel om `AcquireTokenByAuthorizationCode` een token toe te voegen aan de tokencache. Het token kan vervolgens worden gebruikt door de toepassing (meestal in de `AcquireTokenSilent`controllers, die gewoon een token voor een API krijgen met behulp van ).

![Diagram van de stroom van de autorisatiecode](media/msal-authentication-flows/authorization-code.png)

In het voorgaande diagram gaat de toepassing als:

1. Vraagt een autorisatiecode aan, die wordt ingewisseld voor een toegangstoken.
2. Gebruikt het toegangstoken om een web-API aan te roepen.

### <a name="considerations"></a>Overwegingen

- U de autorisatiecode slechts één keer gebruiken om een token in te wisselen. Probeer niet meerdere keren een token te verkrijgen met dezelfde autorisatiecode (het is expliciet verboden door de standaardspecificatie van het protocol). Als u de code meerdere keren opzettelijk inwisselt of omdat u zich niet bewust bent dat een framework dit ook voor u doet, krijgt u de volgende foutmelding:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Als u een ASP.NET of ASP.NET Core-toepassing schrijft, kan dit gebeuren als u het framework niet vertelt dat u de autorisatiecode al hebt ingewisseld. Hiervoor moet u de `context.HandleCodeRedemption()` methode van `AuthorizationCodeReceived` de gebeurtenishandler aanroepen.

- Vermijd het delen van het toegangstoken met ASP.NET, waardoor incrementele toestemming correct kan worden voorkomen. Zie [probleem #693 voor](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)meer informatie .

## <a name="on-behalf-of"></a>Namens

MSAL ondersteunt de [OAuth 2 namens authenticatiestroom.](v2-oauth2-on-behalf-of-flow.md)  Deze stroom wordt gebruikt wanneer een toepassing een service of web-API aanroept, die op zijn beurt een andere service of web-API moet aanroepen. Het idee is om de gedelegeerde gebruikersidentiteit en machtigingen te verspreiden via de aanvraagketen. Als de middle-tier-service geverifieerde aanvragen voor de downstreamservice wil indienen, moet deze namens de gebruiker een toegangstoken van het Microsoft-identiteitsplatform beveiligen.

![Diagram van de stroomvoor rekening](media/msal-authentication-flows/on-behalf-of.png)

In het bovenstaande diagram:

1. De toepassing verwerft een toegangstoken voor de web-API.
2. Een client (web-, desktop-, mobiele of toepassing met één pagina) roept een beveiligde web-API aan, waarbij het toegangstoken als een token aan toonder wordt toegevoegd in de verificatiekop van het HTTP-verzoek. De web-API verifieert de gebruiker.
3. Wanneer de client de web-API aanroept, vraagt de web-API een ander token aan namens de gebruiker.  
4. De beveiligde web-API gebruikt dit token om een downstream web-API aan te roepen namens de gebruiker.  De web-API kan later ook tokens aanvragen voor andere downstream API's (maar nog steeds namens dezelfde gebruiker).

## <a name="client-credentials"></a>Clientreferenties

MSAL ondersteunt de [OAuth 2-clientreferentiesstroom.](v2-oauth2-client-creds-grant-flow.md) Met deze stroom u toegang krijgen tot webgehoste bronnen met behulp van de identiteit van een toepassing. Dit type subsidie wordt vaak gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder onmiddellijke interactie met een gebruiker. Dit soort toepassingen worden vaak aangeduid als daemons of service accounts. 

Met de subsidiestroom van clientreferenties kan een webservice (een vertrouwelijke client) zijn eigen referenties gebruiken, in plaats van zich voor te doen als een gebruiker, om te verifiëren wanneer u een andere webservice aanroept. In dit scenario is de client meestal een middle-tier webservice, een daemon-service of een website. Voor een hoger niveau van zekerheid, het Microsoft-identiteitsplatform staat de oproepservice ook toe om een certificaat (in plaats van een gedeeld geheim) als referentie te gebruiken.

> [!NOTE]
> De vertrouwelijke clientstroom is niet beschikbaar op de mobiele platforms (UWP, Xamarin.iOS en Xamarin.Android), omdat deze alleen openbare clienttoepassingen ondersteunen. Openbare clienttoepassingen weten niet hoe de identiteit van de toepassing aan de Identity Provider kan worden bewezen. Een beveiligde verbinding kan worden bereikt op web-app of web API back-ends door het implementeren van een certificaat.

MSAL.NET ondersteunt twee typen clientreferenties. Deze clientreferenties moeten worden geregistreerd bij Azure AD. De referenties worden doorgegeven aan de constructeurs van de vertrouwelijke clienttoepassing in uw code.

### <a name="application-secrets"></a>Toepassingsgeheimen

![Diagram van vertrouwelijke client met wachtwoord](media/msal-authentication-flows/confidential-client-password.png)

In het voorgaande diagram gaat de toepassing als:

1. Verwerft een token met behulp van toepassingsgeheime of wachtwoordreferenties.
2. Gebruikt het token om aanvragen van de bron in te dienen.

### <a name="certificates"></a>Certificaten

![Diagram van vertrouwelijke cliënt met cert](media/msal-authentication-flows/confidential-client-certificate.png)

In het voorgaande diagram gaat de toepassing als:

1. Verwerft een token met behulp van certificaatreferenties.
2. Gebruikt het token om aanvragen van de bron in te dienen.

Deze clientreferenties moeten zijn:
- Geregistreerd bij Azure AD.
- Geslaagd bij de bouw van de vertrouwelijke client applicatie in uw code.

## <a name="device-code"></a>Apparaatcode

MSAL ondersteunt de [codestroom van het OAuth 2-apparaat](v2-oauth2-device-code.md), waarmee gebruikers zich kunnen aanmelden bij apparaten met invoerbeperking, zoals een smart-tv, IoT-apparaat of printer. Interactieve verificatie met Azure AD vereist een webbrowser. Met de stroom van apparaatcodes kan de gebruiker een ander apparaat (bijvoorbeeld een andere computer of een mobiele telefoon) gebruiken om interactief in te loggen, waarbij het apparaat of besturingssysteem geen webbrowser biedt.

Door gebruik te maken van de apparaatcodestroom verkrijgt de toepassing tokens via een proces in twee stappen dat speciaal is ontworpen voor deze apparaten of besturingssystemen. Voorbeelden van dergelijke toepassingen zijn die welke worden uitgevoerd op IoT-apparaten of command-line tools (CLI). 

![Diagram van de stroom van apparaatcode](media/msal-authentication-flows/device-code.png)

In het bovenstaande diagram:

1. Wanneer gebruikersverificatie vereist is, biedt de app een code en vraagt de gebruiker om een ander apparaat (zoals `https://microsoft.com/devicelogin`een met internet verbonden smartphone) te gebruiken om naar een URL te gaan (bijvoorbeeld). De gebruiker wordt vervolgens gevraagd om de code in te voeren en gaat door een normale verificatie-ervaring, inclusief toestemmingsprompts en multi-factor authenticatie indien nodig.

2. Bij succesvolle verificatie ontvangt de opdrachtregel-app de vereiste tokens via een backkanaal en gebruikt deze om de web-API-aanroepen uit te voeren die deze nodig heeft.

### <a name="constraints"></a>Beperkingen

- Apparaatcodestroom is alleen beschikbaar in openbare clienttoepassingen.
- De autoriteit die is doorgegeven bij de bouw van de openbare cliëntaanvraag moet een van de volgende:
  - Tenant (van het `https://login.microsoftonline.com/{tenant}/` `{tenant}` formulier waarin de GUID de tenant-id vertegenwoordigt of een domein dat is gekoppeld aan de tenant).
  - Voor alle werk-`https://login.microsoftonline.com/organizations/`en schoolaccounts ( ).
- Persoonlijke microsoft-accounts worden nog niet ondersteund door het Azure AD v2.0-eindpunt (u de `/common` of `/consumers` tenants niet gebruiken).

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

MSAL ondersteunt Integrated Windows Authentication (IWA) voor desktop- of mobiele toepassingen die worden uitgevoerd op een domein dat is samengevoegd of Azure AD is toegetreden tot Windows-computer. Met Behulp van IWA kunnen deze toepassingen een token in stilte verkrijgen (zonder enige ui-interactie van de gebruiker). 

![Diagram met geïntegreerde Windows-verificatie](media/msal-authentication-flows/integrated-windows-authentication.png)

In het voorgaande diagram gaat de toepassing als:

1. Verwerft een token met geïntegreerde Windows-verificatie.
2. Gebruikt het token om aanvragen van de bron in te dienen.

### <a name="constraints"></a>Beperkingen

IWA ondersteunt alleen federatieve gebruikers, wat betekent dat gebruikers zijn gemaakt in Active Directory en worden ondersteund door Azure AD. Gebruikers die rechtstreeks in Azure AD zijn gemaakt, zonder Active Directory-ondersteuning (beheerde gebruikers) kunnen deze verificatiestroom niet gebruiken. Deze beperking heeft geen invloed op de [gebruikersnaam/wachtwoordstroom.](#usernamepassword)

IWA is voor apps geschreven voor .NET Framework, .NET Core en Universal Windows Platform platforms.

IWA omzeilt geen meervoudige authenticatie. Als multi-factor authenticatie is geconfigureerd, kan IWA mislukken als een multi-factor authenticatie uitdaging vereist is. Multi-factor authenticatie vereist interactie van de gebruiker.

U bepaalt niet wanneer de identiteitsprovider tweestapsverificatie aanvraagt. De tenantbeheerder wel. Normaal gesproken is tweestapsverificatie vereist wanneer u zich aanmeldt vanuit een ander land, wanneer u niet via VPN bent verbonden met een bedrijfsnetwerk en soms zelfs wanneer u via VPN bent verbonden. Azure AD gebruikt AI om continu te weten te komen of tweestapsverificatie vereist is. Als IWA faalt, moet u terugvallen op een [interactieve gebruikersprompt] (#interactive).

De autoriteit die is doorgegeven bij de bouw van de openbare cliëntaanvraag moet een van de volgende:
- Tenant (van het `https://login.microsoftonline.com/{tenant}/` `tenant` formulier waar de guid de tenant-id vertegenwoordigt of een domein dat is gekoppeld aan de tenant).
- Voor alle werk-`https://login.microsoftonline.com/organizations/`en schoolaccounts ( ). Persoonlijke microsoft-accounts worden niet ondersteund (u geen gebruik maken `/common` of `/consumers` huurders).

Omdat IWA een stille stroom is, moet een van de volgende gevallen:
- De gebruiker van uw toepassing moet eerder toestemming hebben gegeven om de toepassing te gebruiken. 
- De tenantbeheerder moet eerder toestemming hebben gegeven aan alle gebruikers in de tenant om de toepassing te gebruiken.

Dit betekent dat een van de volgende is waar:
- U hebt Als ontwikkelaar **Grant** zelf geselecteerd op de Azure-portal.
- Een tenantbeheerder heeft **de toestemming voor verlening/intrekking van beheerders voor {tenantdomein}** geselecteerd op het tabblad **API-machtigingen** van de registratie voor de toepassing (zie [Machtigingen toevoegen om toegang te krijgen tot web-API's).](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)
- U hebt gebruikers een manier geboden om in te stemmen met de toepassing (zie [Toestemming van individuele gebruikers aanvragen).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- U hebt een manier geboden voor de tenantbeheerder om toestemming te geven voor de toepassing (zie [toestemming voor beheerders).](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

De IWA-stroom is ingeschakeld voor .NET-bureaublad-, .NET Core- en Windows Universal Platform-apps. Op .NET Core moet u de gebruikersnaam aan IWA opgeven, omdat .NET Core geen gebruikersnamen van het besturingssysteem kan verkrijgen.
  
Zie [v2.0-machtigingen en toestemming](v2-permissions-and-consent.md)voor meer informatie over toestemming.

## <a name="usernamepassword"></a>Gebruikersnaam en wachtwoord

MSAL ondersteunt de [OAuth 2 resource eigenaar wachtwoord referenties subsidie](v2-oauth-ropc.md), waarmee een toepassing aan te melden bij de gebruiker door direct omgaan met hun wachtwoord. In uw bureaubladtoepassing u de gebruikersnaam/wachtwoordstroom gebruiken om een token in stilte te verkrijgen. Er is geen gebruikersinterface vereist bij het gebruik van de toepassing.

![Diagram van de gebruikersnaam/wachtwoordstroom](media/msal-authentication-flows/username-password.png)

In het voorgaande diagram gaat de toepassing als:

1. Verwerft een token door de gebruikersnaam en het wachtwoord naar de identiteitsprovider te sturen.
2. Roept een web-API aan met behulp van het token.

> [!WARNING]
> Deze stroom wordt niet aanbevolen. Het vereist een hoge mate van vertrouwen en blootstelling van de gebruiker.  U moet deze stroom alleen gebruiken als andere, veiligere, stromen niet kunnen worden gebruikt. Zie Wat is de oplossing voor het groeiende probleem van wachtwoorden voor meer [informatie?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 

De voorkeursstroom voor het stilletjes aanschaffen van een token op windows-domeingebonden machines is [Geïntegreerde Windows-verificatie.](#integrated-windows-authentication) Anders u ook [de codestroom van het apparaat](#device-code)gebruiken.

Hoewel dit in sommige gevallen handig is (DevOps-scenario's), probeert u dit te voorkomen als u gebruikersnaam/wachtwoord wilt gebruiken in interactieve scenario's waarin u uw eigen gebruikersinterface opgeeft. Met gebruikersnaam/wachtwoord:
- Gebruikers die multi-factor authenticatie moeten doen, kunnen zich niet aanmelden (omdat er geen interactie is).
- Gebruikers kunnen zich niet aanmelden.

### <a name="constraints"></a>Beperkingen

Naast de [beperkingen voor geïntegreerde Windows-verificatie](#integrated-windows-authentication)zijn ook de volgende beperkingen van toepassing:

- De gebruikersnaam/wachtwoordstroom is niet compatibel met voorwaardelijke toegang en meervoudige verificatie. Als uw app wordt uitgevoerd in een Azure AD-tenant waarbij de tenantbeheerder multifactorauthenticatie vereist, u deze stroom niet gebruiken. Veel organisaties doen dat.
- Het werkt alleen voor werk- en schoolaccounts (niet voor Microsoft-accounts).
- De stroom is beschikbaar op .NET desktop en .NET Core, maar niet op Universal Windows Platform.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C-specificaties

Zie [ROPC gebruiken met Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)voor meer informatie over het gebruik van MSAL.NET en Azure AD B2C.

---
title: Verificatie in microsoft-identiteitsplatform | Azure
description: Meer informatie over de basisprincipes van verificatie in het Microsoft-identiteitsplatform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6191e67f097b5ab471c5b31eff11a0e570d1c990
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617028"
---
# <a name="authentication-basics"></a>Basisbeginselen van verificatie

## <a name="what-is-authentication"></a>Wat is verificatie

Dit artikel behandelt veel van de verificatieconcepten die u moet begrijpen om beveiligde web-apps, web-API's of apps te maken die beveiligde web-API's aanroepen. Als u een term ziet die u niet kent, probeert u onze [woordenlijst](developer-glossary.md) of onze video's van het [Microsoft-identiteitsplatform](identity-videos.md) die basisconcepten omvatten.

**Authenticatie** is het proces om te bewijzen dat je bent wie je zegt dat je bent. Verificatie wordt soms afgekort tot 'AuthN'.

**Autorisatie** is de handeling van het verlenen van een geverifieerde partij toestemming om iets te doen. Het geeft aan welke gegevens u mag openen en wat u met die gegevens doen. Autorisatie wordt soms afgekort tot 'AuthZ'.

In plaats van apps te maken die elk hun eigen gebruikersnaam en wachtwoordinformatie behouden, wat een hoge administratieve last met zich meebrengt wanneer u gebruikers in meerdere apps moet toevoegen of verwijderen, kunnen apps die verantwoordelijkheid delegeren aan een gecentraliseerde identiteitsprovider.

Azure Active Directory (Azure AD) is een gecentraliseerde identiteitsprovider in de cloud. Als u verificatie en autorisatie delegeren, kunnen scenario's zoals beleid voor voorwaardelijke toegang die vereisen dat een gebruiker zich op een specifieke locatie bevindt, het gebruik van meervoudige verificatie, evenals een gebruiker in staat stellen zich eenmalig aan te melden en vervolgens automatisch worden aangemeld bij alle web-apps die dezelfde gecentraliseerde directory delen. Deze mogelijkheid wordt aangeduid als Single Sign On (SSO).

Een gecentraliseerde identiteitsprovider is nog belangrijker voor apps met gebruikers over de hele wereld die zich niet per se aanmelden vanuit het netwerk van de onderneming. Azure AD verifieert gebruikers en biedt toegangstokens. Een [toegangstoken](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) is een beveiligingstoken dat wordt uitgegeven door een autorisatieserver. Het bevat informatie over de gebruiker en de app waarvoor het token is bedoeld; die kunnen worden gebruikt om toegang te krijgen tot web-API's en andere beveiligde bronnen.

Het Microsoft-identiteitsplatform vereenvoudigt de verificatie voor toepassingsontwikkelaars door het aanbieden van identity as a service, met ondersteuning voor industriestandaardprotocollen zoals [OAuth 2.0](https://oauth.net/2/) en [OpenID Connect,](https://openid.net/connect/)evenals open-sourcebibliotheken voor verschillende platforms om u te helpen snel te coderen. Hiermee kunnen ontwikkelaars toepassingen bouwen die alle Microsoft-identiteiten ondertekenen, tokens ertoe brengen [Microsoft Graph,](https://developer.microsoft.com/graph/)andere Microsoft API's of API's te bellen die ontwikkelaars hebben gebouwd. Zie [Evolution of Microsoft identity platform](about-microsoft-identity-platform.md)voor meer informatie.

### <a name="tenants"></a>Tenants

Een cloud identity provider bedient veel organisaties. Om gebruikers uit verschillende organisaties gescheiden te houden, wordt Azure AD verdeeld in tenants, met één tenant per organisatie.

Huurders houden gebruikers en hun bijbehorende apps bij. Het Microsoft-identiteitsplatform ondersteunt ook gebruikers die zich aanmelden met persoonlijke Microsoft-accounts.

Azure AD biedt ook Azure Active Directory B2C, zodat organisaties zich kunnen aanmelden bij gebruikers, meestal klanten, met behulp van sociale identiteiten zoals een Google-account. Zie [Azure Active Directory B2C-documentatie](https://docs.microsoft.com/azure/active-directory-b2c) voor meer informatie .

### <a name="security-tokens"></a>Beveiligingstokens

Beveiligingstokens bevatten informatie over gebruikers en apps. Azure AD maakt gebruik van JSON-gebaseerde tokens (JWT's) die claims bevatten.

Een claim geeft beweringen over één entiteit, zoals een [clienttoepassing](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) of [broneigenaar,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)aan een andere entiteit, zoals een [resourceserver](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server).

Claims zijn naam/waardeparen die feiten over het tokenonderwerp doorgeven. Een claim kan bijvoorbeeld feiten bevatten over de beveiligingsprincipal die is geverifieerd door de [autorisatieserver.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server) De claims in een bepaald token zijn afhankelijk van veel dingen, waaronder het type token, het type referentie dat wordt gebruikt om het onderwerp te verifiëren, de toepassingsconfiguratie, enzovoort.

Toepassingen kunnen claims gebruiken voor verschillende taken, zoals:

* Het token valideren
* De tenant van de tokensubject identificeren
* Gebruikersinformatie weergeven
* De toestemming van de proefpersoon bepalen

Een claim bestaat uit sleutelwaardeparen die informatie verstrekken zoals:

* Beveiligingstokenserver die het token heeft gegenereerd
* Datum waarop het token is gegenereerd
* Onderwerp (zoals de gebruiker - behalve voor daemons)
* Doelgroep, de app waarvoor het token is gegenereerd
* App (de client) die om het token vroeg. In het geval van web-apps kan dit hetzelfde zijn als het publiek

Zie [toegangstokens](access-tokens.md) en [ID-tokens](id-tokens.md)voor meer gedetailleerde claiminformatie.

Het is aan de app waarvoor het token is gegenereerd, de web-app die de gebruiker heeft aangemeld of de web-API die wordt aangeroepen, om het token te valideren. Het token wordt ondertekend door de Security Token Server (STS) met een privésleutel. De STS publiceert de bijbehorende openbare sleutel. Als u een token wilt valideren, verifieert de app de handtekening met behulp van de openbare STS-sleutel om te valideren dat de handtekening is gemaakt met behulp van de privésleutel.

Tokens zijn slechts voor een beperkte tijd geldig. Meestal biedt de STS een paar tokens: een toegangstoken om toegang te krijgen tot de toepassing of beveiligde bron, en een vernieuwingstoken dat wordt gebruikt om het toegangstoken te vernieuwen wanneer het toegangstoken bijna afloopt.

Toegangstokens worden doorgegeven aan een web-API als het token aan toonder in de `Authorization` koptekst. Een app kan een vernieuwingstoken aan de STS bieden en als de toegang van de gebruiker tot de app niet is ingetrokken, krijgt deze een nieuw toegangstoken en een nieuw vernieuwingstoken terug. Dit is hoe het scenario van iemand die de onderneming verlaat wordt behandeld. Wanneer de STS het vernieuwingstoken ontvangt, wordt er geen geldig toegangstoken meer doorgegeven als de gebruiker niet langer geautoriseerd is.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitzendt

Afhankelijk van hoe uw client is opgebouwd, kan deze één (of meerdere) van de verificatiestromen gebruiken die worden ondersteund door Azure AD. Deze stromen kunnen een verscheidenheid aan tokens produceren (id_tokens, tokens vernieuwen, toegangstokens) en autorisatiecodes en vereisen verschillende tokens om ze te laten werken. Deze grafiek geeft een overzicht:

|Stroom | Vereist | id_token | toegangstoken | token vernieuwen | autorisatiecode | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Machtigingscodestroom](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Impliciete stroom](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Tokeninwisseling vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token vernieuwen | x | x | x| |
|[Namens-stroom](v2-oauth2-on-behalf-of-flow.md) | toegangstoken| x| x| x| |
|[Clientreferenties](v2-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |

Tokens uitgegeven via de impliciete modus hebben een lengtebeperking als gevolg `response_mode` van `query` `fragment`wordt doorgegeven terug naar de browser via de URL (waar is of ).  Sommige browsers hebben een limiet op de grootte van de URL die in de browserbalk kan worden geplaatst en mislukken wanneer deze te lang is.  Dus, deze tokens `groups` hebben `wids` geen of claims. 

Nu u een overzicht hebt van de basisbeginselen, u het model en de API voor identiteitsapps begrijpen, meer informatie krijgen over hoe inrichting werkt in Azure AD en koppelingen krijgen naar gedetailleerde informatie over veelvoorkomende scenario's die Azure AD ondersteunt.

## <a name="application-model"></a>Toepassingsmodel

Toepassingen kunnen zich aanmelden voor gebruikers zelf of aanmelden aan een identiteitsprovider delegeren. Zie [Verificatiestromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over aanmeldingsscenario's die worden ondersteund door Azure AD.

Als een identiteitsprovider weet dat een gebruiker toegang heeft tot een bepaalde app, moet zowel de gebruiker als de toepassing zijn geregistreerd bij de identiteitsprovider. Wanneer u uw toepassing registreert bij Azure AD, biedt u een identiteitsconfiguratie voor uw toepassing waarmee deze kan worden geïntegreerd met Azure AD. Als u de app registreert, u ook:

* Pas de branding van uw toepassing aan in het aanmeldingsdialoogvenster. Dit is belangrijk omdat dit de eerste ervaring is die een gebruiker met uw app zal hebben.
* Bepaal of u gebruikers alleen wilt laten aanmelden als ze tot uw organisatie behoren. Dit is een enkele tenanttoepassing. Of gebruikers toestaan om in te loggen met een werk- of schoolaccount. Dit is een multi-tenant applicatie. U ook persoonlijke Microsoft-accounts of een sociaal account van LinkedIn, Google enzovoort toestaan.
* Machtigingen voor scope aanvragen. U bijvoorbeeld het bereik 'user.read' aanvragen, waarmee u toestemming krijgt om het profiel van de aangemelde gebruiker te lezen.
* Definieer scopes die toegang tot uw web-API definiëren. Wanneer een app toegang wil krijgen tot uw API, moet deze doorgaans machtigingen aanvragen voor de scopes die u definieert.
* Deel een geheim met Azure AD dat de identiteit van de app bewijst aan Azure AD.  Dit is relevant in het geval dat de app een vertrouwelijke clientapplicatie is. Een vertrouwelijke clienttoepassing is een toepassing die referenties veilig kan bevatten. Hiervoor is een vertrouwde backendserver nodig om de referenties op te slaan.

Eenmaal geregistreerd, krijgt de toepassing een unieke id die de app deelt met Azure AD wanneer deze tokens aanvraagt. Als de app een [vertrouwelijke clienttoepassing](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)is, deelt deze ook het geheim of de openbare sleutel*- afhankelijk van of certificaten of geheimen zijn gebruikt.

Het Microsoft-identiteitsplatform vertegenwoordigt toepassingen met behulp van een model dat twee hoofdfuncties vervult:

* De app identificeren aan de hand van de verificatieprotocollen die worden ondersteund
* Alle id's, URL's, geheimen en gerelateerde informatie verstrekken die nodig zijn om te verifiëren

Het Microsoft-identiteitsplatform:

* Bevat alle gegevens die nodig zijn om verificatie te ondersteunen tijdens runtime
* Bevat alle gegevens om te bepalen tot welke bronnen een app mogelijk moet worden geopend en onder welke omstandigheden aan een bepaald verzoek moet worden voldaan
* Biedt infrastructuur voor het implementeren van app-inrichting in de tenant van de app-ontwikkelaar en voor elke andere Azure AD-tenant
* Verwerkt toestemming van gebruikers tijdens de aanvraagtijd van een token en vergemakkelijkt de dynamische inrichting van apps tussen tenants

Toestemming is het proces van een broneigenaar die toestemming verleent voor een clienttoepassing om namens de eigenaar van de resource toegang te krijgen tot beveiligde bronnen onder specifieke machtigingen. Het Microsoft-identiteitsplatform:

* Stelt gebruikers en beheerders in staat om de app dynamisch toestemming te geven of weigeren om resources namens hen te gebruiken.
* Stelt beheerders in staat om te beslissen wat apps mogen doen, welke gebruikers gebruik mogen maken van specifieke apps en hoe de directoryresources kunnen worden benaderd.

In het Microsoft-identiteitsplatform beschrijft een [toepassingsobject](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) een toepassing. Op implementatietijd gebruikt het Microsoft-identiteitsplatform het toepassingsobject als blauwdruk om een [serviceprincipal](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)te maken, wat een concreet exemplaar van een toepassing in een map of tenant vertegenwoordigt. De serviceprincipal definieert wat de app daadwerkelijk kan doen in een specifieke doelmap, wie deze kan gebruiken, tot welke bronnen hij toegang heeft, enzovoort. Het Microsoft-identiteitsplatform maakt een serviceprincipal van een toepassingsobject via **toestemming.**

In het volgende diagram ziet u een vereenvoudigde Microsoft-identiteitsplatform voor het inrichten van de stroom die wordt aangestuurd door toestemming. Het toont twee huurders: A en B. Huurder A is eigenaar van de applicatie. Huurder B is de aanvraag aan het instantiën via een serviceprincipal.  

![Vereenvoudigde inrichtingsstroom op basis van toestemming](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

De inrichtingsstroom verloopt als volgt:

1. Een gebruiker van tenant B probeert zich aan te melden met de app, het eindpunt van de autorisatie vraagt een token voor de toepassing aan.
1. De gebruikersreferenties worden verkregen en geverifieerd voor verificatie.
1. De gebruiker wordt gevraagd toestemming te geven voor de app om toegang te krijgen tot tenant B.
1. Het Microsoft-identiteitsplatform gebruikt het toepassingsobject in tenant A als blauwdruk voor het maken van een serviceprincipal in tenant B.
1. De gebruiker ontvangt het gevraagde token.

U dit proces herhalen voor extra tenants. Tenant A behoudt de blauwdruk voor de app (toepassingsobject). Gebruikers en beheerders van alle andere tenants waar de app toestemming krijgt, houden controle over wat de toepassing mag doen via het bijbehorende servicehoofdobject in elke tenant. Zie [Hoofdobjecten voor toepassingen en service in het Microsoft-identiteitsplatform](app-objects-and-service-principals.md)voor meer informatie.

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Aanmeldingsstroom voor webapps met Azure AD

Wanneer een gebruiker in de browser naar een web-app navigeert, gebeurt het volgende:

* De web-app bepaalt of de gebruiker is geverifieerd.
* Als de gebruiker niet is geverifieerd, wordt de web-app gemachtigd naar Azure AD om zich bij de gebruiker aan te melden. Die aanmelding voldoet aan het beleid van de organisatie, wat kan betekenen dat de gebruiker wordt gevraagd om zijn referenties in te voeren, multi-factor-authenticatie te gebruiken of helemaal geen wachtwoord te gebruiken (bijvoorbeeld met Windows Hello).
* De gebruiker wordt gevraagd toestemming te geven voor de toegang die de client-app nodig heeft. Dit is de reden waarom client-apps moeten worden geregistreerd bij Azure AD, zodat Azure AD tokens kan leveren die de toegang vertegenwoordigen waarmee de gebruiker heeft ingestemd.

Wanneer de gebruiker is geverifieerd:

* Azure AD stuurt een token naar de web-app.
* Er wordt een cookie opgeslagen, gekoppeld aan het domein van Azure AD, dat de identiteit van de gebruiker bevat in de cookiepot van de browser. De volgende keer dat een app de browser gebruikt om naar het eindpunt van de Azure AD-autorisatie te navigeren, presenteert de browser de cookie, zodat de gebruiker zich niet opnieuw hoeft aan te melden. Dit is ook de manier waarop SSO wordt bereikt. De cookie wordt geproduceerd door Azure AD en kan alleen worden begrepen door Azure AD.
* De web-app valideert vervolgens het token. Als de validatie slaagt, geeft de web-app de beveiligde pagina weer en slaat een sessiecookie op in de cookiepot van de browser. Wanneer de gebruiker naar een andere pagina navigeert, weet de web-app dat de gebruiker is geverifieerd op basis van de sessiecookie.

In het volgende sequentiediagram wordt deze interactie samengevat:

![verificatieproces voor web-apps](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hoe een web-app bepaalt of de gebruiker is geverifieerd

Ontwikkelaars van webapps kunnen aangeven of alle of alleen bepaalde pagina's verificatie vereisen. In ASP.NET/ASP.NET Core wordt dit bijvoorbeeld gedaan `[Authorize]` door het kenmerk toe te voegen aan de controlleracties. 

Dit kenmerk zorgt ervoor dat ASP.NET controleert op de aanwezigheid van een sessiecookie die de identiteit van de gebruiker bevat. Als een cookie niet aanwezig is, stuurt ASP.NET verificatie door naar de opgegeven identiteitsprovider. Als de identiteitsprovider Azure AD is, leidt `https://login.microsoftonline.com`de web-app verificatie door naar , waarin een aanmeldingsdialoogvenster wordt weergegeven.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Hoe een web-app aanmelding bij Azure AD overdraagt en een token verkrijgt

Gebruikersverificatie gebeurt via de browser. Het OpenID-protocol maakt gebruik van standaard HTTP-protocolberichten.
* De web-app stuurt een HTTP 302 (redirect) naar de browser om Azure AD te gebruiken.
* Wanneer de gebruiker is geverifieerd, stuurt Azure AD het token naar de web-app met behulp van een omleiding via de browser.
* De omleiding wordt geleverd door de web-app in de vorm van een omleiding URI. Deze omleidinguri is geregistreerd met het Azure AD-toepassingsobject. Er kunnen verschillende omleidings-URI's zijn omdat de toepassing op verschillende URL's kan worden geïmplementeerd. Dus de web-app zal ook nodig hebben om de omleiding URI te gebruiken opgeven.
* Azure AD controleert of de omleidinguri die door de web-app wordt verzonden, een van de geregistreerde omleidings-URI's voor de app is.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Aanmeldingsstroom voor desktop- en mobiele apps met Azure AD

De hierboven beschreven stroom is, met kleine verschillen, van toepassing op desktop- en mobiele toepassingen.

Desktop- en mobiele toepassingen kunnen een ingesloten webbesturingselement of een systeembrowser gebruiken voor verificatie. In het volgende diagram ziet u hoe een desktop- of mobiele app de Microsoft-verificatiebibliotheek (MSAL) gebruikt om toegangstokens en oproepweb-API's te verkrijgen.

![Bureaublad-app zoals deze lijkt te zijn](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL gebruikt een browser om tokens te krijgen. Net als bij web-apps wordt verificatie gedelegeerd aan Azure AD.

Omdat Azure AD dezelfde identiteitscookie opslaat in de browser als voor web-apps, krijgt de native of mobiele app als de systeembrowser onmiddellijk SSO krijgt met de bijbehorende web-app.

Msal maakt standaard gebruik van de systeembrowser. De uitzondering is .NET Framework-bureaubladtoepassingen waarbij een ingesloten besturingselement wordt gebruikt om een meer geïntegreerde gebruikerservaring te bieden.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [woordenlijst voor ontwikkelaars van het Microsoft-identiteitsplatform](developer-glossary.md) om vertrouwd te raken met algemene termen.
* Zie [Verificatiestromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over andere scenario's voor het verifiëren van gebruikers die worden ondersteund door het Microsoft-identiteitsplatform.
* Zie [MSAL-bibliotheken](msal-overview.md) voor meer informatie over de Microsoft-bibliotheken waarmee u toepassingen ontwikkelen die werken met Microsoft-accounts, Azure AD-accounts en Azure AD B2C-gebruikers in één gestroomlijnd programmeermodel.
* Zie [App-service integreren met het Microsoft-identiteitsplatform](/azure/app-service/configure-authentication-provider-aad) voor meer informatie over het configureren van verificatie voor uw App Service-app.

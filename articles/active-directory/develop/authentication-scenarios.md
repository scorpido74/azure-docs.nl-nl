---
title: Verificatie in het micro soft Identity-platform | Azure
description: Meer informatie over de basis principes van verificatie in micro soft Identity platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161738"
---
# <a name="authentication-basics"></a>Basisbeginselen van verificatie

In dit artikel worden veel van de verificatie concepten beschreven die u moet begrijpen om beveiligde web-apps, Web-Api's of apps te maken die beveiligde web-Api's aanroepen. Als u een term ziet die u niet kent, probeert u onze [woorden lijst](developer-glossary.md) of onze [Video's over micro soft Identity platform](identity-videos.md) die basis concepten omvatten.

## <a name="authentication-vs-authorization"></a>Verificatie versus autorisatie

**Verificatie** is het proces van waaruit u wordt gedicteerd. Verificatie wordt soms afgekort tot 'AuthN'. Micro soft Identity platform implementeert het [OpenID Connect Connect](https://openid.net/connect/) -protocol voor het afhandelen van authenticatie.

**Autorisatie** is het verlenen van een geverifieerde partij toestemming om iets te doen. Hiermee geeft u op welke gegevens u toegang hebt tot en wat u met die gegevens kunt doen. Autorisatie wordt soms afgekort tot 'AuthZ'. Micro soft Identity platform implementeert het [OAuth 2,0](https://oauth.net/2/) -protocol voor het afhandelen van autorisatie.

In plaats van apps te maken die elk eigen gebruikers naam en wacht woord behouden, waardoor een hoge administratieve belasting optreedt wanneer u gebruikers wilt toevoegen aan of verwijderen uit meerdere apps, kunnen apps die verantwoordelijkheid delegeren aan een gecentraliseerde ID-provider.

Azure Active Directory (Azure AD) is een gecentraliseerde ID-provider in de Cloud. Door de verificatie en autorisatie te delegeren, worden scenario's zoals beleids regels voor voorwaardelijke toegang die een gebruiker in een specifieke locatie moeten, het gebruik van multi-factor Authentication en het inschakelen van een gebruiker in één keer aanmelden en vervolgens automatisch aangemeld bij alle web-apps die dezelfde gecentraliseerde map delen. Deze mogelijkheid wordt **eenmalige aanmelding (SSO)** genoemd.

Micro soft Identity platform vereenvoudigt verificatie en autorisatie voor toepassings ontwikkelaars door identiteit als een service te bieden, met ondersteuning voor industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect, evenals open-source-bibliotheken voor verschillende platformen, zodat u snel kunt beginnen met coderen. Ontwikkel aars kunnen toepassingen bouwen die zich aanmelden bij alle micro soft-identiteiten, tokens verkrijgen om [Microsoft Graph](https://developer.microsoft.com/graph/), andere Api's van micro soft of api's te roepen die ontwikkel aars hebben gebouwd. Zie [ontwikkeling van micro soft Identity platform](about-microsoft-identity-platform.md)voor meer informatie.

## <a name="security-tokens"></a>Beveiligings tokens

Een gecentraliseerde ID-provider is met name belang rijk voor apps die gebruikers op de hele wereld hebben die niet noodzakelijkerwijs zich aanmelden vanuit het bedrijfs netwerk. Micro soft Identity platform verifieert gebruikers en biedt beveiligings tokens, zoals [toegangs token](developer-glossary.md#access-token), [vernieuwings token](developer-glossary.md#refresh-token)en [id-tokens](developer-glossary.md#id-token), waarmee een [client toepassing](developer-glossary.md#client-application) toegang kan krijgen tot beveiligde bronnen op een [bron server](developer-glossary.md#resource-server).

Een **toegangs token** is een beveiligings token dat is uitgegeven door een autorisatie server. Het bevat informatie over de gebruiker en de app waarvoor het token is bedoeld; die kunnen worden gebruikt voor toegang tot Web-Api's en andere beveiligde bronnen. Zie [toegangs tokens](access-tokens.md)voor meer informatie over de wijze waarop micro soft Identity platform toegangs tokens verleent.

Toegangs tokens zijn alleen geldig gedurende korte tijd, waardoor autorisatie servers soms een **vernieuwings token** uitgeven op hetzelfde moment dat het toegangs token wordt uitgegeven. De client toepassing kan dit vernieuwings token vervolgens voor een nieuw toegangs token uitwisselen wanneer dat nodig is. Zie het [intrekken van tokens](access-tokens.md#token-revocation)voor meer informatie over hoe micro soft Identity platform vernieuwings tokens gebruikt om machtigingen in te trekken.

**Id-tokens** worden verzonden naar de client toepassing als onderdeel van een [OpenID Connect Connect](v2-protocols-oidc.md) -stroom. Ze kunnen worden verzonden aan de zijkant of in plaats van een toegangs token en worden door de client gebruikt om de gebruiker te verifiëren. Zie [id-tokens](id-tokens.md)voor meer informatie over het oplossen van problemen met id-tokens van micro soft Identity platform.

### <a name="validating-security-tokens"></a>Beveiligings tokens valideren

Het is de app waarvoor het token is gegenereerd, de web-app die is aangemeld bij de gebruiker of de Web-API die wordt aangeroepen om het token te valideren. Het token is ondertekend door de Security Token server (STS) met een persoonlijke sleutel. De STS publiceert de bijbehorende open bare sleutel. Om een token te valideren, controleert de app de hand tekening met behulp van de open bare sleutel STS om te valideren dat de hand tekening is gemaakt met behulp van de persoonlijke sleutel.

Tokens zijn alleen geldig voor een beperkte periode. Doorgaans biedt de STS een paar tokens:

* Een toegangs token voor toegang tot de toepassing of beveiligde bron en
* Een vernieuwings token dat wordt gebruikt voor het vernieuwen van het toegangs token wanneer het toegangs token bijna is verlopen.

Toegangs tokens worden door gegeven aan een web-API als het Bearer- `Authorization` token in de header. Een app kan een vernieuwings token bieden voor de STS en als de gebruiker de toegang tot de app niet heeft ingetrokken, wordt een nieuw toegangs token en een nieuw vernieuwings token teruggestuurd. Zo wordt het scenario van iemand die de onderneming verlaat, afgehandeld. Wanneer de STS het vernieuwings token ontvangt, wordt er geen ander geldig toegangs token uitgegeven als de gebruiker niet meer is gemachtigd.

### <a name="json-web-tokens-jwts-and-claims"></a>JSON-webtokens (JWTs) en claims

Het micro soft Identity-platform implementeert beveiligings tokens als JSON-webtokens (JWTs) die claims bevatten.

Een [claim](developer-glossary.md#claim) biedt bevestigingen over één entiteit, zoals een client toepassing of [resource-eigenaar](developer-glossary.md#resource-owner), naar een andere entiteit, zoals een resource server.

Claims zijn naam/waarde-paren die feiten over het onderwerp van de token door sturen. Een claim kan bijvoorbeeld feiten bevatten over de beveiligingsprincipal die door de [autorisatie server](developer-glossary.md#authorization-server)is geverifieerd. De claims in een bepaald token zijn afhankelijk van veel dingen, waaronder het type token, het type referentie dat wordt gebruikt voor het verifiëren van het onderwerp, de configuratie van de toepassing, enzovoort.

Toepassingen kunnen claims gebruiken voor verschillende taken, zoals:

* Het token valideren
* De Tenant van het token onderwerp identificeren
* Gebruikers gegevens weer geven
* De autorisatie van het onderwerp bepalen

Een claim bestaat uit sleutel-waardeparen die informatie geven, zoals:

* Beveiligings token server die het token heeft gegenereerd
* Datum waarop het token is gegenereerd
* Onderwerp (zoals de gebruiker, met uitzonde ring van daemons)
* Doel groep: de app waarvoor het token is gegenereerd
* De app (de client) die voor het token is aangevraagd. In het geval van web-apps kan dit hetzelfde zijn als de doel groep

Zie [toegangs tokens](access-tokens.md) en [id-tokens](id-tokens.md)voor meer informatie over het implementeren van tokens en claim informatie door het micro soft Identity-platform.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Hoe elke stroom tokens en codes uitstraalt

Afhankelijk van hoe uw client is gebouwd, kunnen er één (of meerdere) verificatie stromen worden gebruikt die worden ondersteund door het micro soft Identity-platform. Deze stromen kunnen een aantal tokens (id_tokens, vernieuwings tokens, toegangs tokens) en autorisatie codes produceren, en vereisen verschillende tokens om ze te laten werken. Dit diagram bevat een overzicht:

|Stroom | Nodig | id_token | toegangs token | token vernieuwen | autorisatie code |
|-----|----------|----------|--------------|---------------|--------------------|
|[Autorisatie code stroom](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Impliciete stroom](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybride OIDC-stroom](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Aflossingen van token vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token vernieuwen | x | x | x| |
|[Namens-stroom](v2-oauth2-on-behalf-of-flow.md) | toegangs token| x| x| x| |
|[Clientreferenties](v2-oauth2-client-creds-grant-flow.md) | | | x (alleen app)| | |

Tokens die zijn uitgegeven via de impliciete modus, hebben een beperkte lengte omdat ze via de URL worden teruggestuurd naar `response_mode` de `query` browser `fragment`(waarbij of).  Voor sommige browsers geldt een limiet voor de grootte van de URL die in de browser balk kan worden geplaatst en die kan worden uitgevoerd als deze te lang is.  Daarom hebben `groups` deze tokens geen `wids` claims.

## <a name="tenants"></a>Tenants

Een Cloud-ID-provider fungeert als veel organisaties. Om gebruikers van verschillende organisaties gescheiden te houden, wordt Azure AD gepartitioneerd in tenants, met één Tenant per organisatie.

Tenants houden gebruikers en hun bijbehorende apps bij. Micro soft Identity platform biedt ook ondersteuning voor gebruikers die zich aanmelden met persoonlijke micro soft-accounts.

Azure AD biedt ook Azure Active Directory B2C, zodat organisaties zich kunnen aanmelden bij gebruikers, meestal klanten, met behulp van sociale identiteiten, zoals een Google-account. Zie [Azure Active Directory B2C-documentatie](https://docs.microsoft.com/azure/active-directory-b2c) voor meer informatie.

Nu u een overzicht van de basis principes hebt, leest u meer over het identiteits-app-model en de API, leert u hoe Provisioning werkt in het micro soft Identity platform en vindt u koppelingen naar gedetailleerde informatie over algemene scenario's die door micro soft Identity platform worden ondersteund.

## <a name="application-model"></a>Toepassingsmodel

Toepassingen kunnen gebruikers zelf aanmelden of zich aanmelden bij een id-provider. Zie [verificatie stromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over aanmeldings scenario's die worden ondersteund door het micro soft Identity-platform.

Als een id-provider weet dat een gebruiker toegang heeft tot een bepaalde app, moeten zowel de gebruiker als de toepassing zijn geregistreerd bij de ID-provider. Wanneer u uw toepassing registreert bij Azure AD, geeft u een identiteits configuratie op voor uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform. Door de app te registreren, kunt u het volgende doen:

* Pas de huis stijl van uw toepassing aan in het dialoog venster voor aanmelden. Dit is belang rijk omdat dit de eerste ervaring is die een gebruiker heeft met uw app.
* Bepaal of u gebruikers alleen wilt laten aanmelden als ze tot uw organisatie behoren. Dit is één Tenant toepassing. Of sta gebruikers toe om zich aan te melden met een werk-of school account. Dit is een toepassing met meerdere tenants. U kunt ook persoonlijke micro soft-accounts of een sociaal account van LinkedIn, Google, enzovoort toestaan.
* Machtigingen voor het bereik van aanvragen. U kunt bijvoorbeeld het bereik ' gebruiker. read ' aanvragen, dat toestemming geeft om het profiel van de aangemelde gebruiker te lezen.
* Definieer bereiken waarmee de toegang tot uw web-API wordt gedefinieerd. Wanneer een app toegang wil krijgen tot uw API, moet dit doorgaans machtigingen aanvragen voor de bereiken die u definieert.
* Deel een geheim met het micro soft Identity-platform waarmee de identiteit van de app wordt bewezen.  Dit is van belang voor het geval dat de app een vertrouwelijke client toepassing is. Een vertrouwelijke client toepassing is een toepassing die referenties veilig kan bevatten. Er is een vertrouwde back-end-server nodig om de referenties op te slaan.

Na de registratie krijgt de toepassing een unieke id die de App deelt met het micro soft Identity-platform wanneer het tokens aanvraagt. Als de app een [vertrouwelijke client toepassing](developer-glossary.md#client-application)is, deelt deze ook het geheim of de open bare sleutel *, afhankelijk van het feit of er certificaten of geheimen zijn gebruikt.

Micro soft Identity platform vertegenwoordigt toepassingen die gebruikmaken van een model dat voldoet aan twee belang rijke functies:

* Bepaal de app aan de hand van de verificatie protocollen die worden ondersteund
* Geef alle id's, Url's, geheimen en gerelateerde informatie die nodig zijn voor verificatie

Micro soft Identity-platform:

* Bevat alle gegevens die nodig zijn voor de ondersteuning van verificatie tijdens runtime
* Bevat alle gegevens om te bepalen welke resources een app nodig heeft voor toegang en onder welke omstandigheden een bepaalde aanvraag moet worden vervuld
* Biedt een infra structuur voor het implementeren van app-inrichting in de Tenant van de app-ontwikkelaar en op elke andere Azure AD-Tenant
* Verwerkt de toestemming van de gebruiker tijdens de tijd van de token aanvraag en vereenvoudigt de dynamische inrichting van apps op tenants

Toestemming is het proces van een resource-eigenaar die toestemming verleent voor een client toepassing om toegang te krijgen tot beveiligde bronnen, onder specifieke machtigingen, namens de eigenaar van de resource. Micro soft Identity-platform:

* Stelt gebruikers en beheerders in staat om de app dynamisch toestemming te geven of weigeren om resources namens hen te gebruiken.
* Stelt beheerders in staat om te beslissen wat apps mogen doen, welke gebruikers gebruik mogen maken van specifieke apps en hoe de directoryresources kunnen worden benaderd.

In het micro soft Identity-platform beschrijft een [toepassings object](developer-glossary.md#application-object) een toepassing. Tijdens de implementatie gebruikt micro soft Identity platform het object Application als een blauw druk om een [Service-Principal](developer-glossary.md#service-principal-object)te maken. Dit is een concreet exemplaar van een toepassing in een directory of Tenant. De Service-Principal definieert wat de app daad werkelijk kan doen in een specifieke doel directory, wie deze kan gebruiken, tot welke resources toegang heeft, enzovoort. Met micro soft Identity platform maakt u via **toestemming**een service-principal van een toepassings object.

Het volgende diagram toont een vereenvoudigd micro soft Identity platform inrichtings stroom op basis van toestemming. Er worden twee tenants weer gegeven: *A* en *B*.

* *Tenant A* is eigenaar van de toepassing.
* *Tenant B* is een instantiëring van de toepassing via een service-principal.

![Vereenvoudigde inrichtingsstroom op basis van toestemming](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

De inrichtingsstroom verloopt als volgt:

1. Een gebruiker van Tenant B probeert zich aan te melden bij de app. het autorisatie-eind punt vraagt een token voor de toepassing aan.
1. De gebruikers referenties zijn verkregen en geverifieerd voor authenticatie.
1. De gebruiker wordt gevraagd toestemming te geven voor de app om toegang te krijgen tot Tenant B.
1. Micro soft Identity platform gebruikt het toepassings object in Tenant A als een blauw druk voor het maken van een Service-Principal in Tenant B.
1. De gebruiker ontvangt het aangevraagde token.

U kunt dit proces herhalen voor aanvullende tenants. Tenant A behoudt de blauw druk voor de app (toepassings object). Gebruikers en beheerders van alle andere tenants waar de app toestemming krijgt, houden de controle over wat de toepassing mag doen via het bijbehorende service-principal-object in elke Tenant. Zie [Application and Service Principal Objects in micro soft Identity platform](app-objects-and-service-principals.md)(Engelstalig) voor meer informatie.

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Aanmeldings stroom voor web-apps met micro soft Identity platform

Wanneer een gebruiker in de browser naar een web-app navigeert, gebeurt het volgende:

* De web-app bepaalt of de gebruiker is geverifieerd.
* Als de gebruiker niet is geverifieerd, wordt de web-app gemachtigd om zich aan te melden bij de gebruiker in azure AD. Deze aanmelding voldoet aan het beleid van de organisatie. Dit kan ertoe leiden dat de gebruiker de referenties invoert, met behulp van multi-factor Authentication of met een wacht woord helemaal (bijvoorbeeld met Windows hello).
* De gebruiker wordt gevraagd toestemming te geven voor de toegang die de client-app nodig heeft. Daarom moeten client-apps worden geregistreerd bij Azure AD, zodat het micro soft Identity-platform tokens kan leveren die de toegang hebben die de gebruiker heeft ingestemd.

Wanneer de gebruiker is geverifieerd:

* Micro soft Identity platform verzendt een token naar de web-app.
* Er wordt een cookie opgeslagen dat is gekoppeld aan het domein van Azure AD. Dit bevat de identiteit van de gebruiker in het cookie jar van de browser. De volgende keer dat een app de browser gebruikt om te navigeren naar het micro soft Identity platform Authorization-eind punt, geeft de browser de cookie weer zodat de gebruiker zich niet opnieuw hoeft aan te melden. Dit is ook de manier waarop SSO wordt behaald. De cookie wordt gemaakt door Azure AD en kan alleen worden geïnterpreteerd door Azure AD.
* De web-app valideert vervolgens het token. Als de validatie slaagt, wordt de beveiligde pagina weer gegeven in de web-app en wordt een sessie cookie in het cookie jar van de browser opgeslagen. Wanneer de gebruiker naar een andere pagina navigeert, weet de web-app dat de gebruiker is geverifieerd op basis van de sessie cookie.

In het volgende sequentie diagram wordt deze interactie samenvatten:

![verificatie proces van de web-app](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hoe een web-app bepaalt of de gebruiker is geverifieerd

Web app-ontwikkel aars kunnen aangeven of voor alle of alleen bepaalde pagina's verificatie is vereist. In ASP.NET/ASP.NET core wordt dit bijvoorbeeld gedaan door het `[Authorize]` kenmerk toe te voegen aan de controller acties.

Met dit kenmerk wordt ASP.NET gecontroleerd op de aanwezigheid van een sessie cookie met de identiteit van de gebruiker. Als er geen cookie aanwezig is, stuurt ASP.NET de verificatie door naar de opgegeven id-provider. Als de ID-provider Azure AD is, wordt de verificatie door de web- `https://login.microsoftonline.com`app omgeleid naar, waardoor een dialoog venster voor aanmelden wordt weer gegeven.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Hoe een web-app zich kan aanmelden bij het micro soft Identity-platform en een token verkrijgt

Gebruikers verificatie gebeurt via de browser. Het OpenID Connect-protocol gebruikt standaard berichten van het HTTP-protocol.

* De web-app stuurt een HTTP 302 (omleiding) naar de browser om het micro soft Identity-platform te gebruiken.
* Wanneer de gebruiker is geverifieerd, verzendt het micro soft Identity-platform het token naar de web-app met behulp van een omleiding via de browser.
* De omleiding wordt verzorgd door de web-app in de vorm van een omleidings-URI. Deze omleidings-URI is geregistreerd bij het Azure AD-toepassings object. Er kunnen verschillende omleidings-Uri's zijn omdat de toepassing kan worden geïmplementeerd op verschillende Url's. De web-app moet dus ook de omleidings-URI opgeven die moet worden gebruikt.
* Azure AD controleert of de omleidings-URI die door de web-app is verzonden, een van de geregistreerde omleidings-Uri's voor de app is.

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>Aanmeldings procedure voor Desk tops en mobiele apps met micro soft Identity platform

De hierboven beschreven stroom geldt, met kleine verschillen, voor desktop-en mobiele toepassingen.

Desktop-en mobiele toepassingen kunnen een Inge sloten Webbe sturings element of een systeem browser gebruiken voor verificatie. In het volgende diagram ziet u hoe een bureau blad of een mobiele app de micro soft Authentication Library (MSAL) gebruikt om toegangs tokens te verkrijgen en Web-Api's aan te roepen.

![Bureau blad-app hoe deze eruitziet](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL maakt gebruik van een browser om tokens op te halen. Net als bij Web-apps wordt de verificatie overgedragen aan het micro soft Identity-platform.

Omdat Azure AD dezelfde identiteits cookie in de browser opslaat als voor web-apps, wordt door de systeem eigen of de mobiele app, direct SSO met de bijbehorende Web-app, gebruikt.

MSAL maakt standaard gebruik van de systeem browser. De uitzonde ring is .NET Framework desktop toepassingen waarbij een Inge sloten besturings element wordt gebruikt om een meer geïntegreerde gebruikers ervaring te bieden.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [verklarende woorden lijst voor ontwikkel aars van micro soft Identity platform](developer-glossary.md) om vertrouwd te raken met algemene termen.
* Zie [verificatie stromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over andere scenario's voor het verifiëren van gebruikers die worden ondersteund door het micro soft Identity-platform.
* Zie [MSAL-bibliotheken](msal-overview.md) voor meer informatie over de micro soft-bibliotheken die u helpen bij het ontwikkelen van toepassingen die werken met micro soft-accounts, Azure AD-accounts en Azure AD B2C gebruikers in één gestroomlijnd programmeer model.
* Zie [app service integreren met het micro soft Identity-platform](/azure/app-service/configure-authentication-provider-aad) voor meer informatie over het configureren van verificatie voor uw app service-app.

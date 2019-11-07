---
title: Verificatie in het micro soft Identity-platform | Azure
description: Meer informatie over de basis principes van verificatie in het micro soft Identity platform (v 2.0).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ab2180c54f07ff5009e2c57d8522f2eb0b81aad
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718382"
---
# <a name="authentication-basics"></a>Basisbeginselen van verificatie

## <a name="what-is-authentication"></a>Wat is verificatie?

In dit artikel worden veel van de verificatie concepten beschreven die u moet begrijpen om beveiligde web-apps, Web-Api's of apps te maken die beveiligde web-Api's aanroepen.

**Verificatie** is het proces van waaruit u wordt gedicteerd. Verificatie wordt soms afgekort tot 'AuthN'.

**Autorisatie** is het verlenen van een geverifieerde partij toestemming om iets te doen. Hiermee geeft u op welke gegevens u toegang hebt tot en wat u met die gegevens kunt doen. Autorisatie wordt soms afgekort tot 'AuthZ'.

In plaats van apps te maken die elk eigen gebruikers naam en wacht woord behouden, waardoor een hoge administratieve belasting optreedt wanneer u gebruikers wilt toevoegen aan of verwijderen uit meerdere apps, kunnen apps die verantwoordelijkheid delegeren aan een gecentraliseerde ID-provider.

Azure Active Directory (Azure AD) is een gecentraliseerde ID-provider in de Cloud. Door de verificatie en autorisatie te delegeren, worden scenario's zoals beleids regels voor voorwaardelijke toegang die een gebruiker in een specifieke locatie moeten, het gebruik van multi-factor Authentication en het inschakelen van een gebruiker in één keer aanmelden en vervolgens automatisch aangemeld bij alle web-apps die dezelfde gecentraliseerde map delen. Deze mogelijkheid wordt eenmalige aanmelding (SSO) genoemd.

Een gecentraliseerde ID-provider is nog belang rijker voor apps die over de hele wereld beschikken over de locatie waar ze zich niet hoeven aan te melden vanuit het netwerk van de onderneming. Azure AD verifieert gebruikers en biedt toegangs tokens. Een toegangs token is een beveiligings token dat is uitgegeven door een autorisatie server. Het bevat informatie over de gebruiker en de app waarvoor het token is bedoeld, dat kan worden gebruikt voor toegang tot Web-Api's en andere beveiligde bronnen.

Het micro soft Identity-platform vereenvoudigt de verificatie voor toepassings ontwikkelaars door identiteit als service te bieden, met ondersteuning voor industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect, evenals open-source bibliotheken voor verschillende platforms die u helpen bij het snel starten van code ring. Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft Graph, andere Microsoft-API's of API's die door ontwikkelaars zijn gemaakt. Zie [ontwikkeling van micro soft Identity platform](about-microsoft-identity-platform.md)voor meer informatie.

### <a name="tenants"></a>Tenants

Een Cloud-ID-provider fungeert als veel organisaties. Om gebruikers van verschillende organisaties gescheiden te houden, wordt Azure AD gepartitioneerd in tenants, met één Tenant per organisatie.

Tenants houden gebruikers en hun bijbehorende apps bij. Het micro soft Identity-platform biedt ook ondersteuning voor gebruikers die zich aanmelden met persoonlijke micro soft-accounts.

Azure AD biedt ook Azure Active Directory B2C, zodat organisaties zich kunnen aanmelden bij gebruikers, meestal klanten, met behulp van sociale identiteiten, zoals een Google-account. Zie [Azure Active Directory B2C-documentatie](https://docs.microsoft.com/azure/active-directory-b2c) voor meer informatie.

### <a name="security-tokens"></a>Beveiligings tokens

Beveiligings tokens bevatten informatie over gebruikers en apps. Azure AD maakt gebruik van JSON-tokens (JWTs) die claims bevatten. Een claim biedt bevestigingen over één entiteit naar een andere. Toepassingen kunnen claims gebruiken voor verschillende taken, zoals:

* Het token valideren
* De Directory Tenant van de houder identificeren
* Gebruikers gegevens weer geven
* De autorisatie van het onderwerp bepalen

Een claim bestaat uit sleutel-waardeparen die informatie geven, zoals:

- de beveiligings token server die het token heeft gegenereerd.
- de datum waarop het token is gegenereerd.
- het onderwerp, zoals de gebruiker (met uitzonde ring van daemons).
- de doel groep, de app waarvoor het token is gegenereerd.
- de app (de client) die voor het token is aangevraagd. In het geval van web-apps kan dit hetzelfde zijn als de doel groep.

Zie het [toegangs tokens](access-tokens.md) en de [id-tokens](id-tokens.md)voor meer gedetailleerde informatie over claims.

Het is de app waarvoor het token is gegenereerd, de web-app die is aangemeld bij de gebruiker of de Web-API die wordt aangeroepen om het token te valideren. Het token is ondertekend door de Security Token server (STS) met een persoonlijke sleutel. De STS publiceert de bijbehorende open bare sleutel. Om een token te valideren, controleert de app de hand tekening met behulp van de open bare sleutel STS om te valideren dat de hand tekening is gemaakt met behulp van de persoonlijke sleutel.

Tokens zijn alleen geldig voor een beperkte periode. De STS biedt doorgaans een paar tokens: een toegangs token voor toegang tot de toepassing of beveiligde resource, en een vernieuwings token dat wordt gebruikt om het toegangs token te vernieuwen wanneer het toegangs token bijna is verlopen. 

Toegangs tokens worden door gegeven aan een web-API als het Bearer-token in de `Authenticate`-header. Een app kan een vernieuwings token bieden voor de STS en als de gebruiker de toegang tot de app niet heeft ingetrokken, wordt een nieuw toegangs token en een nieuw vernieuwings token teruggestuurd. Zo wordt het scenario van iemand die de onderneming verlaat, afgehandeld. Wanneer de STS het vernieuwings token ontvangt, wordt er geen ander geldig toegangs token uitgegeven als de gebruiker niet meer is gemachtigd.

## <a name="application-model"></a>Toepassingsmodel

Toepassingen kunnen gebruikers zelf aanmelden of zich aanmelden bij een id-provider. Zie [verificatie stromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over aanmeldings scenario's die worden ondersteund door Azure AD.

Als een id-provider weet dat een gebruiker toegang heeft tot een bepaalde app, moeten zowel de gebruiker als de toepassing zijn geregistreerd bij de ID-provider. Wanneer u uw toepassing registreert bij Azure AD, geeft u een identiteits configuratie op voor uw toepassing, zodat deze kan worden geïntegreerd met Azure AD. Door de app te registreren, kunt u het volgende doen:

- Pas de huis stijl van uw toepassing aan in het dialoog venster voor aanmelden. Dit is belang rijk omdat dit de eerste ervaring is die een gebruiker heeft met uw app.
- Bepaal of u gebruikers alleen wilt laten aanmelden als ze tot uw organisatie behoren. Dit is één Tenant toepassing. Of sta gebruikers toe om zich aan te melden met een werk-of school account. Dit is een toepassing met meerdere tenants. U kunt ook persoonlijke micro soft-accounts of een sociaal account van LinkedIn, Google, enzovoort toestaan.
- machtigingen voor het bereik van aanvragen. U kunt bijvoorbeeld het bereik ' gebruiker. read ' aanvragen, dat toestemming geeft om het profiel van de aangemelde gebruiker te lezen.
- Definieer bereiken waarmee de toegang tot uw web-API wordt gedefinieerd. Wanneer een app toegang wil krijgen tot uw API, moet dit doorgaans machtigingen aanvragen voor de bereiken die u definieert.
- deel een geheim met Azure AD om de identiteit van de app aan Azure AD te bewijzen.  Dit is van belang voor het geval dat de app een vertrouwelijke client toepassing is. Een vertrouwelijke client toepassing is een toepassing die referenties veilig kan bevatten. Er is een vertrouwde back-end-server nodig om de referenties op te slaan.

Na de registratie krijgt de toepassing een GUID die de App deelt met Azure AD wanneer tokens worden aangevraagd. Als de app een vertrouwelijk client toepassing is, wordt ook het geheim of de open bare sleutel gedeeld, afhankelijk van het feit of er certificaten of geheimen zijn gebruikt.

Het micro soft Identity-platform vertegenwoordigt toepassingen die gebruikmaken van een model dat voldoet aan twee belang rijke functies:

Bepaal de app aan de hand van de verificatie protocollen die worden ondersteund en geef alle id's, Url's, geheimen en gerelateerde informatie op die nodig zijn voor de verificatie.
Het micro soft Identity-platform:

* Bevat alle gegevens die nodig zijn voor de ondersteuning van verificatie tijdens runtime.
* Bevat alle gegevens om te bepalen welke resources een app nodig heeft voor toegang en onder welke omstandigheden een bepaalde aanvraag moet worden afgehandeld.
* Biedt een infra structuur voor het implementeren van app-inrichting in de Tenant van de app-ontwikkelaar en op elke andere Azure AD-Tenant.
* Verwerkt de toestemming van de gebruiker tijdens de tijd van de token aanvraag en vereenvoudigt de dynamische inrichting van apps op tenants

Toestemming is het proces van een resource-eigenaar die toestemming verleent voor een client toepassing om toegang te krijgen tot beveiligde bronnen, onder specifieke machtigingen, namens de eigenaar van de resource. Het micro soft Identity-platform:

* Stelt gebruikers en beheerders in staat om de app dynamisch toestemming te geven of weigeren om resources namens hen te gebruiken.
* Stelt beheerders in staat om te beslissen wat apps mogen doen, welke gebruikers gebruik mogen maken van specifieke apps en hoe de directoryresources kunnen worden benaderd.

In het micro soft Identity-platform beschrijft een **toepassings object** een toepassing als een abstracte entiteit. Tijdens de implementatie gebruikt het micro soft Identity-platform het object van de toepassing als een blauw druk om een **Service-Principal**te maken, die een concreet exemplaar van een toepassing in een directory of Tenant vertegenwoordigt. De Service-Principal definieert wat de app daad werkelijk kan doen in een specifieke doel directory, wie deze kan gebruiken, tot welke resources toegang heeft, enzovoort. Met het micro soft Identity-platform maakt u via **toestemming**een service-principal van een toepassings object.

Het volgende diagram toont een vereenvoudigd micro soft Identity platform inrichtings stroom op basis van toestemming. Er worden twee tenants weer gegeven (A en B). Tenant A is eigenaar van de toepassing. Tenant B is een instantiëring van de toepassing via een service-principal.  

![Vereenvoudigde inrichtingsstroom op basis van toestemming](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

De inrichtingsstroom verloopt als volgt:

1. Een gebruiker van Tenant B probeert zich aan te melden bij de app. het autorisatie-eind punt vraagt een token voor de toepassing aan.
1. De gebruikers referenties zijn verkregen en geverifieerd voor authenticatie.
1. De gebruiker wordt gevraagd toestemming te geven voor de app om toegang te krijgen tot Tenant B.
1. Het micro soft Identity-platform gebruikt het toepassings object in Tenant A als een blauw druk voor het maken van een Service-Principal in Tenant B.
1. De gebruiker ontvangt het aangevraagde token.

U kunt dit proces herhalen voor aanvullende tenants. Tenant A behoudt de blauw druk voor de app (toepassings object). Gebruikers en beheerders van alle andere tenants waar de app toestemming krijgt, houden de controle over wat de toepassing mag doen via het bijbehorende service-principal-object in elke Tenant. Zie [Application and Service Principal Objects in micro soft Identity platform](app-objects-and-service-principals.md)(Engelstalig) voor meer informatie.

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Aanmeldings stroom voor web-apps met Azure AD

Wanneer een gebruiker in de browser naar een web-app navigeert, gebeurt het volgende:

- De web-app bepaalt of de gebruiker is geverifieerd.
- Als de gebruiker niet is geverifieerd, wordt de web-app gemachtigd om zich aan te melden bij de gebruiker in azure AD. Deze aanmelding voldoet aan het beleid van de organisatie. Dit kan ertoe leiden dat de gebruiker de referenties invoert, met behulp van multi-factor Authentication of met een wacht woord helemaal (bijvoorbeeld met Windows hello).
- De gebruiker wordt gevraagd toestemming te geven voor de toegang die de client-app nodig heeft. Daarom moeten client-apps worden geregistreerd bij Azure AD, zodat Azure AD tokens kan leveren die de toegang hebben die de gebruiker heeft ingestemd.

Wanneer de gebruiker is geverifieerd:

- Azure AD verzendt een token naar de web-app.
- Er wordt een cookie opgeslagen dat is gekoppeld aan het domein van Azure AD. Dit bevat de identiteit van de gebruiker in het cookie jar van de browser. De volgende keer dat een app de browser gebruikt om te navigeren naar het eind punt van de Azure AD-autorisatie, geeft de browser de cookie weer zodat de gebruiker zich niet opnieuw hoeft aan te melden. Dit is ook de manier waarop SSO wordt behaald. De cookie wordt gemaakt door Azure AD en kan alleen worden geïnterpreteerd door Azure AD.
- De web-app valideert vervolgens het token. Als de validatie slaagt, wordt de beveiligde pagina weer gegeven in de web-app en wordt een sessie cookie in het cookie jar van de browser opgeslagen. Wanneer de gebruiker naar een andere pagina navigeert, weet de web-app dat de gebruiker is geverifieerd op basis van de sessie cookie.

In het volgende sequentie diagram wordt deze interactie samenvatten:

![verificatie proces van de web-app](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hoe een web-app bepaalt of de gebruiker is geverifieerd

Web app-ontwikkel aars kunnen aangeven of voor alle of alleen bepaalde pagina's verificatie is vereist. In ASP.NET/ASP.NET core wordt dit bijvoorbeeld gedaan door het kenmerk `[Authorize]` toe te voegen aan de controller acties. 

Met dit kenmerk wordt ASP.NET gecontroleerd op de aanwezigheid van een sessie cookie met de identiteit van de gebruiker. Als er geen cookie aanwezig is, stuurt ASP.NET de verificatie door naar de opgegeven id-provider. Als de ID-provider Azure AD is, wordt de verificatie door de web-app omgeleid naar https://login.microsoftonline.com, waarin een dialoog venster voor aanmelden wordt weer gegeven.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Hoe een web-app zich aanmeldt bij Azure AD en een token verkrijgt

Gebruikers verificatie gebeurt via de browser. Het OpenID Connect-protocol gebruikt standaard berichten van het HTTP-protocol.
- De web-app stuurt een HTTP 202 (omleiding) naar de browser om Azure AD te gebruiken.
- Wanneer de gebruiker is geverifieerd, verzendt Azure AD het token naar de web-app met behulp van een omleiding via de browser.
- De omleiding wordt verzorgd door de web-app in de vorm van een omleidings-URI. Deze omleidings-URI is geregistreerd bij het Azure AD-toepassings object. Er kunnen verschillende omleidings-Uri's zijn omdat de toepassing kan worden geïmplementeerd op verschillende Url's. De web-app moet dus ook de omleidings-URi opgeven die moet worden gebruikt.
- Azure AD controleert of de omleidings-URI die door de web-app is verzonden, een van de geregistreerde omleidings-Uri's voor de app is.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Aanmeldings procedure voor Desk tops en mobiele apps met Azure AD

De hierboven beschreven stroom geldt, met kleine verschillen, voor desktop-en mobiele toepassingen.

Desktop-en mobiele toepassingen kunnen een Inge sloten Webbe sturings element of een systeem browser gebruiken voor verificatie. In het volgende diagram ziet u hoe een bureau blad of een mobiele app de micro soft Authentication Library (MSAL) gebruikt om toegangs tokens te verkrijgen en Web-Api's aan te roepen.

![Bureau blad-app hoe deze eruitziet](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

MSAL maakt gebruik van een browser voor het ophalen van tokens en net als bij Web apps, delegeert verificatie aan Azure AD.

Omdat Azure AD dezelfde identiteits cookie in de browser opslaat als voor web-apps, wordt door de systeem eigen of de mobiele app, direct SSO met de bijbehorende Web-app, gebruikt.

MSAL maakt standaard gebruik van de systeem browser, met uitzonde ring van .NET Framework desktop toepassingen waarin een Inge sloten besturings element wordt gebruikt om een meer geïntegreerde gebruikers ervaring te bieden.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [verklarende woorden lijst voor ontwikkel aars van micro soft Identity platform](developer-glossary.md) om vertrouwd te raken met algemene termen.
Zie [verificatie stromen en app-scenario's](authentication-flows-app-scenarios.md) voor meer informatie over andere scenario's voor het verifiëren van gebruikers die worden ondersteund door het micro soft Identity-platform.
Zie [MSAL-bibliotheken](msal-overview.md) voor meer informatie over de micro soft-bibliotheken die u helpen bij het ontwikkelen van toepassingen die werken met micro soft-accounts, Azure AD-accounts en Azure AD B2C gebruikers in één gestroomlijnd programmeer model.

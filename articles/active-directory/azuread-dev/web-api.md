---
title: Web API-apps in Azure Active Directory
description: Beschrijft wat web API-toepassingen zijn en de basisprincipes op protocolstroom, registratie en token verloop voor dit app-type.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154420"
---
# <a name="web-api"></a>Web-API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web API-apps zijn webtoepassingen die resources uit een web-API moeten halen. In dit scenario zijn er twee identiteitstypen die de webtoepassing kan gebruiken om de web-API te verifiëren en aan te roepen:

- **Toepassingsidentiteit** - In dit scenario wordt oauth 2.0-clientreferenties toegekend om te verifiëren als de toepassing en toegang te krijgen tot de web-API. Bij het gebruik van een toepassingsidentiteit kan de web-API alleen detecteren dat de webtoepassing deze aanroept, omdat de web-API geen informatie over de gebruiker ontvangt. Als de toepassing informatie over de gebruiker ontvangt, wordt deze verzonden via het toepassingsprotocol en wordt deze niet ondertekend door Azure AD. De web-API vertrouwt erop dat de webtoepassing de gebruiker heeft geverifieerd. Om deze reden wordt dit patroon een vertrouwd subsysteem genoemd.
- **Gedelegeerde gebruikersidentiteit** - Dit scenario kan op twee manieren worden uitgevoerd: OpenID Connect en OAuth 2.0-autorisatiecodeverlening met een vertrouwelijke client. De webtoepassing verkrijgt een toegangstoken voor de gebruiker, wat aan de web-API bewijst dat de gebruiker met succes is geverifieerd naar de webtoepassing en dat de webtoepassing een gedelegeerde gebruikersidentiteit heeft kunnen verkrijgen om de web-API aan te roepen. Dit toegangstoken wordt in de aanvraag verzonden naar de web-API, die de gebruiker autoriseert en de gewenste bron retourneert.

Zowel de identiteit van de toepassing als de gedelegeerde gebruikersidentiteitstypen worden in de onderstaande stroom besproken. Het belangrijkste verschil tussen hen is dat de gedelegeerde gebruikersidentiteit eerst een autorisatiecode moet verkrijgen voordat de gebruiker zich kan aanmelden en toegang kan krijgen tot de web-API.

## <a name="diagram"></a>Diagram

![Webapplication naar Web API-diagram](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Protocolstroom

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Toepassingsidentiteit met OAuth 2.0 clientreferenties verlenen

1. Een gebruiker is aangemeld bij Azure AD in de webtoepassing (zie de sectie **Web-apps** voor meer informatie).
1. De webtoepassing moet een toegangstoken aanschaffen, zodat deze kan worden geverifieerd naar de web-API en de gewenste bron kan ophalen. Het maakt een verzoek tot het tokeneindpunt van Azure AD, met de referentie- en toepassings-id en de toepassings-ID URI van de web-API.
1. Azure AD verifieert de toepassing en retourneert een JWT-toegangstoken dat wordt gebruikt om de web-API aan te roepen.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangstoken om de JWT-tekenreeks toe te voegen met een aanduiding 'Drager' in de autorisatiekop van de aanvraag aan de web-API. De web-API valideert vervolgens het JWT-token en als de validatie succesvol is, retourneert de gewenste bron.

### <a name="delegated-user-identity-with-openid-connect"></a>Gedelegeerde gebruikersidentiteit met OpenID Connect

1. Een gebruiker is aangemeld bij een webtoepassing met Azure AD (zie de sectie Webbrowser naar webtoepassing hierboven). Als de gebruiker van de webtoepassing nog niet heeft ingestemd met het toestaan van de webtoepassing om de web-API namens de toepassing te bellen, moet de gebruiker toestemming geven. De toepassing geeft de machtigingen weer die het vereist, en als een van deze machtigingen op administratorniveau zijn, kan een normale gebruiker in de map geen toestemming geven. Dit toestemmingsproces is alleen van toepassing op toepassingen met meerdere tenants, niet op toepassingen met één tenant, omdat de toepassing al over de benodigde machtigingen beschikt. Toen de gebruiker zich aanmeldde, ontving de webtoepassing een ID-token met informatie over de gebruiker en een autorisatiecode.
1. Met behulp van de autorisatiecode die is uitgegeven door Azure AD, stuurt de webtoepassing een aanvraag naar het tokeneindpunt van Azure AD met de autorisatiecode, details over de clienttoepassing (toepassings-id en omleidinguri) en de gewenste bron (toepassings-id URI voor de web-API).
1. De autorisatiecode en informatie over de webtoepassing en web-API worden gevalideerd door Azure AD. Bij succesvolle validatie retourneert Azure AD twee tokens: een JWT-toegangstoken en een JWT-vernieuwingstoken.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangstoken om de JWT-tekenreeks toe te voegen met een aanduiding 'Drager' in de autorisatiekop van de aanvraag aan de web-API. De web-API valideert vervolgens het JWT-token en als de validatie succesvol is, retourneert de gewenste bron.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Gedelegeerde gebruikersidentiteit met OAuth 2.0-autorisatiecodeverlening

1. Een gebruiker is al aangemeld bij een webtoepassing waarvan het verificatiemechanisme onafhankelijk is van Azure AD.
1. De webtoepassing vereist een autorisatiecode om een toegangstoken te verkrijgen, dus het geeft een aanvraag via de browser uit aan het autorisatieeindpunt van Azure AD, waarbij de toepassings-id wordt verstrekt en URI wordt omgeleid voor de webtoepassing na succesvolle verificatie. De gebruiker meldt zich aan bij Azure AD.
1. Als de gebruiker van de webtoepassing nog niet heeft ingestemd met het toestaan van de webtoepassing om de web-API namens de toepassing te bellen, moet de gebruiker toestemming geven. De toepassing geeft de machtigingen weer die het vereist, en als een van deze machtigingen op administratorniveau zijn, kan een normale gebruiker in de map geen toestemming geven. Deze toestemming is van toepassing op zowel single- als multi-tenant toepassing. In de afzonderlijke tenantcase kan een beheerder toestemming geven voor toestemming namens zijn of haar gebruikers. Dit kan met `Grant Permissions` de knop in de [Azure-portal.](https://portal.azure.com) 
1. Nadat de gebruiker toestemming heeft gegeven, ontvangt de webtoepassing de autorisatiecode die het nodig heeft om een toegangstoken te verkrijgen.
1. Met behulp van de autorisatiecode die is uitgegeven door Azure AD, stuurt de webtoepassing een aanvraag naar het tokeneindpunt van Azure AD met de autorisatiecode, details over de clienttoepassing (toepassings-id en omleidinguri) en de gewenste bron (toepassings-id URI voor de web-API).
1. De autorisatiecode en informatie over de webtoepassing en web-API worden gevalideerd door Azure AD. Bij succesvolle validatie retourneert Azure AD twee tokens: een JWT-toegangstoken en een JWT-vernieuwingstoken.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangstoken om de JWT-tekenreeks toe te voegen met een aanduiding 'Drager' in de autorisatiekop van de aanvraag aan de web-API. De web-API valideert vervolgens het JWT-token en als de validatie succesvol is, retourneert de gewenste bron.

## <a name="code-samples"></a>Codevoorbeelden

Zie de codevoorbeelden voor webtoepassingen voor webAPI-scenario's. En, kom regelmatig terug - nieuwe monsters worden vaak toegevoegd. Webtoepassing [naar web-API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>App-registratie

Zie [Een app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)registreren als u een toepassing wilt registreren bij het Azure AD v1.0-eindpunt.

* Eén tenant - Voor zowel de toepassingsidentiteit als de gedelegeerde gebruikersidentiteitsaanvragen moeten de webtoepassing en de web-API worden geregistreerd in dezelfde map in Azure AD. De web-API kan worden geconfigureerd om een set machtigingen bloot te leggen, die worden gebruikt om de toegang van de webtoepassing tot de bronnen te beperken. Als een gedelegeerd gebruikersidentiteitstype wordt gebruikt, moet de webtoepassing de gewenste machtigingen selecteren van het vervolgkeuzemenu **Machtigingen voor andere toepassingen** in de Azure-portal. Deze stap is niet vereist als het type toepassingsidentiteit wordt gebruikt.
* Multi-tenant - Ten eerste is de webtoepassing geconfigureerd om aan te geven welke machtigingen nodig zijn om functioneel te zijn. Deze lijst met vereiste machtigingen wordt weergegeven in een dialoogvenster wanneer een gebruiker of beheerder in de doelmap toestemming geeft voor de toepassing, waardoor deze beschikbaar is voor hun organisatie. Voor sommige toepassingen zijn alleen machtigingen op gebruikersniveau vereist, waarmee elke gebruiker in de organisatie kan instemmen. Voor andere toepassingen zijn machtigingen op administratorniveau vereist, waar een gebruiker in de organisatie geen toestemming voor kan geven. Alleen een directorybeheerder kan toestemming geven voor toepassingen waarvoor dit niveau van machtigingen vereist is. Wanneer de gebruiker of beheerder hiermee instemt, worden de webtoepassing en de web-API beide geregistreerd in hun directory.

## <a name="token-expiration"></a>Token vervaldatum

Wanneer de webtoepassing de autorisatiecode gebruikt om een JWT-toegangstoken te krijgen, ontvangt deze ook een JWT-vernieuwingstoken. Wanneer het toegangstoken verloopt, kan het vernieuwingstoken worden gebruikt om de gebruiker opnieuw te verifiëren zonder dat deze opnieuw moet inloggen. Deze vernieuwingstoken wordt vervolgens gebruikt om de gebruiker te verifiëren, wat resulteert in een nieuw toegangstoken en vernieuwingstoken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [toepassingstypen en -scenario's](app-types.md)
- Meer informatie over de [basisprincipes](v1-authentication-scenarios.md) van Azure AD-verificatie

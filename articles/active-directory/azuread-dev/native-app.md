---
title: Native-apps in Azure Active Directory
description: Beschrijft wat native apps zijn en de basisprincipes op protocolstroom, registratie en token verloop voor dit app-type.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154794"
---
# <a name="native-apps"></a>Systeemeigen apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Native apps zijn toepassingen die namens een gebruiker een web-API aanroepen. Dit scenario is gebaseerd op het type OAuth 2.0-autorisatiecodeverlening met een openbare client, zoals beschreven in punt 4.1 van de [OAuth 2.0-specificatie](https://tools.ietf.org/html/rfc6749). De native applicatie verkrijgt een access token voor de gebruiker met behulp van het OAuth 2.0 protocol. Dit toegangstoken wordt vervolgens in de aanvraag verzonden naar de web-API, die de gebruiker autoriseert en de gewenste bron retourneert.

## <a name="diagram"></a>Diagram

![Native Application to Web API Diagram](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Protocolstroom

Als u de AD-verificatiebibliotheken gebruikt, worden de meeste hieronder beschreven protocolgegevens voor u verwerkt, zoals de pop-up van de browser, tokencaching en het verwerken van vernieuwingstokens.

1. Met behulp van een browserpop-up maakt de native toepassing een aanvraag voor het autorisatieeindpunt in Azure AD. Deze aanvraag bevat de toepassings-id en de omleidinguri van de native toepassing zoals weergegeven in de Azure-portal en de toepassings-ID URI voor de web-API. Als de gebruiker zich nog niet heeft aangemeld, wordt hij of zij gevraagd zich opnieuw aan te melden
1. Azure AD verifieert de gebruiker. Als het een multi-tenant applicatie is en toestemming nodig is om de toepassing te gebruiken, moet de gebruiker toestemming geven als hij dat nog niet heeft gedaan. Na het verlenen van toestemming en bij succesvolle verificatie geeft Azure AD een autorisatiecode-antwoord uit op de omleidingsURI van de clienttoepassing.
1. Wanneer Azure AD een autorisatiecodeantwoord uitgeeft dat wordt teruggegeven aan de omleidingsURI, stopt de clienttoepassing de browserinteractie en haalt de autorisatiecode uit het antwoord. Met behulp van deze autorisatiecode stuurt de clienttoepassing een aanvraag naar het tokeneindpunt van Azure AD met de autorisatiecode, details over de clienttoepassing (toepassings-id en omleidinguri) en de gewenste bron (toepassings-id URI voor de web-API).
1. De autorisatiecode en informatie over de clienttoepassing en web-API worden gevalideerd door Azure AD. Bij succesvolle validatie retourneert Azure AD twee tokens: een JWT-toegangstoken en een JWT-vernieuwingstoken. Bovendien retourneert Azure AD basisgegevens over de gebruiker, zoals de weergavenaam en de tenant-id.
1. Via HTTPS gebruikt de clienttoepassing het geretourneerde JWT-toegangstoken om de JWT-tekenreeks toe te voegen met een aanduiding 'Drager' in de autorisatiekop van de aanvraag aan de web-API. De web-API valideert vervolgens het JWT-token en als de validatie succesvol is, retourneert de gewenste bron.
1. Wanneer het toegangstoken verloopt, ontvangt de clienttoepassing een fout die aangeeft dat de gebruiker zich opnieuw moet verifiëren. Als de toepassing een geldig vernieuwingstoken heeft, kan deze worden gebruikt om een nieuw toegangstoken te verkrijgen zonder de gebruiker te vragen zich opnieuw aan te melden. Als het vernieuwingstoken verloopt, moet de toepassing de gebruiker opnieuw interactief verifiëren.

> [!NOTE]
> Het vernieuwingstoken dat is uitgegeven door Azure AD, kan worden gebruikt om toegang te krijgen tot meerdere bronnen. Als u bijvoorbeeld een clienttoepassing hebt die toestemming heeft om twee web-API's aan te roepen, kan het vernieuwingstoken ook worden gebruikt om een toegangstoken tot de andere web-API te krijgen.

## <a name="code-samples"></a>Codevoorbeelden

Zie de codevoorbeelden voor native application to web API-scenario's. En, kom regelmatig terug -- we voegen regelmatig nieuwe monsters toe. [Native Application to Web API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>App-registratie

Zie [Een app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)registreren als u een toepassing wilt registreren bij het Azure AD v1.0-eindpunt.

* Eén tenant - Zowel de native toepassing als de web-API moeten in dezelfde map in Azure AD worden geregistreerd. De web-API kan worden geconfigureerd om een set machtigingen bloot te leggen, die worden gebruikt om de toegang van de native toepassing tot de bronnen te beperken. De clienttoepassing selecteert vervolgens de gewenste machtigingen in het vervolgkeuzemenu 'Machtigingen voor andere toepassingen' in de Azure-portal.
* Multi-tenant - Ten eerste, de native applicatie alleen ooit geregistreerd in de directory van de ontwikkelaar of uitgever. Ten tweede is de native toepassing geconfigureerd om aan te geven welke machtigingen nodig zijn om functioneel te zijn. Deze lijst met vereiste machtigingen wordt weergegeven in een dialoogvenster wanneer een gebruiker of beheerder in de doelmap toestemming geeft voor de toepassing, waardoor deze beschikbaar is voor hun organisatie. Voor sommige toepassingen zijn alleen machtigingen op gebruikersniveau vereist, waarmee elke gebruiker in de organisatie kan instemmen. Voor andere toepassingen zijn machtigingen op administratorniveau vereist, waar een gebruiker in de organisatie geen toestemming voor kan geven. Alleen een directorybeheerder kan toestemming geven voor toepassingen waarvoor dit niveau van machtigingen vereist is. Wanneer de gebruiker of beheerder hiermee instemt, wordt alleen de web-API geregistreerd in zijn map. 

## <a name="token-expiration"></a>Token vervaldatum

Wanneer de native applicatie de autorisatiecode gebruikt om een JWT-toegangstoken te krijgen, ontvangt deze ook een JWT-vernieuwingstoken. Wanneer het toegangstoken verloopt, kan het vernieuwingstoken worden gebruikt om de gebruiker opnieuw te verifiëren zonder dat deze opnieuw moet worden aangemeld. Deze vernieuwingstoken wordt vervolgens gebruikt om de gebruiker te verifiëren, wat resulteert in een nieuw toegangstoken en vernieuwingstoken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [toepassingstypen en -scenario's](app-types.md)
- Meer informatie over de [basisprincipes](v1-authentication-scenarios.md) van Azure AD-verificatie

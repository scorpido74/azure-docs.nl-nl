---
title: Service-to-service-apps in Azure Active Directory
description: Beschrijft welke service-to-service-toepassingen en de basisprincipes op protocolstroom, registratie en tokenverloop voor dit app-type.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154760"
---
# <a name="service-to-service-apps"></a>Service-to-service-apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Service-to-service-toepassingen kunnen een daemon- of servertoepassing zijn die resources uit een web-API moet halen. Er zijn twee subscenario's die van toepassing zijn op deze sectie:

- Een daemon die een web-API moet aanroepen, gebouwd op het type Toekenningstype van OAuth 2.0-clientreferenties

    In dit scenario is het belangrijk om een paar dingen te begrijpen. Ten eerste is gebruikersinteractie niet mogelijk met een daemon-toepassing, waarbij de toepassing een eigen identiteit moet hebben. Een voorbeeld van een daemon-toepassing is een batchtaak of een service voor het besturingssysteem die op de achtergrond wordt uitgevoerd. Dit type toepassing vraagt een toegangstoken aan met behulp van de toepassingsidentiteit en presenteert de toepassings-id, de referenties (wachtwoord of certificaat) en de uri voor toepassings-id's aan Azure AD. Na succesvolle verificatie ontvangt de daemon een toegangstoken van Azure AD, dat vervolgens wordt gebruikt om de web-API aan te roepen.

- Een servertoepassing (zoals een web-API) die een web-API moet aanroepen, gebouwd op OAuth 2.0-conceptspecificatie voor namens

    Stel je voor dat een gebruiker zich in dit scenario heeft geverifieerd op een native toepassing en dat deze native toepassing een web-API moet aanroepen. Azure AD geeft een JWT-toegangstoken uit om de web-API aan te roepen. Als de web-API een andere downstream web-API moet aanroepen, kan deze de stroom namens de gebruiker gebruiken om de identiteit van de gebruiker te delegeren en te verifiëren aan de tweede-tier web-API.

## <a name="diagram"></a>Diagram

![Daemon- of servertoepassing naar web-API-diagram](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Protocolstroom

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Toepassingsidentiteit met OAuth 2.0 clientreferenties verlenen

1. Ten eerste moet de servertoepassing zich verifiëren met Azure AD als zichzelf, zonder menselijke interactie, zoals een interactief aanmeldingsdialoogvenster. Het maakt een verzoek tot het tokeneindpunt van Azure AD, met vermelding van de referentie- en toepassings-id URI.
1. Azure AD verifieert de toepassing en retourneert een JWT-toegangstoken dat wordt gebruikt om de web-API aan te roepen.
1. Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangstoken om de JWT-tekenreeks toe te voegen met een aanduiding 'Drager' in de autorisatiekop van de aanvraag aan de web-API. De web-API valideert vervolgens het JWT-token en als de validatie succesvol is, retourneert de gewenste bron.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Gedelegeerde gebruikersidentiteit met OAuth 2.0 Ontwerpspecificatie namens

De onderstaande stroom gaat ervan uit dat een gebruiker is geverifieerd op een andere toepassing (zoals een native toepassing) en dat de gebruikersidentiteit ervan is gebruikt om een toegangstoken tot de first-tier web-API te verkrijgen.

1. De native toepassing stuurt het toegangstoken naar de web-API op de eerste laag.
1. De web-API van de eerste laag verzendt een aanvraag naar het tokeneindpunt van Azure AD, met toepassings-id en referenties, evenals het toegangstoken van de gebruiker. Bovendien wordt de aanvraag verzonden met een on_behalf_of parameter die aangeeft dat de web-API nieuwe tokens aanvraagt om een downstream web-API aan te roepen namens de oorspronkelijke gebruiker.
1. Azure AD controleert of de web-API op de eerste laag machtigingen heeft voor toegang tot de tweedelaagsweb-API en valideert de aanvraag, waardoor een JWT-toegangstoken en een JWT-vernieuwingstoken worden teruggegeven aan de web-API op de eerste laag.
1. Via HTTPS roept de web-API van de eerste laag vervolgens de tweedelaagsweb-API aan door de tokentekenreeks toe te nemen in de kopautorisatie in de aanvraag. De web-API van de eerste laag kan de tweede-tier web-API blijven aanroepen zolang de toegangstoken en refresh-tokens geldig zijn.

## <a name="code-samples"></a>Codevoorbeelden

Zie de codevoorbeelden voor Daemon- of Server-to-webAPI-scenario's: [Server of Daemon Application to Web API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>App-registratie

* Eén tenant - Voor zowel de identiteit van de toepassing als de gedelegeerde gebruikersidentiteitsaanvragen moet de daemon- of servertoepassing worden geregistreerd in dezelfde map in Azure AD. De web-API kan worden geconfigureerd om een set machtigingen bloot te leggen, die worden gebruikt om de toegang van de daemon of server tot de bronnen te beperken. Als een gedelegeerd gebruikersidentiteitstype wordt gebruikt, moet de servertoepassing de gewenste machtigingen selecteren. Kies op de pagina **API-machtigingen** voor de toepassingsregistratie nadat u **Een machtiging** toevoegen hebt geselecteerd en de API-familie hebt gekozen, **gedelegeerde machtigingen**en selecteer vervolgens uw machtigingen. Deze stap is niet vereist als het type toepassingsidentiteit wordt gebruikt.
* Multi-tenant - Ten eerste is de daemon- of servertoepassing geconfigureerd om aan te geven welke machtigingen nodig zijn om functioneel te zijn. Deze lijst met vereiste machtigingen wordt weergegeven in een dialoogvenster wanneer een gebruiker of beheerder in de doelmap toestemming geeft voor de toepassing, waardoor deze beschikbaar is voor hun organisatie. Voor sommige toepassingen zijn alleen machtigingen op gebruikersniveau vereist, waarmee elke gebruiker in de organisatie kan instemmen. Voor andere toepassingen zijn machtigingen op administratorniveau vereist, waar een gebruiker in de organisatie geen toestemming voor kan geven. Alleen een directorybeheerder kan toestemming geven voor toepassingen waarvoor dit niveau van machtigingen vereist is. Wanneer de gebruiker of beheerder hiermee instemt, worden beide web-API's geregistreerd in hun directory.

## <a name="token-expiration"></a>Token vervaldatum

Wanneer de eerste toepassing de autorisatiecode gebruikt om een JWT-toegangstoken te krijgen, ontvangt deze ook een JWT-vernieuwingstoken. Wanneer het toegangstoken verloopt, kan het vernieuwingstoken worden gebruikt om de gebruiker opnieuw te verifiëren zonder om referenties te vragen. Deze vernieuwingstoken wordt vervolgens gebruikt om de gebruiker te verifiëren, wat resulteert in een nieuw toegangstoken en vernieuwingstoken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over andere [toepassingstypen en -scenario's](app-types.md)
- Meer informatie over de [basisprincipes](v1-authentication-scenarios.md) van Azure AD-verificatie

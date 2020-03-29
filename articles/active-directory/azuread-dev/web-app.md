---
title: Web-apps in Azure Active Directory
description: Beschrijft wat web-apps zijn en de basisprincipes op protocolstroom, registratie en token verloop voor dit app-type.
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
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154403"
---
# <a name="web-apps"></a>Web-apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web-apps zijn toepassingen die een gebruiker in een webbrowser verifiëren voor een webtoepassing. In dit scenario stuurt de webtoepassing de browser van de gebruiker aan om deze aan te melden bij Azure AD. Azure AD retourneert een aanmeldingsreactie via de browser van de gebruiker, die claims over de gebruiker bevat in een beveiligingstoken. Dit scenario ondersteunt aanmelding met behulp van de protocollen OpenID Connect, SAML 2.0 en WS-Federation.

## <a name="diagram"></a>Diagram

![Verificatiestroom voor browser naar webtoepassing](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protocolstroom

1. Wanneer een gebruiker de toepassing bezoekt en zich moet aanmelden, wordt deze omgeleid via een aanmeldingsverzoek naar het verificatieeindpunt in Azure AD.
1. De gebruiker meldt zich aan op de aanmeldingspagina.
1. Als de verificatie is geslaagd, maakt Azure AD een verificatietoken en retourneert een aanmeldingsantwoord op de antwoord-URL van de toepassing die is geconfigureerd in de Azure-portal. Voor een productietoepassing moet deze URL van het antwoord HTTPS zijn. Het geretourneerde token bevat claims over de gebruiker en Azure AD die door de toepassing vereist zijn om het token te valideren.
1. De toepassing valideert het token met behulp van een openbare ondertekeningssleutel en emittentgegevens die beschikbaar zijn in het federatiemetagegevensdocument voor Azure AD. Nadat de toepassing het token heeft gevalideerd, wordt een nieuwe sessie met de gebruiker gestart. Met deze sessie heeft de gebruiker toegang tot de toepassing totdat deze verloopt.

## <a name="code-samples"></a>Codevoorbeelden

Zie de codevoorbeelden voor browser- en webtoepassingsscenario's. En kom regelmatig terug als nieuwe monsters vaak worden toegevoegd.

## <a name="app-registration"></a>App-registratie

Zie [Een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)als u een web-app wilt registreren.

* Eén tenant - Als u een toepassing bouwt die alleen voor uw organisatie is, moet deze worden geregistreerd in de map van uw bedrijf met behulp van de Azure-portal.
* Multi-tenant - Als u een toepassing bouwt die kan worden gebruikt door gebruikers buiten uw organisatie, moet deze worden geregistreerd in de directory van uw bedrijf, maar moet deze ook worden geregistreerd in de directory van elke organisatie die de toepassing gebruikt. Om uw toepassing beschikbaar te maken in hun directory, u een aanmeldingsproces voor uw klanten opnemen waarmee ze toestemming kunnen geven voor uw toepassing. Wanneer ze zich aanmelden voor uw aanvraag, krijgen ze een dialoogvenster te zien met de machtigingen die de toepassing nodig heeft en vervolgens de optie om toestemming te geven. Afhankelijk van de vereiste machtigingen kan een beheerder in de andere organisatie worden verplicht om toestemming te geven. Wanneer de gebruiker of beheerder hiermee instemt, wordt de toepassing geregistreerd in zijn map.

## <a name="token-expiration"></a>Token vervaldatum

De sessie van de gebruiker verloopt wanneer de levensduur van het token dat is uitgegeven door Azure AD verloopt. Uw toepassing kan deze periode indien gewenst verkorten, zoals het afmelden van gebruikers op basis van een periode van inactiviteit. Wanneer de sessie verloopt, wordt de gebruiker gevraagd zich opnieuw aan te melden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [toepassingstypen en -scenario's](app-types.md)
* Meer informatie over de [basisprincipes](v1-authentication-scenarios.md) van Azure AD-verificatie

---
title: Overzicht van micro soft Identity platform (v 2.0)-Azure
description: Meer informatie over het micro soft Identity platform (v 2.0)-eind punt en-platform.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6280f3784247b671521a4994be1f233d4913d90c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194497"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Overzicht van micro soft Identity platform (v 2.0)

Het Microsoft Identity Platform bouwt voort op het ontwikkelaarsplatform van Azure Active Directory (Azure AD). Het stelt ontwikkel aars in staat om toepassingen te bouwen die zich aanmelden bij alle micro soft-identiteiten en om tokens op te halen voor het aanroepen van micro soft-Api's, zoals Microsoft Graph, of Api's die ontwikkel aars hebben gebouwd. Het micro soft Identity-platform bestaat uit:

- **OAuth 2,0 en OpenID Connect maken verbinding met Standard-compatibele verificatie service** waarmee ontwikkel aars een micro soft-identiteit kunnen verifiëren, waaronder:
  - Werk-of school accounts (ingericht via Azure AD)
  - Persoonlijke micro soft-accounts (zoals Skype, Xbox en Outlook.com)
  - Sociale of lokale accounts (via Azure AD B2C)
- **Open-source-bibliotheken**: micro soft-verificatie bibliotheken (MSAL) en ondersteuning voor andere normen-compatibele bibliotheken
- **Portal voor toepassings beheer**: een in de Azure Portal ingebouwde registratie-en configuratie-ervaring, samen met al uw andere Azure-beheer mogelijkheden.
- **API voor toepassings configuratie en Power shell**: Hiermee kunt u programmatische configuratie van uw toepassingen via REST API (Microsoft Graph en Azure Active Directory Graph 1,6) en Power shell, zodat u uw DevOps-taken kunt automatiseren.
- **Inhoud voor ontwikkel aars**: conceptuele en naslag documentatie, voor beelden van Snelstartgids, voor beelden van code, zelf studies en hand leidingen.

Voor ontwikkel aars biedt het micro soft Identity-platform naadloze integratie in de identiteits-en beveiligings ruimte, zoals verificatie zonder wacht woord, authenticatie van de stap en voorwaardelijke toegang.  U hoeft deze functionaliteit niet zelf te implementeren: toepassingen die zijn geïntegreerd met het micro soft-identiteits platform, profiteren van dergelijke innovaties.

Met micro soft Identity platform kunt u eenmaal code schrijven en elke gebruiker bereiken. U kunt een app één keer bouwen en op verschillende platforms werken, of een app bouwen die fungeert als een client en een resource toepassing (API).

## <a name="getting-started"></a>Aan de slag

Werken met identiteiten hoeft niet moeilijk te zijn. 

Bekijk een [video over micro soft Identity platform](identity-videos.md) voor meer informatie over de basis principes. 

Kies een [scenario](authentication-flows-app-scenarios.md) dat voor u van toepassing is: elk scenario pad heeft een Snelstartgids en een overzichts pagina waarmee u binnen enkele minuten aan de slag kunt.

- [Een app met één pagina bouwen](scenario-spa-overview.md)
- [Een web-app bouwen die gebruikers aanmeldt](scenario-web-app-sign-user-overview.md)
- [Een web-app bouwen die web-Api's aanroept](scenario-web-app-call-api-overview.md)
- [Een beveiligde web-API bouwen](scenario-protected-web-api-overview.md)
- [Een web-API bouwen die web-Api's aanroept](scenario-web-api-call-api-overview.md)
- [Een bureau blad-app bouwen](scenario-desktop-overview.md)
- [Een daemon-app bouwen](scenario-daemon-overview.md)
- [Een mobiele app bouwen](scenario-mobile-overview.md)

In het volgende diagram worden de scenario's voor algemene verificatie-apps beschreven. Gebruik dit als referentie bij het integreren van het micro soft Identity-platform met uw app.

[Toepassings scenario's in het micro soft Identity-platform ![](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over basis verificatie concepten, raden we u aan om te beginnen met de volgende onderwerpen:

- [Verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)
- [De basisbeginselen van verificatie](authentication-scenarios.md)
- [Toepassings-en service-principals](app-objects-and-service-principals.md)
- [Doel groepen](v2-supported-account-types.md)
- [Machtigingen en toestemming](v2-permissions-and-consent.md)
- [Id-tokens](id-tokens.md) en [toegangs tokens](access-tokens.md)

Bouw een toepassing met uitgebreide gegevens die [Microsoft Graph](https://docs.microsoft.com/graph/overview)aanroept.

Wanneer u klaar bent om uw app te starten in een **productie omgeving**, raadpleegt u deze aanbevolen procedures:

- [Schakel logboek registratie](msal-logging.md) in uw toepassing in.
- Schakel telemetrie in uw toepassing in.
- Schakel [proxy's in en pas HTTP-clients](msal-net-provide-httpclient.md)aan.
- Test uw integratie door de [controle lijst voor de integratie van micro soft Identity platform](identity-platform-integration-checklist.md)te volgen.

## <a name="learn-more"></a>Meer informatie

Als u van plan bent een klant gerichte toepassing te bouwen die zich aanmeldt bij sociale en lokale identiteiten, raadpleegt u het [Azure AD B2C-overzicht](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).

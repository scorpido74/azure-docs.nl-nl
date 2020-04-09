---
title: Overzicht van Microsoft Identity Platform (v2.0) - Azure
description: Meer informatie over het Microsoft-identiteitsplatform (v2.0) eindpunt en platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2e5bbbd311d71f2925e86ae756b36de7194aa9fb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886242"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Overzicht van Microsoft Identity Platform (v2.0)

Het Microsoft Identity Platform bouwt voort op het ontwikkelaarsplatform van Azure Active Directory (Azure AD). Hiermee kunnen ontwikkelaars toepassingen bouwen die alle Microsoft-identiteiten ondertekenen en tokens ertoe brengen Microsoft API's te bellen, zoals Microsoft Graph, of API's die ontwikkelaars hebben gebouwd. Het Microsoft-identiteitsplatform bestaat uit:

- **OAuth 2.0 en OpenID Connect standaardverificatieservice** waarmee ontwikkelaars elke Microsoft-identiteit kunnen verifiëren, waaronder:
  - Werk- of schoolaccounts (ingericht via Azure AD)
  - Persoonlijke Microsoft-accounts (zoals Skype, Xbox en Outlook.com)
  - Sociale of lokale accounts (via Azure AD B2C)
- **Open-source bibliotheken**: Microsoft Authentication Libraries (MSAL) en ondersteuning voor andere bibliotheken die voldoen aan standaarden
- **Toepassingsbeheerportal:** een registratie- en configuratie-ervaring die is ingebouwd in de Azure-portal, samen met al uw andere Azure-beheermogelijkheden.
- **Application configuration API en PowerShell:** waarmee uw toepassingen programmatisch kunnen worden geconfigureerd via de Microsoft Graph API en PowerShell, zodat u uw DevOps-taken automatiseren.
- **Ontwikkelaarsinhoud:** conceptuele en referentiedocumentatie, quickstartvoorbeelden, codevoorbeelden, zelfstudies en handleidingen.

Voor ontwikkelaars biedt het Microsoft-identiteitsplatform naadloze integratie in innovaties in de identiteits- en beveiligingsruimte, zoals wachtwoordloze verificatie, step-upauthenticatie en voorwaardelijke toegang.  U hoeft dergelijke functionaliteit niet zelf te implementeren: toepassingen die zijn geïntegreerd met het Microsoft-identiteitsplatform profiteren van dergelijke innovaties.

Met het Microsoft-identiteitsplatform u één keer code schrijven en elke gebruiker bereiken. U een app één keer bouwen en deze op veel platforms laten werken, of een app bouwen die fungeert als client en als een API (Resource Application).

## <a name="getting-started"></a>Aan de slag

Werken met identiteiten hoeft niet moeilijk te zijn. 

Bekijk een [video van het Microsoft-identiteitsplatform](identity-videos.md) om de basisprincipes te leren. 

Kies een [scenario](authentication-flows-app-scenarios.md) dat op u van toepassing is: elk scenariopad heeft een snelle start en een overzichtspagina om u binnen enkele minuten aan de slag te krijgen:

- [Een app met één pagina maken](scenario-spa-overview.md)
- [Een web-app bouwen waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)
- [Een web-app bouwen waarmee web-API's worden aangeroepen](scenario-web-app-call-api-overview.md)
- [Een beveiligde web-API bouwen](scenario-protected-web-api-overview.md)
- [Een web-API bouwen die web-API's aanroept](scenario-web-api-call-api-overview.md)
- [Een bureaublad-app maken](scenario-desktop-overview.md)
- [Een daemon-app bouwen](scenario-daemon-overview.md)
- [Een mobiele app bouwen](scenario-mobile-overview.md)

In de volgende grafiek worden veelvoorkomende verificatie-app-scenario's beschreven: gebruik het als referentie bij het integreren van het Microsoft-identiteitsplatform met uw app.

[![Toepassingsscenario's in het Microsoft-identiteitsplatform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over kernverificatieconcepten, raden we u aan om met deze onderwerpen te beginnen:

- [Verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
- [Basisbeginselen van verificatie](authentication-scenarios.md)
- [Aan- en aangevers van toepassingen](app-objects-and-service-principals.md)
- [Publiek](v2-supported-account-types.md)
- [Machtigingen en toestemming](v2-permissions-and-consent.md)
- [ID-tokens](id-tokens.md) en [toegangstokens](access-tokens.md)

Bouw een gegevensrijke toepassing die [Microsoft Graph](https://docs.microsoft.com/graph/overview)aanroept.

Wanneer u klaar bent om uw app in een **productieomgeving**te starten, bekijkt u de volgende aanbevolen procedures:

- [Schakel het inloggen](msal-logging.md) op uw toepassing in.
- Schakel telemetrie in uw toepassing in.
- Schakel [proxy's in en pas HTTP-clients aan.](msal-net-provide-httpclient.md)
- Test uw integratie door de checklist voor integratie [van het Microsoft-identiteitsplatform te volgen.](identity-platform-integration-checklist.md)

## <a name="learn-more"></a>Meer informatie

Als u van plan bent een klantgerichte toepassing te bouwen die in sociale en lokale identiteiten wordt tekent, raadpleegt u het [Overzicht van Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers)

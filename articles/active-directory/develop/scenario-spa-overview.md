---
title: JavaScript-scenario voor apps met één pagina - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een toepassing van één pagina (scenariooverzicht) met behulp van het Microsoft-identiteitsplatform.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701872"
---
# <a name="scenario-single-page-application"></a>Scenario: toepassing met één pagina

Leer alles wat je nodig hebt om een applicatie van één pagina (SPA) te bouwen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

U uw eerste toepassing maken door de JavaScript SPA quickstart te volgen:

> [!div class="nextstepaction"]
> [Snelstart: toepassing met één pagina](./quickstart-v2-javascript.md)

## <a name="overview"></a>Overzicht

Veel moderne webapplicaties zijn gebouwd als client-side single-page applicaties. Ontwikkelaars schrijven ze met Behulp van JavaScript of een SPA-framework zoals Angular, Vue.js en React.js. Deze toepassingen draaien op een webbrowser en hebben andere verificatiekenmerken dan traditionele webtoepassingen aan de serverzijde. 

Het Microsoft-identiteitsplatform maakt toepassingen met één pagina in staat om gebruikers aan te melden en tokens toegang te krijgen tot back-endservices of web-API's met behulp van de [impliciete stroom van OAuth 2.0.](./v2-oauth2-implicit-grant-flow.md) De impliciete stroom stelt de toepassing in staat om ID-tokens te krijgen om de geverifieerde gebruiker te vertegenwoordigen en ook toegang te krijgen tot tokens die nodig zijn om beveiligde API's te bellen.

![Toepassingen met één pagina](./media/scenarios/spa-app.svg)

Deze verificatiestroom bevat geen toepassingsscenario's die gebruikmaken van JavaScript-frameworks met meerdere platforms, zoals Electron en React-Native. Ze vereisen verdere mogelijkheden voor interactie met de native platforms.

## <a name="specifics"></a>Details

Als u dit scenario voor uw toepassing wilt inschakelen, moet u het:

* Toepassingsregistratie met Azure Active Directory (Azure AD). Deze registratie omvat het inschakelen van de impliciete stroom en het instellen van een omleiding URI naar welke tokens worden geretourneerd.
* Toepassingsconfiguratie met de geregistreerde toepassingseigenschappen, zoals toepassings-id.
* Met behulp van Microsoft Authentication Library (MSAL) om de verificatiestroom uit te brengen om in te loggen en tokens te verwerven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-spa-app-registration.md)

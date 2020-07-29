---
title: 'Java script: app-scenario met één pagina-micro soft Identity-platform | Azure'
description: Meer informatie over het maken van een toepassing met één pagina (scenario-overzicht) met behulp van het micro soft Identity-platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885171"
---
# <a name="scenario-single-page-application"></a>Scenario: toepassing met één pagina

Meer informatie over wat u nodig hebt om een toepassing met één pagina (SPA) te maken.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

U kunt uw eerste toepassing maken met behulp van de Java script SPA Quick Start:

> [!div class="nextstepaction"]
> [Snelstartgids: toepassing met één pagina](./quickstart-v2-javascript.md)

## <a name="overview"></a>Overzicht

Veel moderne webtoepassingen zijn gebouwd als toepassingen met één pagina aan de client zijde. Ontwikkel aars schrijven ze met Java script of een beveiligd-wachtwoord verificatie-Framework zoals hoek, Vue.js en React.js. Deze toepassingen worden uitgevoerd in een webbrowser en hebben verschillende verificatie kenmerken dan traditionele webtoepassingen aan de server zijde. 

Met het micro soft Identity-platform kunnen toepassingen met één pagina worden aangemeld en tokens worden opgehaald voor toegang tot back-end-services of Web-Api's met behulp van de [impliciete OAuth 2,0-stroom](./v2-oauth2-implicit-grant-flow.md). Met de impliciete stroom kan de toepassing ID-tokens ophalen die de geverifieerde gebruiker vertegenwoordigen en ook toegangs tokens hebben die nodig zijn om beveiligde Api's aan te roepen.

![Toepassingen met één pagina](./media/scenarios/spa-app.svg)

In deze verificatie stroom zijn geen toepassings scenario's opgenomen die gebruikmaken van platformoverschrijdende java script frameworks zoals elektroden en reageren op systeem eigen. Ze vereisen verdere mogelijkheden voor interactie met de systeem eigen platformen.

## <a name="specifics"></a>Opsporingsgegevens

U hebt het volgende nodig om dit scenario voor uw toepassing in te scha kelen:

* Registratie van toepassingen met Azure Active Directory (Azure AD). Deze registratie omvat het inschakelen van de impliciete stroom en het instellen van een omleidings-URI waarmee tokens worden geretourneerd.
* Toepassings configuratie met de geregistreerde toepassings eigenschappen, zoals de toepassings-ID.
* Micro soft Authentication Library (MSAL) gebruiken om de verificatie stroom uit te voeren en tokens te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-spa-app-registration.md)

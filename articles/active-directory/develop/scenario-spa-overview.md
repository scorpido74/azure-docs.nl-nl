---
title: Java script-overzicht van een toepassings scenario met één pagina-micro soft Identity-platform
description: Meer informatie over het maken van een toepassing met één pagina (scenario-overzicht) met behulp van het micro soft Identity-platform.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f59e8c628d7ba37aaf258541664e40e1d4a2dc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764675"
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

Veel moderne webtoepassingen zijn gebouwd als toepassingen met één pagina aan de client zijde. Ontwikkel aars schrijven ze met Java script of een beveiligd-wachtwoord verificatie-Framework zoals hoek, vue. js en reageren. js. Deze toepassingen worden uitgevoerd in een webbrowser en hebben verschillende verificatie kenmerken dan traditionele webtoepassingen aan de server zijde. 

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
> [Appregistratie](scenario-spa-app-registration.md)

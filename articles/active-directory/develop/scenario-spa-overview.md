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
ms.openlocfilehash: 60e4ca80faa2c8787a13d87ab06cad9243299e50
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291944"
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

Veel moderne webtoepassingen zijn gebouwd als toepassingen met één pagina aan de client zijde. Ontwikkel aars schrijven ze met Java script of een beveiligd-wachtwoord verificatie-Framework zoals hoek, vue en reageren. Deze toepassingen worden uitgevoerd in een webbrowser en hebben verschillende verificatie kenmerken dan traditionele webtoepassingen aan de server zijde. 

Het micro soft Identity-platform biedt **twee** opties voor het inschakelen van toepassingen met één pagina voor het aanmelden van gebruikers en het ophalen van tokens voor toegang tot back-end-services of Web-api's:

- [OAuth 2,0-autorisatie code stroom (met PKCE)](./v2-oauth2-auth-code-flow.md). Met de autorisatie code stroom kan de toepassing een autorisatie code voor **id-** tokens uitwisselen die de geverifieerde gebruikers-en **toegangs** tokens vertegenwoordigen die nodig zijn om beveiligde api's aan te roepen. Daarnaast worden **vernieuwings** tokens geretourneerd die lange termijn toegang bieden tot bronnen namens gebruikers zonder dat hiervoor interactie met die gebruikers nodig is. Dit is de **Aanbevolen** methode.

![Toepassingen met één pagina-auth](./media/scenarios/spa-app-auth.svg)

- [OAuth 2,0-impliciete stroom](./v2-oauth2-implicit-grant-flow.md). Met de impliciete toekennings stroom kan de toepassing **id-** en **toegangs** tokens ophalen. In tegens telling tot de autorisatie code stroom retourneert impliciete toekennings stroom geen **vernieuwings token**.

![Toepassingen met één pagina-impliciet](./media/scenarios/spa-app.svg)

In deze verificatie stroom zijn geen toepassings scenario's opgenomen die gebruikmaken van platformoverschrijdende java script frameworks zoals elektroden en reageren op systeem eigen. Ze vereisen verdere mogelijkheden voor interactie met de systeem eigen platformen.

## <a name="specifics"></a>Opsporingsgegevens

U hebt het volgende nodig om dit scenario voor uw toepassing in te scha kelen:

* Registratie van toepassingen met Azure Active Directory (Azure AD). De registratie stappen verschillen tussen de impliciete overdrachts stroom en de autorisatie code stroom.
* Toepassings configuratie met de eigenschappen van de geregistreerde toepassing, zoals de toepassings-ID.
* Micro soft-verificatie bibliotheek voor Java script (MSAL.js) gebruiken om de verificatie stroom uit te voeren en tokens te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-spa-app-registration.md)

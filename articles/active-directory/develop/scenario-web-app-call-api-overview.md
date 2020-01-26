---
title: Een web-app bouwen die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (overzicht)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758986"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: een web-app die web-Api's aanroept

Informatie over het bouwen van een web-app die gebruikers ondertekent bij het micro soft-identiteits platform en vervolgens Web-Api's aanroept namens de aangemelde gebruiker.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

In dit scenario wordt ervan uitgegaan dat u het volgende scenario al hebt door lopen:

> [!div class="nextstepaction"]
> [Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Overzicht

U voegt verificatie toe aan uw web-app, zodat deze gebruikers kan aanmelden en een web-API kan aanroepen namens de aangemelde gebruiker.

![Web-app die web-API's aanroept](./media/scenario-webapp/web-app.svg)

Web-apps die web-Api's aanroepen, zijn vertrouwelijke client toepassingen.
Daarom registreren ze een geheim (een toepassings wachtwoord of-certificaat) met Azure Active Directory (Azure AD). Dit geheim wordt door gegeven tijdens het aanroepen van Azure AD om een token op te halen.

## <a name="specifics"></a>Opsporingsgegevens

> [!NOTE]
> Het toevoegen van een aanmelding aan een web-app is het beveiligen van de web-app zelf. Deze beveiliging wordt bereikt met behulp van *middleware* -Bibliotheken, niet van de micro soft Authentication Library (MSAL). Het voor gaande scenario, de [Web-app die zich aanmeldt bij gebruikers](scenario-web-app-sign-user-overview.md), valt onder dat onderwerp.
>
> In dit scenario wordt beschreven hoe u Web-Api's aanroept vanuit een web-app. U moet toegangs tokens voor deze web-Api's ontvangen. Voor het verkrijgen van deze tokens gebruikt u MSAL-bibliotheken om deze tokens te verkrijgen.

Voor de ontwikkeling van dit scenario zijn de volgende specifieke taken vereist:

- Tijdens de registratie van de [toepassing](scenario-web-app-call-api-app-registration.md)moet u een antwoord-URI, geheim of certificaat opgeven dat wordt gedeeld met Azure AD. Als u uw app op verschillende locaties implementeert, kunt u deze informatie voor elke locatie opgeven.
- De [configuratie](scenario-web-app-call-api-app-configuration.md) van de toepassing moet de client referenties opgeven die met Azure AD zijn gedeeld tijdens de registratie van de toepassing.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app die web-Api's aanroept: app-registratie](scenario-web-app-call-api-app-registration.md)

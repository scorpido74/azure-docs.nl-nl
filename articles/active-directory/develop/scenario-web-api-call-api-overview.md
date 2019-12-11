---
title: Een web-API bouwen die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-API die downstream Web-Api's (overzicht) aanroept.
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965123"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenario: Web-API voor het aanroepen van web-Api's

Meer informatie over wat u nodig hebt om een web-API te maken die web-Api's aanroept.

## <a name="prerequisites"></a>Vereisten

In dit scenario wordt de beveiligde web-API voor het aanroepen van web-Api's gebaseerd op het scenario ' een web-API beveiligen '. Zie voor meer informatie over dit kern scenario de [beveiligde web API-scenario](scenario-protected-web-api-overview.md) .

## <a name="overview"></a>Overzicht

- Een client (Web-, Desktop-, mobiele of toepassing met één pagina)-wordt niet weer gegeven in het onderstaande diagram: roept een beveiligde web-API aan en biedt een JWT Bearer-token in de http-header autorisatie.
- De beveiligde web-API valideert het token en maakt gebruik van de methode MSAL `AcquireTokenOnBehalfOf` om een andere token aan te vragen (van Azure AD), zodat het zelf een tweede Web-API (met de naam de downstream Web-API) kan aanroepen namens de gebruiker.
- De beveiligde web-API gebruikt dit token om een downstream API aan te roepen. Het kan ook `AcquireTokenSilent`later aanroepen om tokens aan te vragen voor andere downstream-Api's (maar nog steeds namens dezelfde gebruiker). `AcquireTokenSilent` het token zo nodig vernieuwen.

![Web-API die een web-API aanroept](media/scenarios/web-api.svg)

## <a name="specifics"></a>Opsporingsgegevens

Het deel van de registratie van de app met betrekking tot de API-machtigingen is klassiek. De configuratie van de toepassing omvat het gebruik van de OAuth 2,0-stroom om het JWT Bearer-token uit te wisselen op basis van een token voor een stroomafwaartse API. Dit token wordt toegevoegd aan de token cache, waar deze beschikbaar is in de controller van de Web-API, en kan een token op de achtergrond verkrijgen om downstream-Api's aan te roepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Appregistratie](scenario-web-api-call-api-app-registration.md)

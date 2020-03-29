---
title: Een web-API bouwen die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een web-API die downstream web API's aanroept (overzicht).
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
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701736"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: een web-API die web-API's aanroept

Meer informatie over wat u moet weten om een web-API te bouwen die web-API's aanroept.

## <a name="prerequisites"></a>Vereisten

Dit scenario, waarin een beveiligde web-API webAPI's aanroept, bouwt voort op het scenario 'Een web-API beveiligen'. Zie [Scenario: Beveiligde web-API](scenario-protected-web-api-overview.md)voor meer informatie over dit basisscenario.

## <a name="overview"></a>Overzicht

- Een web-, desktop-, mobiele of single-page toepassingsclient (die niet is vertegenwoordigd in het bijbehorende diagram) roept een beveiligde web-API aan en biedt een JSON-webtoken (JWT) aan toonder in de HTTP-header 'Autorisatie'.
- De beveiligde web-API valideert het token en gebruikt `AcquireTokenOnBehalfOf` de MSAL-methode (Microsoft Authentication Library) om een ander token aan te vragen bij Azure Active Directory (Azure AD), zodat de beveiligde web-API namens de gebruiker een tweede web-API of downstream web-API kan aanroepen.
- De beveiligde web-API `AcquireTokenSilent`kan ook later aanbellen om tokens voor andere downstream API's aan te vragen namens dezelfde gebruiker. `AcquireTokenSilent`hiermee wordt het token vernieuwd wanneer dat nodig is.

![Diagram van een web-API die een web-API aanroept](media/scenarios/web-api.svg)

## <a name="specifics"></a>Details

Het app-registratiegedeelte dat is gerelateerd aan API-machtigingen is klassiek. De app-configuratie omvat het gebruik van de OAuth 2.0 On-Behalf-Of-flow om het JWT-token voor een token in te ruilen voor een downstream-API. Dit token wordt toegevoegd aan de tokencache, waar het beschikbaar is in de controllers van de web-API en het kan vervolgens een token in stilte aanschaffen om downstream API's te bellen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-web-api-call-api-app-registration.md)

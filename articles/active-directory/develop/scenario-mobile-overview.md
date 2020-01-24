---
title: Een mobiele app bouwen die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept (overzicht)
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
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0a33b5bb4fc7220a9cf66f40e9805d3fde9fccef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702042"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: mobiele toepassing die web-Api's aanroept

Meer informatie over het bouwen van een mobiele app die web-Api's aanroept.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

Maak uw eerste mobiele toepassing en probeer een Snelstartgids uit te proberen.

> [!div class="nextstepaction"]
> [Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Android-app](./quickstart-v2-android.md)
>
> [Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een iOS-app](./quickstart-v2-ios.md)
>
> [Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Xamarin iOS & Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Overzicht

Een persoonlijke, naadloze gebruikers ervaring is essentieel voor mobiele apps.  Met micro soft Identity platform kunnen mobiele ontwikkel aars die ervaring voor iOS-en Android-gebruikers maken. Uw toepassing kan gebruikers (Azure AD), persoonlijke Microsoft-account gebruikers en Azure AD B2C gebruikers Azure Active Directory aanmelden en tokens verkrijgen om namens u een web-API aan te roepen. Voor het implementeren van deze stromen gebruiken we micro soft Authentication Library (MSAL), waarmee de standaard [OAuth 2.0-autorisatie code stroom](v2-oauth2-auth-code-flow.md)wordt geïmplementeerd.

![Daemon-apps](./media/scenarios/mobile-app.svg)

Overwegingen voor mobiele apps:

- **Gebruikers ervaring is sleutel**: Hiermee staat u gebruikers toe de waarde van uw app te zien voordat hij of zij zich aanmeldt en vraagt u alleen de vereiste machtigingen op.
- **Ondersteuning voor alle gebruikers configuraties**: veel mobiele zakelijke gebruikers bevinden zich onder voorwaardelijke toegang en nalevings beleid voor apparaten. Zorg ervoor dat u deze belang rijke scenario's ondersteunt.
- **Eenmalige aanmelding (SSO) implementeren**: MSAL en micro soft Identity platform maken het mogelijk om eenmalige aanmelding in te scha kelen via de browser van het apparaat of de Microsoft Authenticator (en intune-bedrijfsportal op Android).

## <a name="specifics"></a>Opsporingsgegevens

Houd rekening met het volgende wanneer u een mobiele app op het micro soft Identity-platform bouwt:

- Afhankelijk van het platform is het mogelijk dat de gebruiker zich voor de eerste keer aanmeldt. IOS vereist bijvoorbeeld dat Apps gebruikers interactie weer geven bij gebruik van SSO de eerste keer door Microsoft Authenticator (en Intune-bedrijfsportal op Android).
- Op iOS en Android kan MSAL gebruikmaken van een externe browser (die mogelijk boven op uw app wordt weer gegeven) om gebruikers aan te melden. U kunt de configuratie aanpassen voor gebruik in-app-webweergaven.
- Gebruik nooit een geheim in een mobiele toepassing. Het is toegankelijk voor alle gebruikers.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Appregistratie](scenario-mobile-app-registration.md)

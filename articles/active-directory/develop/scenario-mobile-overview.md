---
title: Een mobiele app bouwen die web-API's aanroept | Azure
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over het bouwen van een mobiele app die webAPI's aanroept (overzicht)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f90f7f23fbdf10b91d8dfc7cd00cca83cd32fbc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882570"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Mobiele toepassing die web-API's aanroept

Meer informatie over het bouwen van een mobiele app die web-API's aanroept.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

Maak je eerste mobiele applicatie en probeer een quickstart uit.

> [!div class="nextstepaction"]
> [Snelstart: een token aanschaffen en Microsoft Graph API aanroepen vanuit een Android-app](./quickstart-v2-android.md)
>
> [Snelstart: een token aanschaffen en Microsoft Graph API aanroepen vanuit een iOS-app](./quickstart-v2-ios.md)
>
> [Snelstart: een token aanschaffen en Microsoft Graph API aanroepen vanuit een Xamarin iOS- en Android-app](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Overzicht

Een gepersonaliseerde, naadloze gebruikerservaring is essentieel voor mobiele apps.  Microsoft-identiteitsplatform stelt mobiele ontwikkelaars in staat om die ervaring te creëren voor iOS- en Android-gebruikers. Uw toepassing kan zich aanmelden bij Azure Active Directory-gebruikers (Azure AD), persoonlijke Gebruikers van Microsoft-account en Azure AD B2C-gebruikers. Het kan ook tokens verwerven om namens hen een web-API aan te roepen. Om deze stromen te implementeren, gebruiken we Microsoft Authentication Library (MSAL). MSAL implementeert de industriestandaard [OAuth2.0 autorisatiecodestroom.](v2-oauth2-auth-code-flow.md)

![Daemon-apps](./media/scenarios/mobile-app.svg)

Overwegingen voor mobiele apps:

- **Gebruikerservaring is essentieel:** gebruikers toestaan om de waarde van uw app te zien voordat u om aanmelding vraagt. Vraag alleen de vereiste machtigingen aan.
- **Alle gebruikersconfiguraties ondersteunen:** veel gebruikers van mobiele bedrijven moeten zich houden aan het beleid inzake voorwaardelijke toegang en beleid voor naleving van apparaten. Zorg ervoor dat u deze belangrijke scenario's ondersteunt.
- **Single sign-on (SSO)** implementeren: Door MSAL en Microsoft-identiteitsplatform te gebruiken, u eenmalige aanmelding inschakelen via de browser van het apparaat of Microsoft Authenticator (en Intune Company Portal op Android).
- **De modus Gedeeld apparaat implementeren:** Stel uw toepassing in staat om te worden gebruikt in scenario's voor gedeelde apparaten, bijvoorbeeld ziekenhuizen, productie, detailhandel en financiën. [Lees meer over het ondersteunen van de modus gedeeld apparaat](msal-shared-devices.md).

## <a name="specifics"></a>Details

Houd rekening met de volgende overwegingen wanneer u een mobiele app bouwt op het identiteitsplatform van Microsoft:

- Afhankelijk van het platform is enige gebruikersinteractie vereist de eerste keer dat gebruikers zich aanmelden. IOS vereist bijvoorbeeld dat apps gebruikersinteractie weergeven wanneer ze SSO voor het eerst gebruiken via Microsoft Authenticator (en Intune Company Portal op Android).
- Op iOS en Android kan MSAL een externe browser gebruiken om gebruikers aan te melden. De externe browser kan boven op uw app worden weergegeven.
- Gebruik nooit een geheim in een mobiele applicatie. In deze toepassingen zijn geheimen toegankelijk voor alle gebruikers.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-mobile-app-registration.md)

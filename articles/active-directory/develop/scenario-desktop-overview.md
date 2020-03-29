---
title: Een desktop-app bouwen die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een bureaublad-app die web-API's aanroept (overzicht)
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
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702144"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: Bureaublad-app die web-API's aanroept

Leer alles wat u nodig hebt om een desktop-app te bouwen die web-API's aanroept.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Aan de slag

Als u dat nog niet hebt gedaan, maakt u uw eerste toepassing door de quickstart van het .NET-bureaublad, het QuickStart Universal Windows Platform (UWP) of de snelle start van de macOS-app te volgen:

> [!div class="nextstepaction"]
> [Quickstart: Een token verkrijgen en Microsoft Graph API aanroepen vanuit een Windows-bureaublad-app](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Snelstart: een token aanschaffen en Microsoft Graph API aanroepen vanuit een UWP-app](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Snelstart: een token aanschaffen en Microsoft Graph API aanroepen vanuit een native macOS-app](./quickstart-v2-ios.md)

## <a name="overview"></a>Overzicht

U schrijft een bureaubladtoepassing en u wilt gebruikers aanmelden bij uw toepassing en web-API's aanroepen, zoals Microsoft Graph, andere Microsoft API's of uw eigen web-API. Je hebt verschillende mogelijkheden:

- U de interactieve tokenacquisitie gebruiken:

  - Als uw bureaubladtoepassing grafische besturingselementen ondersteunt, bijvoorbeeld als het een Windows.Form-toepassing, een WPF-toepassing of een macOS-native toepassing is.
  - Of als het een .NET Core-toepassing is en u ermee instemt dat de verificatieinteractie met Azure AD (Azure AD) in de systeembrowser plaatsvindt.

- Voor windows gehoste toepassingen is het ook mogelijk voor toepassingen die worden uitgevoerd op computers die zijn aangesloten op een Windows-domein of Azure AD om een token in stilte te verkrijgen met geïntegreerde Windows-verificatie.
- Ten slotte, en hoewel het niet wordt aanbevolen, u een gebruikersnaam en een wachtwoord gebruiken in openbare clienttoepassingen. Het is nog steeds nodig in sommige scenario's zoals DevOps. Het gebruik ervan legt beperkingen op aan uw toepassing. Het kan zich bijvoorbeeld niet aanmelden bij een gebruiker die multifactorauthenticatie moet uitvoeren (voorwaardelijke toegang). Ook zal uw aanvraag niet profiteren van single sign-on (SSO).

  Het is ook tegen de principes van moderne authenticatie en is alleen voorzien om legacy redenen.

  ![Bureaubladtoepassing](media/scenarios/desktop-app.svg)

- Als u een draagbaar command-line-hulpprogramma schrijft, waarschijnlijk een .NET Core-toepassing die op Linux of Mac wordt uitgevoerd, en als u accepteert dat verificatie wordt gedelegeerd aan de systeembrowser, u interactieve verificatie gebruiken. .NET Core biedt geen [webbrowser,](https://aka.ms/msal-net-uses-web-browser)dus verificatie gebeurt in de systeembrowser. Anders is de beste optie in dat geval het gebruik van apparaatcodestroom. Deze stroom wordt ook gebruikt voor toepassingen zonder browser, zoals IoT-toepassingen.

  ![Browserloze toepassing](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Details

Desktoptoepassingen hebben een aantal specifieke kenmerken. Ze zijn vooral afhankelijk van de vraag of uw toepassing gebruik maakt van interactieve authenticatie of niet.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bureaublad-app: App-registratie](scenario-desktop-app-registration.md)

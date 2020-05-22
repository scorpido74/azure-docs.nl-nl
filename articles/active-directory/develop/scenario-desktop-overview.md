---
title: Een bureau blad-app bouwen die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept (overzicht)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 92f0909660427e414264442523dba3ed2abe0142
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771856"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: bureau blad-app voor het aanroepen van web-Api's

Meer informatie over wat u nodig hebt om een bureau blad-app te bouwen die web-Api's aanroept.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Aan de slag

Als u dat nog niet hebt gedaan, maakt u uw eerste toepassing door de Snelstartgids voor het .NET-bureau blad, de Universeel Windows-platform (UWP) Quick Start of de macOS-Snelstartgids voor de systeem eigen app te volgen:

> [!div class="nextstepaction"]
> [Quickstart: Een token verkrijgen en Microsoft Graph API aanroepen vanuit een Windows-bureaublad-app](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een UWP-app](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een macOS-systeem eigen app](./quickstart-v2-ios.md)

## <a name="overview"></a>Overzicht

U schrijft een bureaublad toepassing en u wilt gebruikers aanmelden bij uw toepassing en Web-Api's aanroepen, zoals Microsoft Graph, andere micro soft-Api's of uw eigen web-API. U hebt verschillende mogelijkheden:

- U kunt de volgende aanschaf van het interactieve token gebruiken:

  - Als uw bureaublad toepassing grafische besturings elementen ondersteunt, bijvoorbeeld als het een Windows. Form-toepassing, een WPF-toepassing of een macOS-systeem eigen toepassing is.
  - Of, als het een .NET core-toepassing is en u akkoord gaat om de verificatie-interactie met Azure Active Directory (Azure AD) te laten plaatsvinden in de systeem browser.

- Voor Windows-toepassingen die worden gehost, is het ook mogelijk voor toepassingen die worden uitgevoerd op computers die zijn toegevoegd aan een Windows-domein of Azure AD om een token op de achtergrond te verkrijgen met behulp van geïntegreerde Windows-verificatie.
- Ten slotte, hoewel dit niet wordt aangeraden, kunt u een gebruikers naam en wacht woord in open bare client toepassingen gebruiken. Het is nog steeds nodig in sommige scenario's zoals DevOps. Met deze functie worden beperkingen voor uw toepassing opgelegd. Het is bijvoorbeeld niet mogelijk om een gebruiker aan te melden die [multi-factor Authentication](../authentication/concept-mfa-howitworks.md) moet uitvoeren (voorwaardelijke toegang). Uw toepassing profiteert ook niet van eenmalige aanmelding (SSO).

  Het is ook van toepassing op de principes van moderne authenticatie en wordt alleen voor verouderde redenen verschaft.

  ![Bureaublad toepassing](media/scenarios/desktop-app.svg)

- Als u een draagbaar opdracht regel programma schrijft, waarschijnlijk een .NET core-toepassing die wordt uitgevoerd op Linux of Mac, en als u accepteert dat verificatie wordt gedelegeerd aan de systeem browser, kunt u interactieve verificatie gebruiken. .NET core biedt geen [webbrowser](https://aka.ms/msal-net-uses-web-browser), waardoor er verificatie plaatsvindt in de systeem browser. Anders is de beste optie in dat geval het gebruik van de code stroom van het apparaat. Deze stroom wordt ook gebruikt voor toepassingen zonder een browser, zoals IoT-toepassingen.

  ![Browserloze toepassing](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Opsporingsgegevens

Desktop toepassingen hebben een aantal specifieke kenmerken. Ze zijn voornamelijk afhankelijk van of uw toepassing interactieve authenticatie gebruikt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bureau blad-app: app-registratie](scenario-desktop-app-registration.md)

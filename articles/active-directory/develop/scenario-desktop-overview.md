---
title: Een bureau blad-app bouwen die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept (overzicht)
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
ms.openlocfilehash: 545012629686e1fe3ece8a48ed852542e09e54fe
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965514"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: bureau blad-app voor het aanroepen van web-Api's

Meer informatie over wat u nodig hebt om een bureau blad-app te bouwen die web-Api's aanroept

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Aan de slag

Als u dat nog niet hebt gedaan, maakt u uw eerste toepassing door de Snelstartgids van .NET Desktop, de UWP Quick Start of de macOS systeem eigen app Quick Start te volgen:

> [!div class="nextstepaction"]
> [Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Windows-bureau blad-app](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een UWP-app](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Quick Start: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een macOS-systeem eigen app](./quickstart-v2-ios.md)

## <a name="overview"></a>Overzicht

U schrijft een bureaublad toepassing en u wilt gebruikers aanmelden bij uw toepassing en Web-Api's aanroepen, zoals de Microsoft Graph, andere micro soft-Api's of uw eigen web-API. U hebt verschillende mogelijkheden:

- U kunt de volgende aanschaf van het interactieve token gebruiken:

  - Als uw bureaublad toepassing grafische besturings elementen ondersteunt, bijvoorbeeld als het een Windows. Form-toepassing, een WPF-toepassing of een macOS-systeem eigen toepassing is.
  - Van als het een .NET core-toepassing is en u akkoord gaat om de verificatie-interactie met Azure AD te laten plaatsvinden in de systeem browser

- Voor Windows-toepassingen die worden gehost, is het ook mogelijk dat toepassingen die worden uitgevoerd op computers die lid zijn van een Windows-domein of AAD lid zijn van een token op de achtergrond met behulp van geïntegreerde Windows-verificatie.
- Ten slotte, hoewel dit niet wordt aangeraden, kunt u de gebruikers naam en het wacht woord in open bare client toepassingen gebruiken. Het is nog steeds nodig in sommige scenario's (zoals DevOps), maar houd er rekening mee dat het gebruik ervan beperkingen oplegt aan uw toepassing. Het is bijvoorbeeld niet mogelijk om een gebruiker aan te melden die multi-factor Authentication moet uitvoeren (voorwaardelijke toegang). Ook kan uw toepassing niet profiteren van eenmalige aanmelding (SSO).

  Het is ook van toepassing op de principes van moderne authenticatie en wordt alleen voor verouderde redenen verschaft.

  ![Bureaublad toepassing](media/scenarios/desktop-app.svg)

- Als u een draagbaar opdracht regel programma schrijft-waarschijnlijk een .NET core-toepassing die wordt uitgevoerd op Linux of Mac, en als u accepteert dat de verificatie wordt overgedragen aan de systeem browser, kunt u interactieve verificatie gebruiken. (.NET core biedt nog geen [webbrowser](https://aka.ms/msal-net-uses-web-browser) en daarom wordt de verificatie uitgevoerd in de systeem browser), anders is de beste optie in dat geval het gebruik van de code stroom van het apparaat. Deze stroom wordt ook gebruikt voor toepassingen zonder een browser, zoals IoT-toepassingen

  ![Browserloze toepassing](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Opsporingsgegevens

Desktop toepassingen hebben een aantal specifieke mogelijkheden, afhankelijk van het feit of de toepassing gebruikmaakt van interactieve verificatie of niet.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bureau blad-app-app-registratie](scenario-desktop-app-registration.md)

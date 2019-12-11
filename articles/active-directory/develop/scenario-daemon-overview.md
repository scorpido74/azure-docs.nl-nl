---
title: Een daemon-app bouwen die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-Api's aanroept
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8c97ee1e10d53cd8da9687a9109eb4ce8ae3d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965565"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: daemon-toepassing die web-Api's aanroept

Meer informatie over wat u nodig hebt om een daemon-toepassing te bouwen die web-Api's aanroept.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Overzicht

Uw toepassing kan een token verkrijgen om een web-API namens zichzelf aan te roepen (niet namens een gebruiker). Dit scenario is nuttig voor daemon-toepassingen. Het gebruikt de standaard OAuth 2,0- [client referenties](v2-oauth2-client-creds-grant-flow.md) toewijzen.

![Daemon-apps](./media/scenario-daemon-app/daemon-app.svg)

Hier volgen enkele voor beelden van gebruiks voorbeelden voor daemon-apps:

- Webtoepassingen die worden gebruikt voor het inrichten of beheren van gebruikers of het uitvoeren van batch processen in een directory
- Bureaublad toepassingen (zoals Windows-Services op Windows of daemons processen op Linux) die batch-taken uitvoeren, of een besturingssysteem service die op de achtergrond wordt uitgevoerd
- Web-Api's die directory's moeten bewerken, niet specifieke gebruikers

Er is een andere veelvoorkomende situatie waarbij niet-daemon-toepassingen gebruikmaken van client referenties: zelfs wanneer ze namens gebruikers handelen, moeten ze voor technische redenen toegang hebben tot een web-API of een resource met hun identiteit. Een voor beeld is toegang tot geheimen in de sleutel kluis of een Azure-SQL database voor een cache.

Toepassingen die een token voor hun eigen identiteiten verkrijgen:

- Zijn vertrouwelijke client toepassingen. Deze apps, gezien de toegang tot bronnen onafhankelijk van een gebruiker, moeten hun identiteit bewijzen. Ze zijn ook gevoelige apps, die moeten worden goedgekeurd door de Tenant beheerders van de Azure Active Directory (Azure AD).
- Een geheim (toepassings wachtwoord of-certificaat) hebt geregistreerd bij Azure AD. Dit geheim wordt door gegeven tijdens het aanroepen van Azure AD om een token op te halen.

## <a name="specifics"></a>Opsporingsgegevens

> [!IMPORTANT]
>
> - Gebruikers interactie is niet mogelijk met een daemon-toepassing. Een daemon-toepassing vereist een eigen identiteit. Met dit type toepassing wordt een toegangs token aangevraagd met behulp van de toepassings-ID en worden de toepassings-id's, referenties (wacht woord of certificaat) en de URI van de toepassings-ID naar Azure AD gepresenteerd. Na een geslaagde verificatie ontvangt de daemon een toegangs token (en een vernieuwings token) van het micro soft Identity platform-eind punt, dat vervolgens wordt gebruikt om de Web-API aan te roepen (en wordt vervolgens naar behoefte vernieuwd).
> - Omdat de gebruikers interactie niet mogelijk is, is de incrementele toestemming niet mogelijk. Alle vereiste API-machtigingen moeten worden geconfigureerd bij de registratie van de toepassing en de code van de toepassing verzoekt alleen statisch gedefinieerde machtigingen. Dit betekent ook dat daemon-toepassingen geen ondersteuning bieden voor incrementele toestemming.

Voor ontwikkel aars heeft de end-to-end-ervaring voor dit scenario de volgende aspecten:

- Daemon-toepassingen kunnen alleen worden gebruikt in azure AD-tenants. Het kan zinvol zijn om een daemon-toepassing te maken die probeert persoonlijke micro soft-accounts te bewerken. Als u een LOB-app (line-of-Business) bent, maakt u uw daemon-app in uw Tenant. Als u een ISV bent, wilt u mogelijk een multi tenant-daemon-toepassing maken. Het moet worden gezonden door elke Tenant beheerder.
- Tijdens de [registratie](./scenario-daemon-app-registration.md)van de toepassing is de **antwoord-URI** niet nodig. U moet geheimen of certificaten of ondertekende verklaringen met Azure AD delen, en u moet machtigingen voor toepassingen aanvragen en toestemming geven om deze app-machtigingen te gebruiken.
- De [configuratie](./scenario-daemon-app-configuration.md) van de toepassing moet client referenties opgeven die worden gedeeld met Azure ad tijdens de registratie van de toepassing.
- Het [bereik](scenario-daemon-acquire-token.md#scopes-to-request) dat wordt gebruikt om een token met de client referentie stroom te verkrijgen, moet een statisch bereik zijn.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon-app-app-registratie](./scenario-daemon-app-registration.md)

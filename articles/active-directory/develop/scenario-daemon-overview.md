---
title: Een daemon-app bouwen die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-API's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df06c4c55941f4424d6b90d2846af17bf055b2e4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885460"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: Daemon-toepassing die web-API's aanroept

Leer alles wat u nodig hebt om een daemon-toepassing te bouwen die web-API's aanroept.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Overzicht

Uw toepassing kan een token aanschaffen om namens zichzelf een web-API aan te roepen (niet namens een gebruiker). Dit scenario is handig voor daemontoepassingen. Het maakt gebruik van de standaard OAuth 2.0 [client referenties](v2-oauth2-client-creds-grant-flow.md) subsidie.

![Daemon-apps](./media/scenario-daemon-app/daemon-app.svg)

Hier volgen enkele voorbeelden van use cases voor daemon-apps:

- Webtoepassingen die worden gebruikt om gebruikers in te richten of te beheren of batchprocessen in een map uit te brengen
- Desktoptoepassingen (zoals Windows-services op Windows- of daemonprocessen op Linux) die batchtaken uitvoeren of een besturingssysteemservice die op de achtergrond wordt uitgevoerd
- Web API's die mappen moeten manipuleren, niet specifieke gebruikers

Er is nog een veelvoorkomend geval waarin niet-daemon-toepassingen clientreferenties gebruiken: zelfs wanneer ze namens gebruikers handelen, moeten ze om technische redenen toegang krijgen tot een web-API of een bron onder hun eigen identiteit. Een voorbeeld hiervan is toegang tot geheimen in Azure Key Vault of een Azure SQL-database voor een cache.

Toepassingen die een token voor hun eigen identiteit verkrijgen:

- Zijn vertrouwelijke clienttoepassingen. Deze apps, gezien het feit dat ze toegang hebben tot bronnen onafhankelijk van gebruikers, moeten hun identiteit te bewijzen. Het zijn ook nogal gevoelige apps. Ze moeten worden goedgekeurd door de azure active directory-tenantbeheerders (Azure AD).
- Een geheim (toepassingswachtwoord of -certificaat) hebben geregistreerd bij Azure AD. Dit geheim wordt doorgegeven tijdens het gesprek met Azure AD om een token te krijgen.

## <a name="specifics"></a>Details

> [!IMPORTANT]
>
> - Gebruikers kunnen geen interactie hebben met een daemon-toepassing. Een daemon applicatie vereist een eigen identiteit. Dit type toepassing vraagt een toegangstoken aan met behulp van de toepassingsidentiteit en presenteert de toepassings-id, de referenties (wachtwoord of certificaat) en de uri voor toepassings-id's naar Azure AD. Na succesvolle verificatie ontvangt de daemon een access token (en een refresh token) van het eindpunt van het Microsoft-identiteitsplatform. Dit token wordt vervolgens gebruikt om de web-API aan te roepen (en wordt zo nodig vernieuwd).
> - Omdat gebruikers geen interactie kunnen hebben met daemon-toepassingen, is incrementele toestemming niet mogelijk. Alle vereiste API-machtigingen moeten worden geconfigureerd bij toepassingsregistratie. De code van de toepassing vraagt alleen statisch gedefinieerde machtigingen. Dit betekent ook dat daemon-toepassingen geen incrementele toestemming ondersteunen.

Voor ontwikkelaars heeft de end-to-end ervaring voor dit scenario de volgende aspecten:

- Daemon-toepassingen kunnen alleen werken in Azure AD-tenants. Het zou geen zin hebben om een daemon applicatie die probeert te manipuleren Microsoft persoonlijke accounts te bouwen. Als u een lob-app-ontwikkelaar (line-of-business) bent, maakt u uw daemon-app in uw tenant. Als u een ISV bent, u een daemon-toepassing met meerdere tenant's maken. Elke tenantbeheerder moet toestemming geven.
- Tijdens [de registratie van](./scenario-daemon-app-registration.md)de aanvraag is het antwoord URI niet nodig. U moet geheimen of certificaten of ondertekende beweringen delen met Azure AD. U moet ook toepassingsmachtigingen aanvragen en beheerderstoestemming verlenen om deze app-machtigingen te gebruiken.
- De [toepassingsconfiguratie](./scenario-daemon-app-configuration.md) moet clientreferenties bieden die tijdens de toepassingsregistratie zijn gedeeld met Azure AD.
- Het [bereik](scenario-daemon-acquire-token.md#scopes-to-request) dat wordt gebruikt om een token met de clientreferentiesstroom te verkrijgen, moet een statisch bereik zijn.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon app - app registratie](./scenario-daemon-app-registration.md)

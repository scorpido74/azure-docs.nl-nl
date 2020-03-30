---
title: Microsoft Graph API | Microsoft Documenten
description: De Microsoft Graph API is een RESTful web API waarmee u toegang krijgt tot Microsoft Cloud-servicebronnen.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136497"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

De Microsoft Graph API is een RESTful web API waarmee u toegang krijgt tot Microsoft Cloud-servicebronnen. Nadat u uw app hebt geregistreerd en verificatietokens voor een gebruiker of service hebt ontvangen, u aanvragen indienen bij de Microsoft Graph API. Zie [Overzicht van Microsoft Graph](https://docs.microsoft.com/graph/overview)voor meer informatie.

Microsoft Graph stelt REST API's en clientbibliotheken bloot om toegang te krijgen tot gegevens over de volgende Microsoft 365-services:
- Office 365-services: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner en SharePoint
- Enterprise Mobility and Security Services: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager en Intune
- Windows 10-services: activiteiten, apparaten, meldingen
- Dynamics 365 Business Central

## <a name="versions"></a>Versies

Microsoft Graph ondersteunt momenteel twee versies: v1.0 en bèta. De v1.0-versie bevat algemeen beschikbare API's. Gebruik de v1.0-versie voor alle productie-apps. De bèta bevat API's die momenteel in preview zijn. Omdat we mogelijk baanbrekende wijzigingen in onze bèta-API's invoeren, raden we u aan de bètaversie alleen te gebruiken om apps te testen die in ontwikkeling zijn; gebruik geen bèta-API's in uw productie-apps. Zie [Versiebeheer, ondersteuning en het overtreden van wijzigingsbeleid voor Microsoft Graph voor](https://docs.microsoft.com/graph/versioning-and-support)meer informatie.

Zie [Microsoft Graph-bètaeindpuntverwijzing](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta) om de bèta-API's te gebruiken

Zie [Microsoft Graph REST API v1.0-verwijzing](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) om de v1.0-API's te gebruiken

## <a name="get-started"></a>Aan de slag

Als u wilt lezen van of schrijven naar een bron zoals een gebruiker of een e-mailbericht, maakt u een verzoek dat er als volgt uitziet:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Zie De API van [Microsoft Graph gebruiken](https://docs.microsoft.com/graph/use-the-api) voor meer informatie over de elementen van de geconstrueerde aanvraag

Quickstart-voorbeelden zijn beschikbaar om u te laten zien hoe u toegang krijgt tot de kracht van de Microsoft Graph API. De beschikbare voorbeelden hebben toegang tot twee services met één verificatie: Microsoft-account en Outlook. Elke quickstart heeft toegang tot informatie uit de profielen van Microsoft-accountgebruikers en geeft afspraken uit hun agenda weer.
De quickstarts omvatten vier stappen:
- Selecteer uw platform
- Uw app-id (client-id)
- Het voorbeeld maken
- Aanmelden en afspraken in uw agenda weergeven

Wanneer u de quickstart voltooit, hebt u een app die klaar is om te worden uitgevoerd. Zie de [veelgestelde vragen van Microsoft Graph snel aan](https://docs.microsoft.com/graph/quick-start-faq)de slag. Zie [Microsoft Graph QuickStart](https://developer.microsoft.com/graph/quick-start)om aan de slag te gaan met de voorbeelden.

## <a name="tools"></a>Hulpprogramma's

Microsoft Graph Explorer is een webgebaseerd hulpmiddel dat u gebruiken om aanvragen te maken en te testen met behulp van Microsoft Graph API's. U hebt toegang tot `https://developer.microsoft.com/graph/graph-explorer`Microsoft Graph Explorer op: .

Postman is een tool die u ook gebruiken om aanvragen te bouwen en te testen met behulp van de Microsoft Graph API's. U Postman `https://www.getpostman.com/`downloaden op:. Als u wilt communiceren met Microsoft Graph in Postman, gebruikt u de Microsoft Graph-verzameling in Postman. Zie [Postbode gebruiken met de Microsoft Graph API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)voor meer informatie.

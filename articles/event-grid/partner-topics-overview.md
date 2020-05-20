---
title: Onderwerpen over Azure Event Grid partners
description: Verzend gebeurtenissen van derden Event Grid SaaS-en PaaS-partners rechtstreeks naar Azure-Services met Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691122"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Onderwerpen over partners in Azure Event Grid (preview-versie)
Met partner onderwerpen kunt u rechtstreeks verbinding maken met gebeurtenis bronnen van derden met Event Grid. Met deze integratie kunt u zich abonneren op gebeurtenissen van partners op dezelfde manier als u zich abonneert op gebeurtenissen van Azure-Services. 

## <a name="available-partners"></a>Beschik bare partners
De eerste partner die beschikbaar is via Event Grid-partner onderwerpen is Auth0. In het [onderwerp Auth0-partner](auth0-overview.md) kunt u uw Auth0-en Azure-accounts verbinden. De integratie om Auth0-gebeurtenissen in realtime te reageren, te registreren en te controleren.

[Probeer het](auth0-how-to.md) vandaag nog door u aan te melden bij uw Auth0-account en een event grid-integratie te maken. Wanneer u op maken klikt in Auth0, ziet u een onderwerp in behandeling van Auth0 in uw Azure-account. Klik op activeren en u kunt gebeurtenis abonnementen maken, uw gebeurtenissen routeren, filteren en afleveren op dezelfde manier als andere gebeurtenis bronnen.

## <a name="pricing"></a>Prijzen
Partner onderwerpen worden in rekening gebracht op hetzelfde bewerkings tarief als systeem onderwerpen.

## <a name="limits"></a>Limieten
De onderwerpen van de partner zijn beschikbaar als open bare preview. Tijdens de open bare preview zijn de onderwerpen van partners onderhevig aan [dezelfde limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) als systeem onderwerpen en aangepaste onderwerpen.

## <a name="how-do-i-become-an-event-grid-partner"></a>Hoe kan ik een Event Grid partner worden?
De infra structuur die is gemaakt ter ondersteuning van deze start, maakt het eenvoudig en snel voor nieuwe partners om hun gebeurtenis mogelijkheden te integreren met Event Grid. Lees de [documentatie](partner-onboarding-overview.md) voor het voorbereiden van partners voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Auth0-partner onderwerp](auth0-overview.md)
- [Het Auth0-partner onderwerp gebruiken](auth0-how-to.md)
- [Word een Event Grid partner](partner-onboarding-overview.md)
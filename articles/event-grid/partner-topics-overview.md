---
title: Onderwerpen over Azure Event Grid partners
description: Verzend gebeurtenissen van derden Event Grid SaaS-en PaaS-partners rechtstreeks naar Azure-Services met Azure Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560276"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Onderwerpen over partners in Azure Event Grid (preview-versie)
Met behulp van partner onderwerpen kunt u rechtstreeks verbinding maken met gebeurtenis bronnen van derden op Azure Event Grid. Met deze integratie kunt u zich abonneren op gebeurtenissen van partners op dezelfde manier als u zich abonneert op gebeurtenissen van Azure-Services. 

## <a name="available-partners"></a>Beschik bare partners
De eerste partner die beschikbaar is via Event Grid-partner onderwerpen is Auth0. U kunt het [Auth0-partner onderwerp](auth0-overview.md) gebruiken om verbinding te maken met uw Auth0-en Azure-accounts. Met de integratie kunt u Auth0-gebeurtenissen in realtime reageren, registreren en controleren.

[Als u dit wilt uitproberen](auth0-how-to.md), meldt u zich aan bij uw Auth0-account en maakt u een event grid-integratie. Nadat u **maken** in Auth0 hebt geselecteerd, ziet u een onderwerp in behandeling Auth0 in uw Azure-account. Selecteer **activeren**en u kunt Event grid-abonnementen maken om uw gebeurtenissen te routeren, filteren en leveren, net zoals u andere gebeurtenis bronnen hebt.

## <a name="pricing"></a>Prijzen
Partner onderwerpen worden in rekening gebracht op hetzelfde bewerkings tarief als systeem onderwerpen.

## <a name="limits"></a>Limieten
De onderwerpen van de partner zijn beschikbaar als open bare preview. Tijdens de open bare preview zijn de onderwerpen van partners onderhevig aan [dezelfde limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) als systeem onderwerpen en aangepaste onderwerpen.

## <a name="how-do-i-become-an-event-grid-partner"></a>Hoe kan ik een Event Grid partner worden?
De infra structuur die is gemaakt ter ondersteuning van deze start, maakt het eenvoudig en snel voor nieuwe partners om hun gebeurtenis mogelijkheden te integreren met Event Grid. Zie voor meer informatie de documentatie voor het [voorbereiden van partners](partner-onboarding-overview.md).

## <a name="next-steps"></a>Volgende stappen

- [Auth0-partner onderwerp](auth0-overview.md)
- [Het Auth0-partner onderwerp gebruiken](auth0-how-to.md)
- [Word een Event Grid partner](partner-onboarding-overview.md)
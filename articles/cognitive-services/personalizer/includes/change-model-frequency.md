---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055372"
---
### <a name="change-the-model-update-frequency"></a>Bijwerkfrequentie voor model wijzigen

Wijzig voor de Personalizer-resource, op de pagina **Configuratie** in Azure Portal, de **frequentie van de modelupdate** in tien seconden. Met deze korte duur wordt de service snel getraind, zodat u kunt zien hoe de belangrijkste actie voor elke iteratie wordt gewijzigd.

![Bijwerkfrequentie voor model wijzigen](../media/settings/configure-model-update-frequency-settings.png)

Wanneer een Personalizer-lus voor het eerst wordt geïnstantieerd, is er geen model, omdat er geen Reward-API-aanroepen zijn die voor training kunnen worden gebruikt. Rank-aanroepen retourneren gelijke kansen voor elk item. Uw toepassing moet toch altijd inhoud plaatsen met de uitvoer van RewardActionId.
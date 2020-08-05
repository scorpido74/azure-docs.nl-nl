---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: d03d904de68720874ea175c95244ba80c586df82
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133812"
---
## <a name="change-the-model-update-frequency"></a>Bijwerkfrequentie voor model wijzigen

Wijzig voor de Personalizer-resource, op de pagina **Configuratie** in Azure Portal, de **frequentie van de modelupdate** in tien seconden. Met deze korte duur wordt de service snel getraind, zodat u kunt zien hoe de belangrijkste actie voor elke iteratie wordt gewijzigd.

![Bijwerkfrequentie voor model wijzigen](../media/settings/configure-model-update-frequency-settings.png)

Wanneer een Personalizer-lus voor het eerst wordt geïnstantieerd, is er geen model, omdat er geen Reward-API-aanroepen zijn die voor training kunnen worden gebruikt. Rank-aanroepen retourneren gelijke kansen voor elk item. Uw toepassing moet toch altijd inhoud plaatsen met de uitvoer van RewardActionId.
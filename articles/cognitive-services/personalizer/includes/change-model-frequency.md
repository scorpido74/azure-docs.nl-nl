---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122892"
---
## <a name="change-the-model-update-frequency"></a>De frequentie van de modelupdate wijzigen

Wijzig de updatefrequentie van het model in de **Azure-portal** in de pagina Personalizer op de pagina **Configuratie** in 10 seconden. Deze korte duur zal de service snel trainen, zodat u zien hoe de hoogste actie voor elke iteratie verandert.

![Frequentie van modelupdate wijzigen](../media/settings/configure-model-update-frequency-settings.png)

Wanneer een Personalizer-lus eerst wordt geinstantieerd, is er geen model omdat er geen Reward API-aanroepen zijn om van te trainen. Rangcalls geven voor elk item dezelfde waarschijnlijkheid terug. Uw toepassing moet nog steeds altijd de inhoud rangschikken met behulp van de uitvoer van RewardActionId.
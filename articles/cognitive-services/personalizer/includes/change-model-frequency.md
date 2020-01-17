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
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122892"
---
## <a name="change-the-model-update-frequency"></a>De update frequentie van het model wijzigen

Wijzig in de Azure Portal in de resource personaliseren op de pagina **configuratie** de **verwerkings frequentie** van het model in 10 seconden. Met deze korte duur wordt de service snel getraind, zodat u kunt zien hoe de belangrijkste actie voor elke herhaling wordt gewijzigd.

![Update frequentie van het model wijzigen](../media/settings/configure-model-update-frequency-settings.png)

Wanneer een aangepaste lus voor het eerst wordt geïnstantieerd, is er geen model, omdat er geen belonings-API-aanroepen zijn voor de trein van. Rang gesprekken retour neren een gelijke kansen voor elk item. Uw toepassing moet toch altijd inhoud plaatsen met de uitvoer van RewardActionId.
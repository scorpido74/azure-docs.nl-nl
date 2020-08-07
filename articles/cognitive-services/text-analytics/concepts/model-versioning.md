---
title: De model versie opgeven in Text Analytics v3
titleSuffix: Azure Cognitive Services
description: Meer informatie over het opgeven van het Text Analytics-API model dat wordt gebruikt voor uw gegevens.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: b3e8c2c8a5e95cc5c862112410f5073a5e600651
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986760"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Model versie beheer in de Text Analytics-API

Met versie 3 van de Text Analytics-API kunt u de model versie kiezen die wordt gebruikt voor uw gegevens. Gebruik de optionele `model-version` para meter om de versie van het model in uw API-aanvragen te selecteren. Bijvoorbeeld: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Als deze para meter niet is opgegeven, wordt de API standaard ingesteld op de laatste stabiele versie. 

## <a name="available-versions"></a>Beschik bare versies

Gebruik de onderstaande tabel om te bepalen welke model versies door elk eind punt worden ondersteund.


| Eindpunt                        | Ondersteunde versies                                     | nieuwste versie |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Meer informatie over de updates voor deze modellen vindt u in [Wat is er nieuw](../whats-new.md).

## <a name="text-analytics-for-health"></a>Text Analytics voor de status

De [Text Analytics voor de status](../how-tos/text-analytics-for-health.md) container maakt gebruik van afzonderlijke model versies dan de bovenstaande API-eind punten.  Houd er rekening mee dat er slechts één model versie beschikbaar is per container installatie kopie.

| Eindpunt                        | Tag container installatie kopie                     | Model versie |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview`of de nieuwste | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)

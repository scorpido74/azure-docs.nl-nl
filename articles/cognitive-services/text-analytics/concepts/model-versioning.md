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
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: c9c598d385768e604d9b42e3ea055629e6ae2866
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108546"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Model versie beheer in de Text Analytics-API

Met versie 3 van de Text Analytics-API kunt u de model versie kiezen die wordt gebruikt voor uw gegevens. Gebruik de optionele `model-version` para meter om de versie van het model in uw API-aanvragen te selecteren. Bijvoorbeeld: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Als deze para meter niet is opgegeven, wordt de API standaard ingesteld op de laatste stabiele versie. 

## <a name="available-versions"></a>Beschik bare versies

Gebruik de onderstaande tabel om te bepalen welke model versies door elk eind punt worden ondersteund.


| Eindpunt                        | Ondersteunde versies                       | nieuwste versie |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


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
* [Sentiment analyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entiteit herkenning](../how-tos/text-analytics-how-to-entity-linking.md)

---
title: Veelgestelde vragen over metrische gegevens adviseur
titleSuffix: Azure Cognitive Services
description: Veelgestelde vragen over de service Metrics Advisor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 0fde9a0f46073a2f3a24962ea58431581455f474
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936002"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Veelgestelde vragen over metrische gegevens adviseur

### <a name="what-is-the-cost-of-my-instance"></a>Wat zijn de kosten van mijn instantie?

Er zijn momenteel geen kosten voor het gebruik van uw exemplaar tijdens de preview-versie.

### <a name="why-is-the-demo-website-readonly"></a>Waarom is de demo website alleen-lezen?

De [demo website](https://anomaly-detector.azurewebsites.net/) is openbaar beschikbaar. Dit exemplaar wordt alleen-lezen gemaakt om te voor komen dat gegevens per ongeluk worden geüpload.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Waarom kan ik de resource niet maken? De prijs categorie is niet beschikbaar en geeft aan dat u al 1 S0 hebt gemaakt voor dit abonnement?

:::image type="content" source="media/pricing.png" alt-text="Bericht wanneer een F0-resource al bestaat":::

Tijdens de open bare preview-fase kan slechts één exemplaar van de para meters Advisor worden gemaakt onder een abonnement, in één regio.

Als u al een exemplaar hebt gemaakt in dezelfde regio met hetzelfde abonnement, kunt u een andere regio of een ander abonnement proberen om een nieuw exemplaar te maken. U kunt ook een bestaande instantie verwijderen om een nieuwe te maken.

Als u het bestaande exemplaar al hebt verwijderd, maar nog steeds de fout ziet, wacht u ongeveer 20 minuten nadat de resource is verwijderd voordat u een nieuw exemplaar maakt.

## <a name="basic-concepts"></a>Basisbegrippen

### <a name="what-is-multi-dimensional-time-series-data"></a>Wat zijn multi-dimensionale time series-gegevens?

Zie de definitie voor [multi-dimensionale metrische gegevens](glossary.md#multi-dimensional-metric)  in de woorden lijst.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Hoeveel gegevens zijn er nodig om de anomalie detectie te starten?

Ten minste één gegevens punt kan anomalie detectie activeren. Dit brengt echter geen beste nauw keurigheid. De service gaat ervan uit dat een venster met eerdere gegevens punten wordt gebruikt door de waarde die u hebt opgegeven als de regel voor het door voeren van gegevens invoer tijdens het maken van de gegevensfeed.

We raden u aan enkele gegevens te hebben voordat u de tijds tempel wilt detecteren.
Op basis van de granulatie van uw gegevens varieert de aanbevolen gegevens hoeveelheid zoals hieronder.

| Granulariteit | Aanbevolen gegevens hoeveelheid voor detectie |
| ----------- | ------------------------------------- |
| Minder dan 5 minuten | 4 dagen aan gegevens |
| 5 minuten tot 1 dag | 28 dagen aan gegevens |
| Meer dan 1 dag, tot 31 dagen | 4 jaar aan gegevens |
| Meer dan 31 dagen | 48 jaar aan gegevens |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Waarom worden afwijkingen van historische gegevens niet gedetecteerd door Advisor.

Metrics Advisor is ontworpen voor het detecteren van live streaming-gegevens. Er is een beperking van de maximale lengte van historische gegevens die door de service wordt teruggestuurd en de afwijkings detectie uit te voeren. Dit betekent dat alleen gegevens punten na een bepaald vroeg tijds tempel afwijkende detectie resultaten hebben. De vroegste tijds tempel is afhankelijk van de granulatie van uw gegevens.

Op basis van de granulatie van uw gegevens worden de volgende gegevens weer gegeven: de lengte van de historisch bewaarde resultaten voor de detectie van afwijkingen.

| Granulariteit | Maximale lengte van historische gegevens voor anomalie detectie |
| ----------- | ------------------------------------- |
| Minder dan 5 minuten | Time-outtijd-13 uur |
| 5 minuten tot minder dan 1 uur | Time-out voor onboarding-4 dagen  |
| 1 uur tot minder dan 1 dag | Onboard-tijd-14 dagen  |
| 1 dag | Onboard tijd-28 dagen  |
| Meer dan 1 dag, minder dan 31 dagen | Tijd voor onboarding-2 jaar  |
| Meer dan 31 dagen | Onboard tijd-24 jaar   |

### <a name="more-concepts-and-technical-terms"></a>Meer concepten en technische voor waarden

Ga naar de [woorden lijst](glossary.md) voor meer informatie.

## <a name="how-do-i-detect-such-kinds-of-anomalies"></a>Hoe kan ik deze soorten afwijkingen detecteren? 

### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Hoe kan ik detecteert pieken & spannings dips als afwijkingen?

Als er vaste drempel waarden vooraf zijn gedefinieerd, kunt u de ' harde drempel waarde ' in werkelijkheid hand matig instellen in [anomalie detectie configuraties](how-tos/configure-metrics.md#anomaly-detection-methods).
Als er geen drempels zijn, kunt u Slimme detectie gebruiken. dit wordt mogelijk gemaakt door AI. Raadpleeg [de detectie configuratie aanpassen](how-tos/configure-metrics.md#tune-the-detecting-configuration) voor meer informatie.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Hoe kan ik detecteert u niet-conformiteit met reguliere (seizoen) patronen als afwijkingen?

"Slimme detectie" kan het patroon van uw gegevens, met inbegrip van seizoensgebonden patronen, ontdekken. Vervolgens worden de gegevens punten gedetecteerd die niet voldoen aan de reguliere patronen als afwijkingen. Raadpleeg [de detectie configuratie aanpassen](how-tos/configure-metrics.md#tune-the-detecting-configuration) voor meer informatie.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Hoe kan ik vaste lijnen detecteren als afwijkingen?

Als uw gegevens normaal gesp roken onstabiel zijn en een heleboel schommelingen hebben en u wilt worden gewaarschuwd wanneer het te stabiel of zelfs een vlakke lijn wordt, kan ' drempel waarde wijzigen ' worden geconfigureerd om dergelijke gegevens punten te detecteren wanneer de wijziging te klein is.
Raadpleeg de [configuraties voor anomalie detectie](how-tos/configure-metrics.md#anomaly-detection-methods) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- [Overzicht van Metrics Advisor](overview.md)
- [Probeer de demo site](quickstarts/explore-demo.md)
- [De webportal gebruiken](quickstarts/web-portal.md)
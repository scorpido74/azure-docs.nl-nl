---
title: Uw Azure Time Series Insights omgeving schalen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw Azure Time Series Insights omgeving kunt schalen. Gebruik de Azure Portal om capaciteit toe te voegen of af te trekken binnen een prijs-SKU.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 13fa2b892013cf4a3fb96220c901030c6b0aee0b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129086"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Uw Time Series Insights omgeving schalen

In dit artikel wordt beschreven hoe u de capaciteit van uw omgeving wijzigt in uw Time Series Insights-omgeving met behulp van de Azure Portal. Capaciteit is de vermenigvuldiger die wordt toegepast op de ingangs frequentie, opslag capaciteit en kosten die zijn gekoppeld aan uw geselecteerde SKU.

U kunt de Azure Portal gebruiken om de capaciteit binnen een bepaalde prijs-SKU te verg Roten of te verkleinen.

Het wijzigen van de SKU van de prijs categorie is echter niet toegestaan. Een omgeving met een mijn prijs-SKU voor S1 kan bijvoorbeeld niet worden geconverteerd naar een S2 of andersom.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Snelheid en capaciteit van S1 SKU-ingang

| Capaciteit van S1-SKU | Ingangs frequentie | Maximale opslag capaciteit
| --- | --- | --- |
| 1 | 1 GB (1.000.000 gebeurtenissen) | 30 GB (30.000.000 gebeurtenissen) per maand |
| 10 | 10 GB (10.000.000 gebeurtenissen) | 300 GB (300.000.000 gebeurtenissen) per maand |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Tarieven en capaciteit van S2 SKU-ingang

| Capaciteit van S2-SKU | Ingangs frequentie | Maximale opslag capaciteit
| --- | --- | --- |
| 1 | 10 GB (10.000.000 gebeurtenissen) | 300 GB (300.000.000 gebeurtenissen) per maand |
| 10 | 100 GB (100.000.000 gebeurtenissen) | 3 TB (3.000.000.000 gebeurtenissen) per maand |

Capaciteit schaalt lineair, dus een S1-SKU met capaciteit 2 ondersteunt 2 GB (2.000.000) gebeurtenissen per dag ingangs snelheden en 60 GB (60.000.000 gebeurtenissen) per maand.

## <a name="change-the-capacity-of-your-environment"></a>De capaciteit van uw omgeving wijzigen

1. Zoek en selecteer uw Time Series Insights omgeving in het Azure Portal.

1. Selecteer **configureren**in het menu voor uw time series Insights omgeving.

   [![. png configureren](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Pas de schuif regelaar **capaciteit** aan om de capaciteit te selecteren die voldoet aan de vereisten voor uw ingangs tarieven en opslag capaciteit. Let op de **ingangs rente**, **opslag capaciteit**en **geschatte kosten** update dynamisch om de impact van de wijziging weer te geven.

   [![Volledig](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   U kunt ook het nummer van de capaciteits-multiplier in het tekstvak rechts van de schuif regelaar typen.

1. Selecteer **Opslaan** om de omgeving te schalen. De voortgangs indicator wordt weer gegeven totdat de wijziging wordt doorgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Controleer of de nieuwe capaciteit [voldoende is om beperking te voor komen](time-series-insights-diagnose-and-solve-problems.md).
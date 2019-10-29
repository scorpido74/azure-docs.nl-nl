---
title: Uw Azure Time Series Insights omgeving schalen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw Azure Time Series Insights omgeving kunt schalen. Gebruik de Azure Portal om capaciteit toe te voegen of af te trekken binnen een prijs-SKU.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991166"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Uw Time Series Insights omgeving schalen

In dit artikel wordt beschreven hoe u de capaciteit van uw Time Series Insights omgeving kunt wijzigen met behulp van de [Azure Portal](https://portal.azure.com). Capaciteit is de vermenigvuldiger die wordt toegepast op de ingangs frequentie, opslag capaciteit en kosten die zijn gekoppeld aan uw geselecteerde SKU.

U kunt de Azure Portal gebruiken om de capaciteit binnen een bepaalde prijs-SKU te verg Roten of te verkleinen.

Het wijzigen van de SKU van de prijs categorie is echter niet toegestaan. Een omgeving met een mijn prijs-SKU voor S1 kan bijvoorbeeld niet worden geconverteerd naar een S2 of andersom.

## <a name="ga-limits"></a>GA-limieten

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>De capaciteit van uw omgeving wijzigen

1. Zoek en selecteer uw Time Series Insights omgeving in het Azure Portal.

1. Selecteer **configureren**in het menu voor uw time series Insights omgeving.

   [![configureren. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Pas de schuif regelaar **capaciteit** aan om de capaciteit te selecteren die voldoet aan de vereisten voor uw ingangs tarieven en opslag capaciteit. Let op de **ingangs rente**, **opslag capaciteit**en **geschatte kosten** update dynamisch om de impact van de wijziging weer te geven.

   [Schuif regelaar![](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   U kunt ook het nummer van de capaciteits-multiplier in het tekstvak rechts van de schuif regelaar typen.

1. Selecteer **Opslaan** om de omgeving te schalen. De voortgangs indicator wordt weer gegeven totdat de wijziging wordt doorgevoerd.

1. Controleer of de nieuwe capaciteit [voldoende is om beperking te voor komen](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie [uitleg over retentie in time series Insights](time-series-insights-concepts-retention.md).

- Meer informatie over het [configureren van gegevens retentie in azure time series Insights](time-series-insights-how-to-configure-retention.md).

- Meer informatie over [het plannen van uw omgeving](time-series-insights-environment-planning.md).
---
title: Retentie configureren in uw omgeving - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het configureren van retentie in uw Azure Time Series Insights-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 3fdc007caaa4cb79f6083599a5bc176bc022fb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278631"
---
# <a name="configuring-retention-in-time-series-insights"></a>Retentie configureren in inzichten in time-series

In dit artikel wordt beschreven hoe u **de bewaartijd en** **de opslaglimiet voor** gegevens configureren in Azure Time Series Insights.

## <a name="summary"></a>Samenvatting

Elke Azure Time Series Insights-omgeving heeft een instelling om **de bewaartijd voor gegevens**te configureren. De waarde varieert van 1 tot 400 dagen. De gegevens worden verwijderd op basis van de opslagcapaciteit of bewaarduur van het milieu (1-400), afhankelijk van wat het eerst komt.

Elke Time Series Insights-omgeving heeft een extra instelling **Opslaglimiet overschreden gedrag**. Met deze instelling bepaalt u het in- en zuiveringsgedrag wanneer de maximale capaciteit van een omgeving is bereikt. Er zijn twee gedragingen om uit te kiezen:

- **Oude gegevens wissen** (standaard)
- **Binnenlaten onderbreken**

Voor gedetailleerde informatie om deze instellingen beter te begrijpen, bekijkt [u Inzicht in time series insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Gegevensretentie configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek uw bestaande Time Series Insights-omgeving. Selecteer **Alle bronnen** in het menu aan de linkerkant van de Azure-portal. Selecteer uw Time Series Insights-omgeving.

1. Selecteer onder de kop **Instellingen** de optie **Opslagconfiguratie**.

    [![Selecteer onder Instellingen de optie Opslagconfiguratie](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selecteer de **bewaartijd voor gegevens (in dagen)** om de bewaartijd te configureren met de schuifregelaarbalk of typ een getal in tekstvak.

1. Let op de **instelling Capaciteit,** omdat deze configuratie van invloed is op de maximale hoeveelheid gegevensgebeurtenissen en de totale opslagcapaciteit voor het opslaan van gegevens.

1. Het inschakelen van de **opslaglimiet overschreed de gedragsinstelling.** Selecteer **Oude gegevens wissen** of **Ingressgedrag onderbreken.**

    [![Pauze ingress - accepteren en opslaan.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Bekijk de documentatie om inzicht te krijgen in de mogelijke risico's van gegevensverlies. Selecteer **Opslaan** om de wijzigingen te configureren.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [inzicht in Time Series Insights](time-series-insights-concepts-retention.md)voor meer informatie.

- Meer informatie over [het schalen van uw Time Series Insights-omgeving.](time-series-insights-how-to-scale-your-environment.md)

- Meer informatie over [het plannen van uw omgeving.](time-series-insights-environment-planning.md)

---
title: Bewaar termijn configureren in uw Azure Time Series Insights omgeving | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Bewaar periode in uw Azure Time Series Insights omgeving kunt configureren.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: ff4d326af691ae27894dc94d7581ba68951f090e
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990062"
---
# <a name="configuring-retention-in-time-series-insights"></a>Bewaar periode in Time Series Insights configureren

In dit artikel wordt beschreven hoe u de **Bewaar tijd voor gegevens** en de **opslag limiet hebt overschreden** in azure time series Insights.

## <a name="summary"></a>Samenvatting

Elke Azure Time Series Insights omgeving heeft een instelling voor het configureren van **gegevens retentie tijd**. De waarde ligt tussen 1 en 400 dagen. De gegevens worden verwijderd op basis van de opslag capaciteit van de omgeving of de Bewaar periode (1-400), afhankelijk van wat het eerste komt.

Voor elke Time Series Insights omgeving is een extra **opslag limiet**ingesteld. Met deze instelling bepaalt u het gedrag van ingang en leegmaken wanneer de maximale capaciteit van een omgeving wordt bereikt. Er zijn twee manieren om te kiezen:

- **Oude gegevens opschonen** (standaard)
- **Ingangs onderbrekingen**

Lees de informatie [over retentie in time series Insights](time-series-insights-concepts-retention.md)voor gedetailleerde gegevens om deze instellingen beter te begrijpen.  

## <a name="configure-data-retention"></a>Gegevensretentie configureren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek uw bestaande Time Series Insights-omgeving. Selecteer **alle resources** in het menu aan de linkerkant van het Azure Portal. Selecteer uw Time Series Insights-omgeving.

1. Selecteer **configureren**onder de kop **instellingen** .

    [![instellingen selecteren en configureer vervolgens](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Selecteer de **Bewaar tijd voor gegevens (in dagen)** om de retentie te configureren met behulp van de schuif regelaar of typ een getal in het tekstvak.

1. Let op de **capaciteits** instelling, omdat deze configuratie invloed heeft op de maximale hoeveelheid gegevens gebeurtenissen en de totale opslag capaciteit voor het opslaan van gegevens.

1. De instelling voor het gedrag van de **opslag limiet is overschreden** . Selecteer **oude gegevens leegmaken** of **ingangs gedrag onderbreken** .

    [![bewaren van gegevens-accepteren en opslaan.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Accepteer het selectie vakje om aan te geven dat u de documentatie hebt gecontroleerd en inzicht hebt in de mogelijke Risico's van gegevens verlies. Selecteer **Opslaan** om de wijzigingen te configureren.

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie [uitleg over retentie in time series Insights](time-series-insights-concepts-retention.md).

- Meer informatie [over het schalen van uw time series Insights-omgeving](time-series-insights-how-to-scale-your-environment.md).

- Meer informatie over [het plannen van uw omgeving](time-series-insights-environment-planning.md).
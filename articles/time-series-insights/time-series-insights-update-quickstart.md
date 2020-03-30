---
title: 'Snelstart: de demoomgeving van De preview verkennen - Azure Time Series Insights | Microsoft Documenten'
description: Ontdek de belangrijkste functies van de demo-omgeving van Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 02/07/2020
ms.openlocfilehash: dc4a8da69b0398c6487008c106a9f5bcdb8a885e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77110231"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Snelstart: de demoomgeving van Azure Time Series Insights Preview verkennen

Met deze snelle start u aan de slag met de Azure Time Series Insights Preview-omgeving. In de gratis demo tour je door belangrijke functies die zijn toegevoegd aan Time Series Insights Preview.

De Time Series Insights Preview demo-omgeving bevat een scenariobedrijf, Contoso, dat twee windturbineparken exploiteert. Elke boerderij heeft 10 turbines. Elke turbine beschikt over 20 sensoren die elke minuut gegevens rapporteren aan Azure IoT Hub. De sensoren verzamelen informatie over de weersomstandigheden, blade pitch, en yaw positie. Informatie over de prestaties van de generator, het gedrag van de versnellingsbak en veiligheidsmonitoren wordt ook geregistreerd.

In deze quickstart leert u hoe u Time Series Insights gebruiken om bruikbare inzichten in Contoso-gegevens te vinden. U voert ook een korte analyse van de hoofdoorzaak uit om kritieke storingen beter te voorspellen en onderhoud uit te voeren.

> [!IMPORTANT]
> Maak een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) als u er geen hebt.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Verkenner van Time Series Insights nader bekeken in een demo-omgeving

De Time Series Insights Preview-verkenner toont historische gegevens en analyse van de hoofdoorzaak. Aan de slag:

1. Ga naar de [Contoso Wind Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving.  

1. Als u wordt gevraagd, meldt u zich aan bij de Time Series Insights-verkenner met uw Azure-accountreferenties.

## <a name="work-with-historical-data"></a>Werken met historische gegevens

1. In **Contoso Plant 1**, kijk naar windturbine **W7**.  

   1. Wijzig het weergavebereik in **1/1/17 20:00:00:00.00 naar 3/10/17 20:00:00.00 (UTC).**
   1. Als u een sensor wilt selecteren, selecteert u **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed**. Controleer vervolgens de waarden die worden weergegeven.

      [![W7 in Contoso Plant 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Onlangs heeft Contoso brand geconstateerd in windturbine **W7**. De meningen lopen uiteen over de oorzaak van de brand. In Time Series Insights wordt de brandmeldsensor weergegeven die tijdens de brand is geactiveerd.

   1. Wijzig het weergavebereik in **3/9/17 20:00:00:00.00 naar 3/10/17 20:00:00.00 (UTC).**
   1. Selecteer **Veiligheidssysteem** > **FireAlert**.

      [![Contoso vond een brand in windturbine W7](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Bekijk andere gebeurtenissen rond de tijd van de brand om te begrijpen wat er gebeurd is. Oliedruk en actieve waarschuwingen piekten vlak voor de brand.

   1. Selecteer **Pitch System** > **HydraulicOilPressure**.
   1. Selecteer**ActiveWarning** **Pitch System** > .

      [![Andere gebeurtenissen rond dezelfde tijd bekijken](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. De oliedruk en actieve waarschuwingssensoren piekten vlak voor de brand. Vouw de weergegeven tijdreeks en bekijk andere tekenen die duidelijk waren in de aanloop naar de brand. Beide sensoren fluctueerden constant in de tijd. De schommelingen wijzen op een hardnekkig en zorgwekkend patroon.

    * Wijzig het weergavebereik in **2/24/17 20:00:00:00.00 naar 3/10/17 20:00:00.00 (UTC)**.

      [![Oliedruk en actieve waarschuwingssensoren ook spiked](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Het onderzoeken van twee jaar van historische gegevens onthult een andere brand gebeurtenis die dezelfde sensor schommelingen had.

    * Wijzig het weergavebereik in **1/1/16 naar 12/31/17** (alle gegevens).

      [![Zoek naar historische patronen](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Door time series insights en sensortelemetrie te gebruiken, hebben we een langetermijntrend ontdekt die verborgen zit in de historische gegevens. Met deze nieuwe inzichten kunnen we:

* Leg uit wat er werkelijk is gebeurd.
* Corrigeer het probleem.
* Zet betere waarschuwingsmeldingssystemen op hun plaats.

## <a name="root-cause-analysis"></a>Hoofdoorzaakanalyse

1. Sommige scenario's vereisen geavanceerde analyse om aanwijzingen in gegevens te ontdekken. Selecteer de windmolen **W6** op datum **6/25**.

    1. Wijzig het weergavebereik in **6/1/17 20:00:00:00.00 naar 7/1/17 20:00:00.00 (UTC).**
    1. Selecteer **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning**.

       [![Het weergavebereik wijzigen en W6 selecteren](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. De waarschuwing geeft een probleem met de spanning van de generator. Het totale vermogen van de generator bevindt zich binnen de normale parameters in het huidige interval. Door het verhogen van ons interval, een ander patroon ontstaat. Een drop-off is evident.

    1. Verwijder de **VoltageActuatorSwitchWarning-sensor.**
    1. Selecteer **Generatorsysteem** > **ActivePower**.
    1. Wijzig het interval in **3d**.

       [![Het interval wijzigen in 3d](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Door het tijdsbereik uit te breiden, kunnen we bepalen of het probleem is gestopt of dat het doorgaat.

    * Verleng de tijdspanne tot 60 dagen.

      [![Verleng de tijdspanne tot 60 dagen](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Andere sensorgegevenspunten kunnen worden toegevoegd om meer context te bieden. Hoe meer sensoren we bekijken, hoe beter ons begrip van het probleem is. Laten we een markering neerzetten om de werkelijke waarden weer te geven. 

    1. Selecteer **Generatorsysteem**en selecteer vervolgens drie sensoren: **GridVoltagePhase1**, **GridVoltagePhase2**en **GridVoltagePhase3**.
    1. Verwijder een markering van het laatste gegevenspunt in het zichtbare gedeelte.

       [![Een markering laten vallen](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Twee van de spanningssensoren werken vergelijkbaar en binnen normale parameters. Het lijkt erop dat de **GridVoltagePhase3** sensor is de boosdoener.

1. Met zeer contextuele gegevens toegevoegd, de fase 3 drop-off lijkt nog meer het probleem te zijn. We hebben een goede aanwijzing voor de oorzaak van de waarschuwing. We zijn klaar om het probleem door te verwijzen naar ons onderhoudsteam.  

    * Wijzig het display om alle **sensoren van het generatorsysteem** op dezelfde grafiekschaal te bedekken.

      [![Het display wijzigen om alles op te nemen](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de snelle start hebt voltooid, ruimt u de resources op die u hebt gemaakt:

1. Selecteer **alle bronnen**in het linkermenu in de [Azure-portal](https://portal.azure.com)en zoek uw Azure Time Series Insights-brongroep.
1. Verwijder de volledige resourcegroep (en alle bronnen daarin) door Elke resource afzonderlijk **te** verwijderen of te verwijderen.

## <a name="next-steps"></a>Volgende stappen

U bent klaar om uw eigen Time Series Insights Preview-omgeving te maken. Om te beginnen:

> [!div class="nextstepaction"]
> [Een Time Series Insights-omgeving (preview) plannen](time-series-insights-update-plan.md)

Leer de demo en de bijbehorende functies te gebruiken:

> [!div class="nextstepaction"]
> [De Time Series Insights Preview explorer](time-series-insights-update-explorer.md)
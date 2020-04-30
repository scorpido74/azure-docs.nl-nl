---
title: 'Snelstartgids: Verken de preview-demo omgeving-Azure Time Series Insights | Microsoft Docs'
description: Bekijk de belangrijkste functies van de demo omgeving van Azure Time Series Insights preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/27/2020
ms.openlocfilehash: c6cfec1d00fbd5b18e01afb0b51598e615cb0946
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82189195"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Snelstartgids: Verken de Azure Time Series Insights preview-demo omgeving

In deze Snelstartgids kunt u aan de slag met de Azure Time Series Insights preview-omgeving. In de gratis demo bekijkt u de belangrijkste functies die zijn toegevoegd aan Time Series Insights preview.

De Time Series Insights preview-demo omgeving bevat een scenario bedrijf, contoso, dat twee wind turbine-Farms exploiteert. Elke farm heeft 10 turbines. Elke turbine beschikt over 20 sensoren die elke minuut gegevens rapporteren aan Azure IoT Hub. De Sens oren verzamelen informatie over weers omstandigheden, Blade Pitch en yaw positie. Informatie over de prestaties van de generator, het gedrag van de versnellingsbak en veiligheids monitors wordt ook vastgelegd.

In deze Quick Start leert u hoe u Time Series Insights kunt gebruiken om inzicht te krijgen in de gegevens van contoso. U voert ook een korte analyse van de hoofd oorzaak uit om kritieke fouten beter te voors pellen en onderhoud uit te voeren.

> [!IMPORTANT]
> Maak een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) als u er nog geen hebt.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Verkenner van Time Series Insights nader bekeken in een demo-omgeving

In de Time Series Insights preview Explorer worden historische gegevens en analyse van de hoofd oorzaak gedemonstreerd. Aan de slag:

1. Ga naar de [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving.  

1. Als u hierom wordt gevraagd, meldt u zich aan bij de Time Series Insights Explorer met behulp van de referenties van uw Azure-account.

## <a name="work-with-historical-data"></a>Werken met historische gegevens

1. Bekijk in **Contoso plant 1**het wind turbine **W7**.  

   1. Wijzig het weergave bereik in **1/1/17 20:00:00.00 tot 3/10/17 20:00:00.00 (UTC)**.
   1. Als u een sensor wilt selecteren, selecteert u **Contoso plant 1** > **W7** > **Generator systeem** > **GeneratorSpeed**. Controleer vervolgens de waarden die worden weer gegeven.

      [![W7 in contoso plant 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Onlangs heeft Contoso brand geconstateerd in windturbine **W7**. De mening is afhankelijk van wat het vuur heeft veroorzaakt. In Time Series Insights wordt de brand waarschuwing weer gegeven die tijdens de brand werd geactiveerd.

   1. Wijzig het weergave bereik in **3/9/17 20:00:00.00 tot 3/10/17 20:00:00.00 (UTC)**.
   1. Selecteer **veiligheids systeem** > **FireAlert**.

      [![Contoso heeft een vuur in de wind turbine W7 gevonden](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Bekijk andere gebeurtenissen rond het tijdstip van de brand om te begrijpen wat er is gebeurd. De olie druk en actieve waarschuwingen worden net vóór de brand weer gegeven.

   1. Selecteer **systeem** > **HydraulicOilPressure**verkopen.
   1. Selecteer **systeem** > **ActiveWarning**verkopen.

      [![Andere gebeurtenissen rond hetzelfde tijdstip controleren](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. De olie druk en actieve waarschuwings sensors zijn direct vóór de brand opgetreden. Vouw de weer gegeven tijd reeks uit om andere tekens te bekijken waarvan duidelijk is dat deze de brand veroorzaakt. Beide Sens oren schommelen consistent in de loop van de tijd. De schommelingen geven een persistent en Worrisome patroon aan.

    * Wijzig het weergave bereik in **2/24/17 20:00:00.00 tot 3/10/17 20:00:00.00 (UTC)**.

      [![De belasting druk en actieve waarschuwings sensors zijn ook gepiekd](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Wanneer er twee jaar historische gegevens worden onderzocht, wordt een andere brand gebeurtenis met dezelfde sensor schommelingen onthuld.

    * Wijzig het weergave bereik in **1/1/16 tot 12/31/17** (alle gegevens).

      [![Zoeken naar historische patronen](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Door gebruik te maken van Time Series Insights en de telemetrie van de sensor, hebben we een lange termijn trend in de historische gegevens gedetecteerd. Met deze nieuwe inzichten kunnen we het volgende doen:

* Leg uit wat er daad werkelijk is gebeurd.
* Los het probleem op.
* Plaats betere systemen voor waarschuwings meldingen.

## <a name="root-cause-analysis"></a>Hoofdoorzaakanalyse

1. Voor sommige scenario's is geavanceerde analyse vereist om aanwijzingen in gegevens op te slaan. Selecteer de Windmill **W6** op datum **6/25**.

    1. Wijzig het weergave bereik in **6/1/17 20:00:00.00 tot 7/1/17 20:00:00.00 (UTC)**.
    1. Selecteer de**VoltageActuatorSwitchWarning**van het**W6** > -**beveiligings systeem** > van **Contoso 1** > .

       [![Wijzig het weergave bereik en selecteer W6](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. De waarschuwing duidt op een probleem met de spanning van de generator. De algemene energie-uitvoer van de Generator bevindt zich binnen de normale para meters in het huidige interval. Door ons interval te verg Roten, komt er een ander patroon aan de hand. Een vervolg keuzelijst is duidelijk.

    1. Verwijder de **VoltageActuatorSwitchWarning** -sensor.
    1. Selecteer **Generator systeem** > **ActivePower**.
    1. Wijzig het interval in **3D**.

       [![Het interval wijzigen in 3D](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Door het tijds bereik uit te breiden, kunnen we bepalen of het probleem is gestopt of of het wordt voortgezet.

    * Verleng de tijds Panne tot 60 dagen.

      [![De tijds Panne verlengen tot 60 dagen](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Andere sensor gegevens punten kunnen worden toegevoegd om meer context te bieden. Hoe meer Sens oren er worden weer gegeven, het uitgebreidere inzicht van het probleem is. Laten we een markering neerzetten om de werkelijke waarden weer te geven. 

    1. Selecteer **Generator systeem**en selecteer vervolgens drie Sens oren: **GridVoltagePhase1**, **GridVoltagePhase2**en **GridVoltagePhase3**.
    1. Verwijder een markering van het laatste gegevenspunt in het zichtbare gedeelte.

       [![Een markering verwijderen](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Twee van de spannings sensors zijn comparably en binnen de normale para meters. Het lijkt alsof de **GridVoltagePhase3** -sensor de culprit is.

1. Wanneer zeer contextuele gegevens worden toegevoegd, lijkt de fase 3-uitschakeling nog meer om het probleem te verhelpen. Nu hebben we een goede inleiding op de oorzaak van de waarschuwing. We zijn klaar om het probleem naar ons onderhouds team te verwijzen.  

    * Wijzig de weer gave om alle **Generator systeem** sensoren op dezelfde grafiek schaal te bedekken.

      [![Wijzig de weer gave zodat deze alles bevat](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de Quick Start hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **alle resources**en zoek de Azure time series Insights resource groep.
1. Verwijder de hele resource groep (en alle resources erin) door elke resource afzonderlijk **verwijderen** of verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

U bent klaar om uw eigen Time Series Insights-voorbeeld omgeving te maken. Starten:

> [!div class="nextstepaction"]
> [Een Time Series Insights-omgeving (preview) plannen](time-series-insights-update-plan.md)

Meer informatie over het gebruik van de demo en de bijbehorende functies:

> [!div class="nextstepaction"]
> [De Time Series Insights preview Explorer](time-series-insights-update-explorer.md)
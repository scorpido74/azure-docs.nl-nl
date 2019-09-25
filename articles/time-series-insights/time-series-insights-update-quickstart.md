---
title: 'Snelstart: De demo-omgeving van Azure Time Series Insights (preview) verkennen | Microsoft Docs'
description: Quick Start om inzicht te krijgen in de demo omgeving van Azure Time Series Insights preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/23/2019
ms.openlocfilehash: 633eb00b479c6d2e2bf233b42aff7d393b110fd2
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258421"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Quickstart: De demo-omgeving van Azure Time Series Insights (preview) verkennen

In deze Snelstartgids kunt u aan de slag met de Azure Time Series Insights preview-omgeving. In de gratis demo bekijkt u de belangrijkste functies die zijn toegevoegd aan Time Series Insights preview.

De Time Series Insights preview-demo omgeving bevat een scenario bedrijf, contoso, dat twee wind turbine-Farms exploiteert. Elke farm heeft 10 turbines. Elke turbine beschikt over 20 sensoren die elke minuut gegevens rapporteren aan Azure IoT Hub. De Sens oren verzamelen informatie over weers omstandigheden, Blade Pitch en yaw positie. Informatie over de prestaties van de generator, het gedrag van de versnellingsbak en veiligheids monitors wordt ook vastgelegd.

In deze Quick Start leert u hoe u Time Series Insights kunt gebruiken om inzicht te krijgen in de gegevens van contoso. U voert ook een korte analyse van de hoofd oorzaak uit om kritieke fouten beter te voors pellen en onderhoud uit te voeren.

> [!IMPORTANT]
> Maak een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) als u er nog geen hebt.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Verkenner van Time Series Insights nader bekeken in een demo-omgeving

In de Time Series Insights preview Explorer ziet u hoe historische gegevens en basis analyses worden uitgevoerd. Aan de slag:

1. Ga naar de [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving.  

1. Als u hierom wordt gevraagd, meldt u zich aan bij de Time Series Insights Explorer met behulp van de referenties van uw Azure-account.

## <a name="work-with-historical-data"></a>Werken met historische gegevens

1. Bekijk in **Contoso plant 1**het wind turbine **W7**.  

   1. Wijzig het weergave bereik in **1/1/17 20:00 tot 3/10/17 20:00 (UTC)** .
   1. Als u een sensor wilt selecteren, selecteert u **Contoso plant 1** > **W7** > **Generator systeem** > **GeneratorSpeed**. Controleer vervolgens de waarden die worden weer gegeven.

      [![W7 in contoso plant 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Onlangs heeft Contoso brand geconstateerd in windturbine **W7**. De mening is afhankelijk van wat het vuur heeft veroorzaakt. In Time Series Insights ziet u dat de brand waarschuwings sensor tijdens de brand is geactiveerd.

   1. Wijzig het weergave bereik in **3/9/17 20:00 tot 3/10/17 20:00 (UTC)** .
   1. Selecteer **veiligheids systeem** > **FireAlert**.

      [![Contoso heeft een vuur in de wind turbine W7 gevonden](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Bekijk andere gebeurtenissen rond het tijdstip van de brand om te begrijpen wat er is gebeurd. De olie druk en actieve waarschuwingen worden net vóór de brand weer gegeven.

   1. Selecteer **systeem** > **HydraulicOilPressure**verkopen.
   1. Selecteer **systeem** > **ActiveWarning**verkopen.

      [![Andere gebeurtenissen rond hetzelfde tijdstip controleren](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. De olie druk en actieve waarschuwings sensors zijn direct vóór de brand opgetreden. Vouw de weer gegeven tijd reeks uit om andere tekens weer te geven die duidelijk zijn voor de brand. Beide Sens oren schommelen consistent in de loop van de tijd. De schommelingen geven een persistent en Worrisome patroon aan.

    * Wijzig het weergave bereik in **2/24/17 20:00 tot 3/10/17 20:00 (UTC)** .

      [![De belasting druk en actieve waarschuwings sensors zijn ook gepiekd](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Wanneer er twee jaar historische gegevens worden onderzocht, wordt een andere brand gebeurtenis met dezelfde sensor schommelingen onthuld.

    * Wijzig het weergave bereik in **1/1/16 tot 12/31/17** (alle gegevens).

      [![Zoeken naar historische patronen](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Met behulp van Time Series Insights en onze telemetrie-sensor hebben we een trend met lange termijn detectie en problematische trends gedetecteerd in de historische gegevens. Met deze nieuwe inzichten kunnen we het volgende doen:

* Leg uit wat er daad werkelijk is gebeurd.
* Los het probleem op.
* Plaats de superieure waarschuwings meldings systemen.

## <a name="root-cause-analysis"></a>Hoofdoorzaakanalyses

1. Voor sommige scenario's is geavanceerde analyse vereist om subtiele aanwijzingen in gegevens op te slaan. Selecteer de Windmill **W6** op datum **6/25**.

    1. Wijzig het weergave bereik in **6/1/17 20:00 tot 7/1/17 20:00 (UTC)** .
    1. Selecteer de**VoltageActuatorSwitchWarning**van het**W6** > -**beveiligings systeem** > van **Contoso 1** > .

       [![Wijzig het weergave bereik en selecteer W6](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. De waarschuwing geeft aan dat er een probleem is met de spanning die wordt geleverd door de generator. De algemene energie-uitvoer van de generator werkt binnen de normale para meters in het huidige interval. Door ons interval te verg Roten, komt er een ander patroon aan de hand. Een afwijkende vervolg keuzelijst is duidelijk.

    1. Verwijder de **VoltageActuatorSwitchWarning** -sensor.
    1. Selecteer **Generator systeem** > **ActivePower**.
    1. Wijzig het interval in **3D**.

       [![Het interval wijzigen in 3D](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Door het tijds bereik uit te breiden, kunnen we bepalen of het probleem is gestopt of of het wordt voortgezet.

    * Verleng de tijds Panne tot 60 dagen.

      [![De tijds Panne verlengen tot 60 dagen](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Andere sensor gegevens punten kunnen worden toegevoegd om meer context te bieden. Hoe meer Sens oren er worden weer gegeven, het uitgebreidere inzicht van het probleem is. We verwijderen een markering om de werkelijke waarden te bekijken. 

    1. Selecteer **Generator systeem**en selecteer vervolgens drie Sens oren: **GridVoltagePhase1**, **GridVoltagePhase2**en **GridVoltagePhase3**.
    1. Verwijder een markering van het laatste gegevenspunt in het zichtbare gedeelte.

       [![Een markering verwijderen](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Twee van de spannings sensors zijn comparably en binnen de normale para meters. Het lijkt alsof de **GridVoltagePhase3** -sensor de culprit is.

1. Wanneer zeer contextuele gegevens worden toegevoegd, lijkt de fase 3-uitschakeling nog meer om het probleem te verhelpen. Nu hebben we een goede inleiding op de oorzaak van de waarschuwing. We zijn klaar om het probleem naar ons onderhouds team te verwijzen.  

    * Wijzig de weer gave om alle **Generator systeem** sensoren op dezelfde grafiek schaal te bedekken.

      [![Wijzig de weer gave zodat deze alles bevat](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelf studie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **alle resources**, zoek de Azure time series Insights resource groep.
1. Verwijder de hele resource groep (en alle resources erin) door elke resource afzonderlijk **verwijderen** of verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

U bent klaar om uw eigen Time Series Insights-voorbeeld omgeving te maken. Starten:

> [!div class="nextstepaction"]
> [Een Time Series Insights-omgeving (preview) plannen](time-series-insights-update-plan.md)

Meer informatie over het navigeren in de demo en de bijbehorende functies:

> [!div class="nextstepaction"]
> [De Time Series Insights preview Explorer](time-series-insights-update-explorer.md)

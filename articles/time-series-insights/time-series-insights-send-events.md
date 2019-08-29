---
title: Gebeurtenissen verzenden naar een Azure Time Series Insights-omgeving | Microsoft Docs
description: Informatie over het configureren van een event hub en een voorbeeldtoepassing om gebeurtenissen te verzenden u in Azure Time Series Insights bekijken kunt uitvoeren.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 84eb0e230875b999218b67d47a66a3c92b494253
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072845"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Gebeurtenissen verzenden naar een Time Series Insights-omgeving met behulp van een event hub

In dit artikel wordt uitgelegd hoe u een Event Hub in azure Event Hubs maakt en configureert. Ook wordt beschreven hoe u een voorbeeld toepassing uitvoert om gebeurtenissen te pushen naar Azure Time Series Insights van Event Hubs. Als u een bestaande Event Hub hebt met gebeurtenissen in JSON-indeling, kunt u deze zelf studie overs Laan en uw omgeving bekijken in [Azure time series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Een Event Hub configureren

1. Zie voor meer informatie over het maken van een event hub, de [documentatie voor Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Zoek in het zoekvak **Event Hubs**. Selecteer in de geretourneerde lijst **Event Hubs**.
1. Selecteer uw event hub.
1. Wanneer u een Event Hub maakt, maakt u een Event Hub naam ruimte. Als u nog geen Event Hub in de naam ruimte hebt gemaakt, maakt u in het menu onder **entiteiten**een event hub.  

    [![Lijst met Event hubs](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. Nadat u een event hub maakt, selecteert u dit in de lijst van eventhubs.
1. Selecteer in het menu onder **entiteiten**de optie **Event hubs**.
1. Selecteer de naam van de event hub te configureren.
1. Selecteer onder **overzicht**de optie **consumenten groepen**en selecteer vervolgens **consumenten groep**.

    [![Een Consumer groep maken](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Zorg ervoor dat u een Consumer groep maakt die uitsluitend wordt gebruikt door de bron van de Time Series Insights gebeurtenis.

    > [!IMPORTANT]
    > Zorg ervoor dat deze Consumer groep niet wordt gebruikt door een andere service, zoals een Azure Stream Analytics-taak of een andere Time Series Insights omgeving. Als de consumergroep wordt gebruikt door de andere worden services, leesbewerkingen negatief beïnvloed voor deze omgeving en voor andere services. Als u **$Default** als de consumergroep andere lezers mogelijk uw consumentengroep kunnen gebruiken.

1. Selecteer in het menu onder **instellingen**de optie **beleid voor gedeelde toegang**en selecteer vervolgens **toevoegen**.

    [![Selecteer beleid voor gedeelde toegang en selecteer vervolgens de knop toevoegen](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. In de **nieuw beleid voor gedeelde toegang toevoegen** deelvenster maken van een gedeelde toegang met de naam **MySendPolicy**. U kunt dit beleid voor gedeelde toegang gebruiken voor het verzenden C# van gebeurtenissen in de voor beelden verderop in dit artikel.

    [![Voer in het vak beleids naam MySendPolicy in](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. Schakel onder **claim**het selectie vakje **verzenden** in.

## <a name="add-a-time-series-insights-instance"></a>Een Time Series Insights-exemplaar toevoegen

De update Time Series Insights maakt gebruik van exemplaren contextuele gegevens toevoegen aan binnenkomende telemetriegegevens. De gegevens op het moment dat de query is gekoppeld met behulp van een **Time Series-ID**. De **Time Series-id** voor het voorbeeld project Windmills dat later in dit artikel wordt gebruikt `id`is. Voor meer informatie over Time Series Insight-exemplaren en **Time Series-ID**, Zie [Time Series modellen](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Een Time Series Insights-gebeurtenisbron maken

1. Als u een gebeurtenisbron hebt gemaakt, de stappen voor voltooid [een gebeurtenisbron maken](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Een waarde instellen voor `timeSeriesId`. Voor meer informatie over **Time Series-ID**, Zie [Time Series modellen](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Push-gebeurtenissen (windmills voorbeeld)

1. Zoek in de zoekbalk typt, **Event Hubs**. Selecteer in de geretourneerde lijst **Event Hubs**.

1. Selecteer uw Event Hub-exemplaar.

1. Ga naar het **beleid** > voor gedeelde toegang**MySendPolicy**. Kopieer de waarde voor **verbindings reeks-primaire sleutel**.

    [![Kopieer de waarde voor de primaire sleutel connection string](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Ga naar https://tsiclientsample.azurewebsites.net/windFarmGen.html. De URL wordt gesimuleerd Zwart gat apparaten uitgevoerd.
1. In de **Event Hub-verbindingsreeks** vak op de webpagina, plak de verbindingsreeks die u hebt gekopieerd in [gebeurtenissen pusht](#push-events).
  
    [![Plak de primaire-sleutel connection string in het vak Verbindings reeks van de Event hub](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Selecteer **Klik hier om te beginnen**. De simulator genereert exemplaar JSON die u rechtstreeks kunt gebruiken.

1. Ga terug naar uw event hub in Azure portal. Op de pagina **overzicht** ziet u de nieuwe gebeurtenissen die worden ontvangen door de Event hub.

    [![Een Event Hub overzichts pagina waarop de metrische gegevens voor de Event Hub worden weer gegeven](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>Ondersteunde JSON-vormen

### <a name="example-one"></a>Voor beeld 1

* **Invoer**: Een eenvoudig JSON-object.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Uitvoer**: Eén gebeurtenis.

    |id|tijdstempel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Voor beeld twee

* **Invoer**: Een JSON-matrix met twee JSON-objecten. Elk JSON-object wordt geconverteerd naar een gebeurtenis.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Uitvoer**: Twee gebeurtenissen.

    |id|tijdstempel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Voor beeld drie

* **Invoer**: Een JSON-object met een geneste JSON-matrix met twee JSON-objecten.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Uitvoer**: Twee gebeurtenissen. De eigenschap **locatie** is gekopieerd naar elke gebeurtenis.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Voor beeld vier

* **Invoer**: Een JSON-object met een geneste JSON-matrix met twee JSON-objecten. Deze invoer blijkt dat de algemene eigenschappen kunnen worden gerepresenteerd door het complexe JSON-object.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Uitvoer**: Twee gebeurtenissen.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Volgende stappen

- [Bekijk uw omgeving](https://insights.timeseries.azure.com) in de time series Insights Explorer.

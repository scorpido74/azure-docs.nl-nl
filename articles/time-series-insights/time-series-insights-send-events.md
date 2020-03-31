---
title: Gebeurtenissen naar een omgeving verzenden - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het configureren van een gebeurtenishub, het uitvoeren van een voorbeeldtoepassing en het verzenden van gebeurtenissen naar uw Azure Time Series Insights-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254246"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Gebeurtenissen naar een Time Series Insights-omgeving verzenden met behulp van een gebeurtenishub

In dit artikel wordt uitgelegd hoe u een gebeurtenishub maakt en configureert in Azure Event Hubs. Het beschrijft ook hoe u een voorbeeldtoepassing uitvoert om gebeurtenissen naar Azure Time Series Insights van gebeurtenishubs te pushen. Als u een bestaande gebeurtenishub hebt met gebeurtenissen in JSON-indeling, slaat u deze zelfstudie over en bekijkt u uw omgeving in [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Een Event Hub configureren

1. Lees de [documentatie van Gebeurtenishubs](https://docs.microsoft.com/azure/event-hubs/)voor meer informatie over het maken van een gebeurtenishub.
1. Zoek in het zoekvak naar **gebeurtenishubs**. Selecteer **Gebeurtenishubs**in de geretourneerde lijst .
1. Selecteer uw gebeurtenishub.
1. Wanneer u een gebeurtenishub maakt, maakt u een naamruimte voor gebeurtenishubs. Als u nog geen gebeurtenishub hebt gemaakt in de naamruimte, maakt u in het menu onder **Entiteiten**een gebeurtenishub.  

    [![Lijst met gebeurtenishubs](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. Nadat u een gebeurtenishub hebt gemaakt, selecteert u deze in de lijst met gebeurtenishubs.
1. Selecteer in het menu onder **Entiteiten** **gebeurtenishubs**.
1. Selecteer de naam van de gebeurtenishub om deze te configureren.
1. Selecteer **onder Overzicht** **consumentengroepen**en selecteer **vervolgens Consumentengroep**.

    [![Een consumentengroep maken](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Zorg ervoor dat u een consumentengroep maakt die uitsluitend wordt gebruikt door uw time series Insights-gebeurtenisbron.

    > [!IMPORTANT]
    > Zorg ervoor dat deze consumentengroep niet door een andere service wordt gebruikt, zoals een Azure Stream Analytics-taak of een andere Time Series Insights-omgeving. Als de consumentengroep door de andere diensten wordt gebruikt, worden leesbewerkingen negatief beïnvloed, zowel voor deze omgeving als voor andere diensten. Als u **$Default** als consumentengroep gebruikt, kunnen andere lezers uw consumentengroep mogelijk opnieuw gebruiken.

1. Selecteer in het menu onder **Instellingen**de optie **Beleid voor gedeelde toegang**en selecteer Vervolgens **Toevoegen**.

    [![Selecteer Beleid voor gedeelde toegang en selecteer de knop Toevoegen](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Maak **in** het deelvenster Nieuw beleid voor gedeelde toegang toevoegen een gedeelde toegang met de naam **MySendPolicy**. U gebruikt dit beleid voor gedeelde toegang om gebeurtenissen in de C#-voorbeelden later in dit artikel te verzenden.

    [![Voer in het vak Beleidsnaam MijnSendPolicy in](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Schakel **onder Claim**het selectievakje **Verzenden** in.

## <a name="add-a-time-series-insights-instance"></a>Een exemplaar Inzichten in de tijdreeks toevoegen

De Time Series Insights-update gebruikt instanties om contextuele gegevens toe te voegen aan binnenkomende telemetriegegevens. De gegevens worden tijdens querytijd samengevoegd met behulp van een **tijdreeks-id**. De **Time Series ID** voor de steekproef windmolens project `id`dat we later in dit artikel gebruiken is . Lees [Time Series-modellen](./time-series-insights-update-tsm.md)voor meer informatie over timeseries insight-exemplaren en **timeseries-id.**

### <a name="create-a-time-series-insights-event-source"></a>Een gebeurtenisbron voor Time Series Insights maken

1. Als u geen gebeurtenisbron hebt gemaakt, voert u de stappen uit om [een gebeurtenisbron](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)te maken.

1. Stel een `timeSeriesId`waarde in voor . Lees [Time Series Models](./time-series-insights-update-tsm.md)voor meer informatie over Time Series **ID.**

### <a name="push-events-to-windmills-sample"></a>Duw evenementen naar windmolens monster

1. Zoek in de zoekbalk naar **gebeurtenishubs**. Selecteer **Gebeurtenishubs**in de geretourneerde lijst .

1. Selecteer de instantie van de gebeurtenishub.

1. Ga naar **Beleid voor gedeelde toegang** > **MySendPolicy**. Kopieer de waarde voor **verbindingstekenreeksprimaire toets**.

    [![De waarde voor de primaire toetsverbindingstekenreeks kopiëren](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Ga naar https://tsiclientsample.azurewebsites.net/windFarmGen.html. De URL maakt en voert gesimuleerde windmolenapparaten uit.
1. Plak in het vak **Voorverbindingsverbindingstekenreeks op** de webpagina de verbindingstekenreeks die u hebt gekopieerd in het [invoerveld van](#push-events-to-windmills-sample)de windmolen .
  
    [![De tekenreeks primaire sleutelverbinding plakken in het vak Tekenreeks voor gebeurtenishubverbinding](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Selecteer **Klik om te beginnen**. 

    > [!TIP]
    > De windmolensimulator maakt ook JSON die u als payload gebruiken met de [Time Series Insights GA Query API's.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)

    > [!NOTE]
    > De simulator blijft gegevens verzenden totdat het browsertabblad is gesloten.

1. Ga terug naar uw gebeurtenishub in de Azure-portal. Op de pagina **Overzicht** worden de nieuwe gebeurtenissen weergegeven die door de gebeurtenishub zijn ontvangen.

    [![Een overzichtspagina voor gebeurtenishubs met statistieken voor de gebeurtenishub](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Ondersteunde JSON-vormen

### <a name="example-one"></a>Voorbeeld één

* **Invoer**: Een eenvoudig JSON-object.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Uitvoer:** Één gebeurtenis.

    |id|tijdstempel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Voorbeeld twee

* **Invoer:** een JSON-array met twee JSON-objecten. Elk JSON-object wordt omgezet in een gebeurtenis.

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

### <a name="example-three"></a>Voorbeeld drie

* **Invoer:** een JSON-object met een geneste JSON-array die twee JSON-objecten bevat.

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

* **Uitvoer**: Twee gebeurtenissen. De locatie van **de** eigenschap wordt naar elke gebeurtenis gekopieerd.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Voorbeeld vier

* **Invoer:** een JSON-object met een geneste JSON-array die twee JSON-objecten bevat. Deze invoer toont aan dat globale eigenschappen kunnen worden weergegeven door het complexe JSON-object.

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

- [Bekijk uw omgeving](https://insights.timeseries.azure.com) in de Time Series Insights-verkenner.

- Lees meer over [IoT Hub-apparaatberichten](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)

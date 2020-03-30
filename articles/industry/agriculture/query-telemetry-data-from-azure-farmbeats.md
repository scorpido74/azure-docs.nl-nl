---
title: Ingenomen telemetriegegevens opvragen
description: In dit artikel wordt beschreven hoe u ingenomen telemetriegegevens opvragen.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349804"
---
# <a name="query-ingested-telemetry-data"></a>Ingenomen telemetriegegevens opvragen

In dit artikel wordt beschreven hoe u ingenomen sensorgegevens van Azure FarmBeats opvragen.

Het innemen van gegevens uit IoT-bronnen (Internet of Things), zoals apparaten en sensoren, is een veelvoorkomend scenario in FarmBeats. U maakt metadata voor apparaten en sensoren en neemt vervolgens de historische gegevens in farmbeats in een canonieke indeling in. Zodra de sensorgegevens beschikbaar zijn op FarmBeats Datahub, kunnen we hetzelfde opvragen om bruikbare inzichten te genereren of modellen te bouwen.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u verder gaat met dit artikel, moet u ervoor zorgen dat u FarmBeats hebt geïnstalleerd en sensortelemetriegegevens van uw IoT-apparaten hebt ingenomen op FarmBeats.

Om sensor telemetriegegevens in te nemen, bezoek je [historische telemetriegegevens](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Voordat u verder gaat, moet u er ook voor zorgen dat u bekend bent met FarmBeats REST API's, omdat u telemetrie met behulp van de API's zult opvragen. Zie [FarmBeats REST API's voor](rest-api-in-azure-farmbeats.md)meer informatie over FarmBeats API's. **Zorg ervoor dat u API-aanvragen indienen bij uw FarmBeats Datahub-eindpunt.**

## <a name="query-ingested-sensor-telemetry-data"></a>Ingenomen sensortelemetriegegevens van query's

Er zijn twee manieren om telemetriegegevens van FarmBeats te openen en op te vragen:

- API en
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Query met REST API

Volg de stappen om de ingenomen sensortelemetriegegevens op te vragen met FarmBeats REST API's:

1. Identificeer de sensor waarin u geïnteresseerd bent. U dit doen door een GET-aanvraag in te dienen op /Sensor API.

> [!NOTE]
> De **id** en de **sensorModelId** van de geïnteresseerde sensor object.

2. Maak een GET/{id} op /SensorModel API voor de **sensorModelId** zoals aangegeven in stap 1. De "Sensor Model" heeft alle metadata en details over de ingenomen telemetrie van de sensor. **Sensormeting** binnen het **sensormodelobject** bevat bijvoorbeeld details over welke maatregelen de sensor verzendt en in welke typen en eenheden. Bijvoorbeeld:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Noteer het antwoord van de GET/{id} oproep voor het sensormodel.

3. Een POST-aanroep op /Telemetrie-API doen met de volgende invoerpayload

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Het antwoord van de /Telemetrie API ziet er ongeveer als volgt uit:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
In het bovenstaande voorbeeldantwoord geeft de opgevraagde sensor telemetrie gegevens voor twee tijdstempels samen met de maatnaam ('moist_soil_last') en waarden van de gerapporteerde telemetrie in de twee tijdstempels. U moet verwijzen naar het bijbehorende sensormodel (zoals beschreven in stap 2) om het type en de eenheid van de gerapporteerde waarden te interpreteren.

### <a name="query-using-azure-time-series-insights-tsi"></a>Query met Azure Time Series Insights (TSI)

FarmBeats maakt gebruik van [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) om gegevens op IoT-schaal in te nemen, op te slaan, op te vragen en te visualiseren- gegevens die sterk gecontextualiseerd en geoptimaliseerd zijn voor tijdreeksen.

Telemetriegegevens worden ontvangen op een EventHub en vervolgens verwerkt en naar een TSI-omgeving binnen farmbeats-resourcegroep geduwd. Gegevens kunnen dan rechtstreeks worden opgevraagd vanuit de TSI. Zie [TSI-documentatie](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) voor meer informatie

Volg de stappen om gegevens over TSI te visualiseren:

1. Ga naar **Azure Portal** > **FarmBeats DataHub-brongroep** > selecteer **Time Series Insights-omgeving** (tsi-xxxx) > **Beleid voor gegevenstoegang.** Voeg de gebruiker toe met toegang tot reader of inzender.
2. Ga naar de **overzichtspagina** van **de Time Series Insights-omgeving** (tsi-xxxx) en selecteer de URL van Time Series **Insights Explorer.** Je nu de ingenomen telemetrie visualiseren.

Naast het opslaan, opvragen en visualiseren van telemetrie, maakt TSI ook integratie met een Power BI-dashboard mogelijk. Zie [hier]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) voor meer informatie

## <a name="next-steps"></a>Volgende stappen

U hebt nu opgevraagde sensorgegevens van uw Azure FarmBeats-exemplaar. Leer nu hoe u kaarten voor uw boerderijen [genereren.](generate-maps-in-azure-farmbeats.md#generate-maps)

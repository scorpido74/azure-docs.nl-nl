---
title: Real-time IoT-gegevensstromen verwerken met Azure Stream Analytics
description: IoT-sensortags en -gegevensstromen met Stream Analytics en realtime-gegevensverwerking
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426249"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Real-time IoT-gegevensstromen verwerken met Azure Stream Analytics

In dit artikel leert u hoe u logica voor streamverwerking maakt om gegevens te verzamelen van IoT-apparaten (Internet of Things). U gebruikt een real-world Internet of Things (IoT) use case om te laten zien hoe u uw oplossing snel en economisch bouwen.

## <a name="prerequisites"></a>Vereisten

* Maak een gratis [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/).
* Download voorbeeldquery's en gegevensbestanden van [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenario

Contoso, een bedrijf op het gebied van industriële automatisering, heeft hun fabricageproces volledig geautomatiseerd. De machines in dit bedrijf hebben sensoren die in realtime gegevensstromen kunnen genereren. In dit scenario wil een werkvloerbeheerder realtime-inzichten op basis van de sensorgegevens om naar patronen te zoeken en hierop te reageren. U Stream Analytics Query Language (SAQL) over de sensorgegevens gebruiken om interessante patronen te vinden uit de binnenkomende stroom van gegevens.

In dit voorbeeld worden de gegevens gegenereerd door een sensortagapparaat van Texas Instruments. De nettolading van de gegevens heeft de JSON-indeling en ziet er ongeveer als volgt uit:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

In een werkelijk scenario hebt u honderden van dit soort sensoren die gebeurtenissen als een stroom kunnen genereren. In het ideale geval is er een gateway-apparaat waarop bepaalde code wordt uitgevoerd die deze gebeurtenissen pusht naar [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) of [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Uw Stream Analytics-taak zou deze gebeurtenissen opnemen van Event Hubs en realtime analysequery’s uitvoeren tegen de stromen. Dan kunt u de resultaten verzenden naar één van de [ondersteunde outputs](stream-analytics-define-outputs.md).

Voor het gebruiksgemak biedt deze introductiehandleiding een bestand met voorbeeldgegevens dat is verkregen uit echte Sensor Tag-apparaten. U kunt query's uitvoeren op de voorbeeldgegevens en resultaten weergeven. In volgende zelfstudies leert u hoe u een taak verbindt met in- en uitvoer, en deze implementeert in de Azure-service.

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) **+ Een resource maken** in het linkernavigatiemenu. Selecteer vervolgens **de taak Stream Analytics** in **Analytics**.
   
    ![Een nieuwe Stream Analytics-taak maken](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Voer een unieke taaknaam in en controleer of het abonnement het juiste abonnement is voor de taak. Maak een nieuwe brongroep of selecteer een bestaande groep uit uw abonnement.

1. Selecteer een locatie voor uw taak. Gebruik dezelfde locatie voor uw resourcegroep en alle resources om de verwerkingssnelheid te verhogen en de kosten te verlagen. Nadat u de configuraties hebt gemaakt, selecteert u **Maken**.
   
    ![Details van het maken van een nieuwe Stream Analytics-taak](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Een Azure Stream Analytics-query maken
De volgende stap nadat uw taak is gemaakt, is het schrijven van een query. U query's testen op voorbeeldgegevens zonder een invoer of uitvoer aan uw taak te koppelen.

Download de [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) van GitHub. Navigeer vervolgens naar uw Azure Stream Analytics-taak in de Azure-portal.

Selecteer **Query** onder **Taaktopologie** in het linkermenu. Selecteer vervolgens **Voorbeeldinvoer uploaden**. Upload `HelloWorldASA-InputStream.json` het bestand en selecteer **Ok**.

![Tegel van het Dashboard Voor Stroombeheer](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Merk op dat een voorbeeld van de gegevens automatisch wordt ingevuld in de **invoervoorbeeldtabel.**

![Voorbeeld van voorbeeldinvoergegevens](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Query: de onbewerkte gegevens archiveren

De meest eenvoudige vorm van een query is het doorgeven van gegevens waarmee alle invoergegevens worden gearchiveerd op de aangewezen uitvoer. Deze query is de standaardquery die wordt ingevuld in een nieuwe Azure Stream Analytics-taak.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Selecteer **Testquery** en bekijk de resultaten in de tabel **Testresultaten.**

![Testresultaten voor Stream Analytics-query](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Query: gegevens filteren op basis van een voorwaarde

Laten we proberen om de resultaten te filteren op basis van een voorwaarde. We willen graag resultaten laten zien voor alleen die gebeurtenissen die afkomstig zijn van "sensorA."

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Plak de query in de editor en selecteer **Testquery** om de resultaten te bekijken.

![Een gegevensstroom filteren](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Query: waarschuwingen om zakelijke werkstromen te activeren

We gaan onze query gedetailleerder maken. Voor elk type sensor willen we de gemiddelde temperatuur per tijdvenster van 30 seconden controleren en alleen resultaten weergeven als de gemiddelde temperatuur hoger is dan 100 graden.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30-secondenfilterquery](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

U ziet resultaten die slechts 245 rijen en namen van sensoren bevatten waarvan het gemiddelde gematigd groter is dan 100. In deze query wordt de stroom gebeurtenissen gegroepeerd op **dspl**, de sensornaam, en voor een **tumblingvenster** van 30 seconden. Tijdelijke query's moeten aangeven hoe u de tijd wilt laten verlopen. Met de component **TIMESTAMP BY** hebt u de kolom **OUTPUTTIME** opgegeven om tijden te koppelen aan alle temporele berekeningen. Voor gedetailleerde informatie, lees over [time management](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) en [windowing functies](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Query: detecteren van afwezigheid van gebeurtenissen

Hoe kunnen we een query schrijven om een gebrek aan invoergebeurtenissen te vinden? Laten we de laatste keer dat een sensor gegevens verzonden en vervolgens niet gebeurtenissen te sturen voor de komende 5 seconden.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Detecteren van afwezigheid van gebeurtenissen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

We gebruiken hier een **LEFT OUTER**-join voor dezelfde gegevensstroom (self-join). Voor een **INNER**-join wordt alleen een resultaat geretourneerd wanneer een overeenkomst is gevonden.  Als in geval van een **LEFT OUTER**-join geen overeenkomst wordt gevonden met een gebeurtenis vanaf de linkerkant van de join, wordt een rij met NULL voor alle kolommen in de rechterkant geretourneerd. Deze techniek is heel nuttig om de afwezigheid van gebeurtenissen op te sporen. Zie [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)voor meer informatie.

## <a name="conclusion"></a>Conclusie

Het doel van dit artikel is om aan te tonen hoe u verschillende Query-query's van Stream Analytics-query's schrijven en resultaten in de browser zien. Echter, dit is gewoon om je op weg. Stream Analytics ondersteunt tal van in- en uitvoer en kan zelfs gebruikmaken van functies in Azure Machine Learning, waardoor het een krachtig hulpprogramma wordt om gegevensstromen te analyseren. Lees voor meer informatie over het schrijven van query's het artikel over [algemene querypatronen](stream-analytics-stream-analytics-query-patterns.md).


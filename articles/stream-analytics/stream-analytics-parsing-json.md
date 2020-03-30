---
title: Parsing JSON en AVRO in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u werken op complexe gegevenstypen zoals arrays, JSON, CSV-geformatteerde gegevens.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484584"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Parse JSON- en Avro-gegevens in Azure Stream Analytics

Azure Stream Analytics ondersteunt verwerkingsgebeurtenissen in CSV-, JSON- en Avro-gegevensindelingen. Zowel JSON- als Avro-gegevens kunnen worden gestructureerd en bevatten enkele complexe typen, zoals geneste objecten (records) en arrays. 

>[!NOTE]
>AVRO-bestanden die zijn gemaakt door Event Hub Capture, gebruiken een specifieke indeling waarvoor u de *aangepaste deserializer-functie* moet gebruiken. Zie [Invoer lezen in elke indeling met .NET custom deserializers](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)voor meer informatie.



## <a name="record-data-types"></a>Gegevenstypen opnemen
Recordgegevenstypen worden gebruikt om JSON- en Avro-arrays weer te geven wanneer overeenkomstige indelingen worden gebruikt in de invoergegevensstromen. Deze voorbeelden tonen een voorbeeldsensor aan, die invoergebeurtenissen in JSON-indeling leest. Hier is een voorbeeld van een enkele gebeurtenis:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Geneste velden openen in bekend schema
Gebruik puntnotatie (.) om eenvoudig toegang te krijgen tot geneste velden rechtstreeks vanuit uw query. Deze query selecteert bijvoorbeeld de breedte- en lengtepuntencoördinaten onder de eigenschap Locatie in de voorgaande JSON-gegevens. De puntnotatie kan worden gebruikt om meerdere niveaus te navigeren, zoals hieronder wordt weergegeven.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Het resultaat is:

|DeviceID|Lat|Lange|Temperatuur|Versie|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Alle eigenschappen selecteren
U alle eigenschappen van een geneste record selecteren met een wildcard '*'. Kijk een naar het volgende voorbeeld:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Het resultaat is:

|DeviceID|Lat|Lange|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Toegang tot geneste velden wanneer de eigenschapsnaam een variabele is

Gebruik de functie [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) als de eigenschapsnaam een variabele is. Dit maakt het mogelijk voor het bouwen van dynamische query's zonder hardcoding eigenschapnamen.

Stel u bijvoorbeeld voor dat de voorbeeldgegevensstroom **moet worden samengevoegd met referentiegegevens** die drempels bevatten voor elke apparaatsensor. Een fragment van dergelijke referentiegegevens wordt hieronder weergegeven.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Het doel hier is om onze voorbeeldgegevensset vanaf de bovenkant van het artikel toe te voegen aan die referentiegegevens en één gebeurtenis uit te schakelen voor elke sensormeting boven de drempelwaarde. Dat betekent dat onze enkele gebeurtenis hierboven meerdere uitvoergebeurtenissen kan genereren als meerdere sensoren boven hun respectievelijke drempelwaarden zitten, dankzij de join. Zie het onderstaande gedeelte om vergelijkbare resultaten te behalen zonder lid te worden.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** selecteert de eigenschap in *SensorReadings*, welke naam overeenkomt met de eigenschapsnaam afkomstig van de referentiegegevens. Vervolgens wordt de bijbehorende waarde van *SensorReadings* geëxtraheerd.

Het resultaat is:

|DeviceID|SensorNaam|Waarschuwingsbericht|
|-|-|-|
|12345|Vochtigheid|Waarschuwing : Sensor boven drempelwaarde|

### <a name="convert-record-fields-into-separate-events"></a>Recordvelden omzetten in afzonderlijke gebeurtenissen

Als u recordvelden wilt omzetten in afzonderlijke gebeurtenissen, gebruikt u de operator [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) samen met de functie [GetRecordProperties.](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics)

Met de oorspronkelijke voorbeeldgegevens kan de volgende query worden gebruikt om eigenschappen in verschillende gebeurtenissen te extraheren.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Het resultaat is:

|DeviceID|SensorNaam|Waarschuwingsbericht|
|-|-|-|
|12345|Temperatuur|80|
|12345|Vochtigheid|70|
|12345|CustomSensor01 (CustomSensor01)|5|
|12345|CustomSensor02 (CustomSensor02)|99|
|12345|SensorMetadata|[objectobject]|

Met [behulp van MET](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)is het dan mogelijk om deze evenementen naar verschillende bestemmingen te leiden:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Parse JSON-record in SQL-referentiegegevens
Wanneer u Azure SQL Database gebruikt als referentiegegevens in uw taak, is het mogelijk om een kolom met gegevens in JSON-indeling te hebben. Hieronder kunt u een voorbeeld bekijken.

|DeviceID|Gegevens|
|-|-|
|12345|{"sleutel" : "waarde1"}|
|54321|{"sleutel" : "waarde2"}|

U de JSON-record in de kolom *Gegevens* ontleden door een eenvoudige JavaScript-door de gebruiker gedefinieerde functie te schrijven.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

U vervolgens een stap maken in uw Stream Analytics-query zoals hieronder weergegeven om toegang te krijgen tot de velden van uw JSON-records.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Arraygegevenstypen

Arraygegevenstypen zijn een geordende verzameling waarden. Enkele typische bewerkingen op matrixwaarden worden hieronder beschreven. Deze voorbeelden maken gebruik van de functies [GetArrayElement,](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics) [GetArrayElements,](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)en de [operator APPLY.](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics)

Hier is een voorbeeld van één gebeurtenis. Beide `CustomSensor03` `SensorMetadata` en zijn van type **array:**

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Werken met een specifiek arrayelement

Selecteer arrayelement op een opgegeven index (het eerste matrixelement selecteren):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Het resultaat is:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Arraylengte selecteren

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Het resultaat is:

|arrayLengte|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Matrixelementen omzetten in afzonderlijke gebeurtenissen

Selecteer alle array-elementen als afzonderlijke gebeurtenissen. De [operator APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) en de ingebouwde functie [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) extraheert alle arrayelementen als afzonderlijke gebeurtenissen:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Het resultaat is:

|DeviceId|ArrayIndex (ArrayIndex)|Arraywaarde|
|-|-|-|
|12345|0|12|
|12345|1|5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Het resultaat is:

|DeviceId|smKey|mmWaarde|
|-|-|-|
|12345|Fabrikant|ABC|
|12345|Versie|1.2.45|

Als de geëxtraheerde velden in kolommen moeten worden weergegeven, is het mogelijk om de gegevensset te draaien met de [syntaxis MET](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) naast de [JOIN-bewerking.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) Die join vereist een [tijdgrens](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) voorwaarde die duplicatie voorkomt:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Het resultaat is:

|DeviceId|Lat|Lange|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Zie ook
[Gegevenstypen in Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)

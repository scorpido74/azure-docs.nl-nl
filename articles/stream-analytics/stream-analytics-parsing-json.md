---
title: JSON-en AVRO parseren in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u complexe gegevens typen gebruikt zoals matrices, JSON-gegevens in CSV-indeling.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1741510c7398ce74da81f006cb4109d9a33f8f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431596"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>JSON-en Avro-gegevens parseren in Azure Stream Analytics

Azure Stream Analytics ondersteuning voor het verwerken van gebeurtenissen in CSV-, JSON-en Avro-gegevens indelingen. JSON-en Avro-gegevens kunnen worden gestructureerd en bevatten complexe typen, zoals geneste objecten (records) en matrices. 




## <a name="record-data-types"></a>Record gegevens typen
Record gegevens typen worden gebruikt voor het weer geven van JSON-en Avro-matrices wanneer overeenkomende indelingen worden gebruikt in de gegevens stromen voor invoer. In deze voor beelden ziet u een voor beeld van een sensor, waarmee invoer gebeurtenissen in JSON-indeling worden gelezen. Hier volgt een voor beeld van één gebeurtenis:

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


### <a name="access-nested-fields-in-known-schema"></a>Toegang krijgen tot geneste velden in een bekend schema
Gebruik punt notatie (.) om eenvoudig toegang te krijgen tot geneste velden rechtstreeks vanuit uw query. Met deze query worden bijvoorbeeld de breedte-en lengte coördinaten geselecteerd onder de locatie-eigenschap in de voor gaande JSON-gegevens. De punt notatie kan worden gebruikt om te navigeren op meerdere niveaus, zoals hieronder wordt weer gegeven.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Alle eigenschappen selecteren
U kunt alle eigenschappen van een geneste record selecteren met behulp van het Joker teken ' * '. Kijk een naar het volgende voorbeeld:

```SQL
SELECT input.Location.*
FROM input
```

Het resultaat is:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Toegang krijgen tot geneste velden als eigenschaps naam een variabele is
Gebruik de functie [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) als de naam van de eigenschap een variabele is. 

Stel dat een voor beeld van een gegevens stroom moet worden gekoppeld met referentie gegevens die drempels bevatten voor elke sensor van het apparaat. Hieronder ziet u een fragment van dergelijke referentie gegevens.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Record velden omzetten in afzonderlijke gebeurtenissen
Als u record velden wilt omzetten in afzonderlijke gebeurtenissen, gebruikt u de operator [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) samen met de functie [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) . Als het vorige voor beeld bijvoorbeeld meerdere records bevat voor SensorReading, kan de volgende query worden gebruikt om deze op te halen in verschillende gebeurtenissen:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Matrix gegevens typen

Matrix gegevens typen zijn een geordende verzameling waarden. Enkele typische bewerkingen op matrix waarden worden hieronder beschreven. In deze voor beelden wordt ervan uitgegaan dat de invoer gebeurtenissen een eigenschap hebben met de naam ' arrayField ' die een matrix gegevens type is.

In deze voor beelden worden de functies [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)en de operator [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) gebruikt.

### <a name="working-with-a-specific-array-element"></a>Werken met een specifiek matrix element
Selecteer een matrix element bij een opgegeven index (selecteren van het eerste matrix element):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Matrix lengte selecteren

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Matrix elementen omzetten in afzonderlijke gebeurtenissen
Selecteer alle matrix elementen als afzonderlijke gebeurtenissen. De operator apply in combi natie met de ingebouwde functie [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) haalt alle matrix elementen [op](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) als afzonderlijke gebeurtenissen:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Zie ook
[Gegevens typen in Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)

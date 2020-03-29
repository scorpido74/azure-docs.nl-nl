---
title: Door de gebruiker gedefinieerde verwijzingen naar de clientbibliotheek van de client - Azure Digital Twins | Microsoft Documenten
description: Azure Digital Twins door gebruikers gedefinieerde functies clientbibliotheek referentiedocumentatie.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276819"
---
# <a name="user-defined-functions-client-library-reference"></a>Door de gebruiker gedefinieerde verwijzingen naar de clientbibliotheek door de gebruiker

Dit document bevat referentiegegevens voor de clientbibliotheek met gebruikers van Azure Digital Twins.

## <a name="helper-methods"></a>Helpermethoden

De clientbibliotheek definieert helpermethoden voor veelgebruikte bewerkingen.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ∙`space`

Met een spatie-id haalt deze functie de ruimte uit de grafiek op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Ruimte-id |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ∙`sensor`

Met een sensor-id haalt deze functie de sensor uit de grafiek.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Sensor-id |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ∙`device`

Met een apparaat-id haalt deze functie het apparaat uit de grafiek.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Apparaat-id |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ∙`value`

Met een sensor-id en het gegevenstype haalt deze functie de huidige waarde voor die sensor op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId sensorId*  | `guid` | Sensor-id |
| *Datatype*  | `string` | Sensorgegevenstype |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ∙`value`

Met een spatie-id en de waardenaam haalt deze functie de huidige waarde voor die ruimteeigenschap op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Ruimte-id |
| *valueName* | `string` | Naam ruimteeigenschap |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ∙`value[]`

Met een sensor-id en het gegevenstype haalt deze functie de historische waarden voor die sensor op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId sensorId* | `guid` | Sensor-id |
| *Datatype* | `string` | Sensorgegevenstype |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ∙`value[]`

Met een spatie-id en de waardenaam haalt deze functie de historische waarden voor die eigenschap op de ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |
| *valueName* | `string` | Naam ruimteeigenschap |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ∙`space[]`

Met een spatie-id haalt deze functie de onderliggende spaties voor die bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ∙`sensor[]`

Met een spatie-id haalt deze functie de onderliggende sensoren voor die bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ∙`device[]`

Met een spatie-id haalt deze functie de onderliggende apparaten voor die bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ∙`sensor[]`

Met een apparaat-id haalt deze functie de onderliggende sensoren voor dat bovenliggende apparaat op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Apparaat-id |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ∙`space`

Met een spatie-id haalt deze functie de bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Ruimte-id |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ∙`space`

Met een sensor-id haalt deze functie de bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor-id |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ∙`space`

Met een apparaat-id haalt deze functie de bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Apparaat-id |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ∙`space`

Met een sensor-id haalt deze functie het bovenliggende apparaat op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor-id |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ∙`extendedProperty`

Met een spatie-id haalt deze functie de eigenschap en de waarde ervan uit de ruimte.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |
| *Propertyname* | `string` | Naam ruimteeigenschap |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ∙`extendedProperty`

Met een sensor-id haalt deze functie de eigenschap en de waarde ervan uit de sensor.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId sensorId* | `guid` | Sensor-id |
| *Propertyname* | `string` | Naam sensoreigenschap |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ∙`extendedProperty`

Met een apparaat-id haalt deze functie de eigenschap en de waarde ervan op van het apparaat.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Apparaat-id |
| *Propertyname* | `string` | Naam van de eigenschap Apparaat |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, waarde)

Met deze functie stelt u een waarde in op het sensorobject met het opgegeven gegevenstype.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId sensorId* | `guid` | Sensor-id |
| *Datatype*  | `string` | Sensorgegevenstype |
| *value*  | `string` | Waarde |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, waarde)

Met deze functie stelt u een waarde in op het ruimteobject met het opgegeven gegevenstype.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |
| *Datatype* | `string` | Gegevenstype |
| *value* | `string` | Waarde |

### <a name="logmessage"></a>logboek(bericht)

Met deze functie wordt het volgende bericht binnen de door de gebruiker gedefinieerde functie logboeken.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *Bericht* | `string` | Bericht dat moet worden geregistreerd |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Deze functie stuurt een aangepaste melding die moet worden verzonden.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *topologieObjectId*  | `guid` | Graph object identifier. Voorbeelden zijn ruimte, sensor en apparaat-ID.|
| *topologyObjectType*  | `string` | Voorbeelden zijn sensor en apparaat.|
| *nettolading*  | `string` | De JSON payload die met de melding moet worden verzonden. |

## <a name="return-types"></a>Retourtypen

De responsmodellen die worden geretourneerd uit clientreferentiehelpermethoden worden hieronder beschreven.

### <a name="space"></a>Space

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>Ruimtemethoden

#### <a name="parent--space"></a>Ouder() ∙`space`

Met deze functie wordt de bovenliggende ruimte van de huidige ruimte geretourneerd.

#### <a name="childsensors--sensor"></a>ChildSensors() ∙`sensor[]`

Deze functie retourneert de onderliggende sensoren van de huidige ruimte.

#### <a name="childdevices--device"></a>ChildDevices() ∙`device[]`

Met deze functie worden de onderliggende apparaten van de huidige ruimte geretourneerd.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ∙`extendedProperty`

Met deze functie wordt de verlengde eigenschap en de waarde ervan voor de huidige ruimte geretourneerd.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Naam van de uitgebreide eigenschap |

#### <a name="valuevaluename--value"></a>Waarde(waardeNaam) ∙`value`

Met deze functie wordt de waarde van de huidige ruimte geretourneerd.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Naam van de waarde |

#### <a name="historyvaluename--value"></a>Geschiedenis(valueName) ∙`value[]`

Met deze functie worden de historische waarden van de huidige ruimte geretourneerd.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Naam van de waarde |

#### <a name="notifypayload"></a>Melding(payload)

Deze functie stuurt een melding met de opgegeven payload.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *nettolading* | `string` | JSON-payload op te nemen in de kennisgeving |

### <a name="device"></a>Apparaat

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Apparaatmethoden

#### <a name="parent--space"></a>Ouder() ∙`space`

Met deze functie wordt de bovenliggende ruimte van het huidige apparaat geretourneerd.

#### <a name="childsensors--sensor"></a>ChildSensors() ∙`sensor[]`

Met deze functie worden de onderliggende sensoren van het huidige apparaat geretourneerd.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ∙`extendedProperty`

Met deze functie wordt de uitgebreide eigenschap en de waarde ervan voor het huidige apparaat geretourneerd.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Naam van de uitgebreide eigenschap |

#### <a name="notifypayload"></a>Melding(payload)

Deze functie stuurt een melding met de opgegeven payload.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *nettolading* | `string` | JSON-payload op te nemen in de kennisgeving |

### <a name="sensor"></a>Sensor

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Sensormethoden

#### <a name="space--space"></a>Ruimte() ∙`space`

Met deze functie wordt de bovenliggende ruimte van de huidige sensor geretourneerd.

#### <a name="device--device"></a>Inrichting() ∙`device`

Met deze functie wordt het bovenliggende apparaat van de huidige sensor geretourneerd.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ∙`extendedProperty`

Deze functie retourneert de verlengde eigenschap en de waarde ervan voor de huidige sensor.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Naam van de uitgebreide eigenschap |

#### <a name="value--value"></a>Waarde() ∙`value`

Deze functie geeft de waarde van de huidige sensor als resultaat.

#### <a name="history--value"></a>Geschiedenis() ∙`value[]`

Deze functie retourneert de historische waarden van de huidige sensor.

#### <a name="notifypayload"></a>Melding(payload)

Deze functie stuurt een melding met de opgegeven payload.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *nettolading* | `string` | JSON-payload op te nemen in de kennisgeving |

### <a name="value"></a>Waarde

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Uitgebreide eigenschap

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [door gebruikers gedefinieerde functies van Azure Digital Twins](./concepts-user-defined-functions.md).

- Meer informatie over [het maken van door de gebruiker gedefinieerde functies](./how-to-user-defined-functions.md).

- Meer informatie over [het opsporen van door gebruikers gedefinieerde functies](./how-to-diagnose-user-defined-functions.md).

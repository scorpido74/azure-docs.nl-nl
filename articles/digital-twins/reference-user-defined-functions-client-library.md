---
title: Door de gebruiker gedefinieerde functies referentie client bibliotheek-Azure Digital Apparaatdubbels | Microsoft Docs
description: Referentie documentatie voor de door de gebruiker gedefinieerde Azure Digital Apparaatdubbels-client bibliotheek.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 41baacd4485e6702ec29057f5d539724b74e353b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383250"
---
# <a name="user-defined-functions-client-library-reference"></a>Naslag informatie voor de door de gebruiker gedefinieerde functies client bibliotheken

Dit document bevat referentie-informatie voor de client bibliotheek van de door de gebruiker gedefinieerde functies van Azure Digital Apparaatdubbels.

## <a name="helper-methods"></a>Helper-methoden

De client bibliotheek definieert hulp methoden voor veelgebruikte bewerkingen.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (id) ⇒ `space`

Met een spatie-id haalt deze functie de ruimte op uit de grafiek.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Ruimte-id |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (id) ⇒ `sensor`

Op basis van een sensor-ID haalt deze functie de sensor op uit de grafiek.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Sensor-ID |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (id) ⇒ `device`

Met deze functie wordt het apparaat uit de grafiek opgehaald met een apparaat-id.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Apparaat-id |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, data type) ⇒ `value`

Op basis van een sensor-ID en het bijbehorende gegevens type haalt deze functie de huidige waarde voor die sensor op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Sensor-ID |
| *param1*  | `string` | Gegevens type sensor |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, waardenaam) ⇒ `value`

Op basis van een spatie-id en de waardenaam wordt met deze functie de huidige waarde voor die eigenschap Space opgehaald.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Ruimte-id |
| *valueName* | `string` | Naam van de eigenschap Space |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, data type) ⇒ `value[]`

Op basis van een sensor-ID en het bijbehorende gegevens type haalt deze functie de historische waarden voor die sensor op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor-ID |
| *param1* | `string` | Gegevens type sensor |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, data type) ⇒ `value[]`

Op basis van een spatie-id en de waardenaam, haalt deze functie de historische waarden voor die eigenschap op in de ruimte.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |
| *valueName* | `string` | Naam van de eigenschap Space |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (spaceId) ⇒ `space[]`

Met deze functie worden de onderliggende ruimten voor die bovenliggende ruimte opgehaald met een spatie-id.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId) ⇒ `sensor[]`

Met een spatie-id haalt u de onderliggende Sens oren op voor die bovenliggende ruimte.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (spaceId) ⇒ `device[]`

Met een spatie-id haalt u de onderliggende apparaten voor die bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors (deviceId) ⇒ `sensor[]`

Met behulp van een apparaat-id haalt deze functie de onderliggende Sens oren voor dat bovenliggende apparaat op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Apparaat-id |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace (childSpaceId) ⇒ `space`

Met deze functie wordt de bovenliggende ruimte van een spatie-id opgehaald.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Ruimte-id |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace (childSensorId) ⇒ `space`

Met deze functie wordt de bovenliggende ruimte van de sensor-ID opgehaald.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor-ID |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ⇒ `space`

Als er een apparaat-id is opgegeven, haalt deze functie de bovenliggende ruimte op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Apparaat-id |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice (childSensorId) ⇒ `space`

Met behulp van een sensor-ID haalt deze functie het bovenliggende apparaat op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor-ID |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, PropertyName) ⇒ `extendedProperty`

Met een spatie-id haalt deze functie de eigenschap en de waarde ervan op uit de ruimte.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |
| *propertyName* | `string` | Naam van de eigenschap Space |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, PropertyName) ⇒ `extendedProperty`

Op basis van een sensor-ID haalt deze functie de eigenschap en de waarde ervan op uit de sensor.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor-ID |
| *propertyName* | `string` | Naam sensor eigenschap |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, PropertyName) ⇒ `extendedProperty`

Met een apparaat-id haalt deze functie de eigenschap en de waarde van het apparaat op.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Apparaat-id |
| *propertyName* | `string` | Naam van de apparaat-eigenschap |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, data type, waarde)

Deze functie stelt een waarde in voor het sensor-object met het opgegeven gegevens type.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor-ID |
| *param1*  | `string` | Gegevens type sensor |
| *value*  | `string` | Waarde |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, data type, waarde)

Deze functie stelt een waarde in voor het Space-object met het opgegeven gegevens type.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Ruimte-id |
| *param1* | `string` | Gegevenstype |
| *value* | `string` | Waarde |

### <a name="logmessage"></a>logboek (bericht)

Met deze functie wordt het volgende bericht geregistreerd binnen de door de gebruiker gedefinieerde functie.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *Bericht* | `string` | Bericht dat moet worden geregistreerd |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, Payload)

Deze functie verzendt een aangepaste melding om te worden verzonden.

**Soort**: globale functie

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Object-id van grafiek. Voor beelden zijn ruimte, sensor en apparaat-ID.|
| *topologyObjectType*  | `string` | Voor beelden zijn sensor en apparaat.|
| *nettolading*  | `string` | De JSON-nettolading die met de melding moet worden verzonden. |

## <a name="return-types"></a>Retour typen

De antwoord modellen die worden geretourneerd door de hulp methoden voor client verwijzing, worden hieronder beschreven.

### <a name="space"></a>Ruimte

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

### <a name="space-methods"></a>Ruimte methoden

#### <a name="parent--space"></a>Parent () ⇒ `space`

Deze functie retourneert de bovenliggende ruimte van de huidige ruimte.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Deze functie retourneert de onderliggende Sens oren van de huidige ruimte.

#### <a name="childdevices--device"></a>ChildDevices () ⇒ `device[]`

Deze functie retourneert de onderliggende apparaten van de huidige ruimte.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (PropertyName) ⇒ `extendedProperty`

Deze functie retourneert de uitgebreide eigenschap en de waarde voor de huidige ruimte.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Naam van de uitgebreide eigenschap |

#### <a name="valuevaluename--value"></a>Waarde (waardenaam) ⇒ `value`

Deze functie retourneert de waarde van de huidige ruimte.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Naam van de waarde |

#### <a name="historyvaluename--value"></a>Geschiedenis (waardenaam) ⇒ `value[]`

Deze functie retourneert de historische waarden van de huidige ruimte.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Naam van de waarde |

#### <a name="notifypayload"></a>Melding (Payload)

Met deze functie wordt een melding met de opgegeven Payload verzonden.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *nettolading* | `string` | JSON-nettolading die moet worden meegenomen in de melding |

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

#### <a name="parent--space"></a>Parent () ⇒ `space`

Deze functie retourneert de bovenliggende ruimte van het huidige apparaat.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Deze functie retourneert de onderliggende Sens oren van het huidige apparaat.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (PropertyName) ⇒ `extendedProperty`

Deze functie retourneert de uitgebreide eigenschap en de waarde voor het huidige apparaat.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Naam van de uitgebreide eigenschap |

#### <a name="notifypayload"></a>Melding (Payload)

Met deze functie wordt een melding met de opgegeven Payload verzonden.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *nettolading* | `string` | JSON-nettolading die moet worden meegenomen in de melding |

### <a name="sensor"></a>Sensoren

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

### <a name="sensor-methods"></a>Sensor methoden

#### <a name="space--space"></a>Space () ⇒ `space`

Deze functie retourneert de bovenliggende ruimte van de huidige sensor.

#### <a name="device--device"></a>Apparaat () ⇒ `device`

Deze functie retourneert het bovenliggende apparaat van de huidige sensor.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (PropertyName) ⇒ `extendedProperty`

Deze functie retourneert de uitgebreide eigenschap en de waarde ervan voor de huidige sensor.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Naam van de uitgebreide eigenschap |

#### <a name="value--value"></a>Waarde () ⇒ `value`

Deze functie retourneert de waarde van de huidige sensor.

#### <a name="history--value"></a>Geschiedenis () ⇒ `value[]`

Deze functie retourneert de historische waarden van de huidige sensor.

#### <a name="notifypayload"></a>Melding (Payload)

Met deze functie wordt een melding met de opgegeven Payload verzonden.

| Parameter  | Type                | Beschrijving  |
| ------ | ------------------- | ------------ |
| *nettolading* | `string` | JSON-nettolading die moet worden meegenomen in de melding |

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

- Meer informatie over door de [gebruiker gedefinieerde Azure Digital apparaatdubbels-functies](./concepts-user-defined-functions.md).

- Meer informatie [over het maken van door de gebruiker gedefinieerde functies](./how-to-user-defined-functions.md).

- Meer informatie [over het oplossen van door de gebruiker gedefinieerde functies](./how-to-diagnose-user-defined-functions.md).

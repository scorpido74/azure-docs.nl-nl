---
title: Informatie over onderdelen in IoT Plug en Play-modellen | Microsoft Docs
description: Inzicht in het verschil tussen IoT Plug en Play DTDL-modellen die gebruikmaken van onderdelen en modellen die geen onderdelen gebruiken.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352320"
---
# <a name="iot-plug-and-play-components-in-models"></a>IoT Plug en Play-onderdelen in modellen

In de IoT Plug en Play-conventies is een apparaat een IoT Plug en Play-apparaat als het de DTDL-model-ID (Digital apparaatdubbels Definition Language) weergeeft wanneer deze verbinding maakt met een IoT-hub.

Het volgende code fragment toont enkele voor beelden van model-Id's:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Geen onderdelen

Een eenvoudig model maakt geen gebruik van Inge sloten of trapsgewijze componenten. Het bevat header-informatie en een inhouds sectie voor het definiëren van telemetrie, eigenschappen en opdrachten.

In het volgende voor beeld ziet u een deel van een eenvoudig model dat geen gebruik maakt van onderdelen:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Hoewel het model niet expliciet een onderdeel definieert, gedraagt het zich alsof er één onderdeel is met alle telemetrie, eigenschap en opdracht definities.

De volgende scherm afbeelding laat zien hoe het model wordt weer gegeven in het hulp programma Azure IoT Explorer:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Standaard onderdeel in azure IoT Explorer":::

De model-ID wordt opgeslagen in een eigenschap van een apparaatonafhankelijk-apparaat, zoals in de volgende scherm afbeelding wordt weer gegeven:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Model-ID in digitale dubbele eigenschap":::

Een DTDL-model zonder onderdelen is een nuttige vereenvoudiging voor een apparaat met één set telemetrie, eigenschappen en opdrachten. Een model dat geen gebruikmaakt van-onderdelen, maakt het eenvoudig om een bestaand apparaat te migreren naar een IoT-Plug en Play apparaat: u maakt een DTDL-model waarin uw werkelijke apparaat wordt beschreven zonder dat hiervoor onderdelen hoeven te worden gedefinieerd.

## <a name="multiple-components"></a>Meerdere onderdelen

Met onderdelen kunt u een model interface bouwen als een assembly van andere interfaces.

De [Thermo](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) staat-interface wordt bijvoorbeeld gedefinieerd als een model. Wanneer u het [temperatuur controller model](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)definieert, kunt u deze interface opnemen als een of meer onderdelen. In het volgende voor beeld worden deze onderdelen `thermostat1` en genoemd `thermostat2` .

Er zijn twee of meer onderdeel secties voor een DTDL-model met meerdere onderdelen. Elke sectie heeft `@type` ingesteld op `Component` en verwijst expliciet naar een schema zoals wordt weer gegeven in het volgende code fragment:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Dit model bevat drie onderdelen die zijn gedefinieerd in de sectie inhoud-twee `Thermostat` onderdelen en een `DeviceInformation` onderdeel. Er is ook een standaard hoofd onderdeel.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over model onderdelen, zijn hier enkele aanvullende bronnen:

- [Digital Apparaatdubbels Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Model opslagplaatsen](./concepts-model-repository.md)
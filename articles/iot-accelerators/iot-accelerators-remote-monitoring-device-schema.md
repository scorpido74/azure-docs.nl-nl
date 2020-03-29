---
title: Apparaatschema in oplossing voor bewaking op afstand - Azure | Microsoft Documenten
description: In dit artikel wordt het JSON-schema beschreven dat een gesimuleerd apparaat definieert in de oplossing voor bewaking op afstand.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 0f9669d491648ecc621aab27d0908dcc3dc84438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65823332"
---
# <a name="understand-the-device-model-schema"></a>Het schema van het apparaatmodel begrijpen

U gesimuleerde apparaten in de oplossing voor externe bewaking gebruiken om het gedrag ervan te testen. De oplossing voor externe bewaking bevat een apparaatsimulatieservice om gesimuleerde apparaten uit te voeren. Wanneer u de oplossing voor externe bewaking implementeert, wordt een verzameling gesimuleerde apparaten automatisch ingericht. U de bestaande gesimuleerde apparaten aanpassen of uw eigen apparaten maken.

In dit artikel wordt het schema van het apparaatmodel beschreven dat de mogelijkheden en het gedrag van een gesimuleerd apparaat opgeeft. Het apparaatmodel wordt opgeslagen in een JSON-bestand.

> [!NOTE]
> Dit schema van het apparaatmodel is alleen bedoeld voor gesimuleerde apparaten die worden gehost in de apparaatsimulatieservice. Als u een echt apparaat wilt maken, [raadpleegt u Uw apparaat verbinden met de versneller van de oplossing voor externe bewaking.](iot-accelerators-connecting-devices.md)

De volgende artikelen zijn gerelateerd aan het huidige artikel:

* [Implement the device model behavior](iot-accelerators-remote-monitoring-device-behavior.md) describes the JavaScript files you use to implement the behavior of a simulated device.
* [Maak een nieuw gesimuleerd apparaat](iot-accelerators-remote-monitoring-create-simulated-device.md) en zet u in elkaar en laat u zien hoe u een nieuw gesimuleerd apparaattype implementeert voor uw oplossing.

In dit artikel leert u het volgende:

>[!div class="checklist"]
> * Een JSON-bestand gebruiken om een gesimuleerd apparaatmodel te definiëren
> * De eigenschappen van het gesimuleerde apparaat opgeven
> * De telemetrie opgeven die het gesimuleerde apparaat verzendt
> * Geef de cloud-to-device-methoden op waarop het apparaat reageert

## <a name="the-parts-of-the-device-model-schema"></a>De onderdelen van het schema van het apparaatmodel

Elk apparaatmodel, zoals een koeler of vrachtwagen, definieert een type apparaat dat de simulatieservice kan simuleren. Elk apparaatmodel wordt opgeslagen in een JSON-bestand met het volgende schema op het hoogste niveau:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

U de schemabestanden voor de standaard gesimuleerde apparaten in de [map devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) bekijken op GitHub.

In de volgende tabel worden de schemavermeldingen op het hoogste niveau beschreven:

| Schema-vermelding | Beschrijving |
| -- | --- |
| `SchemaVersion` | De schemaversie `1.0.0` is altijd en is specifiek voor de indeling van dit bestand. |
| `Id` | Een unieke ID voor dit apparaatmodel. |
| `Version` | Hiermee wordt de versie van het apparaatmodel geïdentificeerd. |
| `Name` | Een vriendelijke naam voor het apparaatmodel. |
| `Description` | Een beschrijving van het apparaatmodel. |
| `Protocol` | Het verbindingsprotocol dat het apparaat gebruikt. Kan een `AMQP`van `MQTT`, `HTTP`, en . |

In de volgende secties worden de andere secties in het JSON-schema beschreven:

## <a name="simulation"></a>Simulatie

In `Simulation` de sectie definieert u de interne status van het gesimuleerde apparaat. Telemetriewaarden die door het apparaat worden verzonden, moeten deel uitmaken van de status van dit apparaat.

De definitie van de apparaatstatus heeft twee elementen:

* `InitialState`definieert de oorspronkelijke waarden voor alle eigenschappen van het object apparaatstatus.
* `Script`hiermee wordt een JavaScript-bestand geïdentificeerd dat volgens een schema wordt uitgevoerd om de apparaatstatus bij te werken. U dit scriptbestand gebruiken om de telemetriewaarden die door het apparaat worden verzonden, te randomiseren.

Zie Het gedrag van het apparaatmodel begrijpen voor meer informatie over het JavaScript-bestand dat het object met de status van het apparaat [bijwerkt.](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)

In het volgende voorbeeld wordt de definitie van het object apparaatstatus voor een gesimuleerde koeler weergegeven:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

De simulatieservice voert het bestand **chiller-01-state.js** elke vijf seconden uit om de status van het apparaat bij te werken. U de JavaScript-bestanden voor de standaard gesimuleerde apparaten in de [scriptsmap](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) op GitHub bekijken. Volgens de conventie hebben deze JavaScript-bestanden de **achtervoegsel -status** om ze te onderscheiden van de bestanden die het gedrag van de methode implementeren.

## <a name="properties"></a>Eigenschappen

De `Properties` sectie van het schema definieert de eigenschapswaarden die het apparaat rapporteert aan de oplossing. Bijvoorbeeld:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Wanneer de oplossing wordt gestart, worden alle gesimuleerde `Type` apparaten opgevraagd om een lijst met waarden te maken die in de gebruikersinterface moeten worden gebruikt. De oplossing `Latitude` gebruikt `Longitude` de eigenschappen en eigenschappen om de locatie van het apparaat toe te voegen aan de kaart op het dashboard.

## <a name="telemetry"></a>Telemetrie

De `Telemetry` array bevat alle telemetrietypen die het gesimuleerde apparaat naar de oplossing stuurt.

In het volgende voorbeeld wordt elke 10 `floor`seconden `vibration`een `temperature` JSON-telemetriebericht verzonden met , en gegevens van de sensoren van de lift:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate`definieert de structuur van het JSON-bericht dat door het gesimuleerde apparaat wordt verzonden. De tijdelijke `MessageTemplate` aanduidingen `${NAME}` in `NAME` de syntaxis gebruiken waar een sleutel van het [object apparaatstatus](#simulation)staat . Strings moeten worden geciteerd, nummers mogen niet.

`MessageSchema`definieert het schema van het bericht dat door het gesimuleerde apparaat wordt verzonden. Het berichtschema wordt ook gepubliceerd naar IoT Hub om backend-toepassingen in staat te stellen de informatie opnieuw te gebruiken om de binnenkomende telemetrie te interpreteren.

Momenteel u alleen JSON-berichtschema's gebruiken. De velden in het schema kunnen van de volgende typen zijn:

* Object - geserialiseerd met JSON
* Binair - geserialiseerd met basis64
* Tekst
* Booleaans
* Geheel getal
* Double
* DateTime

Als u telemetrieberichten met verschillende intervallen wilt `Telemetry` verzenden, voegt u meerdere telemetrietypen toe aan de array. In het volgende voorbeeld worden elke 10 seconden temperatuur- en vochtigheidsgegevens en de toestand van het licht elke minuut weergegeven:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Een gesimuleerd apparaat kan reageren op cloud-to-device-methoden die worden aangeroepen vanuit een IoT-hub. De `CloudToDeviceMethods` sectie in het schemabestand van het apparaatmodel:

* Hiermee definieert u de methoden waarop het gesimuleerde apparaat kan reageren.
* Hiermee identificeert u het JavaScript-bestand dat de uit te voeren logica bevat.

Het gesimuleerde apparaat stuurt de lijst met methoden die het ondersteunt naar de IoT-hub waarmee het is verbonden.

Zie Het gedrag van [het apparaatmodel begrijpen](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)voor meer informatie over het JavaScript-bestand dat het gedrag van het apparaat implementeert.

In het volgende voorbeeld worden drie ondersteunde methoden en de JavaScript-bestanden opgegeven die deze methoden implementeren:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

U de JavaScript-bestanden voor de standaard gesimuleerde apparaten in de [scriptsmap](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) op GitHub bekijken. Volgens de conventie hebben deze **JavaScript-bestanden** de achtervoegsel -methode om ze te onderscheiden van de bestanden die statusgedrag implementeren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u uw eigen aangepaste gesimuleerde apparaatmodel maakt. Dit artikel liet je zien hoe je:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Een JSON-bestand gebruiken om een gesimuleerd apparaatmodel te definiëren
> * De eigenschappen van het gesimuleerde apparaat opgeven
> * De telemetrie opgeven die het gesimuleerde apparaat verzendt
> * Geef de cloud-to-device-methoden op waarop het apparaat reageert

Nu u meer hebt geleerd over het JSON-schema, is de voorgestelde volgende stap om te leren hoe [u het gedrag van uw gesimuleerde apparaat implementeren.](iot-accelerators-remote-monitoring-device-behavior.md)

Zie voor meer informatie van ontwikkelaars over de oplossing voor externe monitoring:

* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Ontwikkelaarsgids voor het oplossen van problemen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

---
title: Device-schema in externe bewakings oplossing-Azure | Microsoft Docs
description: In dit artikel wordt het JSON-schema beschreven waarmee een gesimuleerd apparaat in de oplossing voor bewaking op afstand wordt gedefinieerd.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683748"
---
# <a name="understand-the-device-model-schema"></a>Het schema van het apparaatmodel begrijpen

U kunt gesimuleerde apparaten gebruiken in de oplossing voor bewaking op afstand om het gedrag ervan te testen. De oplossing voor controle op afstand bevat een Device simulatie service voor het uitvoeren van gesimuleerde apparaten. Wanneer u de oplossing voor controle op afstand implementeert, wordt automatisch een verzameling gesimuleerde apparaten ingericht. U kunt de bestaande gesimuleerde apparaten aanpassen of uw eigen apparaat maken.

In dit artikel wordt het schema voor model modellen beschreven waarmee de mogelijkheden en het gedrag van een gesimuleerd apparaat worden opgegeven. Het model van het apparaat wordt opgeslagen in een JSON-bestand.

> [!NOTE]
> Dit schema voor model modellen is alleen voor gesimuleerde apparaten die worden gehost in de Device simulatie service. Als u een echt apparaat wilt maken, raadpleegt u [uw apparaat aansluiten op de oplossings versneller voor externe controle](iot-accelerators-connecting-devices.md).

De volgende artikelen zijn gerelateerd aan het huidige artikel:

* [Het gedrag van het model van het apparaat implementeren](iot-accelerators-remote-monitoring-device-behavior.md) beschrijft de Java script-bestanden die u gebruikt voor het implementeren van het gedrag van een gesimuleerd apparaat.
* [Maak een nieuw gesimuleerd apparaat](iot-accelerators-remote-monitoring-create-simulated-device.md) en laat zien hoe u een nieuw gesimuleerd apparaattype kunt implementeren in uw oplossing.

In dit artikel leert u het volgende:

>[!div class="checklist"]
> * Een JSON-bestand gebruiken om een model voor gesimuleerd apparaat te definiëren
> * Eigenschappen opgeven voor het gesimuleerde apparaat
> * De telemetrie opgeven die het gesimuleerde apparaat verzendt
> * Geef de Cloud-naar-apparaat-methoden op waarop het apparaat reageert

## <a name="the-parts-of-the-device-model-schema"></a>De onderdelen van het schema voor het model van het apparaat

Elk model apparaat, zoals een chiller of een truck, definieert een type apparaat dat door de simulatie service kan worden gesimuleerd. Elk apparaatprofiel wordt opgeslagen in een JSON-bestand met het volgende schema op het hoogste niveau:

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

U kunt de schema bestanden voor de standaard gesimuleerde apparaten weer geven in de [map devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) op github.

In de volgende tabel worden de schema vermeldingen op het hoogste niveau beschreven:

| Schema vermelding | Beschrijving |
| -- | --- |
| `SchemaVersion` | De schema versie is altijd `1.0.0` en is specifiek voor de indeling van dit bestand. |
| `Id` | Een unieke ID voor dit model. |
| `Version` | Hiermee wordt de versie van het model van het apparaat geïdentificeerd. |
| `Name` | Een beschrijvende naam voor het model van het apparaat. |
| `Description` | Een beschrijving van het model van het apparaat. |
| `Protocol` | Het verbindings protocol dat door het apparaat wordt gebruikt. Dit kan een van `AMQP`, `MQTT`, en `HTTP`zijn. |

In de volgende secties worden de andere secties in het JSON-schema beschreven:

## <a name="simulation"></a>Simulatie

In de `Simulation` sectie definieert u de interne status van het gesimuleerde apparaat. Alle telemetrie-waarden die door het apparaat worden verzonden, moeten deel uitmaken van deze Apparaatstatus.

De definitie van de apparaatstatus heeft twee elementen:

* `InitialState`Hiermee worden de initiële waarden voor alle eigenschappen van het object Apparaatstatus gedefinieerd.
* `Script`identificeert een Java script-bestand dat volgens een schema wordt uitgevoerd om de status van het apparaat bij te werken. U kunt dit script bestand gebruiken om de telemetrische waarden die door het apparaat worden verzonden, te verwillen.

Zie [inzicht in het gedrag van het model](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)van het apparaat voor meer informatie over het Java script-bestand waarmee het object status wordt bijgewerkt.

In het volgende voor beeld ziet u de definitie van het object Apparaatstatus voor een gesimuleerd Chiller-apparaat:

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

De service simulatie voert het bestand **Chiller-01-State. js** elke vijf seconden uit om de status van het apparaat bij te werken. U kunt de Java script-bestanden voor de standaard gesimuleerde apparaten bekijken in de [map scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) op github. Deze Java script-bestanden hebben de achtervoegsel **-status** om ze te onderscheiden van de bestanden waarmee methode gedrag wordt geïmplementeerd.

## <a name="properties"></a>Eigenschappen

De `Properties` sectie van het schema definieert de eigenschaps waarden die het apparaat rapporteert aan de oplossing. Bijvoorbeeld:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

Wanneer de oplossing wordt gestart, wordt een query uitgevoerd op alle gesimuleerde apparaten om `Type` een lijst met waarden te maken die in de gebruikers interface moeten worden gebruikt. De oplossing gebruikt de `Latitude` eigenschappen `Longitude` en om de locatie van het apparaat toe te voegen aan de kaart op het dash board.

## <a name="telemetry"></a>Telemetrie

De `Telemetry` matrix bevat alle typen telemetrie die het gesimuleerde apparaat verzendt naar de oplossing.

In het volgende voor beeld wordt een JSON-telemetrie- `floor`bericht `vibration`elke tien `temperature` seconden verzonden met, en gegevens van de Sens oren van de lift:

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

`MessageTemplate`Hiermee definieert u de structuur van het JSON-bericht dat is verzonden door het gesimuleerde apparaat. De tijdelijke aanduidingen `MessageTemplate` in gebruiken de `${NAME}` syntaxis `NAME` , waarbij een sleutel is van het [object status](#simulation). Teken reeksen moeten aanhalings tekens zijn.

`MessageSchema`Hiermee wordt het schema van het bericht gedefinieerd dat door het gesimuleerde apparaat wordt verzonden. Het bericht schema wordt ook gepubliceerd op IoT Hub zodat back-end-toepassingen de gegevens opnieuw kunnen gebruiken om de binnenkomende telemetrie te interpreteren.

Op dit moment kunt u alleen JSON-bericht schema's gebruiken. De velden die in het schema worden weer gegeven, kunnen van de volgende typen zijn:

* Object-geserialiseerd met JSON
* Binair geserialiseerd met base64
* Tekst
* Booleaans
* Geheel getal
* Double
* DateTime

Als u telemetrie-berichten met verschillende intervallen wilt verzenden, voegt u `Telemetry` meerdere typen telemetrie toe aan de matrix. In het volgende voor beeld worden de gegevens over de Tempe ratuur en de vochtigheid elke 10 seconden en de status van het licht elke minuut verzonden:

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

Een gesimuleerd apparaat kan reageren op Cloud-naar-apparaat-methoden die vanuit een IoT-hub worden aangeroepen. De `CloudToDeviceMethods` sectie in het schema bestand van het model voor apparaten:

* Hiermee definieert u de methoden waarop het gesimuleerde apparaat kan reageren.
* Hiermee wordt het Java script-bestand geïdentificeerd dat de logica bevat die moet worden uitgevoerd.

Het gesimuleerde apparaat verzendt de lijst met methoden die worden ondersteund voor de IoT-hub waarmee deze is verbonden.

Zie [inzicht in het gedrag](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)van het model van het apparaat voor meer informatie over het Java script-bestand dat het gedrag van het apparaat implementeert.

In het volgende voor beeld worden drie ondersteunde methoden en de Java script-bestanden voor de implementatie van deze methoden opgegeven:

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

U kunt de Java script-bestanden voor de standaard gesimuleerde apparaten bekijken in de [map scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) op github. Deze Java script-bestanden hebben de achtervoegsel **-methode** om ze te onderscheiden van de bestanden die het status gedrag implementeren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een eigen aangepast model voor gesimuleerd apparaat maakt. In dit artikel wordt uitgelegd hoe u:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Een JSON-bestand gebruiken om een model voor gesimuleerd apparaat te definiëren
> * Eigenschappen opgeven voor het gesimuleerde apparaat
> * De telemetrie opgeven die het gesimuleerde apparaat verzendt
> * Geef de Cloud-naar-apparaat-methoden op waarop het apparaat reageert

Nu u over het JSON-schema hebt geleerd, is de voorgestelde volgende stap informatie over [het implementeren van het gedrag van uw gesimuleerde apparaat](iot-accelerators-remote-monitoring-device-behavior.md).

Voor meer informatie over ontwikkel aars over de oplossing voor controle op afstand raadpleegt u:

* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Ontwikkelaarsgids voor het oplossen van problemen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

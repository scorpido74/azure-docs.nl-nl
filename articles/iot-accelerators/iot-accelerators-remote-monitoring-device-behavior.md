---
title: Gesimuleerd apparaat in de oplossing voor externe controle-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Java script gebruikt voor het definiëren van het gedrag van een gesimuleerd apparaat in de oplossing voor externe controle.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7f887aac91bdb1b8c752806c7c5076708a40bc10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276166"
---
# <a name="implement-the-device-model-behavior"></a>Het gedrag van het model apparaat implementeren

In het artikel wordt het schema voor [model lering](iot-accelerators-remote-monitoring-device-schema.md) beschreven dat het schema beschrijft dat een model met gesimuleerd apparaat definieert. Dit artikel wordt verwezen naar twee typen java script-bestanden waarmee het gedrag van een gesimuleerd apparaat wordt geïmplementeerd:

- **Status** Java script-bestanden die op vaste intervallen worden uitgevoerd om de interne status van het apparaat bij te werken.
- **Methode** Java script-bestanden die worden uitgevoerd wanneer de oplossing een methode op het apparaat aanroept.

> [!NOTE]
> Gedrag van het apparaats model is alleen voor gesimuleerde apparaten die worden gehost in de Device simulatie service. Als u een echt apparaat wilt maken, raadpleegt u [uw apparaat aansluiten op de oplossings versneller voor externe controle](iot-accelerators-connecting-devices.md).

In dit artikel leert u het volgende:

>[!div class="checklist"]
> * De status van een gesimuleerd apparaat bepalen
> * Definiëren hoe een gesimuleerd apparaat reageert op een methode aanroep van de oplossing voor controle op afstand
> * Fouten opsporen in uw scripts

## <a name="state-behavior"></a>Status gedrag

In het gedeelte [simulatie](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) van het model voor het Apparaatbeheer definieert u de interne status van een gesimuleerd apparaat:

- `InitialState` Hiermee worden de initiële waarden voor alle eigenschappen van het object Apparaatstatus gedefinieerd.
- `Script` identificeert een Java script-bestand dat volgens een schema wordt uitgevoerd om de status van het apparaat bij te werken.

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

De status van het gesimuleerde apparaat, zoals gedefinieerd in de `InitialState` sectie, wordt in het geheugen van de simulatie service bewaard. De status informatie wordt als invoer door gegeven aan de functie die is `main` gedefinieerd in **chiller-01-state.js**. In dit voor beeld voert de simulatie service elke vijf seconden het **chiller-01-state.js** -bestand uit. Met het script kan de status van het gesimuleerde apparaat worden gewijzigd.

Hieronder ziet u het overzicht van een typische `main` functie:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

De `context` para meter heeft de volgende eigenschappen:

- `currentTime` Als een teken reeks met notatie `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, bijvoorbeeld `Simulated.Chiller.123`
- `deviceModel`, bijvoorbeeld `Chiller`

De `state` para meter bevat de status van het apparaat die wordt onderhouden door de Device simulatie service. Deze waarde is het `state` object dat wordt geretourneerd door de vorige aanroep van `main` .

In het volgende voor beeld ziet u een typische implementatie van de- `main` methode voor het afhandelen van de status van het apparaat die wordt onderhouden door de simulatie service:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

In het volgende voor beeld ziet u hoe de `main` methode telemetrische waarden kan simuleren die gedurende een bepaalde periode variëren:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

U kunt de volledige [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) weer geven op github.

## <a name="method-behavior"></a>Methode gedrag

De sectie [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) van het model van het apparaat definieert de methoden waarop een gesimuleerd apparaat reageert.

In het volgende voor beeld ziet u de lijst met methoden die worden ondersteund door een gesimuleerd Chiller-apparaat:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
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

Elke methode heeft een bijbehorend java script-bestand waarmee het gedrag van de methode wordt geïmplementeerd.

De status van het gesimuleerde apparaat, zoals gedefinieerd in de `InitialState` sectie van het schema, wordt in het geheugen van de simulatie service bewaard. De status informatie wordt als invoer door gegeven aan de `main` functie die is gedefinieerd in het Java script-bestand wanneer de methode wordt aangeroepen. Met het script kan de status van het gesimuleerde apparaat worden gewijzigd.

Hieronder ziet u het overzicht van een typische `main` functie:

```javascript
function main(context, previousState, previousProperties) {

}
```

De `context` para meter heeft de volgende eigenschappen:

- `currentTime` Als een teken reeks met notatie `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, bijvoorbeeld `Simulated.Chiller.123`
- `deviceModel`, bijvoorbeeld `Chiller`

De `state` para meter bevat de status van het apparaat die wordt onderhouden door de Device simulatie service.

De `properties` para meter bevat de eigenschappen van het apparaat die zijn geschreven als gerapporteerde eigenschappen naar het IOT hub-apparaat dubbele.

Er zijn drie globale functies die u kunt gebruiken om het gedrag van de-methode te implementeren:

- `updateState` de status van de simulatie service bijwerken.
- `updateProperty` om één apparaat-eigenschap bij te werken.
- `sleep` om de uitvoering te onderbreken om een langlopende taak te simuleren.

In het volgende voor beeld ziet u een verkorte versie van het **IncreasePressure-method.js** script dat wordt gebruikt door de gesimuleerde Chiller-apparaten:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Script bestanden voor fout opsporing

Het is niet mogelijk om een fout opsporingsprogramma te koppelen aan de Java script-interpreter die wordt gebruikt door de Device simulatie service om status-en methode scripts uit te voeren. U kunt echter gegevens in het service logboek registreren. Met de ingebouwde `log()` functie kunt u gegevens opslaan om de uitvoering van de functie bij te houden en fouten op te sporen.

Als er een syntaxis fout optreedt, mislukt de interpreter en schrijft hij een `Jint.Runtime.JavaScriptException` vermelding naar het service logboek.

Het [lokaal artikel over het uitvoeren van de service](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) in github laat zien hoe u de Device simulatie service lokaal uitvoert. Als u de service lokaal uitvoert, kunt u eenvoudiger fouten opsporen in uw gesimuleerde apparaten voordat u ze in de Cloud implementeert.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u het gedrag definieert van uw eigen aangepaste model voor gesimuleerde apparaten. In dit artikel wordt uitgelegd hoe u:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * De status van een gesimuleerd apparaat bepalen
> * Definiëren hoe een gesimuleerd apparaat reageert op een methode aanroep van de oplossing voor controle op afstand
> * Fouten opsporen in uw scripts

Nu u hebt geleerd hoe u het gedrag van een gesimuleerd apparaat kunt opgeven, is de voorgestelde volgende stap informatie over het [maken van een gesimuleerd apparaat](iot-accelerators-remote-monitoring-create-simulated-device.md).

Voor meer informatie over ontwikkel aars over de oplossing voor controle op afstand raadpleegt u:

* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Ontwikkelaarsgids voor het oplossen van problemen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->

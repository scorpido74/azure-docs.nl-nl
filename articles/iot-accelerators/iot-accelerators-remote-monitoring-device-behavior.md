---
title: Gesimuleerd apparaat in oplossing voor bewaking op afstand - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u JavaScript gebruiken om het gedrag van een gesimuleerd apparaat in de oplossing voor externe bewaking te definiëren.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890846"
---
# <a name="implement-the-device-model-behavior"></a>Het gedrag van het apparaatmodel implementeren

In het artikel [Het schema van het apparaatmodel begrijpen,](iot-accelerators-remote-monitoring-device-schema.md) wordt het schema beschreven dat een gesimuleerd apparaatmodel definieert. In dat artikel werd verwezen naar twee typen JavaScript-bestanden die het gedrag van een gesimuleerd apparaat implementeren:

- **Staat** JavaScript-bestanden die met vaste intervallen worden uitgevoerd om de interne status van het apparaat bij te werken.
- **Methode** JavaScript-bestanden die worden uitgevoerd wanneer de oplossing een methode op het apparaat aanroept.

> [!NOTE]
> Apparaatmodelgedrag is alleen voor gesimuleerde apparaten die worden gehost in de apparaatsimulatieservice. Als u een echt apparaat wilt maken, [raadpleegt u Uw apparaat verbinden met de versneller van de oplossing voor externe bewaking.](iot-accelerators-connecting-devices.md)

In dit artikel leert u het volgende:

>[!div class="checklist"]
> * De status van een gesimuleerd apparaat bepalen
> * Definiëren hoe een gesimuleerd apparaat reageert op een methodeaanroep vanuit de oplossing voor externe bewaking
> * Uw scripts debuggen

## <a name="state-behavior"></a>Staatsgedrag

De [sectie Simulatie](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) van het schema van het apparaatmodel definieert de interne status van een gesimuleerd apparaat:

- `InitialState`definieert de oorspronkelijke waarden voor alle eigenschappen van het object apparaatstatus.
- `Script`hiermee wordt een JavaScript-bestand geïdentificeerd dat volgens een schema wordt uitgevoerd om de apparaatstatus bij te werken.

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

De status van het gesimuleerde apparaat, `InitialState` zoals gedefinieerd in de sectie, wordt in het geheugen gehouden door de simulatieservice. De statusinformatie wordt doorgegeven `main` als invoer voor de functie gedefinieerd in **chiller-01-state.js**. In dit voorbeeld voert de simulatieservice het bestand **chiller-01-state.js** elke vijf seconden uit. Het script kan de status van het gesimuleerde apparaat wijzigen.

Het volgende toont de `main` omtrek van een typische functie:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

De `context` parameter heeft de volgende eigenschappen:

- `currentTime`als een tekenreeks met opmaak`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, bijvoorbeeld`Simulated.Chiller.123`
- `deviceModel`, bijvoorbeeld`Chiller`

De `state` parameter bevat de status van het apparaat zoals onderhouden door de apparaatsimulatieservice. Deze waarde `state` is het object dat `main`wordt geretourneerd door de vorige aanroep naar .

In het volgende voorbeeld ziet `main` u een typische implementatie van de methode voor het verwerken van de apparaatstatus die wordt onderhouden door de simulatieservice:

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

In het volgende `main` voorbeeld ziet u hoe de methode telemetriewaarden kan simuleren die in de loop van de tijd variëren:

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

U de volledige [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) bekijken op GitHub.

## <a name="method-behavior"></a>Methodegedrag

De sectie [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) van het schema van het apparaatmodel definieert de methoden waarop een gesimuleerd apparaat reageert.

In het volgende voorbeeld wordt de lijst met methoden weergegeven die worden ondersteund door een gesimuleerde koelmachine:

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

Elke methode heeft een bijbehorend JavaScript-bestand dat het gedrag van de methode implementeert.

De status van het gesimuleerde apparaat, `InitialState` zoals gedefinieerd in de sectie van het schema, wordt in het geheugen gehouden door de simulatieservice. De statusgegevens worden doorgegeven `main` als invoer naar de functie die is gedefinieerd in het JavaScript-bestand wanneer de methode wordt aangeroepen. Het script kan de status van het gesimuleerde apparaat wijzigen.

Het volgende toont de `main` omtrek van een typische functie:

```javascript
function main(context, previousState, previousProperties) {

}
```

De `context` parameter heeft de volgende eigenschappen:

- `currentTime`als een tekenreeks met opmaak`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, bijvoorbeeld`Simulated.Chiller.123`
- `deviceModel`, bijvoorbeeld`Chiller`

De `state` parameter bevat de status van het apparaat zoals onderhouden door de apparaatsimulatieservice.

De `properties` parameter bevat de eigenschappen van het apparaat die als gerapporteerde eigenschappen naar de IoT Hub-apparaattweeling zijn geschreven.

Er zijn drie algemene functies die u gebruiken om het gedrag van de methode te implementeren:

- `updateState`om de status van de simulatieservice bij te werken.
- `updateProperty`om één apparaateigenschap bij te werken.
- `sleep`om de uitvoering te onderbreken om een langlopende taak te simuleren.

In het volgende voorbeeld wordt een verkorte versie van het script **IncreasePressure-method.js** weergegeven dat wordt gebruikt door de gesimuleerde koelerapparaten:

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

## <a name="debugging-script-files"></a>Scriptbestanden debuggen

Het is niet mogelijk om een foutopsporing toe te voegen aan de Javascript-tolk die door de apparaatsimulatieservice wordt gebruikt om status- en methodescripts uit te voeren. U echter wel informatie inloggen in het servicelogboek. Met de `log()` ingebouwde functie u informatie opslaan om de uitvoering van de functie bij te houden en te debuggen.

Als er een syntaxisfout optreedt, `Jint.Runtime.JavaScriptException` mislukt de tolk en schrijft hij een vermelding in het servicelogboek.

In het artikel [Lokaal uitvoeren van de service](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) op GitHub ziet u hoe u de apparaatsimulatieservice lokaal uitvoeren. Als u de service lokaal uitvoert, u uw gesimuleerde apparaten gemakkelijker debuggen voordat u ze implementeert in de cloud.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u het gedrag van uw eigen aangepaste gesimuleerde apparaatmodel definiëren. Dit artikel liet je zien hoe je:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * De status van een gesimuleerd apparaat bepalen
> * Definiëren hoe een gesimuleerd apparaat reageert op een methodeaanroep vanuit de oplossing voor externe bewaking
> * Uw scripts debuggen

Nu u hebt geleerd hoe u het gedrag van een gesimuleerd apparaat opgeven, is de voorgestelde volgende stap om te leren hoe [u een gesimuleerd apparaat maakt.](iot-accelerators-remote-monitoring-create-simulated-device.md)

Zie voor meer informatie van ontwikkelaars over de oplossing voor externe monitoring:

* [Snelzoekgids voor ontwikkelaars](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Ontwikkelaarsgids voor het oplossen van problemen](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->

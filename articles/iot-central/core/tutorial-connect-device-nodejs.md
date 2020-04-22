---
title: Zelfstudie - Een algemene client-app Node.js verbinden met Azure IoT Central | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u als apparaatontwikkelaar een apparaat met een Node.js-client-app verbinden met uw Azure IoT Central-toepassing. U maakt een apparaatsjabloon door een apparaatcapaciteitsmodel te importeren en weergaven toe te voegen waarmee u communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758199"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Zelfstudie: Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Dit artikel is van toepassing op oplossingsbouwers en apparaatontwikkelaars.*

In deze zelfstudie ziet u hoe u als apparaatontwikkelaar een Clienttoepassing Node.js verbinden met uw Azure IoT Central-toepassing. De Node.js applicatie simuleert het gedrag van een milieusensor apparaat. U gebruikt een voorbeeldmodel voor _apparaatmogelijkheden_ om een _apparaatsjabloon_ te maken in IoT Central. U voegt weergaven toe aan de apparaatsjabloon om een operator in staat te stellen met een apparaat te communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Importeer een apparaatcapaciteitsmodel om een apparaatsjabloon te maken.
> * Standaardweergaven en aangepaste weergaven toevoegen aan een apparaatsjabloon.
> * Publiceer een apparaatsjabloon en voeg een echt apparaat toe aan uw IoT Central-toepassing.
> * Maak en voer de node.js-apparaatcode uit en zie deze verbinding maken met uw IoT Central-toepassing.
> * Bekijk de gesimuleerde telemetrie die vanaf het apparaat wordt verzonden.
> * Gebruik een weergave om apparaateigenschappen te beheren.
> * Synchrone en asynchrone opdrachten aanroepen om het apparaat te bedienen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing die is gemaakt met de **sjabloon Aangepaste toepassings.** Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een ontwikkelmachine met [Node.js](https://nodejs.org/) versie 10.0.0 of hoger geïnstalleerd. U kunt `node --version` in de opdrachtregel worden uitgevoerd om uw versie te controleren. De instructies in deze zelfstudie gaan ervan uit dat u de opdracht **knooppunt** uitvoert bij de opdrachtprompt van Windows. U Node.js echter op veel andere besturingssystemen gebruiken.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

In de volgende stappen ziet u hoe u een Clienttoepassing Node.js maakt die verbinding maakt met het echte apparaat dat u aan de toepassing hebt toegevoegd. Deze Node.js applicatie simuleert het gedrag van een echt apparaat.

1. Navigeer in de opdrachtregelomgeving `environmental-sensor` naar de map die u eerder hebt gemaakt.

1. Als u uw Node.js-project wilt initialiseren en de vereiste afhankelijkheden wilt installeren, `npm init`voert u de volgende opdrachten uit - accepteert u alle standaardopties wanneer u dit uitvoert:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Maak een bestand met de `environmental-sensor` naam **environmentalSensor.js** in de map.

1. Voeg de `require` volgende instructies toe aan het begin van het **bestand environmentalSensor.js:**

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Voeg de volgende variabelendeclaraties aan het bestand toe:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    Werk de `{your Scope ID}`tijdelijke `{your Device ID}`aanduidingen en `{your Primary Key}` met de waarden waar u eerder een notitie van hebt gemaakt. In dit voorbeeld `targetTemperature` u tot nul initialiseren, u de huidige meting van het apparaat of een waarde van de apparaattweeling gebruiken.

1. Als u gesimuleerde telemetrie naar uw Azure IoT Central-toepassing wilt verzenden, voegt u de volgende functie toe aan het bestand:

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    De namen van de`temp` telemetrie-items (en) `humid`moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Als u apparaattwee eigenschappen naar uw Azure IoT Central-toepassing wilt verzenden, voegt u de volgende functie toe aan uw bestand:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central gebruikt apparaattweelingen om eigenschapswaarden tussen het apparaat en de IoT Central-toepassing te synchroniseren. De eigenschappenwaarden van het apparaat gebruiken dubbele gerapporteerde eigenschappen van het apparaat. Schrijfbare eigenschappen gebruiken zowel de gerapporteerde als de gewenste eigenschappen van het apparaat.

1. Als u de schrijfbare eigenschappen wilt definiëren en verwerken waarop uw apparaat reageert, voegt u de volgende code toe:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Wanneer de operator een schrijfbare eigenschap instelt in de IoT Central-toepassing, gebruikt de toepassing een gewenste eigenschap van apparaattwee om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een apparaat twin gemeld eigenschap. Wanneer IoT Central de gerapporteerde eigenschapswaarde ontvangt, wordt de eigenschapsweergave bijgewerkt met een status van **gesynchroniseerd**.

    De namen van`name` de `brightness`eigenschappen (en) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Voeg de volgende code toe om de opdrachten te verwerken die vanuit de IoT Central-toepassing worden verzonden:

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    De namen van de`blink` `turnon`opdrachten `turnoff`( `rundiagnostics`, , en ) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

    Momenteel maakt IoT Central geen gebruik van het responsschema dat is gedefinieerd in het apparaatcapaciteitsmodel. Voor een synchrone opdracht kan de responspayload elke geldige JSON zijn. Voor een asynchrone opdracht moet het apparaat onmiddellijk een 202-antwoord retourneren, gevolgd door gerapporteerde eigenschapsupdate wanneer het werk is voltooid. De indeling van de gerapporteerde eigenschapsupdate is:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Een operator kan de reactiepayload in de opdrachtgeschiedenis bekijken.

1. Voeg de volgende code toe voor het voltooien van de verbinding met Azure IoT Central en het aansluiten van de functies in de clientcode:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>De Node.js-toepassing uitvoeren

Als u de apparaatclienttoepassing wilt starten, voert u de volgende opdracht uit in de opdrachtregelomgeving:

```cmd/sh
node environmentalSensor.js
```

U zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en telemetrie gaat verzenden:

![De clienttoepassing uitvoeren](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

U zien hoe het apparaat reageert op opdrachten en eigenschapsupdates:

![De clienttoepassing observeren](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Volgende stappen

Als apparaatontwikkelaar, nu u de basisprincipes hebt geleerd van het maken van een apparaat met Behulp van Node.js, zijn enkele voorgestelde volgende stappen:

- Meer informatie over het verbinden van een echt apparaat met IoT Central in het [Connect a MXChip IoT DevKit-apparaat met uw azure IoT Central-toepassingshow-to-artikel.](./howto-connect-devkit.md)
- Lees [Verbinding maken met Azure IoT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IoT Central en hoe IoT Central apparaatverbindingen beveiligt.

Als u liever door de set iot central-zelf-zelfstudies wilt gaan en meer te weten wilt komen over het bouwen van een IoT Central-oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)

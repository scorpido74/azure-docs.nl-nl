---
title: 'Zelf studie: een algemene node. js-client-app verbinden met Azure IoT Central | Microsoft Docs'
description: In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat een apparaat met een node. js-client-app verbindt met uw Azure IoT Central-toepassing. U maakt een sjabloon voor een apparaat door een mogelijkheidsprofiel te importeren en weer gaven toe te voegen waarmee u kunt communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: a8c5d9479585c0a519d0ad05a4d73f3f15b21287
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81758199"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Zelf studie: een client toepassing maken en verbinden met uw Azure IoT Central-toepassing (node. js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Dit artikel is van toepassing op oplossingen bouwers en ontwikkel aars van apparaten.*

In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat een node. js-client toepassing verbindt met uw Azure IoT Central-toepassing. Met de node. js-toepassing wordt het gedrag van een omgevings sensor apparaat gesimuleerd. U gebruikt een voor beeld van een _mogelijkheidsprofiel_ om een _sjabloon_ voor het apparaat te maken in IOT Central. U voegt weer gaven toe aan de sjabloon voor het apparaat om een operator in staat te stellen te communiceren met een apparaat.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Importeer een mogelijkheidsprofiel om een sjabloon voor een apparaat te maken.
> * Standaard-en aangepaste weer gaven toevoegen aan een sjabloon voor een apparaat.
> * Een sjabloon voor een apparaat publiceren en een echt apparaat toevoegen aan uw IoT Central-toepassing.
> * Maak de apparaatcode van node. js en voer deze uit om verbinding te maken met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie weer geven die vanaf het apparaat is verzonden.
> * Gebruik een weer gave om apparaateigenschappen te beheren.
> * U kunt synchrone en asynchrone opdrachten aanroepen om het apparaat te beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon voor **aangepaste toepassingen** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een ontwikkelings machine waarop de [node. js](https://nodejs.org/) -versie 10.0.0 of hoger is geïnstalleerd. U kunt uitvoeren `node --version` op de opdracht regel om uw versie te controleren. In de instructies in deze zelf studie wordt ervan uitgegaan dat u de **knoop punt** opdracht uitvoert vanaf de Windows-opdracht prompt. U kunt node. js echter gebruiken op veel andere besturings systemen.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

De volgende stappen laten zien hoe u een node. js-client toepassing maakt die verbinding maakt met het echte apparaat dat u hebt toegevoegd aan de toepassing. Met deze node. js-toepassing wordt het gedrag van een echt apparaat gesimuleerd.

1. Navigeer in uw opdracht regel omgeving naar de `environmental-sensor` map die u eerder hebt gemaakt.

1. Als u het node. js-project wilt initialiseren en de vereiste afhankelijkheden wilt installeren, voert u de volgende opdrachten uit: accepteer `npm init`alle standaard opties wanneer u uitvoert.

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Maak in de map een bestand met de `environmental-sensor` naam **environmentalSensor. js** .

1. Voeg de volgende `require` -instructies toe aan het begin van het bestand **environmentalSensor. js** :

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

    Werk de tijdelijke `{your Scope ID}`aanduidingen `{your Device ID}`, en `{your Primary Key}` met de waarden die u eerder hebt genoteerd, bij. In dit `targetTemperature` voor beeld initialiseert u op nul, kunt u het huidige aantal lees bewerkingen van het apparaat of een waarde vanaf het dubbele apparaat gebruiken.

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

    De namen van de telemetrie-`temp` items `humid`(en) moeten overeenkomen met de namen die worden gebruikt in de sjabloon voor het apparaat.

1. Als u dubbele eigenschappen van een apparaat wilt verzenden naar uw Azure IoT Central-toepassing, voegt u de volgende functie toe aan het bestand:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central gebruikt apparaatdubbels om eigenschaps waarden te synchroniseren tussen het apparaat en de IoT Central toepassing. Waarden van eigenschap van apparaat gebruiken dubbele gerapporteerde eigenschappen. Beschrijf bare eigenschappen gebruiken zowel dubbele gerapporteerde als gewenste eigenschappen van het apparaat.

1. Voeg de volgende code toe om de Beschrijf bare eigenschappen te definiëren en te verwerken waarop uw apparaat reageert:

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

    Wanneer de operator een schrijf bare eigenschap in de IoT Central toepassing instelt, gebruikt de toepassing een door het apparaat dubbele gewenste eigenschap om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een dubbele gerapporteerde eigenschap van het apparaat. Wanneer IoT Central de gerapporteerde eigenschaps waarde ontvangt, wordt de eigenschappen weergave bijgewerkt met de status **gesynchroniseerd**.

    De namen van de eigenschappen (`name` en `brightness`) moeten overeenkomen met de namen die worden gebruikt in de sjabloon voor het apparaat.

1. Voeg de volgende code toe om de opdrachten te verwerken die zijn verzonden vanuit de IoT Central-toepassing:

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

    De namen van de opdrachten (`blink`, `turnon` `turnoff`, en `rundiagnostics`) moeten overeenkomen met de namen die worden gebruikt in de sjabloon voor het apparaat.

    Op dit moment gebruikt IoT Central niet het antwoord schema dat is gedefinieerd in het functionaliteits model van het apparaat. Voor een synchrone opdracht kan de nettolading van de reactie een wille keurige geldige JSON zijn. Voor een asynchrone opdracht moet het apparaat onmiddellijk een 202-antwoord retour neren, gevolgd door de gerapporteerde eigenschap bijwerken wanneer het werk is voltooid. De indeling van de gerapporteerde eigenschaps update is:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Een operator kan de reactie lading weer geven in de opdracht geschiedenis.

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

Voer de volgende opdracht uit in de opdracht regel omgeving om de client toepassing van het apparaat te starten:

```cmd/sh
node environmentalSensor.js
```

U kunt zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en dat telemetrie wordt verzonden:

![De client toepassing uitvoeren](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen:

![De client toepassing observeren](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de basis beginselen van het maken van een apparaat met behulp van node. js hebt geleerd, kunt u als apparaat voor ontwikkel aars het volgende doen:

- Meer informatie over het verbinden van een echt apparaat met IoT Central in het [MXChip IOT DevKit-apparaat verbinden met uw Azure IOT Central Application](./howto-connect-devkit.md) procedures-artikel.
- Lees [verbinding maken met Azure IOT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IOT Central en hoe IOT Central verbindingen met apparaten beveiligt.

Als u liever door wilt gaan met de set IoT Central zelf studies en meer wilt weten over het bouwen van een IoT Central oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)

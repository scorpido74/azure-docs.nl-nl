---
title: 'Zelfstudie: een generieke Node.js-client-app verbinden met Azure IoT Central | Microsoft Docs'
description: In deze zelfstudie wordt uitgelegd hoe u, als apparaatontwikkelaar, een apparaat met een Node.js-client-app verbindt met uw Azure IoT Central-toepassing. U maakt een apparaatsjabloon door een apparaatondersteuningsprofiel te importeren en weergaven toe te voegen waarmee u kunt communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 07/07/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt, devx-track-javascript
ms.openlocfilehash: 92b28b89d181de368ef1e39eb695be2e1fad6c37
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423519"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Zelfstudie: Een clienttoepassing maken en verbinden met uw Azure IoT Central-toepassing (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

In deze zelfstudie wordt uitgelegd hoe u, als apparaatontwikkelaar, een Node.js-clienttoepassing verbindt met uw Azure IoT Central-toepassing. De Node.js-toepassing simuleert het gedrag van een omgevingssensorapparaat. U gebruikt een voorbeeld van een _apparaatondersteuningsprofiel_ om _een apparaatsjabloon_ te maken in IoT Central. U voegt weergaven toe aan de apparaatsjabloon zodat een operator met een apparaat kan communiceren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatondersteuningsprofiel importeren om een apparaatsjabloon te maken.
> * Standaardweergaven en aangepaste weergaven toevoegen aan een apparaatsjabloon.
> * Een apparaatsjabloon publiceren en een echt apparaat toevoegen aan uw IoT Central-toepassing.
> * De Node.js-apparaatcode maken, deze uitvoeren en kijken hoe deze verbinding maakt met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie bekijken die vanaf het apparaat wordt verzonden.
> * Een weergave gebruiken om apparaateigenschappen te beheren.
> * Synchrone en asynchrone opdrachten aanroepen om het apparaat te beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon **Aangepaste toepassing**. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md). De toepassing moet op of na 14-07-2020 zijn gemaakt.
* Een ontwikkelmachine waarop [Node.js](https://nodejs.org/) versie 10.0.0 of hoger is geïnstalleerd. Voer `node --version` uit op de opdrachtregel om uw versie te controleren. In de instructies in deze zelfstudie wordt ervan uitgegaan dat u de opdracht **node** uitvoert vanaf de Windows-opdrachtprompt. U kunt Node.js echter gebruiken met verschillende andere besturingssystemen.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

In de volgende stappen ziet u hoe u een Node.js-clienttoepassing maakt die verbinding maakt met het echte apparaat dat u aan de toepassing hebt toegevoegd. Deze Node.js-toepassing simuleert het gedrag van een echt apparaat.

1. Ga in uw opdrachtregelprogramma naar de map `environmental-sensor` die u eerder hebt gemaakt.

1. U kunt het Node.js-project initialiseren en de vereiste afhankelijkheden installeren door de volgende opdrachten uit te voeren (accepteer alle standaardopties wanneer u `npm init`uitvoert):

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Maak een bestand met de naam **environmentalSensor.js** in de map `environmental-sensor`.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **environmentalSensor.js**:

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

    Werk de tijdelijke aanduidingen voor `{your Scope ID}`, `{your Device ID}` en `{your Primary Key}` bij met de waarden die u eerder hebt genoteerd. In dit voorbeeld zet u `targetTemperature` op nul. U kunt ook de huidige waarde van het apparaat of een waarde van de apparaatdubbel gebruiken.

1. Als u telemetrie wilt verzenden naar uw Azure IoT Central-toepassing, voegt u de volgende functie toe aan het bestand:

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

    De namen van de telemetriegegevens (`temp` en `humid`) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Als u eigenschappen van de app wilt verzenden naar uw Azure IoT Central-toepassing, voegt u de volgende functie toe aan het bestand:

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central gebruikt apparaatdubbels om de waarden van eigenschappen te synchroniseren tussen het apparaat en de IoT Central-toepassing. Waarden van eigenschappen van een apparaat gebruiken door apparaatdubbels gerapporteerde eigenschappen. Beschrijfbare eigenschappen gebruiken zowel eigenschappen gerapporteerd door apparaatdubbels als gewenste eigenschappen.

1. Voeg de volgende code toe om de beschrijfbare eigenschappen te definiëren en te verwerken waarop uw apparaat reageert. Het bericht dat het apparaat verzendt als reactie op de [update van de beschrijfbare eigenschap](concepts-telemetry-properties-commands.md#writeable-property-types) moet de velden `av` en `ac` bevatten. Het veld `ad` is optioneel:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed', 200);
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed', 200);
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting]}`);
            writeableProperties[setting](desiredChange[setting], (newValue, status, code) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  ad: status,
                  ac: code,
                  av: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    Wanneer de operator een schrijfbare eigenschap instelt in de IoT Central-toepassing, gebruikt de toepassing een door de apparaatdubbel gewenste eigenschap om de waarde naar het apparaat te verzenden. Het apparaat reageert vervolgens met behulp van een door de apparaatdubbel gerapporteerde eigenschap. Wanneer IoT Central de gerapporteerde eigenschapswaarde ontvangt, wordt de eigenschapsweergave bijgewerkt met de status **gesynchroniseerd**.

    De namen van de eigenschappen (`name` en `brightness`) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

1. Voeg de volgende code toe om de opdrachten te verwerken die worden verzonden vanuit de IoT Central-toepassing:

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

    De namen van de opdrachten (`blink`, `turnon`, `turnoff` en `rundiagnostics`) moeten overeenkomen met de namen die in de apparaatsjabloon worden gebruikt.

    Op dit moment gebruikt IoT Central niet het antwoordschema dat is gedefinieerd in het apparaatondersteuningsprofiel. Voor een synchrone opdracht kan de nettolading van het antwoord een willekeurige geldige JSON zijn. Voor een asynchrone opdracht moet het apparaat onmiddellijk een 202-antwoord retourneren, gevolgd door de gerapporteerde eigenschapsupdate wanneer het werk is voltooid. De indeling van de gerapporteerde eigenschapsupdate is:

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    Een operator kan de nettolading van het antwoord weergeven in de opdrachtgeschiedenis.

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
              state: 'true',
              processorArchitecture: 'ARM',
              swVersion: '1.0.0'
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

Als u de apparaatclienttoepassing wilt uitvoeren, voert u de volgende opdracht uit in uw opdrachtregelprogramma:

```cmd/sh
node environmentalSensor.js
```

U kunt zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en begint met het verzenden van telemetrie:

![De clienttoepassing uitvoeren](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen:

![De clienttoepassing observeren](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="view-raw-data"></a>Onbewerkte gegevens weergeven

[!INCLUDE [iot-central-monitor-environmental-sensor-raw-data](../../../includes/iot-central-monitor-environmental-sensor-raw-data.md)]

## <a name="next-steps"></a>Volgende stappen

U, als apparaatontwikkelaar, kent nu de basisbeginselen van het maken van een apparaat met behulp van Node.js. Hierna volgen enkele voorgestelde volgende stappen:

* Lees [Wat zijn apparaatsjablonen?](./concepts-device-templates.md) voor meer informatie over de rol van apparaatsjablonen wanneer u uw apparaatcode implementeert.
* Lees [Verbinding maken met Azure IoT Central](./concepts-get-connected.md) voor meer informatie over het registreren van apparaten met IoT Central en hoe IoT Central apparaatverbindingen beveiligt.

Als u liever wilt doorgaan met de set zelfstudies over IoT Central en meer wilt leren over het bouwen van een IoT Central-oplossing, raadpleegt u:

> [!div class="nextstepaction"]
> [Een gateway-apparaatsjabloon maken](./tutorial-define-gateway-device-type.md)

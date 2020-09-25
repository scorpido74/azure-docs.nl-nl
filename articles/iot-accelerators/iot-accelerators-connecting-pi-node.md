---
title: Raspberry Pi verbinden met de oplossing voor externe bewaking-Node.js-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u een Raspberry Pi-apparaat verbindt met de oplossings versneller voor externe bewaking met behulp van een toepassing die is geschreven in Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 46f8f811e7d1a019fa332cd289892d110531c84d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261546"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Sluit uw Raspberry Pi-apparaat aan op de oplossings versneller voor externe controle (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelf studie leert u hoe u een echt apparaat verbindt met de oplossings versneller voor externe controle. In deze zelf studie gebruikt u Node.js. Dit is een goede optie voor omgevingen met minimale resource beperkingen.

Als u liever een apparaat simuleert, raadpleegt u [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Vereiste hardware

Een desktop computer waarmee u extern verbinding kunt maken met de opdracht regel op de Raspberry pi.

[Micro soft IOT Starter Kit voor Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) of gelijkwaardige onderdelen. In deze zelf studie wordt gebruikgemaakt van de volgende items uit de kit:

- Raspberry Pi 3
- MicroSD-kaart (met NOOBS)
- Een USB-mini kabel
- Een Ethernet-kabel

### <a name="required-desktop-software"></a>Vereiste bureaublad software

U hebt SSH-client op uw computer nodig om u in staat te stellen op afstand toegang te krijgen tot de opdracht regel op de Raspberry pi.

- Windows bevat geen SSH-client. U kunt het beste [putty](https://www.putty.org/)gebruiken.
- De meeste Linux-distributies en Mac OS bevatten het opdracht regel SSH-hulp programma. Zie [SSH using Linux of Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)voor meer informatie.

### <a name="required-raspberry-pi-software"></a>Vereiste Raspberry Pi-software

Als u dit nog niet hebt gedaan, installeert u Node.js versie 4.0.0 of hoger op uw Raspberry pi. De volgende stappen laten zien hoe u Node.js V6 installeert op uw Raspberry PI:

1. Maak verbinding met uw Raspberry Pi met `ssh` . Zie [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) op de [Raspberry Pi-website](https://www.raspberrypi.org/)voor meer informatie.

1. Gebruik de volgende opdracht om uw Raspberry Pi bij te werken:

    ```sh
    sudo apt-get update
    ```

1. Gebruik de volgende opdrachten voor het verwijderen van een bestaande installatie van Node.js van uw Raspberry PI:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Gebruik de volgende opdracht om Node.js V6 te downloaden en installeren op uw Raspberry PI:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Gebruik de volgende opdracht om te controleren of u Node.js v 6.11.4 hebt geïnstalleerd:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Een Node.js-oplossing maken

Voer de volgende stappen uit met behulp van de `ssh` verbinding met uw Raspberry PI:

1. Maak een map met de naam `remotemonitoring` in uw basismap op de Raspberry pi. Ga naar deze map op de opdracht regel:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Voer de volgende opdrachten uit om de pakketten te downloaden en te installeren die u nodig hebt om de voor beeld-app te volt ooien:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Maak in de `remotemonitoring` map een bestand met de naam **remote_monitoring.js**. Open dit bestand in een teksteditor. Op de Raspberry Pi kunt u de `nano` of `vi` tekst editors gebruiken.

1. Voeg in het **remote_monitoring.js** -bestand de volgende- `require` instructies toe:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Voeg de volgende variabelendeclaraties achter de `require`-instructies toe. Vervang de waarde van de tijdelijke aanduiding door de waarde die u hebt `{device connection string}` genoteerd voor het apparaat dat u in de oplossing voor externe controle hebt ingericht:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Als u een aantal basis-telemetriegegevens wilt definiëren, voegt u de volgende variabelen toe:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Als u bepaalde eigenschaps waarden wilt definiëren, voegt u de volgende variabelen toe:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Voeg de volgende variabele toe om de gerapporteerde eigenschappen te definiëren die moeten worden verzonden naar de oplossing. Deze eigenschappen bevatten meta gegevens die in de gebruikers interface van de Web-UI worden weer gegeven:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Als u de bewerkings resultaten wilt afdrukken, voegt u de volgende Help-functie toe:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Voeg de volgende Help-functie toe om de telemetrische waarden wille keurig te gebruiken:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Voeg de volgende algemene functie toe voor het afhandelen van directe methode aanroepen van de oplossing. De functie geeft informatie weer over de directe methode die is aangeroepen, maar in dit voor beeld wordt het apparaat op geen enkele manier gewijzigd. De oplossing maakt gebruik van directe methoden om op apparaten te reageren:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Voeg de volgende functie toe om de **FirmwareUpdate** direct-methode aanroepen van de oplossing te verwerken. De functie verifieert de para meters die zijn door gegeven in de nettolading van de directe methode en voert vervolgens asynchroon een firmware-update simulatie uit:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Voeg de volgende functie toe om een langlopende firmware-update stroom te simuleren die de voortgang weer rapporteert naar de oplossing:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Voeg de volgende code toe om telemetriegegevens naar de oplossing te verzenden. De client-App voegt eigenschappen toe aan het bericht om het bericht schema te identificeren:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Voeg de volgende code toe om een client exemplaar te maken:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Voeg de volgende code toe aan:

    * Open de verbinding.
    * Stel een handler voor gewenste eigenschappen in.
    * Gerapporteerde eigenschappen verzenden.
    * Handlers voor de directe methoden registreren. In het voor beeld wordt een afzonderlijke handler gebruikt voor de directe methode firmware-update.
    * Begin met het verzenden van telemetrie.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Sla de wijzigingen in het **remote_monitoring.js** bestand op.

1. Als u de voorbeeld toepassing wilt starten, voert u de volgende opdracht uit bij de opdracht prompt op de Raspberry PI:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

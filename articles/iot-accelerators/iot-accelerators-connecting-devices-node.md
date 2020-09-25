---
title: Apparaten inrichten voor externe bewaking in Node.js-Azure | Microsoft Docs
description: Hierin wordt beschreven hoe u een apparaat verbindt met de oplossings versneller voor externe bewaking met behulp van een toepassing die is geschreven in Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: 7982c094117d3cd40d26873ca2250edbda81b1ad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261631"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Uw apparaat aansluiten op de oplossings versneller voor externe controle (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelf studie leert u hoe u een echt apparaat verbindt met de oplossings versneller voor externe controle. In deze zelf studie gebruikt u Node.js. Dit is een goede optie voor omgevingen met minimale resource beperkingen.

Als u liever een apparaat simuleert, raadpleegt u [een nieuw gesimuleerd apparaat maken en testen](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="create-a-nodejs-solution"></a>Een Node.js-oplossing maken

Zorg ervoor dat [Node.js](https://nodejs.org/) versie 4.0.0 of hoger is geïnstalleerd op uw ontwikkel computer. U kunt uitvoeren `node --version` op de opdracht regel om de versie te controleren.

1. Maak een map `remotemonitoring` met de naam op uw ontwikkel computer. Navigeer naar deze map in uw opdracht regel omgeving.

1. Voer de volgende opdrachten uit om de pakketten te downloaden en te installeren die u nodig hebt om de voor beeld-app te volt ooien:

    ```cmd/sh
    npm init
    npm install async azure-iot-device azure-iot-device-mqtt --save
    ```

1. Maak in de `remotemonitoring` map een bestand met de naam **remote_monitoring.js**. Open dit bestand in een teksteditor.

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

1. Als u de voorbeeld toepassing wilt starten, voert u de volgende opdracht uit vanaf een opdracht prompt:

     ```cmd/sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

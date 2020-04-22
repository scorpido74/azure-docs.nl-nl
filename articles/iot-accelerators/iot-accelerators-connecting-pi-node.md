---
title: Raspberry Pi verbinden met de oplossing voor externe bewaking - Node.js - Azure | Microsoft Documenten
description: Beschrijft hoe u een Raspberry Pi-apparaat aansluit op de remote monitoring-oplossingsversneller met behulp van een toepassing die is geschreven in Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.custom: mqtt
ms.openlocfilehash: 9335c45688752ea41801e988157740f4170cfcb4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683947"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Sluit uw Raspberry Pi-apparaat aan op de remote monitoring-oplossingsversneller (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze zelfstudie ziet u hoe u een echt apparaat aansluiten op de versneller van de oplossing op afstand. In deze zelfstudie gebruikt u Node.js, wat een goede optie is voor omgevingen met minimale resourcebeperkingen.

Zie Een nieuw gesimuleerd apparaat maken en testen als u een apparaat liever [simuleert.](iot-accelerators-remote-monitoring-create-simulated-device.md)

### <a name="required-hardware"></a>Vereiste hardware

Een desktopcomputer waarmee u op afstand verbinding maken met de opdrachtregel op de Raspberry Pi.

[Microsoft IoT Starter Kit voor Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) of gelijkwaardige componenten. In deze zelfstudie worden de volgende items uit de kit gebruikt:

- Raspberry Pi 3
- MicroSD-kaart (met NOOBS)
- Een USB Mini-kabel
- Een Ethernet-kabel

### <a name="required-desktop-software"></a>Vereiste desktopsoftware

U hebt SSH-client op uw desktopmachine nodig om op afstand toegang te krijgen tot de opdrachtregel op de Raspberry Pi.

- Windows bevat geen SSH-client. Wij raden u aan [PuTTY te](https://www.putty.org/)gebruiken.
- De meeste Linux-distributies en Mac OS bevatten het ssh-hulpprogramma voor de command-line. Zie [SSH Gebruiken met Linux of Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)voor meer informatie.

### <a name="required-raspberry-pi-software"></a>Vereiste Raspberry Pi-software

Als je dit nog niet hebt gedaan, installeer dan Node.js versie 4.0.0 of hoger op je Raspberry Pi. In de volgende stappen ziet u hoe u Node.js v6 op uw Raspberry Pi installeert:

1. Maak verbinding met `ssh`je Raspberry Pi met behulp van . Zie [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) op de [Raspberry Pi-website](https://www.raspberrypi.org/)voor meer informatie.

1. Gebruik de volgende opdracht om je Raspberry Pi bij te werken:

    ```sh
    sudo apt-get update
    ```

1. Gebruik de volgende opdrachten om bestaande installatie van Node.js uit uw Raspberry Pi te verwijderen:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Gebruik de volgende opdracht om Node.js v6 op je Raspberry Pi te downloaden en te installeren:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Gebruik de volgende opdracht om te controleren of u Node.js v6.11.4 hebt geïnstalleerd:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Een Node.js-oplossing maken

Voer de volgende `ssh` stappen uit met de verbinding met uw Raspberry Pi:

1. Maak een `remotemonitoring` map genaamd in uw thuismap op de Raspberry Pi. Navigeer naar deze map in uw opdrachtregel:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Voer de volgende opdrachten uit om de pakketten te downloaden en te installeren die u nodig hebt om de voorbeeld-app te voltooien:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Maak `remotemonitoring` in de map een bestand met de naam **remote_monitoring.js**. Open dit bestand in een teksteditor. Op de Raspberry Pi kun `nano` `vi` je de teksteditors gebruiken.

1. Voeg in het **bestand remote_monitoring.js** de volgende `require` instructies toe:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Voeg de volgende variabelendeclaraties achter de `require`-instructies toe. Vervang de tijdelijke `{device connection string}` aanduidingswaarde door waarde die u hebt genoteerd voor het apparaat dat u in de oplossing voor externe bewaking hebt ingerichte:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Als u bepaalde basistelemetriegegevens wilt definiëren, voegt u de volgende variabelen toe:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Als u bepaalde eigenschapswaarden wilt definiëren, voegt u de volgende variabelen toe:

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

1. Voeg de volgende variabele toe om de gerapporteerde eigenschappen te definiëren die naar de oplossing moeten worden verzonden. Deze eigenschappen omvatten metagegevens die moeten worden weergegeven in de webgebruikersinterface:

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

1. Als u de bewerkingsresultaten wilt afdrukken, voegt u de volgende helperfunctie toe:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Voeg de volgende helperfunctie toe die u wilt gebruiken om de telemetriewaarden te randomiseren:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Voeg de volgende algemene functie toe om directe methodeaanroepen vanuit de oplossing te verwerken. De functie geeft informatie weer over de directe methode die is aangeroepen, maar in dit voorbeeld wijzigt het apparaat op geen enkele manier. De oplossing maakt gebruik van directe methoden om te handelen op apparaten:

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

1. Voeg de volgende functie toe om de directe methodeaanroepen van **FirmwareUpdate** vanuit de oplossing te verwerken. De functie controleert de parameters die worden doorgegeven in de laadlading van de directe methode en voert vervolgens asynchroon een firmware-updatesimulatie uit:

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

1. Voeg de volgende functie toe om een langlopende firmware-updatestroom te simuleren die de voortgang naar de oplossing rapporteert:

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

1. Voeg de volgende code toe om telemetriegegevens naar de oplossing te verzenden. De client-app voegt eigenschappen toe aan het bericht om het berichtschema te identificeren:

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

1. Voeg de volgende code toe om een clientinstantie te maken:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Voeg de volgende code toe aan:

    * Open de verbinding.
    * Stel een handler in voor de gewenste eigenschappen.
    * Gerapporteerde eigenschappen verzenden.
    * Registreer handlers voor de directe methoden. Het voorbeeld maakt gebruik van een aparte handler voor de directe methode voor firmware-update.
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

1. Sla de wijzigingen op in het **bestand remote_monitoring.js.**

1. Als u de voorbeeldtoepassing wilt starten, voert u de volgende opdracht uit op uw opdrachtprompt op de Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

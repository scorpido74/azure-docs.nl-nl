---
title: Aan de slag met Azure IoT Hub-apparaatbeheer (knooppunt) | Microsoft Documenten
description: Het apparaatbeheer van IoT Hub gebruiken om een herstart van een extern apparaat te starten. U gebruikt de Azure IoT SDK voor Node.js om een gesimuleerde apparaat-app te implementeren die een directe methode en een service-app bevat die de directe methode aanroept.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 5b2e4c03347020b5d5fc67927165403f06854e0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110911"
---
# <a name="get-started-with-device-management-nodejs"></a>Aan de slag met apparaatbeheer (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze handleiding ontdekt u hoe u:

* Gebruik de [Azure-portal](https://portal.azure.com) om een IoT-hub te maken en een apparaatidentiteit te maken in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat die dat apparaat opnieuw opstart. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een Node.js-console-app die de directe herstartmethode aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het einde van deze zelfstudie heb je twee Node.js-console-apps:

* **dmpatterns_getstarted_device.js**, die verbinding maakt met uw IoT-hub met de apparaatidentiteit die eerder is gemaakt, ontvangt een directe herstartmethode, simuleert een fysieke reboot en rapporteert de tijd voor de laatste reboot.

* **dmpatterns_getstarted_service.js**, die een directe methode aanroept in de gesimuleerde apparaat-app, geeft het antwoord weer en geeft de bijgewerkte gerapporteerde eigenschappen weer.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0.x of hoger. [Bereid uw ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beschrijft hoe u Node.js installeert voor deze zelfstudie op Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* U maakt een Node.js-console-app die reageert op een directe methode die door de cloud wordt aangeroepen.

* Een gesimuleerde apparaatopnieuw opstarten activeren

* Gebruik de gerapporteerde eigenschappen om apparaatdubbele query's in te schakelen om apparaten te identificeren en wanneer ze voor het laatst zijn opgestart

1. U maakt een lege map met de naam **simulateddevice**.  Maak in de map **simulateddevice** een bestand met de naam package.json door achter de opdrachtprompt de volgende opdracht op te geven.  Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij uw opdrachtprompt in de map **beheerde apparaten** de volgende opdracht uit om het **SDK-pakket azure-iot-device** Device en **het Azure-iot-device-mqtt--pakket** te installeren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met een teksteditor een **dmpatterns_getstarted_device.js-bestand** in de map **beheerd apparaat.**

4. Voeg aan het begin van het **dmpatterns_getstarted_device.js-bestand** de volgende 'require'-instructies toe:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een **client**exemplaar te maken.  Vervang `{yourdeviceconnectionstring}` de tijdelijke aanduidingswaarde door de tekenreeks van de apparaatverbinding die u eerder hebt gekopieerd in [Een nieuw apparaat registreren in de IoT-hub.](#register-a-new-device-in-the-iot-hub)  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. De volgende functie toevoegen om de directe methode op het apparaat te implementeren

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Open de verbinding met uw IoT-hub en start de directe methodelistener:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Sla het **bestand dmpatterns_getstarted_device.js op** en sluit deze.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële back-off), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met een directe methode

In deze sectie maakt u een Node.js-console-app die een externe reboot op een apparaat initieert met behulp van een directe methode. De app gebruikt apparaatdubbele query's om de laatste herstarttijd voor dat apparaat te ontdekken.

1. Maak een lege map genaamd **triggerrebootondevice**. Maak in de map **triggerrebootondevice** een package.json-bestand met de volgende opdracht bij de opdrachtprompt. Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer op uw opdrachtprompt in de map **triggerrebootondevice** de volgende opdracht uit om het **azure-iothub Device SDK-pakket** en **het Azure-iot-device-mqtt-pakket** te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met een teksteditor een **dmpatterns_getstarted_service.js-bestand** in de map **triggerrebootondevice.**

4. Voeg aan het begin van het **bestand dmpatterns_getstarted_service.js** de volgende 'require'-instructies toe:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Voeg de volgende variabele declaratie toe en vervang de `{iothubconnectionstring}` tijdelijke waarde door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in De [verbindingstekenreeks van de IoT-hub:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Voeg de volgende functie toe om de apparaatmethode aan te roepen om het doelapparaat opnieuw op te starten:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Voeg de volgende functie toe aan query's voor het apparaat en ontvang de laatste herstarttijd:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Voeg de volgende code toe om de functies aan te roepen die de directe methode en query voor de laatste herstarttijd activeren:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Sla het **bestand dmpatterns_getstarted_service.js op** en sluit deze.

## <a name="run-the-apps"></a>De apps uitvoeren

Je bent nu klaar om de apps uit te voeren.

1. Voer bij de opdrachtprompt in de map **manageddevice** de volgende opdracht uit om te beginnen met luisteren naar de directe methode voor opnieuw opstarten.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. Voer bij de opdrachtprompt in de map **triggerrebootondevice** de volgende opdracht uit om de externe herstart te activeren en de apparaattweeling op te vragen om de laatste herstarttijd te vinden.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. U ziet de reactie van het apparaat op de directe herstartmethode en de rebootstatus in de console.

   Het volgende toont het apparaatantwoord op de directe herstartmethode die door de service wordt verzonden:

   ![uitvoer van beheerde apparaat-apps](./media/iot-hub-node-node-device-management-get-started/device.png)

   Het volgende toont de service triggering de reboot en polling het apparaat twin voor de laatste reboot tijd:

   ![triggerrebootondevice app output](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

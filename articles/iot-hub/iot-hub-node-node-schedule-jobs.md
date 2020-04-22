---
title: Taken plannen met Azure IoT Hub (Knooppunt) | Microsoft Documenten
description: Een Azure IoT Hub-taak plannen om een directe methode op meerdere apparaten aan te roepen. U gebruikt de Azure IoT-SDK's voor Node.js om de gesimuleerde apparaat-apps en een service-app te implementeren om de taak uit te voeren.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt
ms.openlocfilehash: d7f9ce37ad85d39388eea90af263f59ce312a6b8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732269"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Taken plannen en uitzenden (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub is een volledig beheerde service waarmee een back-end-app taken kan maken en bijhouden die miljoenen apparaten plannen en bijwerken.  Taken kunnen worden gebruikt voor de volgende acties:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Conceptueel, een taak wraps een van deze acties en houdt de voortgang van de uitvoering met een set van apparaten, die wordt gedefinieerd door een apparaat twin query.  Een back-end-app kan bijvoorbeeld een taak gebruiken om een rebootmethode op te roepen op 10.000 apparaten, die is opgegeven door een dubbele query op het apparaat en die op een toekomstig tijdstip is gepland. Die toepassing kan vervolgens de voortgang bijhouden terwijl elk van deze apparaten de rebootmethode ontvangt en uitvoert.

Meer informatie over elk van deze mogelijkheden vindt u in deze artikelen:

* Apparaattweeling en eigenschappen: [Aan de slag met apparaattweelingen](iot-hub-node-node-twin-getstarted.md) en [zelfstudie: dubbele eigenschappen van het apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IoT Hub-handleiding voor ontwikkelaars - directe methoden](iot-hub-devguide-direct-methods.md) en [zelfstudie: directe methoden](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze handleiding ontdekt u hoe u:

* Maak een Node.js gesimuleerde apparaat app die een directe methode heeft, die **lockDoor**, die kan worden opgeroepen door de oplossing back-end mogelijk te maken.

* Maak een Node.js-console-app die de **direct-methode lockDoor** aanroept in de gesimuleerde apparaat-app met behulp van een taak en de gewenste eigenschappen bijwerkt met behulp van een apparaattaak.

Aan het einde van deze zelfstudie heb je twee Node.js-apps:

* **simDevice.js**, die verbinding maakt met uw IoT-hub met de identiteit van het apparaat en een **lockDoor** directe methode ontvangt.

* **planningJobService.js**, die een directe methode aanroept in de gesimuleerde apparaat-app en de gewenste eigenschappen van de apparaattweeling bijwerkt met behulp van een taak.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0.x of hoger. [Bereid uw ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beschrijft hoe u Node.js installeert voor deze zelfstudie op Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Node.js-console-app die reageert op een directe methode die door de cloud wordt aangeroepen, waardoor een gesimuleerde **lockDoor-methode** wordt geactiveerd.

1. Maak een nieuwe lege map genaamd **simDevice**.  Maak in de **map simDevice** een package.json-bestand met de volgende opdracht op uw opdrachtprompt.  Accepteer alle standaardwaarden:

   ```console
   npm init
   ```

2. Voer op uw opdrachtprompt in de **map simDevice** de volgende opdracht uit om het **SDK-pakket azure-iot-device** Device en **het Azure-iot-device-mqtt--pakket** te installeren:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Maak met behulp van een teksteditor een nieuw **simDevice.js-bestand** in de **map simDevice.**

4. Voeg aan het begin van het **simDevice.js-bestand** de volgende 'require'-instructies toe:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Voeg een **connectionString**-variabele toe en gebruik deze om een **client**exemplaar te maken. Vervang `{yourDeviceConnectionString}` de tijdelijke aanduidingswaarde door de tekenreeks van de apparaatverbinding die u eerder hebt gekopieerd.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Voeg de volgende functie toe om de **lockDoor-methode** te verwerken.

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Voeg de volgende code toe om de handler voor de **lockDoor-methode** te registreren.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Sla het **simDevice.js-bestand op** en sluit deze.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële back-off), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Taken plannen voor het aanroepen van een directe methode en het bijwerken van de eigenschappen van een apparaattweeling

In deze sectie maakt u een Node.js-console-app die een externe **lockDoor** op een apparaat initieert met behulp van een directe methode en de eigenschappen van de apparaattweeling bijwerkt.

1. Maak een nieuwe lege map genaamd **scheduleJobService**.  Maak in de map **ScheduleJobService** een package.json-bestand met de volgende opdracht bij de opdrachtprompt.  Accepteer alle standaardwaarden:

    ```console
    npm init
    ```

2. Voer op uw opdrachtprompt in de map **ScheduleJobService** de volgende opdracht uit om het **Azure-iothub Device SDK-pakket** en **het Azure-iot-device-mqtt--pakket** te installeren:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Maak met behulp van een teksteditor een nieuw **bestand Met JobService.js** in de map **ScheduleJobService.**

4. Voeg aan het begin van het **bestand ScheduleJobService.js** de volgende 'require'-instructies toe:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Voeg de volgende variabele declaratie toe. Vervang `{iothubconnectionstring}` de tijdelijke aanduidingswaarde door de waarde die u hebt gekopieerd in [De verbindingstekenreeks voor de IoT-hub opbrengen](#get-the-iot-hub-connection-string). Als u een ander apparaat dan **myDeviceId**hebt geregistreerd, moet u het wijzigen in de queryvoorwaarde.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Voeg de volgende functie toe die wordt gebruikt om de uitvoering van de taak te controleren:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Voeg de volgende code toe om de taak te plannen die de apparaatmethode aanroept:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Voeg de volgende code toe om de taak te plannen om de apparaattweeling bij te werken:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Sla het **bestand scheduleJobService.js op** en sluit deze.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdrachtprompt in de **map simDevice** de volgende opdracht uit om te beginnen met luisteren naar de directe methode voor opnieuw opstarten.

    ```console
    node simDevice.js
    ```

2. Voer bij de opdrachtprompt in de map **ScheduleJobService** de volgende opdracht uit om de taken te activeren om de deur te vergrendelen en de tweeling bij te werken

    ```console
    node scheduleJobService.js
    ```

3. U ziet het apparaat antwoord op de directe methode en de taakstatus in de console.

   Het volgende toont het apparaatantwoord op de directe methode:

   ![Gesimuleerde uitvoer van apparaat-apps](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Het volgende toont de taak voor het plannen van de service voor de directe methode en de dubbele update van het apparaat en de taken die worden voltooid:

   ![De gesimuleerde apparaat-app uitvoeren](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een taak gebruikt om een directe methode te plannen voor een apparaat en de update van de eigenschappen van de apparaattweeling.

Zie [Zelfstudie: Hoe maak je een firmware-update](tutorial-firmware-update.md)voor het beheren van iot-hub en apparaatbeheer.

Zie Aan de slag met [Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)om verder te gaan met IoT Hub.

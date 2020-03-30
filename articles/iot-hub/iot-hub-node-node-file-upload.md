---
title: Bestanden uploaden van apparaten naar Azure IoT Hub met knooppunt | Microsoft Documenten
description: Bestanden uploaden van een apparaat naar de cloud met Azure IoT-apparaat SDK voor Node.js. Geüploade bestanden worden opgeslagen in een Azure-opslagblobcontainer.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: db3da5ff2d7e8b6fa493f5338fac93df0d1a7fe2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110909"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Bestanden uploaden vanaf uw apparaat naar de cloud met IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Deze zelfstudie bouwt voort op de code in de [zelfstudie Cloud-to-device](iot-hub-node-node-c2d.md) verzenden met IoT Hub om u te laten zien hoe u de [mogelijkheden voor het uploaden van bestanden van IoT Hub](iot-hub-devguide-file-upload.md) gebruiken om een bestand te uploaden naar Azure [blob-opslag.](../storage/index.yml) In deze zelfstudie leert u het volgende:

* Zorg veilig voor een apparaat met een Azure blob URI voor het uploaden van een bestand.

* Gebruik de meldingen voor het uploaden van IoT Hub-bestanden om de verwerking van het bestand in de back-end van uw app te activeren.

De [quickstart van telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md) toont de basisfunctionaliteit van IoT-hub voor apparaat naar cloud. In sommige scenario's u de gegevens die uw apparaten verzenden echter niet eenvoudig in kaart brengen in de relatief kleine device-to-cloudberichten die IoT Hub accepteert. Bijvoorbeeld:

* Grote bestanden die afbeeldingen bevatten
* Video's
* Trillingsgegevens bemonsterd op hoge frequentie
* Een vorm van vooraf verwerkte gegevens.

Deze bestanden worden meestal batch verwerkt in de cloud met behulp van hulpprogramma's zoals [Azure Data Factory](../data-factory/introduction.md) of de [Hadoop](../hdinsight/index.yml) stack. Wanneer u bestanden vanaf een apparaat moet up-landen, u nog steeds de beveiliging en betrouwbaarheid van IoT Hub gebruiken.

Aan het einde van deze zelfstudie voer je twee Node.js-console-apps uit:

* **SimulatedDevice.js**, dat een bestand uploadt naar opslag met behulp van een SAS URI die wordt geleverd door uw IoT-hub.

* **ReadFileUploadNotification.js**, dat meldingen van het uploaden van bestanden ontvangt van uw IoT-hub.

> [!NOTE]
> IoT Hub ondersteunt veel apparaatplatforms en -talen (waaronder C, .NET, Javascript, Python en Java) via Azure IoT-apparaat SDKs. Raadpleeg het [Azure IoT Developer Center] voor stapsgewijze instructies over het verbinden van uw apparaat met Azure IoT Hub.

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0.x of hoger. [Bereid uw ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beschrijft hoe u Node.js installeert voor deze zelfstudie op Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie maakt u de apparaat-app om een bestand te uploaden naar de IoT-hub.

1. Maak een lege map met de naam ```simulateddevice```.  Maak in de map ```simulateddevice``` een package.json-bestand door achter de opdrachtprompt de volgende opdracht op te geven.  Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. In de opdrachtprompt in de map ```simulateddevice``` voert u de volgende opdracht uit om het **azure-iot-device** Device SDK-pakket en het **azure-iot-device-mqtt**-pakket te installeren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met een teksteditor een **SimulatedDevice.js**-bestand in de map ```simulateddevice```.

4. Voeg de volgende ```require``` instructies toe aan het begin van het bestand **SimulatedDevice.js**:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Voeg een `deviceconnectionstring`-variabele toe en gebruik deze om een **client**exemplaar te maken.  Vervang `{deviceconnectionstring}` de naam van het apparaat dat u hebt gemaakt in de sectie *Een IoT-hub maken:*

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Omwille van de eenvoud is de verbindingstekenreeks opgenomen in de code: dit is geen aanbevolen praktijk en afhankelijk van uw use-case en architectuur u veiligere manieren overwegen om dit geheim op te slaan.

6. Voeg de volgende code toe om de client te verbinden:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Maak een callback en gebruik de **functie uploadToBlob** om het bestand te uploaden.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Sla het bestand **SimulatedDevice.js** op en sluit het.

9. Kopieer een afbeeldingsbestand naar `simulateddevice` de `myimage.png`map en wijzig de naam .

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om meldingen van bestandsuploadmeldingen te ontvangen van de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-node.md) Als u meldingen voor het uploaden van bestanden wilt ontvangen, heeft uw service de machtiging **voor verbinding met** de service nodig. Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Een melding voor het uploaden van bestanden ontvangen

In deze sectie maakt u een Node.js-console-app die meldingen van bestandsuploadmeldingen van IoT Hub ontvangt.

U de **verbindingstekenreeks iothub-eigenaar** van uw IoT-hub gebruiken om deze sectie te voltooien. U vindt de verbindingstekenreeks in de [Azure-portal](https://portal.azure.com/) op het beheer van het beheer van het **beleid voor gedeelde toegang.**

1. Maak een lege map met de naam ```fileuploadnotification```.  Maak in de map ```fileuploadnotification``` een package.json-bestand door achter de opdrachtprompt de volgende opdracht op te geven.  Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij de ```fileuploadnotification``` opdrachtprompt in de map de volgende opdracht uit om het **SDK-pakket azure-iothub** te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met behulp van een teksteditor een `fileuploadnotification` **Bestand FileUploadNotification.js** in de map.

4. Voeg de `require` volgende instructies toe aan het begin van het bestand **FileUploadNotification.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Voeg een `iothubconnectionstring`-variabele toe en gebruik deze om een **client**exemplaar te maken.  Vervang `{iothubconnectionstring}` de tijdelijke aanduidingswaarde door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Omwille van de eenvoud is de verbindingstekenreeks opgenomen in de code: dit is geen aanbevolen praktijk en afhankelijk van uw use-case en architectuur u veiligere manieren overwegen om dit geheim op te slaan.

6. Voeg de volgende code toe om de client te verbinden:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Open de client en gebruik de functie **getFileNotificationReceiver** om statusupdates te ontvangen.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Sla het bestand **FileUploadNotification.js op** en sluit deze.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

Voer bij een `fileuploadnotification` opdrachtprompt in de map de volgende opdracht uit:

```cmd/sh
node FileUploadNotification.js
```

Voer bij een `simulateddevice` opdrachtprompt in de map de volgende opdracht uit:

```cmd/sh
node SimulatedDevice.js
```

In de volgende schermafbeelding wordt de uitvoer van de **app SimulatedDevice** weergegeven:

![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-node-node-file-upload/simulated-device.png)

In de volgende schermafbeelding wordt de uitvoer van de **fileuploadnotification-app** weergegeven:

![Uitvoer van de app lezen-bestand-upload-melding](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

U de portal gebruiken om het geüploade bestand weer te geven in de opslagcontainer die u hebt geconfigureerd:

![Geüpload bestand](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de mogelijkheden voor het uploaden van bestanden van IoT Hub gebruiken om het uploaden van bestanden vanaf apparaten te vereenvoudigen. Met de volgende artikelen u de functies en scenario's van de IoT-hub blijven verkennen:

* [Een IoT-hub programmatisch maken](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK’s voor Azure IoT](iot-hub-devguide-sdks.md)

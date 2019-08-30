---
title: Bestanden van apparaten uploaden naar Azure IoT Hub met het knoop punt | Microsoft Docs
description: Het uploaden van bestanden van een apparaat naar de Cloud met Azure IoT Device SDK voor node. js. Geüploade bestanden worden opgeslagen in een Azure Storage-BLOB-container.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 8747111921df494b8d5618dc8d6ece99fa821e47
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147635"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Bestanden van uw apparaat uploaden naar de Cloud met IoT Hub (node. js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In deze zelf studie wordt gebruikgemaakt van de code in de IoT Hub zelf studie [Cloud-naar-apparaat-berichten verzenden](iot-hub-node-node-c2d.md) om u te laten zien hoe u de functies voor het [uploaden van bestanden van IOT hub](iot-hub-devguide-file-upload.md) kunt gebruiken om een bestand te uploaden naar [Azure Blob Storage](../storage/index.yml). In deze zelfstudie leert u het volgende:

* Een apparaat veilig voorzien van een Azure Blob-URI voor het uploaden van een bestand.

* Gebruik de IoT Hub bestands upload meldingen om de verwerking van het bestand in de back-end van de app te activeren.

Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-node.md) Quick Start toont de basis functionaliteit voor het uitwisselen van apparaten van IOT hub. In sommige gevallen kunt u de gegevens die uw apparaten verzenden echter niet eenvoudig toewijzen aan de relatief kleine apparaat-naar-Cloud-berichten die IoT Hub accepteren. Bijvoorbeeld:

* Grote bestanden die installatie kopieën bevatten
* Video's
* Bemonsterde trillings gegevens met hoge frequentie
* Een vorm van vooraf verwerkte gegevens.

Deze bestanden worden meestal batch verwerkt in de Cloud met behulp van hulpprogram ma's als [Azure Data Factory](../data-factory/introduction.md) of de [Hadoop](../hdinsight/index.yml) -stack. Wanneer u bestanden van een apparaat nodig hebt, kunt u nog steeds gebruikmaken van de beveiliging en betrouw baarheid van IoT Hub.

Aan het einde van deze zelf studie voert u twee node. js-console-apps uit:

* **SimulatedDevice. js**, dat een bestand uploadt naar opslag met behulp van een SAS-URI die wordt meegeleverd door uw IOT-hub.

* **ReadFileUploadNotification. js**, waarmee meldingen over het uploaden van bestanden van uw IOT-hub worden ontvangen.

> [!NOTE]
> IoT Hub ondersteunt veel platformen en talen (waaronder C, .NET, java script, python en Java) via Azure IoT-apparaat-Sdk's. Raadpleeg het [Azure IoT-ontwikkelaars centrum] voor stapsgewijze instructies voor het verbinden van uw apparaat met Azure IoT Hub.

## <a name="prerequisites"></a>Vereisten

* Node. js versie 10.0. x of hoger. [Uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) hierin wordt beschreven hoe u node. js installeert voor deze zelf studie op Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Een bestand uploaden vanuit een apparaat-app

In deze sectie maakt u de app apparaat om een bestand te uploaden naar IoT hub.

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

5. Voeg een `deviceconnectionstring`-variabele toe en gebruik deze om een **client**exemplaar te maken.  Vervang `{deviceconnectionstring}` door de naam van het apparaat dat u hebt gemaakt in de sectie *een IOT hub maken* :

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Ter vereenvoudiging van de connection string is opgenomen in de code: dit is geen aanbevolen procedure en afhankelijk van uw gebruiks-en architectuur kunt u overwegen om dit geheim veilig te bewaren.

6. Voeg de volgende code toe om verbinding te maken met de client:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Maak een call back en gebruik de functie **uploadToBlob** om het bestand te uploaden.

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

9. Kopieer een afbeeldings bestand naar `simulateddevice` de map en `myimage.png`Wijzig de naam ervan.

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om meldings berichten over het uploaden van bestanden te ontvangen van de IoT-hub die u hebt gemaakt in telemetrie [van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-node.md). Als u meldings berichten over het uploaden van bestanden wilt ontvangen, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Een melding over het uploaden van een bestand ontvangen

In deze sectie maakt u een node. JS-Console-app die berichten over het uploaden van bestanden van IoT Hub ontvangt.

U kunt de **iothubowner** -Connection String van uw IOT hub gebruiken om deze sectie te volt ooien. U vindt de connection string in de [Azure Portal](https://portal.azure.com/) op de Blade **gedeelde toegangs beleid** .

1. Maak een lege map met de naam ```fileuploadnotification```.  Maak in de map ```fileuploadnotification``` een package.json-bestand door achter de opdrachtprompt de volgende opdracht op te geven.  Accepteer alle standaardwaarden:

    ```cmd/sh
    npm init
    ```

2. Voer bij de opdracht prompt in ```fileuploadnotification``` de map de volgende opdracht uit om het **Azure-iothub SDK-** pakket te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met behulp van een tekst editor een **FileUploadNotification. js** - `fileuploadnotification` bestand in de map.

4. Voeg de volgende `require` -instructies toe aan het begin van het bestand **FileUploadNotification. js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Voeg een `iothubconnectionstring`-variabele toe en gebruik deze om een **client**exemplaar te maken.  Vervang de waarde van de tijdelijkeaanduidingdoordeIOThub-ConnectionStringdieueerderhebtgekopieerdindeIOThub-ConnectionStringophalen:`{iothubconnectionstring}` [](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Ter vereenvoudiging van de connection string is opgenomen in de code: dit is geen aanbevolen procedure en afhankelijk van uw gebruiks-en architectuur kunt u overwegen om dit geheim veilig te bewaren.

6. Voeg de volgende code toe om verbinding te maken met de client:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Open de-client en gebruik de functie **getFileNotificationReceiver** om status updates te ontvangen.

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

8. Sla het bestand **FileUploadNotification. js** op en sluit het.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

Voer de volgende opdracht uit vanaf `fileuploadnotification` een opdracht prompt in de map:

```cmd/sh
node FileUploadNotification.js
```

Voer de volgende opdracht uit vanaf `simulateddevice` een opdracht prompt in de map:

```cmd/sh
node SimulatedDevice.js
```

Op de volgende scherm afbeelding ziet u de uitvoer van de **SimulatedDevice** -app:

![Uitvoer van gesimuleerde apparaat-app](./media/iot-hub-node-node-file-upload/simulated-device.png)

Op de volgende scherm afbeelding ziet u de uitvoer van de **FileUploadNotification** -app:

![Uitvoer van een read-file-upload-meldings-app](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

U kunt de portal gebruiken om het geüploade bestand weer te geven in de opslag container die u hebt geconfigureerd:

![Bestand geüpload](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de functies voor het uploaden van bestanden van IoT Hub kunt gebruiken om Bestands uploads van apparaten te vereenvoudigen. U kunt IoT hub-functies en-scenario's blijven verkennen met de volgende artikelen:

* [Een IoT-hub maken via een programma](iot-hub-rm-template-powershell.md)

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

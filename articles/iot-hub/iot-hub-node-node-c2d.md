---
title: Cloud-to-device-berichten met Azure IoT Hub (Knooppunt) | Microsoft Documenten
description: Cloud-to-device-berichten verzenden naar een apparaat vanaf een Azure IoT-hub met behulp van de Azure IoT SDK's voor Node.js. U wijzigt een gesimuleerde apparaat-app om berichten van cloud naar apparaat te ontvangen en wijzigt een back-end-app om de berichten van cloud naar apparaat te verzenden.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 8071ddbc5f6073598daf0a08d359ccd19ccd1e4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110803"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Cloud-naar-device-berichten verzenden met IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Met [Quickstart telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md) ziet u hoe u een IoT-hub maakt, een apparaatidentiteit indient en een gesimuleerde apparaat-app codet die apparaat-naar-cloudberichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelfstudie bouwt voort op [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-node.md) Het laat je zien hoe je:

* Stuur vanuit uw back-end van oplossing cloud-naar-device-berichten naar één apparaat via IoT Hub.
* Ontvang berichten van cloud naar apparaat op een apparaat.
* Vraag vanaf uw oplossing een bevestiging van de levering *(feedback)* aan voor berichten die vanuit IoT Hub naar een apparaat worden verzonden.

Meer informatie over berichten van cloud tot apparaat vindt u in de [handleiding voor IoT Hub-ontwikkelaars.](iot-hub-devguide-messaging.md)

Aan het einde van deze zelfstudie voert u twee Node.js-console-apps uit:

* **SimulatedDevice**, een aangepaste versie van de app gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub,](quickstart-send-telemetry-node.md)die verbinding maakt met uw IoT-hub en berichten van cloud naar apparaat ontvangt.

* **SendCloudToDeviceMessage**, dat een cloud-naar-apparaatbericht naar de gesimuleerde apparaat-app stuurt via IoT Hub, en vervolgens de leveringsbevestiging ontvangt.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel apparaatplatforms en -talen (waaronder C, Java, Python en Javascript) via Azure IoT-apparaat SDK's. Zie het [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot)voor stapsgewijze instructies over het verbinden van uw apparaat met de code van deze zelfstudie en in het algemeen met Azure IoT Hub.
>

## <a name="prerequisites"></a>Vereisten

* Node.js versie 10.0.x of hoger. [Bereid uw ontwikkelomgeving](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) beschrijft hoe u Node.js installeert voor deze zelfstudie op Windows of Linux.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie wijzigt u de gesimuleerde apparaat-app die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md) om cloud-to-device-berichten van de IoT-hub te ontvangen.

1. Open het bestand **SimulatedDevice.js** met een teksteditor. Dit bestand bevindt zich in de **iot-hub\Quickstarts\simulated-device** map off van de hoofdmap van de Node.js-voorbeeldcode die u hebt gedownload in de [telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md) snelstart.

2. Registreer een handler bij de apparaatclient om berichten te ontvangen die zijn verzonden vanuit IoT Hub. Voeg de `client.on` oproep toe aan net na de lijn waarmee de apparaatclient wordt gemaakt zoals in het volgende fragment:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    In dit voorbeeld roept het apparaat de **volledige** functie op om IoT Hub te laten weten dat het bericht is verwerkt. De oproep om te **voltooien** is niet vereist als u MQTT-transport gebruikt en kan worden weggelaten. Dit is vereist voor HTTPS en AMQP.
  
   > [!NOTE]
   > Als u HTTPS gebruikt in plaats van MQTT of AMQP als transport, controleert de **instantie DeviceClient** zelden op berichten van IoT Hub (minder dan elke 25 minuten). Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide-messaging.md)voor meer informatie over de verschillen tussen MQTT-, AMQP- en HTTPS-ondersteuning en IoT Hub-beperking.
   >

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om cloud-naar-device-berichten te verzenden via de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-node.md) Als u berichten van cloud naar apparaat wilt verzenden, heeft uw service de service-machtiging nodig om verbinding **te maken.** Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een cloud-naar-apparaatbericht verzenden

In deze sectie maakt u een Node.js-console-app die cloud-to-device-berichten naar de gesimuleerde apparaat-app verzendt. U hebt de apparaat-id nodig van het apparaat dat u in de [telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md) snelstart. U hebt ook de IoT-hubverbindingstekenreeks nodig die u eerder hebt gekopieerd in [De verbindingstekenreeks voor IoT-hub .](#get-the-iot-hub-connection-string)

1. Maak een lege map met de naam **sendcloudtodevicemessage**. Maak in de map **sendcloudtodevicemessage** een package.json-bestand met de volgende opdracht op uw opdrachtprompt. Accepteer alle standaardwaarden:

    ```shell
    npm init
    ```

2. Voer op uw opdrachtprompt in de map **sendcloudtodevicemessage** de volgende opdracht uit om het **azure-iothub-pakket** te installeren:

    ```shell
    npm install azure-iothub --save
    ```

3. Maak met behulp van een teksteditor een **SendCloudToDeviceMessage.js-bestand** in de map **sendcloudtodevicemessage.**

4. Voeg de `require` volgende instructies toe aan het begin van het bestand **SendCloudToDeviceMessage.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Voeg de volgende code toe aan het bestand **SendCloudToDeviceMessage.js.** Vervang de tijdelijke aanduidingswaarden '{iot-hub-verbinding}' en {device id}door de reeks IoT-hubverbinding en apparaat-id die u eerder hebt opgemerkt:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Voeg de volgende functie toe om de bewerkingsresultaten aan de console af te drukken:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Voeg de volgende functie toe om feedbackberichten voor bezorging op de console af te drukken:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Voeg de volgende code toe om een bericht naar uw apparaat te verzenden en het feedbackbericht af te handelen wanneer het apparaat het bericht van cloud-to-device erkent:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Het bestand **SendCloudToDeviceMessage.js** opslaan en sluiten.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdrachtprompt in de map **gesimuleerd apparaat** de volgende opdracht uit om telemetrie naar IoT Hub te verzenden en naar berichten van cloud naar apparaat te luisteren:

    ```shell
    node SimulatedDevice.js
    ```

    ![De gesimuleerde apparaat-app uitvoeren](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Voer bij een opdrachtprompt in de map **sendcloudtodevicemessage** de volgende opdracht uit om een cloud-to-device-bericht te verzenden en te wachten op de bevestigingsfeedback:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![De app uitvoeren om de opdracht cloud-to-device te verzenden](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Voor de eenvoud voert deze zelfstudie geen beleid voor nieuwe try's uit. In productiecode moet u opnieuw proberenbeleid implementeren (zoals exponentiële back-off), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u berichten van cloud naar apparaat verzenden en ontvangen.

Zie [Azure IoT Remote Monitoring solution accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/)voor voorbeelden van complete end-to-end oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.

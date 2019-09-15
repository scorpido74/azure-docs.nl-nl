---
title: Cloud-naar-apparaat-berichten met Azure IoT Hub (python) | Microsoft Docs
description: Cloud-naar-apparaat-berichten verzenden naar een apparaat vanuit een Azure IoT-hub met behulp van de Azure IoT Sdk's voor python. U wijzigt een gesimuleerde apparaat-app om Cloud-naar-apparaat-berichten te ontvangen en een back-end-app te wijzigen om de Cloud-naar-apparaat-berichten te verzenden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001953"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Cloud-naar-apparaat-berichten verzenden met IoT Hub (python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die zorgt voor betrouw bare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing. Het [verzenden van telemetrie van een apparaat naar een IOT hub](quickstart-send-telemetry-python.md) Quick Start laat zien hoe u een IOT-hub kunt maken, een apparaat-id kunt inrichten en een gesimuleerde apparaat-app kunt coderen die apparaat-naar-Cloud-berichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelf studie is gebaseerd op het [verzenden van telemetrie van een apparaat naar een IOT-hub](quickstart-send-telemetry-python.md). U ziet hoe u het volgende kunt doen:

* Via de back-end van uw oplossing kunt u Cloud-naar-apparaat-berichten verzenden naar één apparaat via IoT Hub.

* Cloud-naar-apparaat-berichten op een apparaat ontvangen.

* Van de back-end van uw oplossing, aanvraag bezorgings bevestiging (*feedback*) aanvragen voor berichten die worden verzonden naar een apparaat vanuit IOT hub.

Meer informatie over Cloud-naar-apparaat-berichten vindt u in de [hand leiding voor IOT hub ontwikkel aars](iot-hub-devguide-messaging.md).

Aan het einde van deze zelf studie voert u twee python-console-apps uit:

* **SimulatedDevice.py**, een gewijzigde versie van de app die is gemaakt in [telemetrie verzenden van een apparaat naar een IOT-hub](quickstart-send-telemetry-python.md), die verbinding maakt met uw IOT-hub en Cloud-naar-apparaat-berichten ontvangt.

* **SendCloudToDeviceMessage.py**, waarmee een Cloud-naar-apparaat-bericht naar de gesimuleerde apparaat-app wordt verzonden via IOT hub, waarna de ontvangst bevestiging wordt ontvangen.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie maakt u een python-console-app voor het simuleren van het apparaat en het ontvangen van Cloud-naar-apparaat-berichten van de IoT-hub.

1. Maak een **SimulatedDevice.py** -bestand met behulp van een tekst editor.

2. Voeg de volgende `import` instructies en variabelen toe aan het begin van het **SimulatedDevice.py** -bestand:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Voeg de volgende code toe aan het **SimulatedDevice.py** -bestand. Vervang de tijdelijke aanduiding voor ' {deviceConnectionString} ' door het apparaat connection string voor het apparaat dat u hebt gemaakt in de Quick- [verzen ding van een apparaat naar een IOT hub](quickstart-send-telemetry-python.md) -Snelstartgids:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Voeg de volgende functie toe om ontvangen berichten af te drukken naar de-console:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Voeg de volgende code toe om de client te initialiseren en te wachten op het ontvangen van het Cloud-naar-apparaat-bericht:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Voeg de volgende hoofd functie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Sla het **SimulatedDevice.py** -bestand op en sluit het.

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

In dit artikel maakt u een back-end-service om Cloud-naar-apparaat-berichten te verzenden via de IoT-hub die u hebt gemaakt in [telemetrie van een apparaat naar een IOT-hub verzenden](quickstart-send-telemetry-python.md). Als u Cloud-naar-apparaat-berichten wilt verzenden, moet u de service **Connect** -machtiging hebben. Standaard wordt elke IoT Hub gemaakt met een gedeeld toegangs beleid met de naam **service** dat deze machtiging verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een Cloud-naar-apparaat-bericht verzenden

In deze sectie maakt u een python-console-app die Cloud-naar-apparaat-berichten naar de gesimuleerde apparaat-app verzendt. U hebt de apparaat-ID van het apparaat dat u hebt toegevoegd in de [telemetrie van een apparaat verzenden naar een IOT hub](quickstart-send-telemetry-python.md) Quick Start. U hebt ook de IoT hub-connection string nodig die u eerder hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string).

1. Maak een **SendCloudToDeviceMessage.py** -bestand met behulp van een tekst editor.

2. Voeg de volgende `import` instructies en variabelen toe aan het begin van het **SendCloudToDeviceMessage.py** -bestand:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Voeg de volgende code toe aan het **SendCloudToDeviceMessage.py** -bestand. Vervang de tijdelijke aanduidingen ' {IOT hub connection string} ' en ' {apparaat id} ' door de connection string van de IoT-hub en de apparaat-ID die u eerder hebt genoteerd:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Voeg de volgende functie toe om feedback berichten af te drukken naar de-console:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Voeg de volgende code toe om een bericht te verzenden naar uw apparaat en het feedback bericht af te handelen wanneer het apparaat het Cloud-naar-apparaat-bericht bevestigt:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Voeg de volgende hoofd functie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Sla het **SendCloudToDeviceMessage.py** -bestand op en sluit het.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Open een opdracht prompt en installeer de **Azure IOT hub Device SDK voor python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Voer bij de opdracht prompt de volgende opdracht uit om te Luis teren naar Cloud-naar-apparaat-berichten:

    ```shell
    python SimulatedDevice.py
    ```

    ![De app gesimuleerde apparaten uitvoeren](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Open een nieuwe opdracht prompt en installeer de **Azure IOT hub Service SDK voor python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Voer bij een opdracht prompt de volgende opdracht uit om een Cloud-naar-apparaat-bericht te verzenden en te wachten op de feedback van het bericht:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Voer de app uit om de Cloud-naar-apparaat-opdracht te verzenden](./media/iot-hub-python-python-c2d/send-command.png)

5. Noteer het bericht dat is ontvangen door het apparaat.

    ![Bericht ontvangen](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Cloud-naar-apparaat-berichten kunt verzenden en ontvangen.

Voor voor beelden van complete end-to-end-oplossingen die gebruikmaken van IoT Hub, raadpleegt u de [Azure IOT-oplossing voor externe controle](https://azure.microsoft.com/documentation/suites/iot-suite/).

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.

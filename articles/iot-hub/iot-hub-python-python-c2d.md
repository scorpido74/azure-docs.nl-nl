---
title: Cloud-to-device-berichten met Azure IoT Hub (Python) | Microsoft Documenten
description: Cloud-naar-device-berichten verzenden naar een apparaat vanaf een Azure IoT-hub met behulp van de Azure IoT-SDK's voor Python. U wijzigt een gesimuleerde apparaat-app om berichten van cloud naar apparaat te ontvangen en wijzigt een back-end-app om de berichten van cloud naar apparaat te verzenden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110432"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Cloud-naar-device-berichten verzenden met IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Met [Quickstart telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-python.md) ziet u hoe u een IoT-hub maakt, een apparaatidentiteit indient en een gesimuleerde apparaat-app codet die apparaat-naar-cloudberichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelfstudie bouwt voort op [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-python.md) Het laat je zien hoe je:

* Stuur vanuit uw back-end van oplossing cloud-naar-device-berichten naar één apparaat via IoT Hub.

* Ontvang berichten van cloud naar apparaat op een apparaat.

* Vraag vanaf uw oplossing een bevestiging van de levering *(feedback)* aan voor berichten die vanuit IoT Hub naar een apparaat worden verzonden.

Meer informatie over berichten van cloud tot apparaat vindt u in de [handleiding voor IoT Hub-ontwikkelaars.](iot-hub-devguide-messaging.md)

Aan het einde van deze zelfstudie voert u twee Python-console-apps uit:

* **SimulatedDevice.py**, een aangepaste versie van de app die is gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub,](quickstart-send-telemetry-python.md)die verbinding maakt met uw IoT-hub en berichten van cloud naar apparaat ontvangt.

* **SendCloudToDeviceMessage.py**, dat een cloud-to-device-bericht naar de gesimuleerde apparaat-app stuurt via IoT Hub en vervolgens de leveringsbevestiging ontvangt.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie maakt u een Python-console-app om het apparaat te simuleren en berichten van cloud naar apparaat te ontvangen vanaf de IoT-hub.

1. Maak met behulp van een teksteditor een **SimulatedDevice.py** bestand.

2. Voeg aan `import` het begin van het **SimulatedDevice.py** bestand de volgende instructies en variabelen toe:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Voeg de volgende code toe aan **SimulatedDevice.py** bestand. Vervang de tijdelijke aanduiding '{deviceConnectionString}' door de tekenreeks voor apparaatverbinding voor het apparaat dat u hebt gemaakt in de [quickstart Telemetrie verzenden van een apparaat naar een IoT-hub:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Voeg de volgende functie toe om ontvangen berichten af te drukken op de console:

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

5. Voeg de volgende code toe om de client te initialiseren en wacht tot u het bericht van cloud-naar-apparaat ontvangt:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Voeg de volgende hoofdfunctie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Sla **SimulatedDevice.py** bestand op en sluit deze af.

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om cloud-naar-device-berichten te verzenden via de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-python.md) Als u berichten van cloud naar apparaat wilt verzenden, heeft uw service de service-machtiging nodig om verbinding **te maken.** Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een cloud-naar-apparaatbericht verzenden

In deze sectie maakt u een Python-console-app die cloud-naar-apparaatberichten verzendt naar de gesimuleerde apparaat-app. U hebt de apparaat-id nodig van het apparaat dat u in de [telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-python.md) snelstart. U hebt ook de de reeks IoT-hubverbindingen nodig die u eerder hebt gekopieerd in [De verbindingstekenreeks voor iot-hub opbrengen.](#get-the-iot-hub-connection-string)

1. Maak met behulp van een teksteditor een **SendCloudToDeviceMessage.py** bestand.

2. Voeg aan `import` het begin van het **SendCloudToDeviceMessage.py** bestand de volgende instructies en variabelen toe:

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

3. Voeg de volgende code toe aan **SendCloudToDeviceMessage.py** bestand. Vervang de tijdelijke aanduidingswaarden '{iot-hub-verbinding}' en {device id}door de reeks IoT-hubverbinding en apparaat-id die u eerder hebt opgemerkt:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Voeg de volgende functie toe om feedbackberichten op de console af te drukken:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Voeg de volgende code toe om een bericht naar uw apparaat te verzenden en het feedbackbericht af te handelen wanneer het apparaat het bericht van cloud-to-device erkent:

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

6. Voeg de volgende hoofdfunctie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Sla **SendCloudToDeviceMessage.py bestand** op en sluit deze af.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Open een opdrachtprompt en installeer de **Azure IoT Hub Device SDK voor Python.**

    ```shell
    pip install azure-iothub-device-client
    ```

2. Voer bij de opdrachtprompt de volgende opdracht uit om naar berichten van cloud naar apparaat te luisteren:

    ```shell
    python SimulatedDevice.py
    ```

    ![De gesimuleerde apparaat-app uitvoeren](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Open een nieuwe opdrachtprompt en installeer de **Azure IoT Hub Service SDK voor Python.**

    ```shell
    pip install azure-iothub-service-client
    ```

4. Voer bij een opdrachtprompt de volgende opdracht uit om een cloud-naar-apparaatbericht te verzenden en te wachten op de feedback van het bericht:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![De app uitvoeren om de opdracht cloud-to-device te verzenden](./media/iot-hub-python-python-c2d/send-command.png)

5. Let op het bericht dat door het apparaat is ontvangen.

    ![Ontvangen bericht](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u berichten van cloud naar apparaat verzenden en ontvangen.

Zie [Azure IoT Remote Monitoring solution accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/)voor voorbeelden van complete end-to-end oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.

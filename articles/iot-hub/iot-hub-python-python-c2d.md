---
title: Cloud-to-device-berichten met Azure IoT Hub (Python) | Microsoft Documenten
description: Cloud-naar-device-berichten verzenden naar een apparaat vanaf een Azure IoT-hub met behulp van de Azure IoT-SDK's voor Python. U wijzigt een gesimuleerde apparaat-app om berichten van cloud naar apparaat te ontvangen en wijzigt een back-end-app om de berichten van cloud naar apparaat te verzenden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f0760f6e61904295771ba349f8101e2d6dc6afe3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759744"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Cloud-naar-device-berichten verzenden met IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Met [Quickstart telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-python.md) ziet u hoe u een IoT-hub maakt, een apparaatidentiteit indient en een gesimuleerde apparaat-app codet die apparaat-naar-cloudberichten verzendt.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Deze zelfstudie bouwt voort op [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-python.md) Het laat je zien hoe je:

* Stuur vanuit uw back-end van oplossing cloud-naar-device-berichten naar één apparaat via IoT Hub.

* Ontvang berichten van cloud naar apparaat op een apparaat.

Meer informatie over berichten van cloud tot apparaat vindt u in de [handleiding voor IoT Hub-ontwikkelaars.](iot-hub-devguide-messaging.md)

Aan het einde van deze zelfstudie voert u twee Python-console-apps uit:

* **SimulatedDevice.py**, een aangepaste versie van de app die is gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub,](quickstart-send-telemetry-python.md)die verbinding maakt met uw IoT-hub en berichten van cloud naar apparaat ontvangt.

* **SendCloudToDeviceMessage.py**, die cloud-to-device-berichten naar de gesimuleerde apparaat-app stuurt via IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="receive-messages-in-the-simulated-device-app"></a>Berichten ontvangen in de gesimuleerde apparaat-app

In deze sectie maakt u een Python-console-app om het apparaat te simuleren en berichten van cloud naar apparaat te ontvangen vanaf de IoT-hub.

1. Installeer de **Azure IoT Hub Device SDK voor Python**via een opdrachtprompt in uw werkmap:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Maak met een teksteditor een bestand met de naam **SimulatedDevice.py**.

1. Voeg aan `import` het begin van het **SimulatedDevice.py** bestand de volgende instructies en variabelen toe:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Voeg de volgende code toe aan **SimulatedDevice.py** bestand. Vervang `{deviceConnectionString}` de tijdelijke aanduidingswaarde door de tekenreeks van de apparaatverbinding voor het apparaat dat u hebt gemaakt in de [quickstart Telemetrie verzenden van een apparaat naar een IoT-hub:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Voeg de volgende functie toe om ontvangen berichten af te drukken op de console:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Voeg de volgende code toe om de client te initialiseren en wacht tot u het bericht van cloud-naar-apparaat ontvangt:

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
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Voeg de volgende hoofdfunctie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Sla het bestand **SimulatedDevice.py** op en sluit het.

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om cloud-naar-device-berichten te verzenden via de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-python.md) Als u berichten van cloud naar apparaat wilt verzenden, heeft uw service de service-machtiging nodig om verbinding **te maken.** Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Een cloud-naar-apparaatbericht verzenden

In deze sectie maakt u een Python-console-app die cloud-naar-apparaatberichten verzendt naar de gesimuleerde apparaat-app. U hebt de apparaat-id nodig van het apparaat dat u in de [telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-python.md) snelstart. U hebt ook de IoT-hubverbindingstekenreeks nodig die u eerder hebt gekopieerd in [De verbindingstekenreeks voor IoT-hub .](#get-the-iot-hub-connection-string)

1. Open in uw werkmap een opdrachtprompt en installeer de **Azure IoT Hub Service SDK voor Python.**

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Maak met een teksteditor een bestand met de naam **SendCloudToDeviceMessage.py**.

1. Voeg aan `import` het begin van het **SendCloudToDeviceMessage.py** bestand de volgende instructies en variabelen toe:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Voeg de volgende code toe aan **SendCloudToDeviceMessage.py** bestand. Vervang `{iot hub connection string}` de `{device id}` waardevan de tijdelijke aanduiding en de tijdelijke aanduiding door de Reeks voor de Verbinding van de IoT-hub en de apparaat-id die u eerder hebt opgemerkt:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Voeg de volgende code toe om berichten naar uw apparaat te verzenden:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Voeg de volgende hoofdfunctie toe:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Sla **SendCloudToDeviceMessage.py bestand** op en sluit deze af.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdrachtprompt in uw werkmap de volgende opdracht uit om naar berichten van cloud naar apparaat te luisteren:

    ```shell
    python SimulatedDevice.py
    ```

    ![De gesimuleerde apparaat-app uitvoeren](./media/iot-hub-python-python-c2d/device-1.png)

1. Open een nieuwe opdrachtprompt in uw werkmap en voer de volgende opdracht uit om berichten van cloud naar apparaat te verzenden:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![De app uitvoeren om de opdracht cloud-to-device te verzenden](./media/iot-hub-python-python-c2d/service.png)

1. Let op de berichten die door het apparaat zijn ontvangen.

    ![Ontvangen bericht](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u berichten van cloud naar apparaat verzenden en ontvangen.

Zie [Azure IoT Remote Monitoring solution accelerator](https://azure.microsoft.com/documentation/suites/iot-suite/)voor voorbeelden van complete end-to-end oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.

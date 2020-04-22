---
title: Aan de slag met Azure IoT Hub-apparaattweeling (Python) | Microsoft Documenten
description: Azure IoT Hub-apparaattweelingen gebruiken om tags toe te voegen en vervolgens een IoT Hub-query te gebruiken. U gebruikt de Azure IoT-SDK's voor Python om de gesimuleerde apparaat-app en een service-app te implementeren die de tags toevoegt en de IoT Hub-query uitvoert.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 0bb1371de827fbb68afd5d114f49afa4acec0deb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759736"
---
# <a name="get-started-with-device-twins-python"></a>Aan de slag met apparaattweelingen (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Aan het einde van deze zelfstudie heb je twee Python-console-apps:

* **AddTagsAndQuery.py**, een Python back-end app, die tags en query's apparaat tweelingen toevoegt.

* **ReportConnectivity.py**, een Python-app, die een apparaat simuleert dat verbinding maakt met uw IoT-hub met de eerder gemaakte apparaatidentiteit en de verbindingsconditie rapporteert.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Python-console-app die locatiemetagegevens toevoegt aan de apparaattweeling die is gekoppeld aan uw **{Device ID}**. Vervolgens wordt het apparaat twins opgevraagd die zijn opgeslagen in de IoT-hub en selecteert u de apparaten in Redmond en vervolgens de apparaten die een mobiele verbinding melden.

1. Open in uw werkmap een opdrachtprompt en installeer de **Azure IoT Hub Service SDK voor Python.**

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Maak met behulp van **AddTagsAndQuery.py** een teksteditor een nieuw AddTagsAndQuery.py-bestand.

3. Voeg de volgende code toe om de vereiste modules uit de service-SDK te importeren:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Voeg de volgende code toe. Vervang `[IoTHub Connection String]` de IoT-hubverbindingstekenreeks die u hebt gekopieerd in [De verbindingstekenreeks voor iot-hub opbrengen](#get-the-iot-hub-connection-string). Vervang `[Device Id]` de apparaat-id die u hebt geregistreerd in [Registreer een nieuw apparaat in de IoT-hub.](#register-a-new-device-in-the-iot-hub)
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Voeg de volgende code toe aan het **AddTagsAndQuery.py** bestand:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    Het **IoTHubRegistryManager-object** onthult alle methoden die nodig zijn om te communiceren met apparaattweelingen van de service. De code initialiseert eerst het **IoTHubRegistryManager-object,** werkt vervolgens de apparaattweeling bij **voor DEVICE_ID**en voert uiteindelijk twee query's uit. De eerste selecteert alleen het apparaat tweeling van apparaten in de **Redmond43** plant, en de tweede verfijnt de query om alleen de apparaten die ook zijn aangesloten via een mobiel netwerk te selecteren.

6. Voeg aan het einde van **AddTagsAndQuery.py** de volgende code toe om de **functie iothub_service_sample_run** te implementeren:

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Voer de toepassing uit met:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    U ziet één apparaat in de resultaten voor de query waarin wordt gevraagd naar alle apparaten in **Redmond43** en geen voor de query die de resultaten beperkt tot apparaten die een mobiel netwerk gebruiken.

    ![eerste query met alle apparaten in Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

In de volgende sectie maakt u een apparaat-app die de verbindingsinformatie rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een Python-console-app die verbinding maakt met uw hub als uw **{Device ID}** en vervolgens de gerapporteerde eigenschappen van de apparaattweeling bijwerkt om de informatie te bevatten die is verbonden met behulp van een mobiel netwerk.

1. Installeer de **Azure IoT Hub Device SDK voor Python**via een opdrachtprompt in uw werkmap:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak met een teksteditor een nieuw **ReportConnectivity.py** bestand.

3. Voeg de volgende code toe om de vereiste modules uit de SDK van het apparaat te importeren:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Voeg de volgende code toe. Vervang `[IoTHub Device Connection String]` de tijdelijke aanduidingswaarde door de tekenreeks van de apparaatverbinding die u hebt gekopieerd in [Een nieuw apparaat registreren in de IoT-hub.](#register-a-new-device-in-the-iot-hub)

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Voeg de volgende code toe aan het **ReportConnectivity.py** bestand om de functionaliteit van de apparaattweeling te implementeren:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    Het **iothubmoduleclientobject** onthult alle methoden die u nodig hebt om vanaf het apparaat met apparaattweelingen te communiceren. De vorige code, nadat het **iothubmoduleclientobject** is geïnitialiseerde, haalt de apparaattweeling voor uw apparaat op en werkt de gerapporteerde eigenschap bij met de verbindingsinformatie.

6. Voeg aan het einde van **ReportConnectivity.py** de volgende code toe om de **functie iothub_client_sample_run** te implementeren:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Voer de apparaat-app uit:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    U ziet de bevestiging dat de dubbele gerapporteerde eigenschappen van het apparaat zijn bijgewerkt.

    ![gerapporteerde eigenschappen bijwerken vanuit apparaat-app](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Nu het apparaat zijn connectiviteitsinformatie heeft gerapporteerd, moet het in beide query's worden weergegeven. Ga terug en voer de query's opnieuw uit:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Deze keer moet uw **{Device ID}** worden weergegeven in beide queryresultaten.

    ![tweede query in de service-app](./media/iot-hub-python-twin-getstarted/service-2.png)

    In de app voor uw apparaat ziet u een bevestiging dat de gewenste eigenschappen twin patch is ontvangen die door de service-app is verzonden.

    ![de gewenste eigenschappen ontvangen op de apparaat-app](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt apparaatmetagegevens toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om informatie over apparaatconnectiviteit in de apparaattweeling te rapporteren. U hebt ook geleerd hoe u deze informatie opvragen met behulp van het register.

Gebruik de volgende bronnen om te leren hoe u:

* Verstuur telemetrie vanaf apparaten met de [zelfstudie Aan de slag met IoT Hub.](quickstart-send-telemetry-python.md)

* Configureer apparaten met de gewenste eigenschappen van Device Twin met de gewenste eigenschappen gebruiken om de zelfstudie [van apparaten te configureren.](tutorial-device-twins.md)

* Bedien apparaten interactief (zoals het inschakelen van een ventilator vanuit een door de gebruiker bestuurde app), met de zelfstudie [Direct methoden](quickstart-control-device-python.md) gebruiken.

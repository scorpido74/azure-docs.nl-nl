---
title: Aan de slag met Azure IoT Hub Device apparaatdubbels (python) | Microsoft Docs
description: Azure IoT Hub Device apparaatdubbels gebruiken om labels toe te voegen en vervolgens een IoT Hub query te gebruiken. U gebruikt de Azure IoT Sdk's voor python voor het implementeren van de gesimuleerde apparaat-app en een service-app waarmee de tags worden toegevoegd en de IoT Hub query wordt uitgevoerd.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 729371ede8df3dc21f70bc5742de52f2be874507
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029997"
---
# <a name="get-started-with-device-twins-python"></a>Aan de slag met Device apparaatdubbels (python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Aan het einde van deze zelf studie hebt u twee python-console-apps:

* **AddTagsAndQuery.py**, een python back-end-app, waarmee labels en query's apparaat apparaatdubbels worden toegevoegd.

* **ReportConnectivity.py**, een python-app, die een apparaat simuleert dat verbinding maakt met uw IOT-hub met de apparaat-id die u eerder hebt gemaakt, en rapporteert de connectiviteits voorwaarde.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>De IoT hub-connection string ophalen

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een python-console-app waarmee de meta gegevens van de locatie worden toegevoegd aan het apparaat dat is gekoppeld aan uw **{apparaat-id}** . Vervolgens wordt een query uitgevoerd op het apparaat apparaatdubbels dat is opgeslagen in de IoT-hub en vervolgens de apparaten selecteert die zich in Redmond bevinden. vervolgens wordt een mobiele verbinding gerapporteerd.

1. Open in uw werkmap een opdracht prompt en installeer de **Azure IOT hub Service SDK voor python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Het PIP-pakket voor Azure-iothub-service-client is momenteel alleen beschikbaar voor Windows-besturings systemen. Voor Linux/Mac OS raadpleegt u de sectie met Linux-en Mac OS-specifieke secties in de [ontwikkel omgeving voorbereiden voor python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) post.
   >

2. Maak een nieuw **AddTagsAndQuery.py** -bestand met behulp van een tekst editor.

3. Voeg de volgende code toe om de vereiste modules uit de service-SDK te importeren:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Voeg de volgende code toe. Vervang `[IoTHub Connection String]` door de IoT hub-connection string die u hebt gekopieerd in [de IOT hub-Connection String ophalen](#get-the-iot-hub-connection-string). Vervang `[Device Id]` door de apparaat-ID die u hebt geregistreerd in [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub).
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Voeg de volgende code toe aan het **AddTagsAndQuery.py** -bestand:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    Het **register** object bevat alle methoden die nodig zijn om te communiceren met apparaatdubbels van de service. De code initialiseert eerst het **register** object, werkt het apparaat vervolgens bij voor **deviceId**en voert ten slotte twee query's uit. De eerste selecteert alleen het apparaat apparaatdubbels van apparaten die zich in de **Redmond43** -installatie bevinden en de tweede verfijnt de query om alleen de apparaten te selecteren die ook zijn verbonden via een mobiel netwerk.

6. Voeg de volgende code toe aan het einde van **AddTagsAndQuery.py** om de functie **iothub_service_sample_run** te implementeren:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Voer de toepassing uit met:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    U ziet één apparaat in de resultaten voor de query die vraagt naar alle apparaten in **Redmond43** en geen voor de query waarmee de resultaten worden beperkt tot apparaten die gebruikmaken van een mobiel netwerk.

    ![eerste query waarin alle apparaten in Redmond worden weer gegeven](./media/iot-hub-python-twin-getstarted/service-1.png)

In de volgende sectie maakt u een apparaat-app die de connectiviteits gegevens rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een python-console-app die verbinding maakt met uw hub als uw **{apparaat-id}** en vervolgens de gerapporteerde eigenschappen van het apparaat bijwerkt om de gegevens te bevatten die zijn verbonden met behulp van een mobiel netwerk.

1. Installeer in de werkmap van een opdracht prompt de **Azure IOT hub Device SDK voor python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak een nieuw **ReportConnectivity.py** -bestand met behulp van een tekst editor.

3. Voeg de volgende code toe om de vereiste modules uit de SDK van het apparaat te importeren:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Voeg de volgende code toe. Vervang de tijdelijke aanduiding voor @no__t 0 door het apparaat dat connection string u hebt gekopieerd in [een nieuw apparaat registreren in de IOT-hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Voeg de volgende code toe aan het **ReportConnectivity.py** -bestand om de apparaatdubbels-functionaliteit van het apparaat te implementeren:

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
            print ( "IoTHubClient sample stopped" )
    ```

    Het **client** object bevat alle methoden die u nodig hebt om te communiceren met apparaatdubbels van het apparaat. De vorige code, na het initialiseren van het **client** object, haalt het apparaat op voor uw apparaat en werkt de gerapporteerde eigenschap bij met de verbindings gegevens.

6. Voeg de volgende code toe aan het einde van **ReportConnectivity.py** om de functie **iothub_client_sample_run** te implementeren:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. De apparaat-app uitvoeren:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    U ziet een bevestiging dat de apparaatdubbels van het apparaat zijn bijgewerkt.

    ![apparaatdubbels bijwerken](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Nu het apparaat de verbindings gegevens heeft gerapporteerd, zou het in beide query's moeten worden weer gegeven. Ga terug en voer de query's opnieuw uit:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Deze keer dat uw **{device id}** in beide query resultaten moet worden weer gegeven.

    ![tweede query](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt meta gegevens van apparaten toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om connectiviteits gegevens van apparaten te rapporteren in het dubbele apparaat. U hebt ook geleerd hoe u deze gegevens in het REGI ster kunt opvragen.

Gebruik de volgende bronnen voor meer informatie over:

* Verzend telemetrie van apparaten met de zelf studie [aan de slag met IOT hub](quickstart-send-telemetry-python.md) .

* Configureer apparaten met behulp van de gewenste eigenschappen van het apparaat met de zelf studie [gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md) .

* Apparaten interactief beheren (bijvoorbeeld door een ventilator in te scha kelen vanuit een door de gebruiker beheerde app), met de zelf studie [directe methoden gebruiken](quickstart-control-device-python.md) .

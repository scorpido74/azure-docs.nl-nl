---
title: Aan de slag met Azure IoT Hub-apparaatbeheer (Python) | Microsoft Documenten
description: Het apparaatbeheer van IoT Hub gebruiken om een herstart van een extern apparaat te starten. U gebruikt de Azure IoT SDK voor Python om een gesimuleerde apparaatapp te implementeren die een directe methode en een service-app bevat die de directe methode aanroept.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.openlocfilehash: 6d6a50db42924d868b57cacc415246ee6990859c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110472"
---
# <a name="get-started-with-device-management-python"></a>Aan de slag met apparaatbeheer (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

In deze handleiding ontdekt u hoe u:

* Gebruik de Azure-portal om een IoT-hub te maken en een apparaatidentiteit te maken in uw IoT-hub.

* Maak een gesimuleerde apparaat-app die een directe methode bevat die dat apparaat opnieuw opstart. Directe methoden worden aangeroepen vanuit de cloud.

* Maak een Python-console-app die de directe herstartmethode aanroept in de gesimuleerde apparaat-app via uw IoT-hub.

Aan het einde van deze zelfstudie heb je twee Python-console-apps:

* **dmpatterns_getstarted_device.py**, dat verbinding maakt met uw IoT-hub met de apparaatidentiteit die eerder is gemaakt, ontvangt een directe herstartmethode, simuleert een fysieke reboot en rapporteert de tijd voor de laatste reboot.

* **dmpatterns_getstarted_service.py**, die een directe methode aanroept in de gesimuleerde apparaat-app, geeft het antwoord weer en geeft de bijgewerkte gerapporteerde eigenschappen weer.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie doet u het volgende:

* Een Python-console-app maken die reageert op een directe methode die door de cloud wordt aangeroepen

* Een apparaat opnieuw opstarten simuleren

* Gebruik de gerapporteerde eigenschappen om apparaatdubbele query's in te schakelen om apparaten te identificeren en wanneer ze voor het laatst zijn opgestart

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-apparaatpakket** te installeren:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak met een teksteditor een bestand met de naam **dmpatterns_getstarted_device.py** in uw werkmap.

3. Voeg de `import` volgende instructies toe aan het begin van het **dmpatterns_getstarted_device.py-bestand.**

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Voeg de **CONNECTION_STRING** variabele toe. Vervang `{deviceConnectionString}` de tijdelijke aanduidingswaarde door de tekenreeks van de apparaatverbinding. U hebt deze verbindingstekenreeks eerder gekopieerd in [Een nieuw apparaat registreren in de IoT-hub.](#register-a-new-device-in-the-iot-hub)  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Voeg de volgende functiecallbacks toe om de directe methode op het apparaat te implementeren.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Start de directe methode luisteraar en wacht.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Sla het **bestand van dmpatterns_getstarted_device.py op** en sluit deze.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële back-off), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Een externe herstart op het apparaat activeren met een directe methode

In deze sectie maakt u een Python-console-app die een externe reboot op een apparaat initieert met behulp van een directe methode. De app gebruikt apparaatdubbele query's om de laatste herstarttijd voor dat apparaat te ontdekken.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-hubpakket** te installeren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Maak met een teksteditor een bestand met de naam **dmpatterns_getstarted_service.py** in uw werkmap.

3. Voeg de `import` volgende instructies toe aan het begin van het **dmpatterns_getstarted_service.py-bestand.**

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Voeg de volgende variabele declaratie toe. Vervang `{IoTHubConnectionString}` de waarde van de tijdelijke aanduiding door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub .](#get-the-iot-hub-connection-string) Vervang `{deviceId}` de tijdelijke aanduidingswaarde door de apparaat-id die u hebt geregistreerd in [Registreer een nieuw apparaat in de IoT-hub.](#register-a-new-device-in-the-iot-hub)

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Voeg de volgende functie toe om de apparaatmethode aan te roepen om het doelapparaat opnieuw op te starten, vervolgens de apparaattweeling op te vragen en de laatste herstarttijd te krijgen.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Sla het **dmpatterns_getstarted_service.py-bestand op** en sluit deze.

## <a name="run-the-apps"></a>De apps uitvoeren

Je bent nu klaar om de apps uit te voeren.

1. Voer bij de opdrachtprompt de volgende opdracht uit om te beginnen met luisteren naar de directe methode voor opnieuw opstarten.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Voer bij een andere opdrachtprompt de volgende opdracht uit om de externe herstart en query te activeren voor de apparaattweeling om de laatste herstarttijd te vinden.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. U ziet de reactie van het apparaat op de directe methode in de console.

   Het volgende toont het apparaatantwoord op de directe herstartmethode:

   ![Gesimuleerde uitvoer van apparaat-apps](./media/iot-hub-python-python-device-management-get-started/device.png)

   Het volgende toont de service die de directe herstartmethode aanroept en de apparaattweeling voor status peilt:

   ![Uitvoer van de rebootservice activeren](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
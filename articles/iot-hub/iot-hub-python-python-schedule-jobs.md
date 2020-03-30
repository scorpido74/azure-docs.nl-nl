---
title: Taken plannen met Azure IoT Hub (Python) | Microsoft Documenten
description: Een Azure IoT Hub-taak plannen om een directe methode op meerdere apparaten aan te roepen. U gebruikt de Azure IoT SDK's voor Python om de gesimuleerde apparaat-apps en een service-app te implementeren om de taak uit te voeren.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527398"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Taken plannen en uitzenden (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub is een volledig beheerde service waarmee een back-end-app taken kan maken en bijhouden die miljoenen apparaten plannen en bijwerken.  Taken kunnen worden gebruikt voor de volgende acties:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

Conceptueel, een taak wraps een van deze acties en houdt de voortgang van de uitvoering met een set van apparaten, die wordt gedefinieerd door een apparaat twin query.  Een back-end-app kan bijvoorbeeld een taak gebruiken om een rebootmethode op te roepen op 10.000 apparaten, die is opgegeven door een dubbele query op het apparaat en die op een toekomstig tijdstip is gepland.  Die toepassing kan vervolgens de voortgang bijhouden terwijl elk van deze apparaten de rebootmethode ontvangt en uitvoert.

Meer informatie over elk van deze mogelijkheden vindt u in deze artikelen:

* Apparaattweeling en eigenschappen: [Aan de slag met apparaattweelingen](iot-hub-python-twin-getstarted.md) en [zelfstudie: dubbele eigenschappen van het apparaat gebruiken](tutorial-device-twins.md)

* Directe methoden: [IoT Hub-handleiding voor ontwikkelaars - directe methoden](iot-hub-devguide-direct-methods.md) en [zelfstudie: directe methoden](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

In deze handleiding ontdekt u hoe u:

* Maak een Python gesimuleerde apparaat app die een directe methode heeft, die **lockDoor**, die kan worden opgeroepen door de oplossing back-end mogelijk te maken.

* Maak een Python-console-app die de **directe lockdoor-methode** aanroept in de gesimuleerde apparaat-app met behulp van een taak en de gewenste eigenschappen bijwerkt met behulp van een apparaattaak.

Aan het einde van deze zelfstudie heb je twee Python-apps:

**simDevice.py**, die verbinding maakt met uw IoT-hub met de identiteit van het apparaat en een **lockDoor-directe** methode ontvangt.

**scheduleJobService.py**, die een directe methode aanroept in de gesimuleerde apparaat-app en de gewenste eigenschappen van de apparaattweeling bijwerkt met behulp van een taak.

> [!NOTE]
> De **Azure IoT SDK voor Python** biedt geen directe ondersteuning voor de functionaliteit van **Jobs.** In plaats daarvan biedt deze zelfstudie een alternatieve oplossing met behulp van asynchrone threads en timers. Zie de lijst **met SDK-functies serviceclient** op de azure [IoT SDK voor Python-pagina](https://github.com/Azure/azure-iot-sdk-python) voor meer updates.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Een gesimuleerde apparaattoepassing maken

In deze sectie maakt u een Python-console-app die reageert op een directe methode die door de cloud wordt aangeroepen, waardoor een gesimuleerde **lockDoor-methode** wordt geactiveerd.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-apparaatpakket** te installeren:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Maak met een teksteditor een nieuw **simDevice.py-bestand** in uw werkmap.

3. Voeg aan `import` het begin van het **simDevice.py** bestand de volgende instructies en variabelen toe. Vervang `deviceConnectionString` de verbindingstekenreeks van het apparaat dat u hierboven hebt gemaakt:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Voeg de volgende functie callback toe om de **lockDoor-methode** te verwerken:

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Voeg een andere functie callback toe om updates van apparaattweelingen af te handelen:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Voeg de volgende code toe om de handler voor de **lockDoor-methode** te registreren. Ook de `main` routine:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Sla het **simDevice.py-bestand** op en sluit deze.

> [!NOTE]
> Om de zaken niet nodeloos ingewikkeld te maken, is in deze handleiding geen beleid voor opnieuw proberen geïmplementeerd. In productiecode moet u beleid voor opnieuw proberen implementeren (zoals een exponentiële back-off), zoals voorgesteld in het artikel [Transient Fault Handling](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice die een directe methode op een apparaat aanroept en de apparaattweeling bijwerkt. De service heeft de **service-verbindingtoestemming** nodig om een directe methode op een apparaat aan te roepen. De service heeft ook het **register gelezen** en **register schrijfmachtigingen** nodig om het identiteitsregister te lezen en te schrijven. Er is geen standaard beleid voor gedeelde toegang dat alleen deze machtigingen bevat, dus u moet er een maken.

Voer de volgende stappen uit om een beleid voor gedeelde toegang te maken waarmee **serviceconnect,** **registerlees-** en **registerschrijfmachtigingen** worden toegekend en om een verbindingstekenreeks voor dit beleid te krijgen:

1. Open uw IoT-hub in de [Azure-portal.](https://portal.azure.com) De eenvoudigste manier om naar uw IoT-hub te gaan, is **door Resourcegroepen**te selecteren, de brongroep te selecteren waar uw IoT-hub zich bevindt en vervolgens uw IoT-hub te selecteren in de lijst met bronnen.

2. Selecteer in het linkerdeelvenster van uw IoT-hub de optie **Beleid voor gedeelde toegang**.

3. Selecteer **Toevoegen**in het bovenste menu boven de lijst met beleidsregels .

4. Voer **in** het deelvenster Een beleid voor gedeelde toegang toevoegen een beschrijvende naam voor uw beleid in. bijvoorbeeld: *serviceEnRegistryReadWrite*. Selecteer **onder Machtigingen**de optie **Serviceverbinding** en **Registerschrijven** **(Registerlezen** wordt automatisch geselecteerd wanneer u **Registerschrijven selecteert).** Selecteer vervolgens **Maken**.

    ![Een nieuw beleid voor gedeelde toegang toevoegen](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Selecteer uw nieuwe beleid in de lijst met beleidsregels in het deelvenster **Beleid voor gedeelde toegang.**

6. Selecteer **onder Gedeelde toegangstoetsen**het kopieerpictogram voor de **verbindingstekenreeks : primaire sleutel** en sla de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Zie [Toegangsbeheer en machtigingen](./iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het gedeelde toegangsbeleid en machtigingen voor IoT Hub.

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Taken plannen voor het aanroepen van een directe methode en het bijwerken van de eigenschappen van een apparaattweeling

In deze sectie maakt u een Python-console-app die een externe **lockDoor** op een apparaat initieert met behulp van een directe methode en ook de gewenste eigenschappen van de apparaattweeling bijwerkt.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-hubpakket** te installeren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Maak met een teksteditor een nieuw **scheduleJobService.py-bestand** in uw werkmap.

3. Voeg aan `import` het begin van het **scheduleJobService.py** bestand de volgende instructies en variabelen toe. Vervang `{IoTHubConnectionString}` de tijdelijke aanduiding door de IoT-hubverbindingstekenreeks die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub .](#get-the-iot-hub-connection-string) Vervang `{deviceId}` de tijdelijke aanduiding door de apparaat-id die u hebt geregistreerd in [Registreer een nieuw apparaat in de IoT-hub:](#register-a-new-device-in-the-iot-hub)

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Voeg de volgende functie toe die wordt gebruikt om voor apparaten te zoeken:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Voeg de volgende methoden toe om de taken uit te voeren die de directe methode en apparaattweeling aanroepen:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Voeg de volgende code toe om de taken te plannen en de taakstatus bij te werken. Ook de `main` routine:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Sla het **scheduleJobService.py-bestand** op en sluit deze.

## <a name="run-the-applications"></a>De toepassingen uitvoeren

U kunt nu de toepassingen gaan uitvoeren.

1. Voer bij de opdrachtprompt in uw werkmap de volgende opdracht uit om te beginnen met luisteren naar de directe methode voor opnieuw opstarten:

    ```cmd/sh
    python simDevice.py
    ```

2. Voer bij een andere opdrachtprompt in uw werkmap de volgende opdracht uit om de taken te activeren om de deur te vergrendelen en de tweeling bij te werken:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. U ziet de reacties van het apparaat op de directe methode en de update van apparaattweelingen in de console.

    ![Voorbeeld 1 van IoT Hub-taak - apparaatuitvoer](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job voorbeeld 2- apparaatuitvoer](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een taak gebruikt om een directe methode te plannen voor een apparaat en de update van de eigenschappen van de apparaattweeling.

Zie [Een firmware-update uitvoeren](tutorial-firmware-update.md)om verder te gaan met IoT Hub- en apparaatbeheerpatronen, zoals firmware-update op afstand via de ether.
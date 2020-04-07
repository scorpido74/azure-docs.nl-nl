---
title: Identiteit van Azure IoT Hub-module en moduletweeling (Python)
description: Meer informatie over het maken van moduleidentiteit en het bijwerken van moduletwin met IoT SDK's voor Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756911"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Aan de slag met de identiteit van de IoT Hub-module en moduletweeling (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Moduleidentiteiten en moduletweelingen](iot-hub-devguide-module-twins.md) zijn vergelijkbaar met de identiteit van Azure IoT Hub-apparaten en apparaattweelingen, maar bieden een fijnere granulariteit. Terwijl azure IoT Hub-apparaatidentiteiten en apparaattweelingen een back-endtoepassing inschakelen om een apparaat te configureren en inzicht te bieden in de omstandigheden van het apparaat, bieden moduleidentiteiten en moduletweelingen deze mogelijkheden voor afzonderlijke onderdelen van een apparaat. Op geschikte apparaten met meerdere componenten, zoals apparaten op basis van het besturingssysteem of firmware-apparaten, kunnen ze geïsoleerde configuratie en voorwaarden voor elk onderdeel mogelijk maken.
>

Aan het einde van deze zelfstudie heb je drie Python-apps:

* **CreateModule**, waarmee een apparaatidentiteit, een module-identiteit en bijbehorende beveiligingssleutels worden gemaakt om uw apparaat en moduleclients met elkaar te verbinden.

* **UpdateModuleTwinDesiredProperties**, die bijgewerkte module twin gewenste eigenschappen naar uw IoT Hub stuurt.

* **ReceiveModuleTwinDesiredPropertiesPatch**, die de module twin desired properties patch op uw apparaat ontvangt.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een back-endservice die een apparaat toevoegt aan het identiteitsregister en vervolgens een module aan dat apparaat toevoegt. Deze service vereist het **register schrijftoestemming** (die ook **register te lezen).** U maakt ook een service die gewenste eigenschappen toevoegt aan de moduletwin voor de nieuw gemaakte module. Deze service heeft toestemming nodig voor het verbinden van de **service.** Hoewel er standaard beleid voor gedeelde toegang is waarmee deze machtigingen afzonderlijk worden verleend, maakt u in deze sectie een aangepast beleid voor gedeelde toegang dat beide machtigingen bevat.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Een apparaatidentiteit en een module-identiteit maken in IoT Hub

In deze sectie maakt u een Python-service-app die een apparaatidentiteit en een module-identiteit maakt in het identiteitsregister in uw IoT-hub. Een apparaat of module kan geen verbinding maken met de IoT-hub, tenzij er een vermelding in het identiteitsregister is opgenomen. Zie [Het identiteitsregister in uw IoT-hub begrijpen](iot-hub-devguide-identity-registry.md)voor meer informatie. Als u deze consoletoepassing uitvoert, worden er een unieke id en een unieke sleutel gemaakt voor zowel het apparaat als de module. Deze waarden worden door het apparaat en de module gebruikt om zichzelf te identificeren bij het verzenden van apparaat-naar-cloud-berichten naar IoT Hub. De id's zijn hoofdlettergevoelig.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-hubpakket** te installeren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Voer bij uw opdrachtprompt de volgende opdracht uit om het **msrest-pakket** te installeren. U hebt dit pakket nodig om **HTTPOperationError-uitzonderingen** te vangen.

    ```cmd/sh
    pip install msrest
    ```

1. Maak met behulp van een teksteditor een bestand met de naam **CreateModule.py** in uw werkmap.

1. Voeg de volgende code toe aan uw Python-bestand. Vervang *YourIoTHubConnectionString* door de verbindingstekenreeks die u hebt gekopieerd in [De verbindingstekenreeks voor IoT-hub opbrengen](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Voer bij de opdrachtprompt de volgende opdracht uit:

    ```cmd/sh
    python CreateModule.py
    ```

Deze app maakt een apparaatidentiteit met ID **myFirstDevice** en een module-identiteit met ID **myFirstModule** onder apparaat **myFirstDevice**. (Als de apparaat- of module-id al bestaat in het identiteitsregister, wordt de bestaande apparaat- of module-informatie door de code gewoon opgehaald.) De app geeft de ID en de primaire sleutel voor elke identiteit weer.

> [!NOTE]
> In het identiteitsregister van IoT Hub worden alleen apparaat- en module-id's opgeslagen waarmee veilig toegang tot de IoT-hub kan worden verkregen. In het identiteitsregister worden apparaat-id's en -sleutels opgeslagen die als beveiligingsreferenties worden gebruikt. In het identiteitsregister wordt ook een vlag ingeschakeld/uitgeschakeld voor elk apparaat opgeslagen die u kunt gebruiken om de toegang tot dat apparaat uit te schakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Er is geen vlag voor ingeschakeld/uitgeschakeld voor module-id's. Zie [Het identiteitsregister in uw IoT-hub begrijpen](iot-hub-devguide-identity-registry.md)voor meer informatie.
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>De moduletwin bijwerken met Python-service SDK

In deze sectie maakt u een Python-service-app die de gewenste eigenschappen van de module twee bijwerkt.

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-hub-pakket** te installeren. U deze stap overslaan als u het **azure-iot-hub-pakket** in de vorige sectie hebt geïnstalleerd.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Maak met een teksteditor een bestand met de naam **UpdateModuleTwinDesiredProperties.py** in uw werkmap.

1. Voeg de volgende code toe aan uw Python-bestand. Vervang *YourIoTHubConnectionString* door de verbindingstekenreeks die u hebt gekopieerd in [De verbindingstekenreeks voor IoT-hub opbrengen](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Updates ontvangen aan de apparaatzijde

In deze sectie maakt u een Python-app om de gewenste module-gewenste eigenschappen-update op uw apparaat te krijgen.

1. Haal uw module verbindingstekenreeks. Navigeer in [azure-portal](https://portal.azure.com/)naar uw IoT-hub en selecteer **IoT-apparaten** in het linkerdeelvenster. Selecteer **myFirstDevice** in de lijst met apparaten en open het. Selecteer **myFirstModule**onder **Module-identiteiten**. Kopieer de moduleverbindingsreeks. Je hebt het nodig in een volgende stap.

   ![Details van de Azure Portal-module](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Voer bij de opdrachtprompt de volgende opdracht uit om het **azure-iot-apparaatpakket** te installeren:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Maak met een teksteditor een bestand met de naam **ReceiveModuleTwinDesiredPropertiesPatch.py** in uw werkmap.

1. Voeg de volgende code toe aan uw Python-bestand. Vervang *YourModuleConnectionString* door de moduleverbindingstekenreeks die u in stap 1 hebt gekopieerd.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>De apps uitvoeren

In deze sectie voert u de **App ReceiveModuleTwinDesiredPropertiesPatch-apparaat** uit en voert u vervolgens de **updateModuleTwinDesiredProperties-service-app** uit om de gewenste eigenschappen van uw module bij te werken.

1. Open een opdrachtprompt en voer de apparaat-app uit:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Eerste uitvoer van apparaat-apps](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Open een afzonderlijke opdrachtprompt en voer de service-app uit:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    De gewenste eigenschap **TelemetrieInterval** wordt weergegeven in de bijgewerkte moduletwee in de uitvoer van uw service-app:

   ![Uitvoer van service-apps](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Dezelfde eigenschap wordt weergegeven in de gewenste eigenschappenpatch die is ontvangen in de uitvoer van uw apparaatapp:

   ![Apparaat-app-uitvoer toont gewenste eigenschappenpatch](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met IoT Hub en andere IoT-scenario's wilt verkennen, leest u deze artikelen:

* [Aan de slag met apparaatbeheer](iot-hub-node-node-device-management-get-started.md)

* [Aan de slag met IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
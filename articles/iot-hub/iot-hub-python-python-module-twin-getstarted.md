---
title: Identiteit van Azure IoT Hub-module en moduletweeling (Python)
description: Meer informatie over het maken van moduleidentiteit en het bijwerken van moduletwin met IoT SDK's voor Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: menchi
ms.openlocfilehash: e18d448d9aee0137f1167d23a2bbf53486d0c764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953844"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Aan de slag met de identiteit van de IoT Hub-module en moduletweeling (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Module-id's en moduledubbels](iot-hub-devguide-module-twins.md) zijn vergelijkbaar met Azure IoT Hub-apparaat-id's en apparaatdubbels, maar bieden een hogere granulariteit. Met een Azure IoT Hub-apparaat-id en apparaatdubbel kan een apparaat worden geconfigureerd via de back-endtoepassing en kunt u inzicht krijgen in de toestand van een apparaat. Een module-id en moduledubbel bieden deze mogelijkheden voor afzonderlijke onderdelen van een apparaat. Op apparaten met meerdere onderdelen, zoals apparaten met een besturingssysteem of firmware, kunt u afzonderlijke configuraties en voorwaarden voor elk onderdeel instellen.
>

Aan het einde van deze zelfstudie heb je twee Python-apps:

* **CreateIdentities**: deze toepassing maakt een apparaat-id, een module-id en de bijbehorende beveiligingssleutel waarmee uw apparaat- en moduleclients verbonden kunnen worden.

* **UpdateModuleTwinReportedProperties**: deze toepassing verzendt bijgewerkte gerapporteerde eigenschappen van de moduledubbel naar uw IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Een apparaatidentiteit en een module-identiteit maken in IoT Hub

In deze sectie maakt u een Python-app die een apparaatidentiteit en een module-identiteit maakt in het identiteitsregister in uw IoT-hub. Een apparaat of module kan alleen verbinding maken met de IoT-hub als het apparaat of de module is vermeld in het identiteitsregister. Zie het gedeelte 'Identiteitsregister' van de [iot-hub-ontwikkelaarshandleiding](iot-hub-devguide-identity-registry.md)voor meer informatie. Als u deze consoletoepassing uitvoert, worden er een unieke id en een unieke sleutel gemaakt voor zowel het apparaat als de module. Deze waarden worden door het apparaat en de module gebruikt om zichzelf te identificeren bij het verzenden van apparaat-naar-cloud-berichten naar IoT Hub. De id's zijn hoofdlettergevoelig.

Voeg de volgende code toe aan uw Python-bestand:

```python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(
        DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID +
          "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(
        DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID +
          "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print("Unexpected error {0}".format(iothub_error))
except KeyboardInterrupt:
    print("IoTHubRegistryManager sample stopped")
```

Deze app maakt een apparaatidentiteit met ID **myFirstDevice** en een module-identiteit met ID **myFirstModule** onder apparaat **myFirstDevice**. (Als die module-id al bestaat in het identiteitsregister, wordt de bestaande module-informatie door de code gewoon opgehaald.) De app geeft vervolgens de primaire sleutel voor die identiteit weer. U gebruikt deze sleutel in de gesimuleerde module-app om verbinding te maken met uw IoT-hub.

> [!NOTE]
> In het identiteitsregister van IoT Hub worden alleen apparaat- en module-id's opgeslagen waarmee veilig toegang tot de IoT-hub kan worden verkregen. In het identiteitsregister worden apparaat-id's en -sleutels opgeslagen die als beveiligingsreferenties worden gebruikt. In het identiteitsregister wordt ook een vlag ingeschakeld/uitgeschakeld voor elk apparaat opgeslagen die u kunt gebruiken om de toegang tot dat apparaat uit te schakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Er is geen vlag voor ingeschakeld/uitgeschakeld voor module-id's. Zie [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide-identity-registry.md)voor meer informatie.
>

## <a name="update-the-module-twin-using-python-device-sdk"></a>De moduletwin bijwerken met Python-apparaat SDK

In deze sectie maakt u een Python-app op uw gesimuleerde apparaat die de gerapporteerde eigenschappen van de module twee bijwerkt.

1. **Haal uw moduleverbindingstekenreeks op** - als u zich nu aanmeldt bij de [Azure-portal](https://portal.azure.com/). Navigeer naar uw IoT Hub en klik op IoT-apparaten. Zoek myFirstDevice, open het en je ziet dat myFirstModule is gemaakt. Kopieer de moduleverbindingsreeks. Deze is vereist voor de volgende stap.

   ![Details van de Azure Portal-module](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

2. **UpdateModuleTwinReportedProperties-app maken**

   Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

In dit codevoorbeeld ziet u hoe u de gerapporteerde eigenschappen van de moduledubbel kunt ophalen en bijwerken met het AMQP-protocol.

## <a name="get-updates-on-the-device-side"></a>Updates ontvangen aan de apparaatzijde

Naast de bovenstaande code u onder het codeblok toevoegen om het dubbele updatebericht op uw apparaat te ontvangen.

```python
import time
import threading
from azure.iot.device import IoTHubModuleClient

CONNECTION_STRING = "{deviceConnectionString}"


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

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan met IoT Hub en andere IoT-scenario's wilt verkennen, leest u deze artikelen:

* [Aan de slag met apparaatbeheer](iot-hub-node-node-device-management-get-started.md)

* [Aan de slag met IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
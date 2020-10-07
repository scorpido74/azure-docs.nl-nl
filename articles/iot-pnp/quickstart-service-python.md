---
title: Interactie met een IoT Plug en Play-apparaat dat is verbonden met uw Azure IoT-oplossing (Python) | Microsoft Docs
description: Gebruik Python om verbinding te maken met een IoT Plug en Play-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574960"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Quickstart: Interactie met een IoT Plug en Play-apparaat dat is verbonden met uw oplossing (Python)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug en Play vereenvoudigt IoT omdat u kunt communiceren met het model van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Python gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Als u deze quickstart wilt uitvoeren, moet Python 3.7 op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [python.org](https://www.python.org/). U kunt uw Python-versie controleren met de volgende opdracht:  

```cmd/sh
python --version
```

Het **azure-iot-device**-pakket wordt gepubliceerd als PIP.

Installeer het pakket als volgt in uw lokale Python-omgeving:

```cmd/sh
pip install azure-iot-device
```

Installeer het **azure-iot-device**-pakket door de volgende opdracht uit te voeren:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Zie het [leesmij-voorbeeldbestand](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md) (Engelstalig) voor meer informatie over de voorbeeldconfiguratie.

In deze quickstart gebruikt u als voorbeeld een thermostaat die in Python is geschreven als IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open een terminalvenster in een map naar keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) te klonen op deze locatie:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Dit terminalvenster wordt gebruikt als uw **apparaat**terminal. Ga naar de map van de gekloonde opslagplaats en ga naar de map */azure-iot-sdk-python/azure-iot-device/samples/pnp*.

1. Voer het voorbeeldapparaat (thermostaat) uit met de volgende opdracht:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Deze berichten geven aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat het servicevoorbeeld werkt.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in Python om te communiceren met het voorbeeldapparaat dat u zojuist hebt ingesteld.

1. Open een ander terminalvenster om als **service**terminal te gebruiken. 

1. Ga naar de map */azure-iot-sdk-python/azure-iot-hub/samples* van de gekloonde Python SDK-opslagplaats.

1. De map met voorbeelden bevat vier voorbeeldbestanden die de bewerkingen demonstreren met de Digital Twin Manager-klasse: *get_digital_twin_sample.py, update_digitial_twin_sample.py, invoke_command_sample.py en invoke_component_command_sample-.py*.  Deze voorbeelden laten zien hoe u elke API gebruikt voor interactie met IoT Plug en Play-apparaten:

### <a name="get-digital-twin"></a>Digital twin opvragen

In [Quickstarts en zelfstudies voor het instellen van uw omgeving voor IoT Plug en Play](set-up-environment.md) hebt u twee omgevingsvariabelen gemaakt om het voorbeeld zodanig te configureren dat verbinding wordt gemaakt met uw IoT-hub en het apparaat:

* **IOTHUB_CONNECTION_STRING**: de verbindingsreeks voor de IoT-hub die u eerder hebt genoteerd.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Gebruik de volgende opdracht in de **service**terminal om dit voorbeeld uit te voeren:

```cmd/sh
python get_digital_twin_sample.py
```

In de uitvoer ziet u de Digital Twin van het apparaat en de bijbehorende model-id:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

In het volgende codefragment ziet u de voorbeeldcode uit *get_digital_twin_sample.py*:

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Een digital twin bijwerken

In dit voorbeeld ziet u hoe u een *patch* kunt gebruiken om eigenschappen bij te werken via de digital twin van uw apparaat. In het volgende codefragment van *update_digital_twin_sample.py* ziet u hoe u de patch samenstelt:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Gebruik de volgende opdracht in de **service**terminal om dit voorbeeld uit te voeren:

```cmd/sh
python update_digital_twin_sample.py
```

U kunt controleren of de update is toegepast als in de **apparaat**terminal de volgende uitvoer wordt weergegeven:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

In de **service**terminal wordt bevestigd dat de patch is gelukt:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

Als u een opdracht wilt aanroepen, voert u het voorbeeld *invoke_command_sample.py* uit. In dit voorbeeld ziet u hoe u een opdracht aanroept op een eenvoudig thermostaatapparaat. Voordat u dit voorbeeld uitvoert, moet u de omgevingsvariabelen `IOTHUB_COMMAND_NAME` en `IOTHUB_COMMAND_PAYLOAD` instellen in de **service**terminal:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

Gebruik de volgende opdracht in de **service**terminal om dit voorbeeld uit te voeren:
  
```cmd/sh
python invoke_command_sample.py
```

In de **service**terminal ziet u een bevestigingsbericht van het apparaat:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

U kunt in de **apparaat**terminal zien dat het apparaat de opdracht ontvangt:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-oplossing. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars voor IoT Plug and Play-modellen](concepts-developer-guide-device-csharp.md)

---
title: Interactie met IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing - Node.js | Microsoft Docs
description: Gebruik Node.js (.NET) om verbinding te maken met een IoT Plug en Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 7f86ae8ffefa88fe1cdfa429b7de4671422839ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043998"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Quickstart: Interactie met een IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug en Play Preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Node.js gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet Node.js op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub (dit is voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u een voorbeeld van een omgevingssensor die in Node.js is geschreven als IoT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Azure IoT Samples for Node.js](https://github.com/azure-samples/azure-iot-samples-node) te klonen op deze locatie:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Dit terminalvenster wordt nu gebruikt als uw _apparaat_terminal. Ga naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-samples-node/digital-twins/Quickstart/Device**. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de _device connection string_ (verbindingsreeks voor het apparaat):

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer het voorbeeld uit met de volgende opdracht:

    ```cmd/sh
    node sample_device.js
    ```

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Dit geeft aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat de servicevoorbeelden werkten.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in Node.js om te communiceren met het voorbeeld-apparaat.

1. Open een ander terminalvenster (dit is uw _service_terminal). Ga naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-samples-node/digitaltwin/Quickstarts/Service**. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de _IoT-hub-verbindingsreeks_ zodat de service verbinding kan maken met het volgende:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de terminal hebt aangesloten, zag u het volgende bericht om de online status aan te geven:

    ```cmd/sh
    reported state property as online
    ```

1. Open in de map **/azure-iot-samples-node/digital-twins/Quickstarts/Service** het bestand **get_digital_twin. js-** . Vervang de tijdelijke aanduiding `<DEVICE_ID_GOES_HERE>` door de apparaat-id en sla het bestand op.

1. Ga naar de terminal _service_ en gebruik de volgende opdracht om het voorbeeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Ga in de uitvoer van de terminal _service_ naar het onderdeel `environmentalSensor`. U ziet dat de eigenschap `state` is gerapporteerd als _online_:

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Een schrijfbare eigenschap bijwerken

1. Open het bestand **update_digital_twin_property. js**.

1. Aan het begin van het bestand is er een set constanten gedefinieerd met tijdelijke aanduidingen in hoofdletters. Vervang de tijdelijke aanduiding `<DEVICE_ID_GOES_HERE>` door de werkelijke apparaat-id, werk de resterende constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Ga naar de terminal _service_ en gebruik de volgende opdracht om het voorbeeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. De uitvoer van de terminal _service_ toont de bijgewerkte apparaatgegevens. Ga naar het onderdeel `environmentalSensor` om de nieuwe helderheidswaarde van 42 weer te geven.

    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

1. Ga naar uw terminal _apparaat_, u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Ga terug naar de terminal _service_ en voer de onderstaande opdracht uit om de apparaatgegevens opnieuw op te halen om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. In de uitvoer van de terminal _service_ onder het onderdeel `environmentalSensor` ziet u dat de bijgewerkte helderheidswaarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daadwerkelijk de update van de eigenschap heeft verwerkt.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

1. Open het bestand **invoke_command. js**.

1. Vervang de tijdelijke aanduiding `<DEVICE_ID_GOES_HERE>` door de werkelijke apparaat-id bovenaan het bestand. Werk de resterende constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Ga naar de terminal _service_. Gebruik de volgende opdracht om het voorbeeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    node invoke_command.js
    ```

1. De uitvoer van de terminal _service_ moet de volgende bevestiging weergeven:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Ga naar het terminal _apparaat_, u ziet dat de opdracht is bevestigd:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-oplossing. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

> [!div class="nextstepaction"]
> [Uitleg: Verbinding maken en werken met een apparaat](howto-develop-solution.md)

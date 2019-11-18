---
title: Interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uw Azure IoT-oplossing | Microsoft Docs
description: Gebruik node. js om verbinding te maken met een IoT Plug en Play preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3275c01059a61e4eb8591948695656f4e4b722ed
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152125"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Snelstartgids: interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uw oplossing (node. js)

IoT Plug en Play preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze Quick start ziet u hoe u node. js kunt gebruiken om verbinding te maken met een IoT-Plug en Play apparaat te beheren dat is verbonden met uw oplossing.

## <a name="prerequisites"></a>Vereisten

U kunt deze Snelstartgids alleen volt ooien als u node. js hebt op de ontwikkel computer. U kunt de meest recente aanbevolen versie voor meerdere platforms downloaden vanuit [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub-windows.md](../../includes/iot-pnp-prepare-iot-hub-windows.md)]

## <a name="connect-your-device"></a>Uw apparaat verbinden

In deze Quick Start gebruikt u een voor beeld van een omgevings sensor die is geschreven in node. js als het IoT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Open een Terminal venster in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT-voor beelden voor node. js](https://github.com/azure-samples/azure-iot-samples-node) github-opslag plaats naar deze locatie te klonen:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Dit Terminal venster wordt nu gebruikt als uw _apparaat_ -Terminal. Ga naar de gekloonde opslag plaats en ga naar de map **/Azure-IOT-samples-node/Digital-Twins/QuickStarts/Device** . Installeer alle afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer het voor beeld uit met de volgende opdracht:

    ```cmd/sh
    node sample_device.js
    ```

1. Er worden berichten weer gegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zelf online heeft gerapporteerd. Dit geeft aan dat het apparaat is begonnen met het verzenden van telemetriegegevens naar de hub en nu gereed is voor het ontvangen van opdrachten en updates van eigenschappen. Sluit deze terminal niet. u hebt deze later nodig om te bevestigen dat de service voorbeelden ook hebben gewerkt.

## <a name="build-the-solution"></a>De oplossing bouwen

In deze Quick Start gebruikt u een IoT-voorbeeld oplossing in node. js om te communiceren met het voor beeld-apparaat.

1. Open een ander Terminal venster (dit is uw _service_ Terminal). Ga naar de map van uw gekloonde opslag plaats en navigeer naar de map **/Azure-IOT-samples-node/Digital-Twins/QuickStarts/service** . Installeer alle afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de _IOT hub-Connection String_ zodat de service er verbinding mee kan maken:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de Terminal hebt aangesloten, hebt u het volgende bericht met de online status gezien:

    ```cmd/sh
    reported state property as online
    ```

1. Open het bestand **get_digital_twin. js**in de map **/Azure-IOT-samples-node/Digital-Twins/QuickStarts/service** . Vervang de tijdelijke aanduiding `<DEVICE_ID_GOES_HERE>` door de apparaat-ID en sla het bestand op.

1. Ga naar de _service_ Terminal en gebruik de volgende opdracht om het voor beeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Schuif in de Terminal uitvoer van de _service_ naar het onderdeel `environmentalSensor`. U ziet dat de eigenschap `state` is gerapporteerd als _online_:

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

### <a name="update-a-writable-property"></a>Een Beschrijf bare eigenschap bijwerken

1. Open het bestand **update_digital_twin_property. js**.

1. Aan het begin van het bestand is er een set constanten gedefinieerd met tijdelijke aanduidingen voor hoofd letters. Vervang de tijdelijke aanduiding `<DEVICE_ID_GOES_HERE>` door de werkelijke apparaat-ID, werk de resterende constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Ga naar de _service_ Terminal en gebruik de volgende opdracht om het voor beeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. De _service_ Terminal uitvoer toont de bijgewerkte apparaatgegevens. Ga naar het onderdeel `environmentalSensor` om de nieuwe helderheids waarde van 42 weer te geven.

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

1. Ga naar uw _apparaat_ -Terminal, u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Ga terug naar de _service_ Terminal en voer de onderstaande opdracht uit om de apparaatgegevens opnieuw op te halen om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. In de _service_ Terminal output, onder het onderdeel `environmentalSensor`, ziet u dat de bijgewerkte helderheids waarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daad werkelijk de update van de eigenschap heeft verwerkt.
    
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

1. Vervang aan het begin van het bestand de tijdelijke aanduiding `<DEVICE_ID_GOES_HERE>` door de apparaat-ID. Werk de resterende constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Ga naar de _service_ Terminal. Gebruik de volgende opdracht om het voor beeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    node invoke_command.js
    ```

1. De uitvoer in de _service_ terminal moet de volgende bevestiging weer geven:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Naar de _apparaat_ -Terminal gaat, ziet u dat de opdracht is bevestigd:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en interactie met een apparaat](howto-develop-solution.md)

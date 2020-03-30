---
title: Interactie met een IoT Plug and Play Preview-apparaat dat is aangesloten op uw Azure IoT-oplossing | Microsoft Documenten
description: Gebruik Node.js om verbinding te maken met en te communiceren met een IoT Plug and Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550737"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Snelstart: interactie met een IoT Plug and Play Preview-apparaat dat is verbonden met uw oplossing (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

IoT Plug and Play Preview vereenvoudigt IoT doordat u communiceren met de mogelijkheden van een apparaat zonder kennis te hebben van de onderliggende apparaatimplementatie. Deze quickstart laat zien hoe je Node.js gebruiken om verbinding te maken met een IoT Plug and Play-apparaat dat is verbonden met je oplossing.

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, heb je Node.js op je ontwikkelmachine nodig. U de nieuwste aanbevolen versie voor meerdere platforms downloaden van [nodejs.org.](https://nodejs.org)

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u een voorbeeldomgevingssensor die in Node.js is geschreven als het IoT Plug and Play-apparaat. In de volgende instructies ziet u hoe u het apparaat installeert en uitvoert:

1. Open een terminalvenster in de map van uw keuze. Voer de volgende opdracht uit om de [Azure IoT-samples voor Node.js](https://github.com/azure-samples/azure-iot-samples-node) GitHub-opslagplaats op deze locatie te klonen:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Dit terminalvenster wordt nu gebruikt als _terminal voor uw apparaat._ Ga naar de map van uw gekloonde opslagplaats en navigeer naar de map **/azure-iot-samples-node/digital-twins/Quickstarts/Device.** Installeer alle afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. De _tekenreeks voor apparaatverbinding_configureren:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer het voorbeeld uit met de volgende opdracht:

    ```cmd/sh
    node sample_device.js
    ```

1. U ziet berichten waarin staat dat het apparaat bepaalde informatie heeft verzonden en zichzelf online heeft gemeld. Dit geeft aan dat het apparaat is begonnen met het verzenden van telemetriegegevens naar de hub en nu klaar is om opdrachten en eigenschapsupdates te ontvangen. Sluit deze terminal niet, u hebt deze later nodig om te bevestigen dat de servicemonsters ook werkten.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een voorbeeld van IoT-oplossing in Node.js om te communiceren met het voorbeeldapparaat.

1. Open een ander terminalvenster (dit wordt uw _serviceterminal)._ Ga naar de map van uw gekloonde opslagplaats en navigeer naar de map **/azure-iot-samples-node/digital-twins/Quickstarts/Service.** Installeer alle afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de _IoT-hubverbindingstekenreeks_ zodat de service verbinding kan maken:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het _apparaat_ in de terminal hebt aangesloten, zag u het volgende bericht met de onlinestatus:

    ```cmd/sh
    reported state property as online
    ```

1. Open het bestand **get_digital_twin.js**in de map **/azure-iot-samples-node/digital-twins/Quickstarts/Service.** Vervang `<DEVICE_ID_GOES_HERE>` de tijdelijke aanduiding door uw apparaat-id en sla het bestand op.

1. Ga naar de _serviceterminal_ en gebruik de volgende opdracht om het voorbeeld uit te voeren voor informatie over het leesapparaat:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Schuif in de uitvoer van `environmentalSensor` de _serviceterminal_ naar de component. U ziet `state` dat de woning is gemeld als _online:_

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

### <a name="update-a-writable-property"></a>Een beschrijfbare eigenschap bijwerken

1. Open het bestand **update_digital_twin_property.js**.

1. Aan het begin van het bestand is er een reeks constanten gedefinieerd met tijdelijke aanduidingen in hoofdletters. Vervang `<DEVICE_ID_GOES_HERE>` de tijdelijke aanduiding door uw werkelijke apparaat-id, werk de resterende constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Ga naar de _serviceterminal_ en gebruik de volgende opdracht om het voorbeeld uit te voeren voor het bijwerken van de eigenschap:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. De uitvoer van de _serviceterminal_ toont de bijgewerkte apparaatinformatie. Schuif naar `environmentalSensor` de component om de nieuwe helderheidswaarde van 42 te zien.

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

1. Ga naar de terminal van uw _apparaat,_ u ziet dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Ga terug naar uw _serviceterminal_ en voer de onderstaande opdracht uit om de apparaatgegevens opnieuw te krijgen, om te bevestigen dat de accommodatie is bijgewerkt.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. In de uitvoer van `environmentalSensor` de _serviceterminal,_ onder het onderdeel, ziet u dat de bijgewerkte helderheidswaarde is gerapporteerd. Opmerking: het kan even duren voordat het apparaat de update heeft voltooid. U deze stap herhalen totdat het apparaat de eigenschapupdate daadwerkelijk heeft verwerkt.
    
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

1. Open het bestand **invoke_command.js**.

1. Vervang aan het begin van `<DEVICE_ID_GOES_HERE>` het bestand de tijdelijke aanduiding door uw werkelijke apparaat-id. Werk de resterende constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Ga naar de _serviceterminal._ Gebruik de volgende opdracht om het voorbeeld uit te voeren om een beroep te doen op de opdracht:

    ```cmd/sh
    node invoke_command.js
    ```

1. De uitvoer in de _serviceterminal_ moet de volgende bevestiging weergeven:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Ga naar de terminal van het _apparaat,_ u ziet dat de opdracht is erkend:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een apparaat](howto-develop-solution.md)

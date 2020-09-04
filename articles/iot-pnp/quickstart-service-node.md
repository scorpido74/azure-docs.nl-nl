---
title: Interactie met IoT Plug en Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing (Node.js) | Microsoft Docs
description: Gebruik Node.js (.NET) om verbinding te maken met een IoT Plug en Play Preview-apparaat dat is verbonden met uw Azure IoT-oplossing.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-javascript
ms.openlocfilehash: fd65dcc9ce0be07daa5848a0ac583cf795150e47
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184734"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Quickstart: Interactie met een IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

IoT Plug en Play Preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze quickstart ziet u hoe u Node.js gebruikt om verbinding te maken met een IoT-Plug en Play-apparaat dat is verbonden met uw oplossing en hoe u dit beheert.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet Node.js op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Kloon de SDK-opslagplaats met de voorbeeldcode

De service-SDK is in preview, wat betekent dat u de voorbeelden moet klonen uit een [preview-branch van de Node-SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh). Open een terminalvenster in een map naar keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats van de branch **pnp-preview-refresh** van de [Microsoft Azure IoT SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node) te klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

In deze quickstart gebruikt u als voorbeeld een thermostaat die in Node.js is geschreven als IoT Plug en Play-apparaat. Het voorbeeldapparaat uitvoeren:

1. Open een terminalvenster en navigeer naar de lokale map met de Microsoft Azure IoT SDK voor Node.js-opslagplaats die u hebt gekloond vanuit GitHub.

1. Dit terminalvenster wordt gebruikt als uw **apparaatterminal**. Ga naar de map van de gekloonde opslagplaats en navigeer naar de map */azure-iot-sdk-node/device/samples/pnp*. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de _device connection string_ (verbindingsreeks voor het apparaat):

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer het voorbeeldapparaat (thermostaat) uit met de volgende opdracht:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Er worden berichten weergegeven met de melding dat het apparaat een aantal gegevens heeft verzonden en zichzelf online heeft gerapporteerd. Deze berichten geven aan dat het apparaat nu klaar is om opdrachten en updates van eigenschappen te ontvangen, en dat er telemetriegegevens worden verzonden naar de hub. Sluit deze terminal niet. U hebt deze later nodig om te bevestigen dat het servicevoorbeeld werkt.

## <a name="run-the-sample-solution"></a>De voorbeeldoplossing uitvoeren

In deze quickstart gebruikt u een IoT-voorbeeldoplossing in C# om te communiceren met het voorbeeldapparaat dat u zojuist hebt ingesteld.

1. Open een ander terminalvenster om als **serviceterminal** te gebruiken. De service-SDK is in preview, wat betekent dat u de voorbeelden moet klonen uit een [preview-branch van de Node-SDK](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node -b pnp-preview-refresh
    ```

1. Ga naar de map van de gekloonde branch van de opslagplaats en ga naar de map */azure-iot-sdk-node/digital-twins/samples/service/javascript*. Installeer de afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer omgevingsvariabelen voor uw apparaat-id en _IoT Hub-verbindingsreeks_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het voorbeeldapparaat (thermostaat) in de **apparaatterminal** hebt uitgevoerd, zag u het volgende bericht om de online status aan te geven:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Ga naar de terminal **service** en gebruik de volgende opdracht om het voorbeeld voor het lezen van apparaatgegevens uit te voeren:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. Bekijk het antwoord van de digitale dubbel in de uitvoer van de **serviceterminal**. U ziet hier de model-id van het apparaat en de bijbehorende eigenschappen die zijn gerapporteerd:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. Het volgende fragment toont de code in *get_digital_twin. js*, waarmee de model-id van de 	apparaatdubbel wordt opgehaald:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

In dit scenario wordt `Model Id: dtmi:com:example:Thermostat;1` uitgevoerd.

### <a name="update-a-writable-property"></a>Een schrijfbare eigenschap bijwerken

1. Open het bestand *update_digital_twin.js* in de code-editor.

1. Bekijk de voorbeeldcode. U kunt zien hoe u een JSON-patch maakt om de digitale dubbel van het apparaat bij te werken. In dit voorbeeld wordt de temperatuur van de thermostaat vervangen door de waarde 42:

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. Gebruik de volgende opdracht in de terminal **service** om het voorbeeld voor het bijwerken van de eigenschap uit te voeren:

    ```cmd/sh
    node update_digital_twin.js
    ```

1. U kunt in de terminal **apparaat** zien dat het apparaat de update heeft ontvangen:

    ```cmd/sh
    The following properties will be updated for root interface:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. Voer in de **serviceterminal** de volgende opdracht uit om te bevestigen dat de eigenschap is bijgewerkt:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. In de uitvoer van de **serviceterminal**, in het antwoord van de digitale dubbel onder het onderdeel `thermostat1`, ziet u dat de bijgewerkte doeltemperatuur wordt vermeld. Het kan enige tijd duren voordat het apparaat de update heeft voltooid. Herhaal deze stap totdat het apparaat de update van de eigenschap heeft verwerkt:

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

1. Open het bestand *invoke_command. js* en controleer de code.

1. Ga naar de terminal **service**. Gebruik de volgende opdracht om het voorbeeld voor het aanroepen van de opdracht uit te voeren:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. De uitvoer van de terminal **service** geeft de volgende bevestiging weer:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. U kunt in de terminal **apparaat** zien dat de opdracht is bevestigd:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-oplossing. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug en Play-previewmodellen](concepts-developer-guide.md)

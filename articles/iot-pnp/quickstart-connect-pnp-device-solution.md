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
ms.openlocfilehash: 246d3eac8f9d8aff6d603ea8686e430ce0f772ea
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878248"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Quickstart: Interactie met een IoT Plug en Play preview-apparaat dat is verbonden met uw oplossing

IoT Plug en Play preview vereenvoudigt IoT door u te laten communiceren met de mogelijkheden van een apparaat zonder kennis van de onderliggende implementatie van het apparaat. In deze Quick start ziet u hoe u node. js kunt gebruiken om verbinding te maken met een IoT-Plug en Play apparaat te beheren dat is verbonden met uw oplossing.

## <a name="prerequisites"></a>Vereisten

Down load en installeer node. js vanuit [nodejs.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voeg de Microsoft Azure IoT-extensie voor Azure CLI toe:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang **YourIoTHubName** en **YourDevice** door uw werkelijke namen. Als u geen IoT Hub hebt, volgt u [deze instructies](../iot-hub/iot-hub-create-using-cli.md) om er een te maken:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Voer de volgende opdrachten uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Voer de volgende opdrachten uit om de _IOT hub-Connection String_ voor uw hub op te halen:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Uw apparaat verbinden

In deze Quick Start gebruikt u een voor beeld van een omgevings sensor die is geschreven in node. js als het IoT Plug en Play-apparaat. De volgende instructies laten zien hoe u het apparaat installeert en uitvoert:

1. Kloon de GitHub-opslag plaats:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. Ga in een terminal naar de hoofdmap van de gekloonde opslag plaats, navigeer naar de map **/Azure-IOT-samples-node/Digital-Twins/QuickStarts/Device** en installeer alle afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Voer het voor beeld uit met de volgende opdracht:

    ```cmd/sh
    node sample_device.js
    ```

1. Er worden berichten weer gegeven met de mede deling dat het apparaat telemetrie en de bijbehorende eigenschappen heeft verzonden. Het apparaat is nu klaar om opdrachten en updates van eigenschappen te ontvangen. Sluit deze terminal niet, u hebt deze later nodig om te bevestigen dat de service voorbeelden ook hebben gewerkt.

## <a name="build-the-solution"></a>De oplossing bouwen

In deze Quick Start gebruikt u een IoT-voorbeeld oplossing in node. js om te communiceren met het voor beeld-apparaat.

1. Open een andere terminal. Ga naar de map van uw gekloonde opslag plaats en navigeer naar de map **/Azure-IOT-samples-node/Digital-Twins/QuickStarts/service** . Installeer alle afhankelijkheden door de volgende opdracht uit te voeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de _hub Connection String_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Een eigenschap lezen

1. Wanneer u het apparaat in de Terminal verbindt, wordt het volgende bericht weer gegeven:

    ```cmd/sh
    reported state property as online
    ```

1. Open het bestand **get_digital_twin. js**. Vervang de `deviceID` door uw apparaat-id en sla het bestand op.

1. Ga naar de terminal die u voor het uitvoeren van service voorbeelden hebt geopend en voer de volgende opdracht uit:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. In de uitvoer ziet u onder het onderdeel _environmentalSensor_ dezelfde status als is gerapporteerd:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Een Beschrijf bare eigenschap bijwerken

1. Open het bestand **update_digital_twin_property. js**.

1. Aan het begin van het bestand is er een set constanten gedefinieerd met tijdelijke aanduidingen voor hoofd letters. Vervang de **DeviceID** door de daad werkelijke apparaat-id, werk de constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Ga naar de terminal die u voor het uitvoeren van service voorbeelden hebt geopend en gebruik de volgende opdracht om het voor beeld uit te voeren:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. In de terminal ziet u de digitale dubbele informatie die is gekoppeld aan uw apparaat. Zoek het onderdeel _environmentalSensor_, u ziet de nieuwe helderheids waarde 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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
    Received an update for brightness: 60
    updated the property
    ```
2. Ga terug naar uw _service_ Terminal en voer de onderstaande opdracht opnieuw uit om te bevestigen dat de eigenschap is bijgewerkt.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. In de uitvoer ziet u onder het onderdeel environmentalSensor de bijgewerkte helderheids waarde is gerapporteerd. Opmerking: het kan enige tijd duren voordat het apparaat de update heeft voltooid. U kunt deze stap herhalen totdat het apparaat daad werkelijk de update van de eigenschap heeft verwerkt.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Een opdracht aanroepen

1. Open het bestand **invoke_command. js**.

1. Vervang aan het begin van het bestand de `deviceID` door uw daad werkelijke apparaat-id. Werk de constanten bij met de volgende waarden en sla het bestand op:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Ga naar de terminal die u hebt geopend voor het uitvoeren van Service Sample. Gebruik de volgende opdracht om het voor beeld uit te voeren:

    ```cmd/sh
    node invoke_command.js
    ```

1. In de terminal ziet het resultaat eruit als in de volgende uitvoer:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Naar de _apparaat_ -Terminal gaat, ziet u dat de opdracht is bevestigd:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om door te gaan met latere artikelen, kunt u de resources die u in deze Quick Start hebt gebruikt, behouden. Anders kunt u de resources die u hebt gemaakt voor deze Quick Start verwijderen om te voor komen dat er extra kosten in rekening worden gebracht.

Als u de hub en het geregistreerde apparaat wilt verwijderen, voert u de volgende stappen uit met behulp van de Azure CLI:

```azurecli-interactive
az group delete --name <Your group name>
```

Voer de volgende stappen uit met behulp van de Azure CLI om alleen het apparaat te verwijderen dat u bij uw IoT Hub hebt geregistreerd:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-oplossing. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Uitleg: Verbinding maken met en interactie met een apparaat](howto-develop-solution.md)

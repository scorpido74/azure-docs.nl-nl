---
title: 'Quick Start: telemetrie naar Azure IoT verzenden (node. js)'
description: In deze snelstart voert u twee Node.js-voorbeeldtoepassingen uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en telemetrie van de IoT-hub te lezen voor verwerking in de cloud.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: 5c34dcc606e87e11a3a018df1b2d6bbedb262d04
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209108"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Quick Start: verzend telemetrie van een apparaat naar een IoT-hub en lees het met een back-end-toepassing (node. js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 In deze Snelstartgids verzendt u telemetrie vanuit een gesimuleerd apparaat toepassing via Azure IoT Hub naar een back-end-toepassing voor verwerking. IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. Deze Snelstartgids maakt gebruik van twee vooraf geschreven node. js-toepassingen: een om de telemetrie en één te verzenden om de telemetrie van de hub te lezen. Voordat u deze twee toepassingen kunt uitvoeren, moet u een IoT-hub maken en een apparaat registreren bij de hub.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node. js 10 +](https://nodejs.org). Als u de Azure Cloud Shell gebruikt, moet u de geïnstalleerde versie van node. js niet bijwerken. De Azure Cloud Shell heeft al de meest recente versie van node. js.

* [Een voor beeld van een node. js-project](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Poort 8883 is geopend in de firewall. Het voor beeld van het apparaat in deze Snelstartgids maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs-en educatieve netwerk omgevingen. Zie [verbinding maken met IOT hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-extensie toevoegen

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IoT-extensie voegt IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS)-specifieke opdrachten toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyNodeDevice**: dit is de naam van het apparaat dat u wilt registreren. Het is raadzaam om **MyNodeDevice** te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, moet u deze naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Voer de volgende opdracht uit in Azure Cloud Shell om het _apparaat Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde later in de Quick Start.

1. U hebt ook het _Event hubs-compatibele eind punt_, _Event hubs compatibel pad_en de _primaire sleutel_ van de service van uw IOT-hub nodig om de back-endtoepassing in staat te stellen verbinding te maken met uw IOT-hub en de berichten op te halen. Met de volgende opdrachten worden deze waarden opgehaald voor uw IoT-hub:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Noteer deze drie waarden, die u later in de Quick Start gebruikt.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Open een lokaal terminalvenster en ga naar de hoofdmap van het voorbeeldproject in Node.js. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

1. Open het bestand **SimulatedDevice.js** in een teksteditor van uw keuze.

    Vervang de waarde van de `connectionString` variabele door het apparaat Connection String u eerder een notitie hebt gemaakt. Sla de wijzigingen vervolgens op in **SimulatedDevice. js**.

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de toepassing voor het gesimuleerde apparaat uit te voeren:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

De back-endtoepassing maakt verbinding met het eindpunt **Events** aan de servicezijde van uw IoT-hub. De toepassing ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

1. Open een ander lokaal terminalvenster en ga naar de hoofdmap van het voorbeeldproject in Node.js. Navigeer vervolgens naar de map **iot-hub\Quickstarts\read-d2c-messages**.

1. Open het bestand **ReadDeviceToCloudMessages.js** in een teksteditor van uw keuze. Werk de volgende variabelen bij en sla de wijzigingen in het bestand op.

    | Variabele | Waarde |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Vervang de waarde van de variabele door het Event Hubs-compatibele eind punt dat u eerder een notitie hebt gemaakt. |
    | `eventHubsCompatiblePath`     | Vervang de waarde van de variabele door het Event Hubs compatibele pad dat u eerder hebt genoteerd. |
    | `iotHubSasKey`                | Vervang de waarde van de variabele door de primaire sleutel van de service die u eerder hebt genoteerd. |

1. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken te installeren en de back-endtoepassing uit te voeren:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de back-endtoepassing telemetriegegevens ontvangt die door het gesimuleerde apparaat naar de hub zijn verzonden:

    ![De back-endtoepassing uitvoeren](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start kunt u een IoT-hub instellen, een apparaat registreren, gesimuleerde telemetrie naar de hub verzenden met behulp van een node. js-toepassing en de telemetrie van de hub lezen met behulp van een eenvoudige back-end-toepassing.

Ga verder met de volgende snelstartgids als u wilt weten hoe u een gesimuleerd apparaat beheert vanuit een back-endtoepassing.

> [!div class="nextstepaction"]
> [Snelstartgids: Een apparaat beheren dat is verbonden met een IoT-hub](quickstart-control-device-node.md)

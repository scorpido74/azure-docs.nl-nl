---
title: 'Snelstartgids: Telemetrie verzenden naar Azure IoT Hub (C#) | Microsoft Docs'
description: In deze snelstart voert u twee C#-voorbeeldtoepassingen uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en telemetrie van de IoT-hub te lezen voor verwerking in de cloud.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: b1ee14afcf46dfbedfb9d696b6a0add22ccd39cc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769123"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Snelstart: telemetrie verzenden van een apparaat naar een IoT-hub en lezen met een back-end applicatie (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze snelstartgids verzendt u telemetrie vanuit een toepassing voor een gesimuleerd apparaat via IoT Hub naar een back-endtoepassing voor verwerking.

In de snelstartgids worden twee vooraf geschreven C#-toepassingen gebruikt, één voor het verzenden van de telemetrie en één voor het lezen van de telemetrie van de hub. Voordat u deze twee toepassingen kunt uitvoeren, moet u een IoT-hub maken en een apparaat registreren bij de hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

De twee voorbeeldtoepassingen die u uitvoert in deze snelstartgids zijn geschreven in C#. .NET Core SDK 2.1.0 of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer.

U kunt de .NET Core SDK voor meerdere platforms downloaden van [.NET](https://www.microsoft.com/net/download/all).

Gebruik de volgende opdracht om de huidige versie van C# op uw ontwikkelcomputer te controleren:

```cmd/sh
dotnet --version
```

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt specifieke opdrachten voor IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS) toe aan Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Download de Azure IoT [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) C#-voorbeelden uit en haal het ZIP-archief uit.

Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in deze quickstart maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze snelstart gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaatidentiteit te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyDotnetDevice**: Dit is de naam van het apparaat dat u registreert. Het wordt aanbevolen om **MyDotnetDevice** te gebruiken zoals getoond. Als u een andere naam voor uw apparaat kiest, moet u die naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Voer de volgende opdracht uit in Azure Cloud Shell om de _tekenreeks voor apparaatverbinding_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde later in de snelstart.

3. U hebt ook het _eindpunt,_ het pad dat compatibel is met _gebeurtenishubs_en _de primaire servicesleutel_ van uw IoT-hub nodig om de back-endtoepassing in staat te stellen verbinding te maken met uw IoT-hub en de berichten op te halen. Met de volgende opdrachten worden deze waarden opgehaald voor uw IoT-hub:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Noteer deze drie waarden, die u later in de quickstart gebruikt.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verstuurt gesimuleerde telemetrie over temperatuur en luchtvochtigheid.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

2. Open het bestand **SimulatedDevice.cs** in een teksteditor van uw keuze.

    Vervang de waarde `s_connectionString` van de variabele door de verbindingstekenreeks van het apparaat waar u eerder een notitie van hebt gemaakt. Sla uw wijzigingen vervolgens op **in SimulatedDevice.cs.**

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste pakketten te installeren voor de toepassing voor het gesimuleerde apparaat:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in het lokale terminalvenster de volgende opdracht uit om de toepassing voor het gesimuleerde apparaat te compileren en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

De back-endtoepassing maakt verbinding met het eindpunt **Events** aan de servicezijde van uw IoT-hub. De toepassing ontvangt de berichten die van het gesimuleerde apparaat naar de cloud worden verzonden. Een back-endtoepassing van IoT Hub wordt meestal uitgevoerd in de cloud om berichten van apparaat naar cloud te ontvangen en verwerken.

1. Navigeer in een ander lokaal terminalvenster naar de hoofdmap van het voorbeeldproject in C#. Navigeer vervolgens naar de map **iot-hub\Quickstarts\read-d2c-messages**.

2. Open het bestand **ReadDeviceToCloudMessages.cs** in een teksteditor van uw keuze. Werk de volgende variabelen bij en sla de wijzigingen in het bestand op.

    | Variabele | Waarde |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Vervang de waarde van de variabele door het eindpunt dat u eerder hebt notitie van het eindpunt dat u eerder hebt gemaakt. |
    | `s_eventHubsCompatiblePath`     | Vervang de waarde van de variabele door het pad dat compatibel is met gebeurtenishubs waarop u eerder een notitie hebt gemaakt. |
    | `s_iotHubSasKey`                | Vervang de waarde van de variabele door de primaire servicesleutel waar u eerder een notitie van hebt gemaakt. |

3. Voer in het lokale terminalvenster de volgende opdrachten uit om de vereiste bibliotheken voor de back-endtoepassing te installeren:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in het lokale terminalvenster de volgende opdrachten uit om de back-endtoepassing te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de back-endtoepassing telemetriegegevens ontvangt die door het gesimuleerde apparaat naar de hub zijn verzonden:

    ![De back-endtoepassing uitvoeren](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat, verzondu gesimuleerde telemetrie naar de hub met behulp van een C#-toepassing en leest u de telemetrie van de hub met behulp van een eenvoudige back-endtoepassing.

Ga verder met de volgende snelstartgids als u wilt weten hoe u een gesimuleerd apparaat beheert vanuit een back-endtoepassing.

> [!div class="nextstepaction"]
> [Snelstartgids: Een apparaat beheren dat is verbonden met een IoT-hub](quickstart-control-device-dotnet.md)
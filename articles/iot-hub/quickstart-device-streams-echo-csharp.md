---
title: Communiceren met apparaat-app in C# met Azure IoT Hub-apparaatstreams
description: In deze quickstart voert u twee voorbeeld-C#-toepassingen uit die communiceren via een apparaatstream die is ingesteld via IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675527"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snelstart: communiceren met een apparaattoepassing in C# via IoT Hub-apparaatstreams (voorbeeld)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub ondersteunt momenteel apparaatstromen als [voorbeeldfunctie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Deze quickstart omvat twee C#-toepassingen die gebruik maken van apparaatstromen om gegevens heen en weer te sturen (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:
  * VS - centraal
  * Centrale EUAP van de VS
  * Europa - noord
  * Azië - zuidoost

* De twee voorbeeldtoepassingen die u in deze quickstart uitvoert, zijn geschreven in C#. Je hebt de .NET Core SDK 2.1.0 of hoger op je ontwikkelmachine nodig.
  * Download de [.NET Core SDK voor meerdere platforms van .NET](https://www.microsoft.com/net/download/all).
  * Controleer de huidige versie van C# op uw ontwikkelingsmachine met de volgende opdracht:

   ```
   dotnet --version
   ```

* Voeg de Azure IoT-extensie voor Azure CLI toe aan uw Cloud Shell-exemplaar door de volgende opdracht uit te voeren. De IOT-extensie voegt dps-specifieke opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toe aan de Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Download de Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) en haal het ZIP-archief eruit. Je hebt het nodig aan zowel de apparaatkant als de servicekant.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Cloud Shell als u de identiteit van het apparaat wilt maken:

   > [!NOTE]
   > * Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.
   > * Voor de naam van het apparaat dat u registreert, wordt aanbevolen *om MyDevice* te gebruiken zoals weergegeven. Als u een andere naam voor uw apparaat kiest, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Voer de volgende opdracht uit in Cloud Shell om de tekenreeks voor de *verbinding van* het apparaat op te halen voor het apparaat dat u zojuist hebt geregistreerd:

   > [!NOTE]
   > Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Let op de tekenreeks voor geretourneerde apparaatverbinding voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. U hebt ook de *serviceverbindingsreeks* van uw IoT Hub nodig, zodat de toepassing aan de servicezijde verbinding kan maken met uw IoT Hub en een apparaatstream kan opzetten. Met de volgende opdracht haalt u deze waarde voor uw IoT-hub op:

   > [!NOTE]
   > Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Let op de tekenreeks geretourneerde serviceverbinding voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Communiceren tussen het apparaat en de service via apparaatstromen

In deze sectie voert u zowel de toepassing aan de apparaatzijde als de service-side-applicatie uit en communiceert u tussen de twee.

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

Navigeer in een lokaal terminalvenster naar de `iot-hub/Quickstarts/device-streams-echo/service` map in de projectmap met uitgepakte items. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De reeks serviceverbindingen van uw IoT-hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |

Compileren en uitvoeren van de code met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
De toepassing wacht tot de apparaattoepassing beschikbaar is.

> [!NOTE]
> Er treedt een time-out op als de toepassing aan de apparaatzijde niet op tijd reageert.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Navigeer in een ander lokaal `iot-hub/Quickstarts/device-streams-echo/device` terminalvenster naar de map in de projectmap met uitgepakte items. Houd de volgende informatie bij de hand:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | De tekenreeks van de apparaatverbinding van uw IoT-hub. |

Compileren en uitvoeren van de code met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Aan het einde van de laatste stap initieert de service-side applicatie een stream naar uw apparaat. Nadat de stream is ingesteld, stuurt de toepassing een tekenreeksbuffer naar de service via de stream. In dit voorbeeld, de service-side applicatie gewoon echo's terug dezelfde gegevens naar het apparaat, die een succesvolle bidirectionele communicatie tussen de twee toepassingen toont.

Console-uitvoer aan de apparaatzijde:

![Console-uitvoer aan de apparaatzijde](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Console-uitvoer aan de servicezijde:

![Console-uitvoer aan de servicezijde](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Het verkeer dat over de stroom wordt verzonden, wordt via de IoT-hub getunneld in plaats van rechtstreeks te worden verzonden. De voordelen worden beschreven in [de voordelen voor apparaatstromen.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat, vestigde u een apparaatstroom tussen C#-toepassingen op het apparaat en de servicezijde en gebruikt u de stroom om gegevens heen en weer te sturen tussen de toepassingen.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

---
title: Communiceren met een apparaat-app C# in via Azure IOT hub Device streams (preview) | Microsoft Docs
description: In deze Quick Start voert u twee voorbeeld C# toepassingen uit die communiceren via een apparaat stroom die is ingesteld via IOT hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 85905f97500848be0e17da7d8a65209878713fc2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516526"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snelstartgids: communiceren met een apparaat-app C# in via IOT hub Device streams (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub ondersteunt momenteel het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. In deze Quick Start C# worden twee toepassingen uitgevoerd die gebruikmaken van de streams van apparaten om gegevens heen en weer te sturen (ECHO).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:
  * VS - centraal
  * Centrale VS-EUAP

* De twee voorbeeld toepassingen die u in deze Quick start uitvoert, zijn C#geschreven in. U hebt de .NET Core SDK 2.1.0 of hoger nodig op uw ontwikkel computer.
  * Down load de [.net core SDK voor meerdere platforms van .net](https://www.microsoft.com/net/download/all).
  * Controleer de huidige versie van C# op uw ontwikkel computer met behulp van de volgende opdracht:

   ```
   dotnet --version
   ```

* Voeg de Azure IoT-extensie voor Azure CLI toe aan uw Cloud Shell-exemplaar door de volgende opdracht uit te voeren. De IOT-extensie voegt IoT Hub, IoT Edge en IoT-specifieke opdrachten (Device Provisioning Service) toe aan de Azure CLI.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Down load het C# voorbeeld project](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) en pak het zip-archief uit. U hebt deze zowel aan de kant van het apparaat als aan de kant van de service nodig.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Als u de apparaat-id wilt maken, voert u de volgende opdracht uit in Cloud Shell:

   > [!NOTE]
   > * Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.
   > * Voor de naam van het apparaat dat u wilt registreren, is het raadzaam om *mijn* te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Als u de *apparaat Connection String* wilt ophalen voor het apparaat dat u zojuist hebt geregistreerd, voert u de volgende opdracht uit in Cloud shell:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bekijk de geretourneerde apparaat connection string voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. U hebt ook de *serviceverbindingsreeks* van uw IoT Hub nodig, zodat de toepassing aan de servicezijde verbinding kan maken met uw IoT Hub en een apparaatstream kan opzetten. Met de volgende opdracht haalt u deze waarde voor uw IoT-hub op:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Bekijk de geretourneerde service connection string voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Communiceren tussen het apparaat en de service via Device streams

In deze sectie voert u zowel de toepassing op het apparaat als de toepassing aan de service zijde uit en communiceert u tussen de twee toepassingen.

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

Ga in een lokaal Terminal venster naar de map `iot-hub/Quickstarts/device-streams-echo/service` in de map ungezipte project. Zorg ervoor dat u de volgende informatie bij de hand hebt:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De service connection string van uw IoT-hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |

Compileer de code en voer deze uit met de volgende opdrachten:

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
De toepassing wordt gewacht totdat de Device-toepassing beschikbaar wordt.

> [!NOTE]
> Er treedt een time-out op als de toepassing aan de apparaatzijde niet op tijd reageert.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Navigeer in een ander lokaal Terminal venster naar de map `iot-hub/Quickstarts/device-streams-echo/device` in de map ungezipte project. Zorg ervoor dat u de volgende informatie bij de hand hebt:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | Het apparaat connection string van uw IoT Hub. |

Compileer de code en voer deze uit met de volgende opdrachten:

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

Aan het einde van de laatste stap initieert de toepassing aan de service zijde een stroom naar uw apparaat. Nadat de stroom is gemaakt, verzendt de toepassing een teken reeks buffer naar de service via de stroom. In dit voor beeld wordt de toepassing aan de service zijde eenvoudigweg teruggestuurd naar dezelfde gegevens op het apparaat. Dit illustreert een geslaagde bidirectionele communicatie tussen de twee toepassingen.

Console-uitvoer aan de kant van het apparaat:

![Console-uitvoer aan de kant van het apparaat](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Console-uitvoer aan de kant van de service:

![Console-uitvoer aan de service zijde](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Het verkeer dat via de stroom wordt verzonden, wordt via de IoT-hub getunneld en niet rechtstreeks verzonden. De voor delen worden beschreven in de [voor delen van Device streams](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick start gaat u een IoT-hub instellen, een apparaat registreren, een apparaat stroom tot C# stand brengen tussen toepassingen op het apparaat en de service zijde en de stream gebruiken om gegevens heen en weer te sturen tussen de toepassingen.

Zie voor meer informatie over het streamen van apparaten:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

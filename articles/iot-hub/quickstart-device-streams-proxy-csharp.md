---
title: Azure-IoT Hub Device streams C# Quick start voor SSH en RDP
description: In deze Quick Start hebt u twee voor beelden van C#-toepassingen die SSH-en RDP-scenario's voor een IoT Hub apparaat stroom mogelijk maken.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 97551ac63066f7064c16a001d9ce1f6bc31465ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80586588"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Quick Start: SSH en RDP via een IoT Hub Device stream inschakelen met behulp van een C#-proxy toepassing (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub biedt momenteel ondersteuning voor het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. In deze snelstartgids vindt u twee C#-toepassingen die het verkeer van client-server toepassingen (zoals beveiligde Shell [SSH] en Remote Desktop Protocol [RDP] mogelijk maken via een apparaat stroom die is ingesteld via een IoT-hub. Zie voor een overzicht van de installatie de [voor beeld van een lokale proxy toepassing voor SSH of RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

In dit artikel wordt eerst de installatie van SSH (met behulp van poort 22) beschreven en wordt beschreven hoe u de installatie poort van RDP wijzigt. Omdat apparaatversleuteling een toepassings-en protocol-neutraal zijn, kan hetzelfde voor beeld worden gewijzigd om andere soorten toepassings verkeer te kunnen verwerken. Bij deze wijziging wordt doorgaans alleen de communicatie poort gewijzigd die wordt gebruikt door de beoogde toepassing.

## <a name="how-it-works"></a>Hoe werkt het?

In de volgende afbeelding ziet u hoe de lokale en service-Local proxy toepassingen in dit voor beeld end-to-end-connectiviteit mogelijk maken tussen de SSH-client en SSH-daemon-processen. We gaan ervan uit dat de daemon wordt uitgevoerd op hetzelfde apparaat als de toepassing voor de lokale proxy van het apparaat.

![Installatie van de lokale proxy toepassing](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. De service-Local proxy-toepassing maakt verbinding met de IoT-hub en initieert een apparaat stroom naar het doel apparaat.

1. De apparaat-Local proxy-toepassing voltooit de stream-initiatie-Handshake en brengt een end-to-end streaming-tunnel tot stand via het streaming-eind punt van de IoT-hub aan de service zijde.

1. De apparaat-lokale proxy toepassing maakt verbinding met de SSH-daemon die luistert op poort 22 op het apparaat. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie ' de toepassing apparaat-lokale proxy uitvoeren '.

1. De service-Local proxy-toepassing wacht op nieuwe SSH-verbindingen van een gebruiker door op een aangewezen poort te Luis teren, in dit geval poort 2222. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie ' de service-lokale proxy toepassing uitvoeren '. Wanneer de gebruiker verbinding maakt via de SSH-client, kan de tunnel SSH-toepassings verkeer laten overzetten tussen de SSH-client en de server toepassing.

> [!NOTE]
> SSH-verkeer dat via een apparaat stroom wordt verzonden, wordt via het streaming-eind punt van de IoT-hub getunneld in plaats van rechtstreeks naar de service en het apparaat te verzenden. Zie de [voor delen van het gebruik van IOT Hub-apparaten](iot-hub-device-streams-overview.md#benefits)voor meer informatie.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale VS-EUAP
  * Azië - zuidoost
  * Europa - noord

* De twee voorbeeld toepassingen die u in deze Quick start uitvoert, zijn geschreven in C#. U hebt de .NET Core SDK 2.1.0 of hoger nodig op uw ontwikkel computer.

  U kunt de [.net core SDK voor meerdere platforms downloaden van .net](https://www.microsoft.com/net/download/all).

* Controleer de huidige versie van C# op uw ontwikkel computer met behulp van de volgende opdracht:

    ```
    dotnet --version
    ```

* Voer de volgende opdracht uit om de Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt IoT Hub, IoT Edge en IoT-specifieke opdrachten (Device Provisioning Service) toe aan de Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Down load de Azure IOT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)-voor beelden en pak het zip-archief uit.

* Een geldig gebruikers account en referenties op het apparaat (Windows of Linux) dat wordt gebruikt om de gebruiker te verifiëren.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze Quick Start gebruikt u Azure Cloud Shell om een gesimuleerd apparaat te registreren.

1. Als u de apparaat-id wilt maken, voert u de volgende opdracht uit in Cloud Shell:

   > [!NOTE]
   > * Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.
   > * Voor de naam van het apparaat dat u wilt registreren, is het raadzaam om *mijn* te gebruiken zoals wordt weer gegeven. Als u een andere naam kiest voor uw apparaat, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Als u de *apparaat Connection String* wilt ophalen voor het apparaat dat u zojuist hebt geregistreerd, voert u de volgende opdrachten uit in Cloud shell:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bekijk de geretourneerde apparaat connection string voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. Als u verbinding wilt maken met uw IoT-hub en een apparaat stroom wilt maken, hebt u ook de *service connection string* van uw IOT-hub nodig om de toepassing aan de service zijde in te scha kelen. Met de volgende opdracht haalt u deze waarde voor uw IoT-hub op:

   > [!NOTE]
   > Vervang de tijdelijke aanduiding *YourIoTHubName* door de naam die u hebt gekozen voor uw IOT-hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Bekijk de geretourneerde service connection string voor later gebruik in deze Quick Start. Het lijkt op het volgende voorbeeld:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stroom voor het tunnelen van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

Navigeer in een lokaal Terminal venster naar de `device-streams-proxy/device` map in de map ungezipte project. Zorg ervoor dat u de volgende informatie bij de hand hebt:

| Argumentnaam | Argumentwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | Het apparaat connection string van het apparaat dat u eerder hebt gemaakt. |
| `targetServiceHostName` | Het IP-adres waarnaar de SSH-server luistert. `localhost` Als dit het geval is, is het adres hetzelfde als dat van de lokale proxy toepassing voor het apparaat wordt uitgevoerd. |
| `targetServicePort` | De poort die wordt gebruikt door uw toepassings Protocol (voor SSH, is standaard poort 22).  |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

Navigeer in een ander lokaal Terminal venster naar `iot-hub/quickstarts/device-streams-proxy/service` in de map ungezipte project. Zorg ervoor dat u de volgende informatie bij de hand hebt:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT Hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |
| `localPortNumber` | Een lokale poort waarmee uw SSH-client verbinding maakt. In dit voor beeld gebruiken we poort 2222, maar u kunt andere wille keurige getallen gebruiken. |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>De SSH-client uitvoeren

Gebruik nu uw SSH-client toepassing en maak verbinding met de service-lokale proxy toepassing op poort 2222 (in plaats van de SSH-daemon rechtstreeks).

```
ssh {username}@localhost -p 2222
```

Op dit moment vraagt het SSH-aanmeldings venster u uw referenties in.

Console-uitvoer aan de service zijde (de service-Local proxy-toepassing luistert op poort 2222):

![Service-lokale uitvoer van proxy toepassing](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Console-uitvoer op de apparaat-lokale proxy toepassing, die verbinding maakt met de SSH-daemon op *iP_address: 22*:

![Apparaat-lokale proxy toepassing uitvoer](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Console-uitvoer van de SSH-client toepassing. De SSH-client communiceert met de SSH-daemon door verbinding te maken met poort 22, die de service-Local proxy toepassing luistert naar:

![Uitvoer van SSH-client toepassing](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP naar een apparaat via apparaatstreams

De instellingen voor RDP zijn vergelijkbaar met de installatie van SSH (zie hierboven). U gebruikt de RDP-doel-IP en poort 3389 in plaats daarvan en gebruikt u de RDP-client (in plaats van de SSH-client).

### <a name="run-the-device-local-proxy-application-rdp"></a>Het apparaat-Local proxy Application (RDP) uitvoeren

Navigeer in een lokaal Terminal venster naar de `device-streams-proxy/device` map in de map ungezipte project. Zorg ervoor dat u de volgende informatie bij de hand hebt:

| Argumentnaam | Argumentwaarde |
|----------------|-----------------|
| `DeviceConnectionString` | Het apparaat connection string van het apparaat dat u eerder hebt gemaakt. |
| `targetServiceHostName` | De hostnaam of het IP-adres waarop de RDP-server wordt uitgevoerd. `localhost` Als dit het geval is, is het adres hetzelfde als dat van de lokale proxy toepassing voor het apparaat wordt uitgevoerd. |
| `targetServicePort` | De poort die wordt gebruikt door uw toepassings Protocol (voor RDP, standaard is dit poort 3389).  |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>De service-Local proxy Application (RDP) uitvoeren

Navigeer in een ander lokaal Terminal venster naar `device-streams-proxy/service` in de map ungezipte project. Zorg ervoor dat u de volgende informatie bij de hand hebt:

| Parameternaam | Parameterwaarde |
|----------------|-----------------|
| `ServiceConnectionString` | De serviceverbindingsreeks van uw IoT Hub. |
| `MyDevice` | De id van het apparaat dat u eerder hebt gemaakt. |
| `localPortNumber` | Een lokale poort waarmee uw SSH-client verbinding maakt. In dit voorbeeld gebruiken we poort 2222, maar u kunt dat wijzigen in willekeurige andere getallen. |

Compileer de code en voer deze uit met de volgende opdrachten:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP-client uitvoeren

Gebruik nu uw RDP-client toepassing en maak verbinding met de service-lokale proxy toepassing op poort 2222 (dit was een wille keurige beschik bare poort die u eerder hebt gekozen).

![RDP maakt verbinding met de service-lokale proxy toepassing](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start stelt u een IoT-hub in, registreert u een apparaat, het geïmplementeerde apparaat-lokale en service-Local proxy toepassingen om een apparaatgegevens te maken via de IoT-hub en de proxy toepassingen te gebruiken om SSH-of RDP-verkeer te tunnelen. Hetzelfde paradigma kan worden aangepast aan andere client-server protocollen, waarbij de server op het apparaat wordt uitgevoerd (bijvoorbeeld de SSH-daemon).

Zie voor meer informatie over het streamen van apparaten:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

---
title: Azure IoT Hub Device streams C Quick start voor SSH en RDP
description: In deze Quick Start voert u een voorbeeld toepassing C uit die fungeert als een proxy voor het inschakelen van SSH-en RDP-scenario's via IoT Hub apparaten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 03b0269b1a4500fd8ae26cd5e56f48427c5506aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429186"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snelstartgids: SSH en RDP via een stroom van een IoT Hub apparaat inschakelen met behulp van een C-proxy toepassing (preview)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub ondersteunt momenteel het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Zie [de voorbeeld pagina van de lokale proxy](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)voor een overzicht van de installatie.

In deze Quick Start wordt de configuratie beschreven voor het gebruik van trans porting Secure Shell (SSH)-verkeer (via poort 22) via apparaat stromen. Het Setup-verkeer voor Remote Desktop Protocol (RDP) is vergelijkbaar en vereist een eenvoudige configuratie wijziging. Omdat apparaatversleuteling het toepassings-en protocol-neutraal zijn, kunt u deze Snelstartgids aanpassen voor andere soorten toepassings verkeer.

## <a name="how-it-works"></a>Het werkt als volgt

In de volgende afbeelding ziet u hoe de lokale proxy Programma's van het apparaat en de service end-to-end-connectiviteit mogelijk maken tussen de SSH-client en SSH-daemon-processen. Tijdens de open bare preview ondersteunt de C SDK alleen apparaatnamen op het apparaat. Als gevolg hiervan wordt in deze Snelstartgids beschreven hoe u alleen de apparaat-Local proxy toepassing uitvoert. Volg de instructies in een van de volgende Quick starts om de bijbehorende service-side toepassing te bouwen en uit te voeren:

* [SSH/RDP via IoT Hub Device streams C# met behulp van proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP via IOT hub-NodeJS-proxy](./quickstart-device-streams-proxy-nodejs.md).

![Lokale proxy instellen](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. De service-Local proxy maakt verbinding met de IoT-hub en start een stroom van het apparaat naar het doel apparaat.

2. De apparaat-Local proxy voltooit de stream-initiatie-Handshake en brengt een end-to-end streaming-tunnel tot stand via het streaming-eind punt van de IoT-hub aan de service zijde.

3. De apparaat-Local proxy maakt verbinding met de SSH-daemon die luistert op poort 22 op het apparaat. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie ' de toepassing apparaat-lokale proxy uitvoeren '.

4. De service-Local proxy wacht op nieuwe SSH-verbindingen van een gebruiker door op een aangewezen poort te Luis teren, in dit geval poort 2222. Deze instelling kan worden geconfigureerd, zoals beschreven in de sectie ' de toepassing apparaat-lokale proxy uitvoeren '. Wanneer de gebruiker verbinding maakt via een SSH-client, kan de tunnel SSH-toepassings verkeer tussen de SSH-client en server Programma's overdragen.

> [!NOTE]
> SSH-verkeer dat via een apparaat stroom wordt verzonden, wordt via het streaming-eind punt van de IoT-hub getunneld in plaats van rechtstreeks naar de service en het apparaat te verzenden. Zie de [voor delen van het gebruik van IOT Hub-apparaten](iot-hub-device-streams-overview.md#benefits)voor meer informatie. Daarnaast illustreert de afbeelding de SSH-daemon die op hetzelfde apparaat (of dezelfde computer) wordt uitgevoerd als de lokale proxy van het apparaat. In deze Quick Start biedt het gebruik van het IP-adres van de SSH-daemon mogelijk dat de apparaat-Local proxy en de daemon ook op verschillende computers kunnen worden uitgevoerd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale VS-EUAP
  * Europa - noord
  * Azië - zuidoost

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/) met de [Desktop ontwikkeling waarop C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) de werk belasting is ingeschakeld.
* Installeer de meest recente versie van [Git](https://git-scm.com/download/).

* Voer de volgende opdracht uit om de Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt IoT Hub, IoT Edge en IoT-specifieke opdrachten (Device Provisioning Service) toe aan de Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze Quick Start gebruikt u de [Azure IOT Device SDK voor C](iot-hub-device-sdk-c-intro.md). U bereidt een ontwikkel omgeving voor die wordt gebruikt om de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) te klonen en te bouwen vanuit github. De SDK op GitHub bevat de voorbeeld code die wordt gebruikt in deze Quick Start.

1. Down load het [cmake build-systeem](https://cmake.org/download/).

    Het is belang rijk dat de Visual Studio-vereisten (Visual Studio en de *ontwikkeling van het C++ bureau blad met* werk belasting) op uw computer zijn geïnstalleerd *voordat* u de cmake-installatie start. Nadat de vereiste onderdelen zijn geïnstalleerd en de down load is gecontroleerd, kunt u het CMake build-systeem installeren.

1. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats te klonen:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten duren.

1. Maak een *cmake* -submap in de hoofdmap van de Git-opslag plaats en navigeer naar die map. Voer de volgende opdrachten uit vanuit de map *Azure-IOT-SDK-c* :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit in de *cmake* -Directory om een versie van de SDK te bouwen die specifiek is voor uw ontwikkelings-client platform.

   * In Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Voer in Windows de volgende opdrachten uit in de opdracht prompt voor ontwikkel aars voor Visual Studio 2015 of 2017. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de *cmake* -map.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell met de [IOT-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om een gesimuleerd apparaat te registreren.

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

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In deze sectie maakt u een end-to-end-stroom voor het tunnelen van SSH-verkeer.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

1. Bewerk het bron bestand **iothub_client_c2d_streaming_proxy_sample. c** in de map `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`en geef uw apparaat de Connection String, het IP-adres of de hostnaam van het doel apparaat en de SSH-poort 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Het voor beeld compileren:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Voer het gecompileerde programma uit op het apparaat:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Voer de proxytoepassing in de service uit

Zoals beschreven in de sectie ' Hoe werkt het? ', moet een end-to-end-stroom voor het maken van een tunnel-SSH-verkeer een lokale proxy aan elk end (op zowel de service als aan de zijden van het apparaat). Tijdens de open bare preview ondersteunt de IoT Hub C SDK alleen Device streams op het apparaat. Als u de service-Local proxy wilt bouwen en uitvoeren, volgt u de instructies in een van de volgende Quick starts:

   * [SSH/RDP via IoT Hub Device streams C# met behulp van proxy-apps](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP via IoT Hub-apparaten met behulp van node. js-proxy-apps](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Een SSH-sessie tot stand brengen

Nadat de apparaat-en service-Local proxy's worden uitgevoerd, gebruikt u uw SSH-client programma en maakt u verbinding met de service-Local proxy op poort 2222 (in plaats van de SSH-daemon rechtstreeks).

```cmd/sh
ssh {username}@localhost -p 2222
```

Op dit moment vraagt het SSH-aanmeldings venster u uw referenties in.

In de volgende afbeelding ziet u de console-uitvoer op de apparaat-Local proxy, waarmee verbinding wordt gemaakt met de SSH-daemon op `IP_address:22`:

![Apparaat-lokale proxy-uitvoer](./media/quickstart-device-streams-proxy-c/device-console-output.png)

De volgende afbeelding toont de console-uitvoer van het SSH-client programma. De SSH-client communiceert met de SSH-daemon door verbinding te maken met poort 22, waar de service-Local proxy luistert:

![SSH-client uitvoer](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids stelt u een IoT-hub in, registreert u een apparaat, implementeert u een apparaat-en een service-Local proxy programma om een apparaat stroom te maken via IoT Hub, en gebruikt u de proxy's om SSH-verkeer te tunnelen.

Zie voor meer informatie over het streamen van apparaten:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

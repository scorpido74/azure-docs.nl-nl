---
title: Azure IoT Hub-apparaat streamt C-quickstart voor SSH en RDP
description: In deze quickstart voert u een voorbeeld-C-toepassing uit die fungeert als een proxy om SSH- en RDP-scenario's in te schakelen via IoT Hub-apparaatstreams.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 91cf594e74d91b8b4df2108d23459016fa843700
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586600"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Snelstart: SSH en RDP inschakelen via een IoT Hub-apparaatstream met behulp van een C-proxytoepassing (voorbeeld)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT Hub ondersteunt momenteel apparaatstromen als [voorbeeldfunctie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub-apparaatstreams](./iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Zie de pagina Lokaal [proxyvoorbeeld](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)voor een overzicht van de installatie.

Deze quickstart beschrijft de setup voor het tunnelen van Secure Shell (SSH) verkeer (met behulp van poort 22) via apparaatstreams. De installatie voor RDP-verkeer (Remote Desktop Protocol) is vergelijkbaar en vereist een eenvoudige configuratiewijziging. Omdat apparaatstreams toepassings- en protocolagnostisch zijn, u deze quickstart wijzigen om andere soorten toepassingsverkeer mogelijk te maken.

## <a name="how-it-works"></a>Hoe werkt het?

De volgende figuur illustreert hoe de apparaat- en service-lokale proxyprogramma's end-to-end connectiviteit tussen de SSH-client en SSH daemon-processen mogelijk maken. Tijdens openbare preview ondersteunt de C SDK alleen apparaatstreams aan de apparaatzijde. Als gevolg hiervan heeft deze quickstart instructies nodig om alleen de apparaatlokale proxy-toepassing uit te voeren. Als u de bijbehorende service-side applicatie wilt bouwen en uitvoeren, volgt u de instructies in een van de volgende snelstarts:

* [SSH/RDP via IoT Hub-apparaatstreams met C#-proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP via IoT Hub-apparaatstreams met nodeJS-proxy](./quickstart-device-streams-proxy-nodejs.md).

![Lokale proxy-instelling](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. De service-lokale proxy maakt verbinding met de IoT-hub en start een apparaatstream naar het doelapparaat.

2. De apparaat-lokale proxy voltooit de stroominitiatiehandshake en stelt een end-to-end streamingtunnel in via het streaming-eindpunt van de IoT-hub aan de servicezijde.

3. De apparaat-lokale proxy maakt verbinding met de SSH daemon die luistert op poort 22 op het apparaat. Deze instelling is configureerbaar, zoals beschreven in de sectie 'Voer de apparaat-lokale proxy-toepassing uit'.

4. De service-lokale proxy wacht op nieuwe SSH-verbindingen van een gebruiker door te luisteren op een aangewezen poort, die in dit geval poort 2222 is. Deze instelling is configureerbaar, zoals beschreven in de sectie 'Voer de apparaat-lokale proxy-toepassing uit'. Wanneer de gebruiker verbinding maakt via ssh-client, maakt de tunnel het mogelijk om SSH-applicatieverkeer over te zetten tussen de SSH-client en serverprogramma's.

> [!NOTE]
> SSH-verkeer dat via een apparaatstream wordt verzonden, wordt door het streaming-eindpunt van de IoT-hub getunneld in plaats van rechtstreeks tussen service en apparaat verzonden. Zie voor meer informatie de [voordelen van het gebruik van Iot Hub-apparaatstreams.](iot-hub-device-streams-overview.md#benefits) Bovendien illustreert de figuur de SSH-daemon die op hetzelfde apparaat (of dezelfde machine) draait als de apparaatlokale proxy. In deze quickstart, het verstrekken van de SSH daemon IP-adres kan het apparaat-lokale proxy en de daemon te draaien op verschillende machines ook.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale EUAP van de VS
  * Europa - noord
  * Azië - zuidoost

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/) met de [desktopontwikkeling met C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload ingeschakeld.
* Installeer de meest recente versie van [Git](https://git-scm.com/download/).

* Voer de volgende opdracht uit om de Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt dps-specifieke opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toe aan de Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze quickstart gebruikt u de [Azure IoT-apparaat SDK voor C](iot-hub-device-sdk-c-intro.md). U bereidt een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) van GitHub te klonen en te bouwen. De SDK op GitHub bevat de voorbeeldcode die wordt gebruikt in deze quickstart.

1. Download het [CMake-buildsysteem.](https://cmake.org/download/)

    Het is belangrijk dat de vereisten voor Visual Studio (Visual Studio en de *desktopontwikkeling met C++* workload) op uw machine worden *geïnstalleerd, voordat* u de CMake-installatie start. Nadat de vereisten zijn ingevoerd en de download is geverifieerd, u het CMake-buildsysteem installeren.

1. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository te klonen:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking duurt enkele minuten.

1. Maak een *cmake-submap* in de hoofdmap van de git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten uit de map *azure-iot-sdk-c* uit:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit de *cmake-map* uit om een versie van de SDK te bouwen die specifiek is voor uw ontwikkelclientplatform.

   * In Linux:

      ```bash
      cmake ..
      make -j
      ```

   * Voer in Windows de volgende opdrachten uit in Opdrachtvoor ontwikkelaars voor Visual Studio 2015 of 2017. Een Visual Studio-oplossing voor het gesimuleerde apparaat wordt gegenereerd in de *cmake-map.*

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

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell met de [IoT-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Cloud Shell als u de identiteit van het apparaat wilt maken:

   > [!NOTE]
   > * Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.
   > * Voor de naam van het apparaat dat u registreert, wordt aanbevolen *om MyDevice* te gebruiken zoals weergegeven. Als u een andere naam voor uw apparaat kiest, gebruikt u die naam in dit artikel en werkt u de apparaatnaam bij in de voorbeeldtoepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Voer de volgende opdrachten uit in Cloud Shell om de *tekenreeks voor apparaatverbinding voor* het apparaat dat u zojuist hebt geregistreerd, uit te voeren:

   > [!NOTE]
   > Vervang de *Placeholder YourIoTHubName* door de naam die u voor uw IoT-hub hebt gekozen.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Let op de tekenreeks voor geretourneerde apparaatverbinding voor later gebruik in deze quickstart. Het lijkt op het volgende voorbeeld:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH naar een apparaat via apparaatstreams

In dit gedeelte stel je een end-to-end stroom vast om ssh-verkeer te tunnelen.

### <a name="run-the-device-local-proxy-application"></a>Voer de proxytoepassing in het apparaat uit

1. Bewerk het bronbestand **iothub_client_c2d_streaming_proxy_sample.c** in de map `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`en geef de tekenreeks van uw apparaatverbinding, ip/hostnaam van het doelapparaat en de SSH-poort 22 op:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Stel het monster samen:

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

Zoals besproken in de sectie "Hoe het werkt", vereist het instellen van een end-to-end stream naar tunnel SSH-verkeer aan elk uiteinde (aan beide einden (aan zowel de service als de apparaatzijden). Tijdens openbare preview ondersteunt de IoT Hub C SDK alleen apparaatstreams aan de apparaatzijde. Als u de service-lokale proxy wilt bouwen en uitvoeren, volgt u de instructies in een van de volgende snelstarts:

   * [SSH/RDP via IoT Hub-apparaatstreams met C# proxy-apps](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP via IoT Hub-apparaatstreams met node.js-proxy-apps](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Een SSH-sessie tot stand brengen

Nadat zowel de apparaat- als service-lokale proxy's zijn uitgevoerd, gebruikt u uw SSH-clientprogramma en maakt u rechtstreeks verbinding met de service-lokale proxy op poort 2222 (in plaats van de SSH-daemon).

```cmd/sh
ssh {username}@localhost -p 2222
```

Op dit punt vraagt het SSH-aanmeldingsvenster u om uw referenties in te voeren.

De volgende afbeelding toont de console-uitvoer op de apparaat-lokale `IP_address:22`proxy, die verbinding maakt met de SSH-daemon op :

![Apparaatlokale proxy-uitvoer](./media/quickstart-device-streams-proxy-c/device-console-output.png)

De volgende afbeelding toont de console-uitvoer van het SSH-clientprogramma. De SSH-client communiceert met de SSH daemon door verbinding te maken met poort 22, waarop de service-local proxy luistert:

![SSH-clientuitvoer](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat, zette u een apparaat- en een servicelokaal proxyprogramma in om een apparaatstroom via IoT Hub tot stand te brengen en gebruikte u de proxy's om SSH-verkeer te tunnelen.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

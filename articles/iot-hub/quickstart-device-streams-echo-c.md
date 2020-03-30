---
title: Communiceren met apparaat-app in C met Azure IoT Hub-apparaatstreams
description: In deze quickstart voert u een C-apparaat-side applicatie uit die communiceert met een IoT-apparaat via een apparaatstream.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675531"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snelstart: communiceren met een apparaattoepassing in C via IoT Hub-apparaatstreams (voorbeeld)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub ondersteunt momenteel apparaatstromen als [voorbeeldfunctie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[IoT Hub-apparaatstreams](iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Tijdens openbare preview ondersteunt de C SDK alleen apparaatstreams aan de apparaatzijde. Als gevolg hiervan dekt deze quickstart instructies om alleen de toepassing aan de apparaatzijde uit te voeren. Zie de volgende artikelen om een bijbehorende service-side applicatie uit te voeren:

* [Communiceren met apparaat-apps in C# via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-csharp.md)

* [Communiceren met apparaat-apps in Node.js via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-nodejs.md)

De C-toepassing aan de apparaatzijde in deze quickstart heeft de volgende functionaliteit:

* Een apparaatstream naar een IoT-apparaat tot stand brengen.

* Ontvang gegevens die vanuit de service-side applicatie worden verzonden en sluit deze weer.

De code toont het initiatieproces van een apparaatstream en hoe u deze gebruiken om gegevens te verzenden en te ontvangen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende voorwaarden nodig:

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/) met de **desktopontwikkeling met C++** workload ingeschakeld.

* Installeer de meest recente versie van [Git](https://git-scm.com/download/).

* Voer de volgende opdracht uit om de Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt dps-specifieke opdrachten (IoT Hub, IoT Edge en IoT Device Provisioning Service) toe aan de Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

De preview van apparaatstreams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale EUAP van de VS
  * Europa - noord
  * Azië - zuidoost

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze quickstart gebruikt u de [Azure IoT-apparaat SDK voor C](iot-hub-device-sdk-c-intro.md). U bereidt een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) van GitHub te klonen en te bouwen. De SDK op GitHub bevat de voorbeeldcode die wordt gebruikt in deze quickstart.

   > [!NOTE]
   > Voordat u met deze procedure begint, moet u ervoor zorgen dat Visual Studio is geïnstalleerd met de **bureaubladontwikkeling met C++-werkbelasting.**

1. Installeer het [CMake-buildsysteem](https://cmake.org/download/) zoals beschreven op de downloadpagina.

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

   * Open in Windows een [opdrachtvoor ontwikkelaars voor Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Voer de opdracht uit voor uw versie van Visual Studio. Deze quickstart maakt gebruik van Visual Studio 2019. Met deze opdrachten wordt een Visual Studio-oplossing gemaakt voor het gesimuleerde apparaat in de *cmake-map.*

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

U moet een apparaat registreren bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u Azure Cloud Shell met de [IoT-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om een gesimuleerd apparaat te registreren.

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

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Communiceren tussen het apparaat en de service via apparaatstromen

In deze sectie voert u zowel de toepassing aan de apparaatzijde als de service-side-applicatie uit en communiceert u tussen de twee.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Voer de volgende stappen uit om de toepassing aan de apparaatzijde uit te voeren:

1. Geef uw apparaatreferenties op door het **bronbestand iothub_client_c2d_streaming_sample.c** in de `iothub_client/samples/iothub_client_c2d_streaming_sample` map te bewerken en uw apparaatverbindingstekenreeks toe te voegen.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Compileer de code met de volgende opdrachten:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Voer het gecompileerde programma uit:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>De toepassing aan de servicezijde uitvoeren

Zoals eerder vermeld, ondersteunt de IoT Hub C SDK alleen apparaatstreams aan de apparaatzijde. Als u de bijbehorende service-side applicatie wilt bouwen en uitvoeren, volgt u de instructies in een van de volgende snelstarts:

* [Communiceren met een apparaat-app in C# via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-csharp.md)

* [Communiceren met een apparaat-app in Node.js via IoT Hub-apparaatstreams](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart stelt u een IoT-hub in, registreerde u een apparaat, vestigde u een apparaatstroom tussen een C-toepassing op het apparaat en een andere toepassing aan de servicezijde en gebruikte u de stream om gegevens heen en weer te sturen tussen de toepassingen.

Zie voor meer informatie over apparaatstreams:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

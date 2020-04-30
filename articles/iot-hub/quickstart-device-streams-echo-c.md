---
title: Communiceren met de app apparaat in C met Azure IoT Hub Device streams
description: In deze Snelstartgids voert u een C-toepassing voor apparaten uit die met een IoT-apparaat communiceert via een apparaatgegevens stroom.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78675531"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Snelstartgids: communiceren met een apparaat-app in C via IoT Hub Device streams (preview)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub ondersteunt momenteel het streamen van apparaten als een [Preview-functie](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[IoT Hub-apparaatstreams](iot-hub-device-streams-overview.md) zorgen ervoor dat service- en apparaattoepassingen kunnen communiceren op een beveiligde manier die de firewall toestaat. Tijdens de open bare preview ondersteunt de C SDK alleen apparaatnamen op het apparaat. Als gevolg hiervan wordt in deze Snelstartgids beschreven hoe u alleen de toepassing aan het apparaat uitvoert. Als u een bijbehorende toepassing aan de service zijde wilt uitvoeren, raadpleegt u de volgende artikelen:

* [Communiceren met apparaat-apps in C# via IoT Hub Device streams](./quickstart-device-streams-echo-csharp.md)

* [Communiceren met apparaat-apps in node. js via IoT Hub-streams](./quickstart-device-streams-echo-nodejs.md)

De C-toepassing aan de apparaatzijde in deze quickstart heeft de volgende functionaliteit:

* Een apparaatstream naar een IoT-apparaat tot stand brengen.

* Gegevens ontvangen die worden verzonden vanuit de toepassing aan de service zijde en deze terugsturen.

De code demonstreert het start proces van een apparaat stroom en hoe dit kan worden gebruikt om gegevens te verzenden en te ontvangen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende vereisten nodig:

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/) met de **bureau blad-ontwikkeling met** de werk belasting C++ ingeschakeld.

* Installeer de meest recente versie van [Git](https://git-scm.com/download/).

* Voer de volgende opdracht uit om de Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt IoT Hub, IoT Edge en IoT-specifieke opdrachten (Device Provisioning Service) toe aan de Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

De preview van Device streams wordt momenteel alleen ondersteund voor IoT-hubs die in de volgende regio's zijn gemaakt:

  * VS - centraal
  * Centrale VS-EUAP
  * Europa - noord
  * Azië - zuidoost

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze Quick Start gebruikt u de [Azure IOT Device SDK voor C](iot-hub-device-sdk-c-intro.md). U bereidt een ontwikkel omgeving voor die wordt gebruikt om de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) te klonen en te bouwen vanuit github. De SDK op GitHub bevat de voorbeeld code die wordt gebruikt in deze Quick Start.

   > [!NOTE]
   > Voordat u met deze procedure begint, moet u ervoor zorgen dat Visual Studio is geïnstalleerd met de werk belasting van het **bureau blad met C++** .

1. Installeer het [cmake build-systeem](https://cmake.org/download/) zoals beschreven op de download pagina.

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

   * Open in Windows een [opdracht prompt voor ontwikkel aars voor Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Voer de opdracht uit voor uw versie van Visual Studio. Deze Snelstartgids maakt gebruik van Visual Studio 2019. Met deze opdrachten maakt u een Visual Studio-oplossing voor het gesimuleerde apparaat in de *cmake* -map.

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

U moet een apparaat registreren bij uw IoT-hub voordat u verbinding kunt maken. In deze sectie gebruikt u Azure Cloud Shell met de [IOT-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om een gesimuleerd apparaat te registreren.

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

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Communiceren tussen het apparaat en de service via Device streams

In deze sectie voert u zowel de toepassing op het apparaat als de toepassing aan de service zijde uit en communiceert u tussen de twee toepassingen.

### <a name="run-the-device-side-application"></a>De toepassing aan de apparaatzijde uitvoeren

Voer de volgende stappen uit om de toepassing aan het apparaat zijde uit te voeren:

1. Geef de referenties van uw apparaat op door het bron bestand **iothub_client_c2d_streaming_sample. c** te bewerken in de `iothub_client/samples/iothub_client_c2d_streaming_sample` map en uw apparaat connection string toe te voegen.

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

Zoals eerder vermeld, ondersteunt de IoT Hub C SDK alleen Device streams op het apparaat. Volg de instructies in een van de volgende Quick starts om de bijbehorende service-side toepassing te bouwen en uit te voeren:

* [Communiceren met een apparaat-app in C# via IoT Hub Device streams](./quickstart-device-streams-echo-csharp.md)

* [Communiceren met een apparaat-app in node. js via IoT Hub-streams](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start stelt u een IoT-hub in, registreert u een apparaat en maakt u een apparaat stroom tussen een C-toepassing op het apparaat en een andere toepassing aan de service kant, en gebruikt u de stroom om gegevens heen en weer te sturen tussen de toepassingen.

Zie voor meer informatie over het streamen van apparaten:

> [!div class="nextstepaction"]
> [Overzicht van apparaatstreams](./iot-hub-device-streams-overview.md)

---
title: Telemetrie verzenden naar Azure IoT Hub (C#) | Microsoft Docs
description: In deze snelstart voert u twee C-voorbeeldtoepassingen uit om gesimuleerde telemetrie te verzenden naar een IoT-hub en telemetrie van de IoT-hub te lezen voor verwerking in de cloud.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 842efca1b40827f63ab23581aeac7e5226d04349
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900285"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Quickstart: Telemetrie vanaf een apparaat verzenden naar een IoT-hub en lezen met een back-endtoepassing (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub is een Azure-service waarmee grote hoeveelheden telemetrie van uw IoT-apparaten naar de cloud kunt opnemen voor opslag of verwerking. In deze snelstartgids verzendt u telemetrie vanuit een toepassing voor een gesimuleerd apparaat via IoT Hub naar een back-endtoepassing voor verwerking.

De snelstartgids maakt gebruik van een voorbeeldtoepassing C van de [Azure IoT device-SDK voor C](iot-hub-device-sdk-c-intro.md) om telemetrie te verzenden naar een IoT-hub. De Azure IoT device SDK's zijn geschreven in [ANSI C (C99)](https://wikipedia.org/wiki/C99) voor portabiliteit en brede platformcompatibiliteit. Voordat u de voorbeeldcode uitvoert, maakt u een IoT-hub en registreert het gesimuleerde apparaat bij die hub.

Dit artikel is geschreven voor Windows, maar u kunt deze snelstartgids ook op Linux uitvoeren.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/) met de [' Desktop Development C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) '-werk belasting ingeschakeld.
* Installeer de meest recente versie van [Git](https://git-scm.com/download/).
* Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar. De IOT-extensie voegt IoT Hub, IoT Edge en IoT Device Provisioning Service (DPS)-specifieke opdrachten toe aan Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

Voor deze Quick Start gebruikt u de [Azure IOT Device SDK voor C](iot-hub-device-sdk-c-intro.md). 

U kunt de SDK door het installeren van de pakketten en bibliotheken voor de volgende omgevingen gebruiken:

* **Linux**: apt-get-pakketten zijn beschikbaar voor Ubuntu 16,04 en 18,04 met behulp van de volgende CPU-architecturen: amd64, arm64, armhf en i386. Zie [Gebruik van apt-get om een project C apparaat-client op Ubuntu te maken](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md) voor meer informatie.

* **mbed**: Voor ontwikkel aars die apparaat-apps op het mbed-platform maken, hebben we een bibliotheek en voor beelden gepubliceerd waarmee u binnen enkele minuten aan de slag kunt met Azure IoT Hub. Zie voor meer informatie, [Gebruik van de bibliotheek mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Als u ontwikkelt op Arduino, kunt u gebruikmaken van de Azure IoT-bibliotheek die beschikbaar is in de Arduino IDE-bibliotheek beheer. Zie voor meer informatie, [De Azure IoT Hub-bibliotheek voor Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: de IoT Hub apparaat-SDK is beschikbaar als CocoaPods voor de ontwikkeling van Mac- en iOS-apparaten. Zie [iOS-voorbeelden voor Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient) voor meer informatie.

In deze Quick start gaat u echter een ontwikkel omgeving voorbereiden die wordt gebruikt voor het klonen en bouwen van de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) van github. De SDK op GitHub bevat de voorbeeldcode gebruikt in deze snelstartgids.

1. Down load het [cmake build-systeem](https://cmake.org/download/).

    Het is belang rijk dat de Visual Studio-vereisten (Visual Studio en de ' Desktop Development with C++'-werk belasting) op uw computer zijn geïnstalleerd **voordat** u `CMake` de installatie start. Zodra aan de vereisten is voldaan en de download is geverifieerd, installeert u het CMake-bouwsysteem.

2. Open een opdracht prompt of git bash shell en navigeer naar een werkmap waarnaar u de Azure IoT C SDK wilt klonen. Voer de volgende opdracht uit voor het klonen van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-opslagplaats:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

3. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten uit vanuit de werkmap:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw ontwikkelings-client platform. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`.

    ```cmd
    cmake ..
    ```

    Als `cmake` uw C++ compiler niet wordt gevonden, kunt u tijdens het uitvoeren van de bovenstaande opdracht build-fouten krijgen. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Een apparaat registreren

Een apparaat moet zijn geregistreerd bij uw IoT-hub voordat het verbinding kan maken. In deze sectie gebruikt u de Azure Cloud Shell met de IOT- [extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om een gesimuleerd apparaat te registreren.

1. Voer de volgende opdracht uit in Azure Cloud Shell om de apparaat-id te maken.

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

   **MyCDevice**: dit is de naam van het geregistreerde apparaat. Gebruik MyCDevice, zoals wordt weergegeven. Als u een andere naam kiest voor uw apparaat, moet u deze naam ook in dit artikel gebruiken en de apparaatnaam bijwerken in de voorbeeld toepassingen voordat u ze uitvoert.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Voer de volgende opdrachten uit in Azure Cloud Shell om de _apparaat Connection String_ te verkrijgen voor het apparaat dat u hebt geregistreerd:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Noteer de apparaatverbindingsreeks. Deze ziet er ongeveer als volgt uit:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    U gebruikt deze waarde verderop in de snelstartgids.

## <a name="send-simulated-telemetry"></a>Gesimuleerde telemetrie verzenden

De toepassing voor het gesimuleerde apparaat maakt verbinding met een apparaatspecifiek eindpunt op uw IoT-hub en verzendt een tekenreeks als gesimuleerde telemetrie.

1. Open het bronbestand iothub_convenience_sample.c met een teksteditor, en bekijk de voorbeeldcode voor het verzenden van telemetrie. Het bestand bevindt zich op de volgende locatie onder de werkmap waar u de Azure IoT C-SDK hebt gekloond:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Zoek de declaratie van de `connectionString` constante:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Vervang de waarde van de `connectionString` constante door de apparaatverbindingsreeks die u eerder hebt genoteerd. Sla uw wijzigingen vervolgens op naar **iothub_convenience_sample.c**.

3. Navigeer in een lokaal terminalvenster naar de *iothub_convenience_sample*-projectmap in de CMake-map die u hebt gemaakt in de Azure IoT C SDK. Voer de volgende opdracht uit vanuit de werkmap:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Voer CMake uit in uw lokale terminalvenster om het voorbeeld te bouwen met de bijgewerkte `connectionString`-waarde:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Voer in het lokale terminal venster de volgende opdracht uit om de toepassing voor gesimuleerde apparaten uit te voeren:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    De volgende schermafbeelding geeft de uitvoer weer als de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    ![Het gesimuleerde apparaat uitvoeren](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>De telemetrie van uw hub lezen

In deze sectie gebruikt u de Azure Cloud Shell met de IOT- [extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) om de apparaat-berichten te bewaken die worden verzonden door het gesimuleerde apparaat.

1. Voer met de Azure Cloud Shell de volgende opdracht uit om te verbinden en berichten te lezen uit uw IoT-hub:

   **YourIoTHubName**: vervang deze tijdelijke aanduiding door een door u gekozen naam voor de IoT-hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Lees de apparaatberichten met de Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een IoT-hub geconfigureerd, een apparaat geregistreerd, gesimuleerde telemetrie verzonden naar de hub met behulp van een C-toepassing en de telemetrie gelezen van de hub met behulp van de Azure CLI.

Voor meer informatie over het ontwikkelen met de Azure loT Hub C SDK gaat u verder naar de volgende instructiegids:

> [!div class="nextstepaction"]
> [Ontwikkelen met behulp van Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)
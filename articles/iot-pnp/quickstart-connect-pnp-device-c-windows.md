---
title: Voor beeld van een IoT Plug en Play preview-apparaat code koppelen aan IoT Hub (Windows) | Microsoft Docs
description: Maak IoT Plug en Play preview-voorbeeld code op Windows die verbinding maakt met een IoT-hub en voer deze uit. Gebruik het hulp programma Azure IoT Explorer om de informatie weer te geven die door het apparaat wordt verzonden naar de hub.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769853"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Snelstartgids: verbinding maken met een voor beeld van een IoT Plug en Play preview-toepassing die wordt uitgevoerd op Windows naar IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

In deze Quick start ziet u hoe u een voor beeld van een IoT-voorbeeld Plug en Play kunt maken, hoe u het kunt verbinden met uw IoT-hub en hoe u het hulp programma Azure IoT Explorer kunt gebruiken om de informatie weer te geven die wordt verzonden naar de hub. De voorbeeld toepassing is geschreven in C en is opgenomen in de Azure IoT Hub Device C SDK. Een oplossings ontwikkelaar kan het hulp programma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT-Plug en Play apparaat zonder dat er toestel code hoeft te worden weer gegeven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale computer:

* [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) : Zorg ervoor dat u het **NuGet package manager** -onderdeel en de **Desktop ontwikkeling met** de werk belasting van C++ opneemt tijdens de installatie van Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer de nieuwste versie van **Azure IOT Explorer** vanaf de [bibliotheek](https://github.com/Azure/azure-iot-explorer/releases) pagina van het hulp programma door het MSI-bestand te selecteren onder ' assets ' voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick start gaat u een ontwikkel omgeving voorbereiden die u kunt gebruiken om de Azure IoT Hub Device C SDK te klonen en te bouwen.

Open een opdracht prompt in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT C-sdk's en-bibliotheken](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats te klonen op deze locatie:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de opgenomen voorbeeld code te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een `cmake` submap in de hoofdmap van de apparaat-SDK en navigeer naar die map:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Als cmake uw C++-compiler niet kan vinden, krijgt u tijdens het uitvoeren van de voor gaande opdracht fouten bij het bouwen. Als dat het geval is, kunt u proberen deze opdracht uit te voeren op de [Visual Studio-opdracht prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Het voor beeld van het apparaat uitvoeren

Voer een voorbeeld toepassing in de SDK uit om een IoT-Plug en Play apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Als u de voorbeeld toepassing wilt uitvoeren, gebruikt u deze opdrachten en geeft u het _apparaat Connection String_ als een para meter.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Het apparaat is nu gereed om opdrachten en updates van eigenschappen te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voor beeld uitvoeren tijdens het uitvoeren van de volgende stappen.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **instellingen**om ervoor te zorgen dat het hulp programma de interface model definities van uw apparaat kan lezen. In het menu instellingen **op het verbonden apparaat** wordt mogelijk al weer gegeven in de Plug en Play configuraties. Als dat niet het geval is, selecteert u **+ module-definitie bron toevoegen** en vervolgens **op het aangesloten apparaat** om het toe te voegen.

1. Ga terug naar de overzichts pagina **apparaten** en zoek de apparaat-id die u eerder hebt gemaakt. Wanneer de toepassing nog steeds wordt uitgevoerd in de opdracht prompt, controleert u of de **verbindings status** van het apparaat in azure IOT Explorer wordt gerapporteerd als _verbonden_ (als dat niet het geval is, kunt u op **vernieuwen** drukken totdat dit is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** uit om de interface en IOT Plug en Play primitieven, eigenschappen, opdrachten en telemetrie, weer te geven.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en werken met een IoT Plug en Play preview-apparaat](howto-develop-solution.md)

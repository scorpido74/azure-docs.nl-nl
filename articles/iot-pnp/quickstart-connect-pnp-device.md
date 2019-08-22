---
title: Voor beeld van een IoT Plug en Play preview-apparaat code verbinden met IoT Hub | Microsoft Docs
description: Maak een preview-versie van IoT Plug en Play voor beeld van een programma dat verbinding maakt met een IoT-hub en voer deze uit. Gebruik het hulp programma Azure IoT Explorer om de informatie weer te geven die door het apparaat wordt verzonden naar de hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b233fcecfe80d1ce4464d2d02fdddb188f9265a7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878241"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Quickstart: Een voor beeld van een IoT Plug en Play preview-apparaat toepassing verbinden met IoT Hub

In deze Quick start ziet u hoe u een voor beeld van een IoT-voorbeeld Plug en Play kunt maken, hoe u het kunt verbinden met uw IoT-hub en hoe u het hulp programma Azure IoT Explorer kunt gebruiken om de informatie weer te geven die wordt verzonden naar de hub. De voorbeeld toepassing is geschreven in C en is opgenomen in de Azure IoT Device SDK voor C. Een oplossings ontwikkelaar kan het hulp programma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT-Plug en Play apparaat zonder dat er toestel code hoeft te worden weer gegeven.

## <a name="prerequisites"></a>Vereisten

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale computer:

* [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) : Zorg ervoor dat u het onderdeel **NuGet package manager** en de **Desktop ontwikkeling C++ met** werk belasting opneemt tijdens de installatie van Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer het hulp programma Azure IoT Explorer vanaf de pagina [nieuwste release](https://github.com/Azure/azure-iot-explorer/releases) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voeg de Microsoft Azure IoT-extensie voor Azure CLI toe:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduidingen **YourIoTHubName** en **YourDevice** door uw werkelijke namen. Als u geen IoT Hub hebt, volgt u [deze instructies om er een te maken](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Voer de volgende opdrachten uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Voer de volgende opdrachten uit om de _IOT hub-Connection String_ voor uw hub op te halen:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

### <a name="get-azure-iot-device-sdk-for-c"></a>Azure IoT Device SDK ophalen voor C

In deze Quick start gaat u een ontwikkel omgeving voorbereiden die u kunt gebruiken om de Azure IoT C-SDK te klonen en te bouwen.

Open een opdrachtprompt. Voer de volgende opdracht uit voor het klonen van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-opslagplaats:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

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
    > Als cmake uw C++ compiler niet kan vinden, krijgt u tijdens het uitvoeren van de vorige opdracht fouten bij het bouwen. Als dat het geval is, kunt u proberen deze opdracht uit te voeren op de [Visual Studio-opdracht prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Het voor beeld van het apparaat uitvoeren

Voer de toepassing uit door de IoT hub-apparaat connection string als para meter door te geven.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

De toepassing wordt gestart met het verzenden van gegevens naar IoT Hub.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

1. Open Azure IoT Explorer. u ziet de pagina **app-configuraties** .

1. Voer uw IoT Hub connection string in en klik op **verbinding maken**.

1. Nadat u verbinding hebt gemaakt, wordt de overzichts pagina van het apparaat weer gegeven.

1. Als u uw bedrijfs opslagplaats wilt toevoegen, selecteert u **instellingen**, vervolgens **+ Nieuw**en vervolgens **op het aangesloten apparaat**.

1. Zoek op de pagina overzicht van apparaten de apparaat-id die u eerder hebt gemaakt en selecteer deze om meer details weer te geven.

1. Vouw de interface met de ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** uit om de IOT Plug en Play-primitieven-eigenschappen, opdrachten en telemetrie weer te geven.

1. Selecteer de telemetrie-pagina om de telemetriegegevens weer te geven die het apparaat verzendt.

1. Selecteer de pagina **Eigenschappen (niet-schrijfbaar)** om de niet-Beschrijf bare eigenschappen weer te geven die door het apparaat worden gerapporteerd.

1. Selecteer de pagina **Eigenschappen (schrijfbaar)** om de Beschrijf bare eigenschappen die u kunt bijwerken weer te geven.

1. Vouw de **naam**van de eigenschap uit, update met een nieuwe naam en selecteer **Beschrijf bare eigenschap bijwerken**. 

1. Voor een overzicht van de nieuwe naam wordt weer gegeven in de kolom **gemelde eigenschap** , klikt u op de knop **vernieuwen** boven op de pagina.

1. Selecteer de **opdracht** pagina om alle opdrachten weer te geven die het apparaat ondersteunt.

1. Vouw de **knip** opdracht uit en stel een nieuw Knipper tijds interval in. Selecteer **opdracht verzenden** om de opdracht op het apparaat aan te roepen.

1. Ga naar het gesimuleerde apparaat om te controleren of de opdracht wordt uitgevoerd zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Uitleg: Verbinding maken met en interactie met een IoT Plug en Play preview-apparaat](howto-develop-solution.md)

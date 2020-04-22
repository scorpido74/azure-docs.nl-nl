---
title: IoT Plug and Play Preview-voorbeeldapparaatcode verbinden met IoT Hub (Windows) | Microsoft Documenten
description: IoT Plug and Play Preview-voorbeeldapparaatcode bouwen en uitvoeren op Windows die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma voor Azure IoT explorer om de informatie weer te geven die door het apparaat naar de hub wordt verzonden.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769853"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Snelstart: een voorbeeld van een IoT-stekker en play-voorbeeld-apparaattoepassing verbinden die op Windows wordt uitgevoerd met IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Met deze quickstart u zien hoe u een voorbeeld van IoT Plug and Play-apparaattoepassing maken, deze aansluiten op uw IoT-hub en het azure IoT explorer-hulpprogramma gebruiken om de informatie te bekijken die naar de hub wordt verzendt. De voorbeeldtoepassing is geschreven in C en is opgenomen in de Azure IoT Hub Device C SDK. Een ontwikkelaar van een oplossing kan het Azure IoT explorer-hulpprogramma gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder dat er apparaatcode hoeft te worden bekeken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, moet u de volgende software op uw lokale machine installeren:

* [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/) - zorg ervoor dat u de **nuget-pakketbeheercomponent** en de **desktopontwikkeling met C++-workload** opneemt wanneer u Visual Studio installeert.
* [Git.](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT-verkenner installeren

Download en installeer de nieuwste versie van **Azure IoT explorer** van de [repository-pagina](https://github.com/Azure/azure-iot-explorer/releases) van de tool door het MSI-bestand onder 'Assets' te selecteren voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart bereidt u een ontwikkelomgeving voor die u gebruiken om de Azure IoT Hub Device C SDK te klonen en te bouwen.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de [GitHub-opslagplaats azure IoT C-sedk's en bibliotheken](https://github.com/Azure/azure-iot-sdk-c) op deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de meegeleverde voorbeeldcode te maken. De toepassing die u bouwt, simuleert een apparaat dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak `cmake` een submap in de hoofdmap van de apparaatSDK en navigeer naar die map:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK van het apparaat en de gegenereerde code-stub te bouwen:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Als cmake uw C++ compiler niet kan vinden, krijg je buildfouten wanneer je de vorige opdracht uitvoert. Als dat gebeurt, probeert u deze opdracht uit te voeren op de [opdrachtprompt van Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Het voorbeeld van het apparaat uitvoeren

Voer een voorbeeldtoepassing uit in de SDK om een IoT Plug and Play-apparaat te simuleren dat telemetrie naar uw IoT-hub verzendt. Als u de voorbeeldtoepassing wilt uitvoeren, gebruikt u deze opdrachten en geeft u de _tekenreeks voor apparaatverbinding door_ als parameter.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Het apparaat is nu klaar om opdrachten en eigendomsupdates te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Houd het voorbeeld actief terwijl u de volgende stappen uitvoert.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT-verkenner gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **Instellingen**om ervoor te zorgen dat het hulpprogramma de definities van het interfacemodel van uw apparaat kan lezen. In het menu Instellingen wordt **op het aangesloten apparaat** mogelijk al weergegeven in de plug-en play-configuraties. Als dit niet het zo is, selecteert u **+ Moduledefinitiebron toevoegen** en vervolgens **op het aangesloten apparaat** om deze toe te voegen.

1. Zoek terug op de **overzichtspagina Apparaten** de apparaatidentiteit die u eerder hebt gemaakt. Controleer of de **verbindingsstatus** van het apparaat in Azure IoT-verkenner wordt gemeld als _Verbonden_ (zo niet, druk op **Vernieuwen** totdat dit het geval is). Selecteer het apparaat om meer details weer te geven.

1. Breid de interface uit met **ID-urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** om de interface en IoT Plug and Play-primitieven te onthullen: eigenschappen, opdrachten en telemetrie.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een IoT Plug and Play Preview-apparaat](howto-develop-solution.md)

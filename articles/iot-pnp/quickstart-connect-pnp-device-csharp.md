---
title: Voor beeld van een IoT Plug en Play preview-apparaat code verbinden met IoT Hub | Microsoft Docs
description: Gebruik C# (.net) om IOT Plug en Play preview-voorbeeld code te bouwen en uit te voeren die verbinding maakt met een IOT-hub. Gebruik het hulp programma Azure IoT Explorer om de informatie weer te geven die door het apparaat wordt verzonden naar de hub.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b32fc103b4ed4d7058c8af42ffa126ee5527f45a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550840"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Quick Start: een voor beeld van een IoT Plug en Play preview-apparaatC#toepassing verbinden met IOT hub ()

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

In deze Quick start ziet u hoe u een voor beeld van een IoT-voorbeeld Plug en Play kunt maken, hoe u het kunt verbinden met uw IoT-hub en hoe u het hulp programma Azure IoT Explorer kunt gebruiken om de informatie weer te geven die wordt verzonden naar de hub. De voorbeeld toepassing is geschreven in C# (met .net) en wordt meegeleverd als onderdeel van de Azure IOT-voor beelden C# voor (.net)-verzameling. Een oplossings ontwikkelaar kan het hulp programma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT-Plug en Play apparaat zonder dat er toestel code hoeft te worden weer gegeven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze Quick Start te volt ooien, moet u .NET Core 2,2 installeren op uw ontwikkel computer. U kunt deze versie van de .NET Core SDK voor meerdere platformen downloaden van [.net Core 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2).

U kunt de versie van .NET controleren die zich op uw ontwikkel computer bevindt door de volgende opdracht uit te voeren in een lokaal Terminal venster: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer de nieuwste versie van **Azure IOT Explorer** vanaf de [bibliotheek](https://github.com/Azure/azure-iot-explorer/releases) pagina van het hulp programma door het MSI-bestand te selecteren onder ' assets ' voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick Start maakt u een ontwikkel omgeving die u kunt gebruiken om de Azure IoT-voor beelden voor C# (.net) te klonen en te maken.

Open een opdracht prompt in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT-voor C# beelden voor (.net)](https://github.com/Azure-Samples/azure-iot-samples-csharp) github-opslag plaats naar deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Het volt ooien van deze bewerking kan enkele minuten duren.

## <a name="run-the-device-sample"></a>Het voor beeld van het apparaat uitvoeren

U gebruikt de gekloonde voorbeeld code voor het bouwen van een toepassing die een apparaat simuleert dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal Terminal venster naar de map van uw gekloonde opslag plaats en navigeer naar de map **Azure-IOT-samples-csharp/digitaltwin/samples/Device/EnvironmentalSensorSample** . 

1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Voer een voorbeeld toepassing uit om een IoT Plug en Play-apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Gebruik in hetzelfde Terminal venster de volgende opdracht om de benodigde pakketten te bouwen en de voorbeeld toepassing uit te voeren:

    ```cmd\sh
        dotnet run
    ```

Er worden berichten weer gegeven met de melding dat het apparaat is geregistreerd en wacht op updates vanuit de Cloud. Dit geeft aan dat het apparaat nu gereed is om opdrachten en updates van eigenschappen te ontvangen en dat er telemetriegegevens worden verzonden naar de hub. Laat het voor beeld uitvoeren tijdens het uitvoeren van de volgende stappen.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **instellingen**om ervoor te zorgen dat het hulp programma de interface model definities van uw apparaat kan lezen. In het menu instellingen **op het verbonden apparaat** wordt mogelijk al weer gegeven in de Plug en Play configuraties. Als dat niet het geval is, selecteert u **+ module-definitie bron toevoegen** en vervolgens **op het aangesloten apparaat** om het toe te voegen.

1. Ga terug naar de overzichts pagina **apparaten** en zoek de apparaat-id die u eerder hebt gemaakt. Wanneer de toepassing nog steeds wordt uitgevoerd in de opdracht prompt, controleert u of de **verbindings status** van het apparaat in azure IOT Explorer wordt gerapporteerd als _verbonden_ (als dat niet het geval is, kunt u op **vernieuwen** drukken totdat dit is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de ID **urn: csharp_sdk_sample: EnvironmentalSensor: 1** uit om de interface en IOT Plug en Play primitieven, eigenschappen, opdrachten en telemetrie, weer te geven.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en werken met een IoT Plug en Play preview-apparaat](howto-develop-solution.md)

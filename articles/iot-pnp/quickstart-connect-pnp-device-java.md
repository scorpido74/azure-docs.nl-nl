---
title: Voor beeld van een IoT Plug en Play preview-apparaat code verbinden met IoT Hub | Microsoft Docs
description: Met behulp van Java kunt u IoT Plug en Play preview-voorbeeld code maken en uitvoeren die verbinding maakt met een IoT-hub. Gebruik het hulp programma Azure IoT Explorer om de informatie weer te geven die door het apparaat wordt verzonden naar de hub.
author: baanders
ms.author: baanders
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e1454a197620cacaa8c8303e8f36a8d4a87aec00
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531306"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Snelstartgids: een voor beeld van een IoT Plug en Play preview-toepassings toepassing verbinden met IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

In deze Quick start ziet u hoe u een voor beeld van een IoT-voorbeeld Plug en Play kunt maken, hoe u het kunt verbinden met uw IoT-hub en hoe u het hulp programma Azure IoT Explorer kunt gebruiken om de informatie weer te geven die wordt verzonden naar de hub. De voorbeeld toepassing is geschreven in Java en wordt meegeleverd als onderdeel van de Azure IoT-voor beelden voor Java-verzameling. Een oplossings ontwikkelaar kan het hulp programma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT-Plug en Play apparaat zonder dat er toestel code hoeft te worden weer gegeven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze Snelstartgids wilt volt ooien, moet u Java SE 8 op uw ontwikkel computer hebben. U moet ook maven 3 installeren.

Zie [uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) in de Microsoft Azure IOT Device SDK voor Java voor meer informatie over hoe u deze kunt instellen.

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer de nieuwste versie van **Azure IOT Explorer** vanaf de [bibliotheek](https://github.com/Azure/azure-iot-explorer/releases) pagina van het hulp programma door het MSI-bestand te selecteren onder ' assets ' voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick Start maakt u een ontwikkel omgeving die u kunt gebruiken om de Azure IoT-voor beelden voor Java te klonen en samen te stellen.

Open een Terminal venster in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT-voor beelden voor Java](https://github.com/Azure-Samples/azure-iot-samples-java) github-opslag plaats naar deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Het volt ooien van deze bewerking kan enkele minuten duren.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de gekloonde voorbeeld code voor het bouwen van een toepassing die een apparaat simuleert dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal Terminal venster naar de map van uw gekloonde opslag plaats en ga naar de map **/Azure-IOT-samples-Java/Digital-Twin/samples/Device/JdkSample** . Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren en de toepassing met gesimuleerde apparaten te bouwen:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Het voor beeld van het apparaat uitvoeren

Voer een voorbeeld toepassing uit om een IoT Plug en Play-apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Gebruik de volgende opdracht om de voorbeeld toepassing uit te voeren:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Er worden berichten weer gegeven met de melding dat het apparaat is verbonden, het uitvoeren van verschillende installatie stappen en het wachten op service-updates, gevolgd door telemetrie-Logboeken. Dit geeft aan dat het apparaat nu gereed is om opdrachten en updates van eigenschappen te ontvangen en dat er telemetriegegevens worden verzonden naar de hub. Laat het voor beeld uitvoeren tijdens het uitvoeren van de volgende stappen.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **instellingen**om ervoor te zorgen dat het hulp programma de interface model definities van uw apparaat kan lezen. In het menu instellingen **op het verbonden apparaat** wordt mogelijk al weer gegeven in de Plug en Play configuraties. Als dat niet het geval is, selecteert u **+ module-definitie bron toevoegen** en vervolgens **op het aangesloten apparaat** om het toe te voegen.

1. Ga terug naar de overzichts pagina **apparaten** en zoek de apparaat-id die u eerder hebt gemaakt. Wanneer de toepassing nog steeds wordt uitgevoerd in de opdracht prompt, controleert u of de **verbindings status** van het apparaat in azure IOT Explorer wordt gerapporteerd als _verbonden_ (als dat niet het geval is, kunt u op **vernieuwen** drukken totdat dit is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de ID **urn: java_sdk_sample: EnvironmentalSensor: 1** uit om de interface en IOT Plug en Play primitieven, eigenschappen, opdrachten en telemetrie, weer te geven.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en werken met een IoT Plug en Play preview-apparaat](howto-develop-solution.md)

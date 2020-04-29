---
title: Voor beeld van een IoT Plug en Play preview-apparaat code verbinden met IoT Hub | Microsoft Docs
description: Met behulp van node. js kunt u IoT Plug en Play preview-voorbeeld code maken en uitvoeren die verbinding maakt met een IoT-hub. Gebruik het hulp programma Azure IoT Explorer om de informatie weer te geven die door het apparaat wordt verzonden naar de hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76964784"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Quick Start: een voor beeld van een IoT Plug en Play preview-apparaat toepassing verbinden met IoT Hub (node. js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

In deze Quick start ziet u hoe u een voor beeld van een IoT-voorbeeld Plug en Play kunt maken, hoe u het kunt verbinden met uw IoT-hub en hoe u het hulp programma Azure IoT Explorer kunt gebruiken om de informatie weer te geven die wordt verzonden naar de hub. De voorbeeld toepassing is geschreven voor node. js en is opgenomen in de Azure IoT Hub Device SDK voor node. js. Een oplossings ontwikkelaar kan het hulp programma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT-Plug en Play apparaat zonder dat er toestel code hoeft te worden weer gegeven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

U kunt deze Snelstartgids alleen volt ooien als u node. js hebt op de ontwikkel computer. U kunt de meest recente aanbevolen versie voor meerdere platforms downloaden vanuit [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer de nieuwste versie van **Azure IOT Explorer** vanaf de [bibliotheek](https://github.com/Azure/azure-iot-explorer/releases) pagina van het hulp programma door het MSI-bestand te selecteren onder ' assets ' voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick Start maakt u een ontwikkel omgeving die u kunt gebruiken om de Azure IoT Hub Device SDK voor node. js te klonen en te maken.

Open een opdracht prompt in de gewenste map. Voer de volgende opdracht uit om de [Microsoft Azure IOT SDK voor node. js](https://github.com/Azure/azure-iot-sdk-node) github-opslag plaats naar deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Het volt ooien van deze bewerking kan enkele minuten duren.

## <a name="install-required-libraries"></a>Vereiste bibliotheken installeren

U gebruikt de SDK van het apparaat om de opgenomen voorbeeld code te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal Terminal venster naar de map van uw gekloonde opslag plaats en ga naar de map **/Azure-IOT-SDK-node/digitaltwins/samples/Device/JavaScript** . Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren:

    ```cmd/sh
    npm install
    ```
1. De connection string van het _apparaat_configureren:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Het voorbeeld apparaat uitvoeren

Voer een voorbeeld toepassing in de SDK uit om een IoT-Plug en Play apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Gebruik de volgende opdracht om de voorbeeld toepassing uit te voeren:

```cmd\sh
    node sample_device.js
```

U ziet de volgende uitvoer, waarmee wordt aangegeven dat het apparaat bezig is met het verzenden van telemetriegegevens naar de hub en nu gereed is voor het ontvangen van opdrachten en updates van eigenschappen.

   ![Bevestigings berichten voor apparaat](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Laat het voor beeld uitvoeren tijdens het uitvoeren van de volgende stappen.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **instellingen**om ervoor te zorgen dat het hulp programma de interface model definities van uw apparaat kan lezen. In het menu instellingen **op het verbonden apparaat** wordt mogelijk al weer gegeven in de Plug en Play configuraties. Als dat niet het geval is, selecteert u **+ module-definitie bron toevoegen** en vervolgens **op het aangesloten apparaat** om het toe te voegen.

1. Ga terug naar de overzichts pagina **apparaten** en zoek de apparaat-id die u eerder hebt gemaakt. Wanneer de toepassing nog steeds wordt uitgevoerd in de opdracht prompt, controleert u of de **verbindings status** van het apparaat in azure IOT Explorer wordt gerapporteerd als _verbonden_ (als dat niet het geval is, kunt u op **vernieuwen** drukken totdat dit is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de ID **urn: contoso: com: EnvironmentalSensor: 1** uit om de interface en IOT Plug en Play primitieven, eigenschappen, opdrachten en telemetrie, weer te geven.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en werken met een IoT Plug en Play preview-apparaat](howto-develop-solution.md)

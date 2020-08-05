---
title: IoT Plug and Play Preview-code koppelen aan IoT Hub - Node.js | Microsoft Docs
description: Lees hoe u met behulp van Node.js voorbeeldcode voor een IoT Plug and Play Preview-apparaat schrijft en uitvoert die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 68d8aca755f7b8df8eb7e65f4a3c21513feefede
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044172"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Quickstart: Een voorbeeld van een IoT Plug and Play Preview-apparaattoepassing verbinden met IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven voor Node.js en is opgenomen in de Azure IoT Device-SDK voor Node.js. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet Node.js op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer installeren

Download en installeer de nieuwste versie van **Azure IoT Explorer-** via de pagina [repository](https://github.com/Azure/azure-iot-explorer/releases) van de tool door onder Assets het MSI-bestand te selecteren voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub (dit is voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device C#-SDK voor Node.js.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Microsoft Azure IoT SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) te klonen op deze locatie:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Deze bewerking kan enkele minuten duren.

## <a name="install-required-libraries"></a>Vereiste bibliotheken installeren

U gebruikt de SDK van het apparaat om de opgenomen voorbeeldcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van de gekloonde opslagplaats en ga naar de map **/azure-iot-sdk-node/digitaltwins/samples/device/javascript**. Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren:

    ```cmd/sh
    npm install
    ```
1. Configureer de _device connection string_ (verbindingsreeks voor het apparaat):

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

Voer een voorbeeldtoepassing in de SDK uit om een IoT Plug en Play-apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Gebruik de volgende opdracht om de voorbeeldtoepassing uit te voeren:

```cmd\sh
    node sample_device.js
```

U ziet de volgende uitvoer, wat aangeeft dat het apparaat nu telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen.

   ![Bevestigingsberichten apparaat](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Code valideren met de Azure IoT Explorer

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om ervoor te zorgen dat het hulpprogramma de interfacemodeldefinities van uw apparaat kan lezen, selecteert u **Instellingen**. In het menu Instellingen wordt **Op het verbonden apparaat** mogelijk al weergegeven in de Plug en Play-configuraties. Als dat niet het geval is, selecteert u **+ Bron moduledefinitie toevoegen** en vervolgens **Op het verbonden apparaat** om het apparaat toe te voegen.

1. Zoek op de overzichtspagina **Devices** de apparaat-id die u eerder hebt gemaakt. Controleer terwijl de apparaattoepassing nog steeds wordt uitgevoerd bij de opdrachtprompt of bij **Connection state** in Azure IoT Explorer de status van het apparaat wordt weergegeven als _Connected_ (als dat niet zo is, selecteert u **Refresh** tot dat wel het geval is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de id **urn:contoso:com:EnvironmentalSensor:1** uit om de interface en primitieven (eigenschappen, opdrachten en telemetrie) van IoT Plug and Play te bekijken.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-hub. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

> [!div class="nextstepaction"]
> [Uitleg: Connect to and interact with an IoT Plug and Play Preview device](howto-develop-solution.md) (Verbinding maken met een IoT Plug and Play Preview-apparaat en ermee communiceren)

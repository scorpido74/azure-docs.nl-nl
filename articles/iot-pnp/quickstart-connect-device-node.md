---
title: Node.js-voorbeeldcode voor een IoT Plug and Play Preview-apparaat koppelen aan Azure IoT Hub
description: Lees hoe u met behulp van Node.js voorbeeldcode voor een IoT Plug and Play Preview-apparaat schrijft en uitvoert die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: ericmitt
ms.author: ericmitt
ms.date: 07/10/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: f6a3190ec87c0b06aba8f065a3e7518f4a76cf24
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352634"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Quickstart: Een voorbeeld van een IoT Plug and Play Preview-apparaattoepassing verbinden met IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

In deze quickstart ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de telemetrie weer te geven die wordt verzonden. De voorbeeldtoepassing wordt geschreven in Node.js en is opgenomen in de Azure IoT Device-SDK voor Node.js. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze quickstart wilt uitvoeren, moet Node.js op uw ontwikkelcomputer zijn geïnstalleerd. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om in het tweede deel van deze quickstart te communiceren met het voorbeeldapparaat, gebruikt u het hulpprogramma **Azure IoT Explorer**. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om de verbindingsreeks voor IoT Hub te vinden.

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze quickstart:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>De code downloaden

In deze quickstart bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device C#-SDK voor Node.js.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Microsoft Azure IoT SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) te klonen op deze locatie:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Vereiste bibliotheken installeren

U gebruikt de SDK van het apparaat om de opgenomen voorbeeldcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van de gekloonde opslagplaats en ga naar de map */azure-iot-sdk-node/device/samples/pnp*. Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren:

    ```cmd/sh
    npm install
    ```

1. Configureer de omgevingswaarde met de verbindingsreeks voor het apparaat die u eerder hebt genoteerd:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

Open het bestand _simple_thermostat.js_. In dit bestand ziet u hoe u het volgende kunt doen:

1. De vereiste interfaces importeren.
1. Een handler voor updates en een handler voor opdrachten schrijven.
1. De gewenste patches voor eigenschappen verwerken en telemetrie verzenden.
1. Uw apparaat desgewenst inrichten met behulp van Azure Device Provisioning Service (DPS).

In de main-functie kunt u zien hoe alles bij elkaar komt:

1. Maak het apparaat met behulp van de tekenreeks voor verbinding of richt het apparaat in met behulp van DPS.
1. Gebruik de optie **modelID** om het IoT Plug en Play-apparaatmodel op te geven.
1. Schakel de opdracht-handler in.
1. Verzend telemetrie van het apparaat naar uw hub.
1. Haal de apparaatdubbel op en werk de gerapporteerde eigenschappen bij.
1. Schakel de gewenste handler voor het bijwerken van eigenschappen in.

Voer de voorbeeldtoepassing uit om een IoT Plug en Play-apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. Gebruik de volgende opdracht om de voorbeeldtoepassing uit te voeren:

```cmd\sh
node simple_thermostat.js
```

U ziet de volgende uitvoer, wat aangeeft dat het apparaat nu telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen.

![Bevestigingsberichten apparaat](media/quickstart-connect-device-node/device-confirmation-node.png)

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT Plug and Play-apparaat kunt verbinden met een IoT-hub. Als u meer wilt weten over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten, leest u:

> [!div class="nextstepaction"]
> [Interactie met een IoT Plug en Play Preview-apparaat dat is verbonden met uw oplossing](quickstart-service-node.md)

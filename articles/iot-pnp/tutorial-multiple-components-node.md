---
title: Node.js-voorbeeldcode voor een IoT Plug and Play Preview-apparaat met meerdere onderdelen koppelen aan IoT Hub | Microsoft Docs
description: Schrijf Node.js-voorbeeldcode voor een IoT Plug and Play Preview-apparaat dat meerdere onderdelen gebruikt en verbinding maakt met een IoT-hub, en voer deze uit. Gebruik het hulpprogramma Azure IoT Explorer om de gegevens te bekijken die door het apparaat naar de hub worden verzonden.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: fda03aa4bd087caec9cb130ac05b0e262d53402f
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351742"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Zelfstudie: Een voorbeeld van een IoT Plug and Play Preview-apparaattoepassing met verschillende onderdelen verbinden met IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

In deze zelfstudie ziet u hoe u een voorbeeld van een IoT Plug and Play-apparaattoepassing met onderdelen en een hoofdinterface maakt, hoe u de toepassing verbindt met uw IoT-hub en hoe u het hulpprogramma Azure IoT Explorer gebruikt om de gegevens weer te geven die naar de hub worden verzonden. De voorbeeldtoepassing wordt geschreven voor Node.js en is opgenomen in de Azure IoT Device-SDK voor Node.js. Een ontwikkelaar van oplossingen kan het hulpprogramma Azure IoT Explorer gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder apparaatcode weer te geven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u Node.js nodig op uw ontwikkelcomputer. U kunt de nieuwste aanbevolen omgeving voor meerdere platforms downloaden van [nodejs.org](https://nodejs.org).

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om in het tweede deel van deze zelfstudie te communiceren met het voorbeeldapparaat, gebruikt u het hulpprogramma **Azure IoT Explorer**. [Download en installeer de nieuwste release van Azure IoT Explorer](./howto-use-iot-explorer.md) voor uw besturingssysteem.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub. Noteer deze verbindingsreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> U kunt het hulpprogramma Azure IoT Explorer ook gebruiken om de verbindingsreeks voor IoT Hub te vinden.

Voer de volgende opdracht uit om de _apparaatverbindingsreeks_ op te halen voor het apparaat dat u aan de hub hebt toegevoegd. Noteer deze verbindingsreeks voor later gebruik in deze zelfstudie:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>De code downloaden

In deze zelfstudie bereidt u een ontwikkelomgeving voor die kan worden gebruikt voor het klonen en compileren van de Azure IoT Hub Device-SDK voor Node.js.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de GitHub-opslagplaats [Microsoft Azure IoT SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) te klonen op deze locatie:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

Deze bewerking kan enkele minuten duren.

## <a name="install-required-libraries"></a>Vereiste bibliotheken installeren

U gebruikt de SDK van het apparaat om de opgenomen voorbeeldcode te maken. De toepassing die u bouwt, simuleert een Plug en Play-apparaat met meerdere onderdelen en een basisinterface die verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van de gekloonde opslagplaats en ga naar de map */azure-iot-sdk-node/device/samples/pnp*. Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren:

```cmd/sh
npm install
```

Hiermee installeert u de relevante NPM-bestanden die nodig zijn voor het uitvoeren van de voorbeelden in de map.

1. Configureer de omgevingswaarde met de verbindingsreeks voor het apparaat die u eerder hebt genoteerd:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>De code bekijken

Ga naar de map *azure-iot-sdk-node\device\samples\pnp*.

De map *azure-iot-sdk-node\device\samples\pnp* bevat de voorbeeldcode voor het IoT Plug en Play-apparaat voor temperatuurregeling.

Met de code in het bestand *pnpTemperatureController.js* wordt een IoT Plug en Play-apparaat voor temperatuurregeling geïmplementeerd. Het model dat met dit voorbeeld wordt geïmplementeerd, maakt gebruik van [meerdere onderdelen](concepts-components.md). Het [Digital Twins Definition Language-modelbestand (DTDL) voor het thermostaatapparaat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definieert de telemetrie, eigenschappen en opdrachten die het apparaat implementeert.

Open het bestand *pnpTemperatureController.js* in de code-editor van uw keuze. De voorbeeldcode laat zien hoe u het volgende kunt doen:

1. De `modelId` definiëren, wat de DTMI is voor het apparaat dat wordt geïmplementeerd. Deze DTMI is door de gebruiker gedefinieerd en moet overeenkomen met de DTMI van het [DTDL-model voor de temperatuurregeling](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

1. De onderdelen implementeren die zijn gedefinieerd in het DTDL model van de temperatuurregeling. De onderdelen van een echte temperatuurregeling moeten deze twee interfaces implementeren. Deze twee interfaces zijn al gepubliceerd in een centrale opslagplaats. In dit voorbeeld zijn dit de twee interfaces:
  - Thermostaat
  - Apparaatgegevens ontwikkeld door Azure

1. Onderdeelnamen definiëren. Dit voorbeeld bevat twee thermostaten en één onderdeel met apparaatgegevens.

1. Opdrachtnaam definiëren. Dit zijn de opdrachten waarop het apparaat reageert.

1. De constante `serialNumber` definiëren. Het `serialNumber` staat vast voor een bepaald apparaat.

1. De opdracht-handlers definiëren.

1. De functies definiëren voor het verzenden van opdrachtantwoorden.

1. De helperfuncties definiëren voor het vastleggen van opdrachtverzoeken.

1. Een helperfunctie definiëren om de eigenschappen te maken.

1. Een listener definiëren voor updates van eigenschappen.

1. Een functie definiëren om telemetrie te verzenden vanaf dit apparaat. Zowel de thermostaten als de hoofdinterface verzenden telemetrie. Deze functie ontvangt de onderdeelnaam als parameter.

1. Een `main`-functie definiëren die:

    1. De apparaat-SDK gebruikt om een apparaatclient te maken en verbinding te maken met uw IoT-hub. Het apparaat verzendt de `modelId` zodat de IoT-hub het apparaat kan identificeren als een IoT Plug en Play-apparaat.

    1. Begint te luisteren naar opdrachtaanvragen met de functie `onDeviceMethod`. De functie stelt een listener in voor opdrachtverzoeken vanaf de service:
        - De apparaat-DTDL definieert de opdrachten `reboot` en `getMaxMinReport`.
        - De functie `commandHandler` definieert hoe het apparaat reageert op een opdracht.

    1. Begint telemetrie te verzenden met `setInterval` en `sendTelemetry`.

    1. Maakt gebruik van de functie `helperCreateReportedPropertiesPatch` om de eigenschappen te maken en van `updateComponentReportedProperties` om de eigenschappen bij te werken.

    1. Gebruikt `desiredPropertyPatchListener` om naar updates van eigenschappen te luisteren.

    1. Schakelt alle listeners en taken uit, en sluit de lus af wanneer u op **Q** of **q** drukt.

Nu u de code hebt gezien, gebruikt u de volgende opdracht om het voorbeeld uit te voeren:

```cmd\sh
node pnpTemperatureController.js
```

U ziet de volgende uitvoer, wat aangeeft dat het apparaat nu telemetriegegevens naar de hub stuurt en nu klaar is om opdrachten en updates van eigenschappen te ontvangen.

![Bevestigingsberichten apparaat](media/tutorial-multiple-components-node/multiple-component.png)

Laat het voorbeeld actief tijdens het uitvoeren van de volgende stappen.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Code valideren met Azure IoT Explorer

Nadat het voorbeeld van de apparaatclient is gestart, gebruikt u het hulpprogramma Azure IoT Explorer om te verifiëren dat het werkt.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play-apparaat met onderdelen kunt verbinden met een IoT-hub. Voor meer informatie over IoT Plug and Play-apparaatmodellen raadpleegt u:

> [!div class="nextstepaction"]
> [Handleiding voor ontwikkelaars van IoT Plug and Play Preview-modellen](concepts-developer-guide.md)

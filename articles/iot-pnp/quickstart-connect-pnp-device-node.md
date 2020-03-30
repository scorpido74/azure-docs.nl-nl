---
title: IoT Plug and Play Preview-voorbeeldapparaatcode verbinden met IoT-hub | Microsoft Documenten
description: Met Behulp van Node.js u IoT Plug and Play Preview-voorbeeldapparaatcode bouwen en uitvoeren die verbinding maakt met een IoT-hub. Gebruik het hulpprogramma voor Azure IoT explorer om de informatie weer te geven die door het apparaat naar de hub wordt verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c5d69ae21cd240b0c68b9694a55de2cf879a1966
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964784"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Snelstart: een voorbeeld van een IoT-stekker en play-apparaattoepassing verbinden met IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Met deze quickstart u zien hoe u een voorbeeld van IoT Plug and Play-apparaattoepassing maken, deze aansluiten op uw IoT-hub en het azure IoT explorer-hulpprogramma gebruiken om de informatie te bekijken die naar de hub wordt verzendt. De voorbeeldtoepassing is geschreven voor Node.js en is opgenomen in de Azure IoT Hub Device SDK voor Node.js. Een ontwikkelaar van een oplossing kan het Azure IoT explorer-hulpprogramma gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder dat er apparaatcode hoeft te worden bekeken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, heb je Node.js op je ontwikkelmachine nodig. U de nieuwste aanbevolen versie voor meerdere platforms downloaden van [nodejs.org.](https://nodejs.org)

Gebruik de volgende opdracht om de huidige versie van Node.js op uw ontwikkelcomputer te controleren:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT-verkenner installeren

Download en installeer de nieuwste versie van **Azure IoT explorer** van de [repository-pagina](https://github.com/Azure/azure-iot-explorer/releases) van de tool door het MSI-bestand onder 'Assets' te selecteren voor de meest recente update.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart bereidt u een ontwikkelomgeving voor die u gebruiken om de Azure IoT Hub Device SDK voor Node.js te klonen en te bouwen.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de [Microsoft Azure IoT SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-repository op deze locatie te klonen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="install-required-libraries"></a>Vereiste bibliotheken installeren

U gebruikt de SDK van het apparaat om de meegeleverde voorbeeldcode te maken. De toepassing die u bouwt, simuleert een apparaat dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Ga in een lokaal terminalvenster naar de map van uw gekloonde opslagplaats en navigeer naar de **map /azure-iot-sdk-node/digitaltwins/samples/device/javascript.** Voer vervolgens de volgende opdracht uit om de vereiste bibliotheken te installeren:

    ```cmd/sh
    npm install
    ```
1. De _tekenreeks voor apparaatverbinding_configureren:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Het voorbeeldapparaat uitvoeren

Voer een voorbeeldtoepassing uit in de SDK om een IoT Plug and Play-apparaat te simuleren dat telemetrie naar uw IoT-hub verzendt. Als u de voorbeeldtoepassing wilt uitvoeren, gebruikt u de volgende opdracht:

```cmd\sh
    node sample_device.js
```

U ziet de volgende uitvoer, die aangeeft dat het apparaat is begonnen met het verzenden van telemetriegegevens naar de hub en nu klaar is om opdrachten en eigenschapsupdates te ontvangen.

   ![Bevestigingsberichten voor apparaten](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Houd het voorbeeld actief terwijl u de volgende stappen uitvoert.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT-verkenner gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Selecteer **Instellingen**om ervoor te zorgen dat het hulpprogramma de definities van het interfacemodel van uw apparaat kan lezen. In het menu Instellingen wordt **op het aangesloten apparaat** mogelijk al weergegeven in de plug-en play-configuraties. Als dit niet het zo is, selecteert u **+ Moduledefinitiebron toevoegen** en vervolgens **op het aangesloten apparaat** om deze toe te voegen.

1. Zoek terug op de **overzichtspagina Apparaten** de apparaatidentiteit die u eerder hebt gemaakt. Controleer of de **verbindingsstatus** van het apparaat in Azure IoT-verkenner wordt gemeld als _Verbonden_ (zo niet, druk op **Vernieuwen** totdat dit het geval is). Selecteer het apparaat om meer details weer te geven.

1. Breid de interface uit met **ID-urn:contoso:com:EnvironmentalSensor:1** om de interface en IoT Plug and Play-primitieven te onthullen: eigenschappen, opdrachten en telemetrie.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een IoT Plug and Play Preview-apparaat](howto-develop-solution.md)

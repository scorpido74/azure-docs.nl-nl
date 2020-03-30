---
title: IoT Plug and Play Preview-voorbeeldapparaatcode verbinden met IoT Hub (Linux) | Microsoft Documenten
description: IoT Plug and Play Preview-voorbeeldapparaatcode op Linux bouwen en uitvoeren die verbinding maakt met een IoT-hub. Gebruik de Azure CLI om de informatie weer te geven die door het apparaat naar de hub wordt verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531246"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Snelstart: sluit een voorbeeld van IoT Plug and Play Preview-apparaattoepassing die op Linux wordt uitgevoerd, aan op IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Deze quickstart laat u zien hoe u een voorbeeld van IoT Plug and Play-apparaattoepassing op Linux bouwen, deze aansluiten op uw IoT-bub en de Azure CLI gebruiken om de informatie te bekijken die naar de hub wordt verzendt. De voorbeeldtoepassing is geschreven in C en is opgenomen in de Azure IoT-apparaat SDK voor C. Een ontwikkelaar van een oplossing kan de Azure CLI gebruiken om inzicht te krijgen in de mogelijkheden van een IoT Plug and Play-apparaat zonder dat er apparaatcode hoeft te worden bekeken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Deze quickstart gaat ervan uit dat je Ubuntu Linux gebruikt. De stappen in deze tutorial zijn getest met Ubuntu 18.04.

Om deze quickstart te voltooien, moet u de volgende software op uw lokale Linux-machine installeren:

Installeer **GCC,** **Git,** **cmake**en alle `apt-get` afhankelijkheden met de opdracht:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Controleer of `cmake` de versie van is boven **2.8.12** en de versie van **GCC** is boven **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart bereidt u een ontwikkelomgeving voor die u gebruiken om de Azure IoT Hub Device C SDK te klonen en te bouwen.

Open een opdrachtprompt in de map van uw keuze. Voer de volgende opdracht uit om de [GitHub-opslagplaats azure IoT C-sedk's en bibliotheken](https://github.com/Azure/azure-iot-sdk-c) op deze locatie te klonen:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de meegeleverde voorbeeldcode te maken. De toepassing die u bouwt, simuleert een apparaat dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak `cmake` een submap in de hoofdmap van de apparaatSDK en navigeer naar die map:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK van het apparaat en de gegenereerde code-stub te bouwen:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Uw modelopslagplaats bijwerken

Voordat u het voorbeeld uitvoert, voegt u het apparaatcapaciteitsmodel en de interfacedefinities toe aan uw bedrijfsmodelopslagplaats:

1. Meld u aan bij de [Azure Certified for IoT-portalportal](https://preview.catalog.azureiotsolutions.com) met uw Microsoft-werk- of schoolaccount of uw Microsoft Partner ID als u er een hebt.

1. Selecteer **Bedrijfsopslagplaats** en vervolgens **Capaciteitsmodellen**.

1. Selecteer **Nieuw** en **upload vervolgens**.

1. Selecteer het `SampleDevice.capabilitymodel.json` bestand `digitaltwin_client/samples` in de map in de hoofdmap van de apparaatSDK. Selecteer **Openen** en vervolgens **Opslaan** om het modelbestand naar uw opslagplaats te uploaden.

1. Selecteer **Bedrijfsopslagplaats** en vervolgens **Interfaces**.

1. Selecteer **Nieuw** en **upload vervolgens**.

1. Selecteer het `EnvironmentalSensor.interface.json` bestand `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` in de map in de hoofdmap van de apparaatSDK. Selecteer **Openen** en vervolgens **Opslaan** om het interfacebestand naar uw opslagplaats te uploaden.

1. Selecteer **Bedrijfsopslagplaats** en vervolgens **Verbindingstekenreeksen**. Maak een notitie van de eerste _bedrijfsmodel repository verbindingstekenreeks,_ zoals u deze later in deze quickstart gebruikt.

## <a name="run-the-device-sample"></a>Het voorbeeld van het apparaat uitvoeren

Voer een voorbeeldtoepassing uit in de SDK om een IoT Plug and Play-apparaat te simuleren dat telemetrie naar uw IoT-hub verzendt. Ga als volgende over de volgende toepassing:

1. Navigeer `cmake` vanuit de map naar de map met het uitvoerbare bestand:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Voer het uitvoerbare bestand uit:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Het apparaat is nu klaar om opdrachten en eigendomsupdates te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Houd het voorbeeld actief terwijl u de volgende stappen uitvoert.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>De Azure IoT CLI gebruiken om de code te valideren

Nadat het voorbeeld van de apparaatclient is gestart, controleert u of het werkt met de Azure CLI.

Gebruik de volgende opdracht om de telemetrie weer te geven die het voorbeeldapparaat verzendt. Het kan nodig zijn om een minuut of twee te wachten voordat u telemetrie in de uitvoer ziet:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Gebruik de volgende opdracht om de eigenschappen weer te geven die door het apparaat worden verzonden:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat aansluiten op een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug and Play-apparaten:

> [!div class="nextstepaction"]
> [How-to: Verbinding maken met en communiceren met een apparaat](howto-develop-solution.md)

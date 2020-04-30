---
title: Voor beeld van een IoT Plug en Play preview-apparaat code koppelen aan IoT Hub (Linux) | Microsoft Docs
description: Maak IoT Plug en Play preview-voorbeeld code op Linux en voer deze uit om verbinding te maken met een IoT-hub. Gebruik de Azure CLI om de informatie weer te geven die door het apparaat naar de hub is verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75531246"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Quick Start: een voor beeld van een IoT Plug en Play preview-Device toepassing die op Linux wordt uitgevoerd, verbinden met IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

In deze Quick start ziet u hoe u een voor beeld van een IoT Plug en Play Device-toepassing in Linux maakt, hoe u deze verbindt met uw IoT-Bub en hoe u de Azure CLI gebruikt om de informatie weer te geven die wordt verzonden naar de hub. De voorbeeld toepassing is geschreven in C en is opgenomen in de Azure IoT Device SDK voor C. Een oplossings ontwikkelaar kan de Azure CLI gebruiken om inzicht te krijgen in de mogelijkheden van een IoT-Plug en Play apparaat zonder dat er toestel code hoeft te worden weer gegeven.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u Ubuntu Linux gebruikt. De stappen in deze zelf studie zijn getest met Ubuntu 18,04.

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale Linux-computer:

Installeer **gcc**, **Git**, **cmake**en alle afhankelijkheden met behulp van de `apt-get` opdracht:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Controleer of de versie `cmake` van is boven **2.8.12** en de versie van **gcc** hoger is dan **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick start gaat u een ontwikkel omgeving voorbereiden die u kunt gebruiken om de Azure IoT Hub Device C SDK te klonen en te bouwen.

Open een opdracht prompt in de gewenste map. Voer de volgende opdracht uit om de [Azure IOT C-sdk's en-bibliotheken](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats te klonen op deze locatie:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de opgenomen voorbeeld code te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een `cmake` submap in de hoofdmap van de apparaat-SDK en navigeer naar die map:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Uw model opslagplaats bijwerken

Voordat u het voor beeld uitvoert, voegt u het functionaliteits model van het apparaat en de interface definities toe aan uw bedrijfs model opslagplaats:

1. Meld u aan bij de [Portal van Azure Certified voor IOT](https://preview.catalog.azureiotsolutions.com) met uw werk-of school account van micro soft of uw micro soft-partner-id als u er een hebt.

1. Selecteer **bedrijfs opslagplaats** en vervolgens **mogelijkheden modellen**.

1. Selecteer **Nieuw** en **Upload**vervolgens.

1. Selecteer het bestand `SampleDevice.capabilitymodel.json` in de `digitaltwin_client/samples` map in de hoofdmap van de apparaat-SDK. Selecteer **openen** en vervolgens **Opslaan** om het model bestand te uploaden naar uw opslag plaats.

1. Selecteer **bedrijfs opslagplaats** en vervolgens de **interfaces**.

1. Selecteer **Nieuw** en **Upload**vervolgens.

1. Selecteer het bestand `EnvironmentalSensor.interface.json` in de `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` map in de hoofdmap van de apparaat-SDK. Selecteer **openen** en vervolgens **Opslaan** om het interface bestand te uploaden naar de opslag plaats.

1. Selecteer **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**. Noteer de eerste opslagplaats van het _bedrijfs model Connection String_, zoals u deze verderop in deze Quick Start gebruikt.

## <a name="run-the-device-sample"></a>Het voor beeld van het apparaat uitvoeren

Voer een voorbeeld toepassing in de SDK uit om een IoT-Plug en Play apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. De voorbeeld toepassing uitvoeren:

1. Navigeer vanuit `cmake` de map naar de map met het uitvoer bare bestand:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Voer het uitvoer bare bestand uit:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Het apparaat is nu gereed om opdrachten en updates van eigenschappen te ontvangen en is begonnen met het verzenden van telemetriegegevens naar de hub. Laat het voor beeld uitvoeren tijdens het uitvoeren van de volgende stappen.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>De code valideren met behulp van de Azure IoT CLI

Nadat het voor beeld van de apparaatclient is gestart, controleert u of deze werkt met de Azure CLI.

Gebruik de volgende opdracht om de telemetrie te bekijken waarvan het voor beeld-apparaat wordt verzonden. Mogelijk moet u een minuut of twee wachten voordat u een telemetrie in de uitvoer ziet:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Gebruik de volgende opdracht om de eigenschappen weer te geven die zijn verzonden door het apparaat:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en interactie met een apparaat](howto-develop-solution.md)

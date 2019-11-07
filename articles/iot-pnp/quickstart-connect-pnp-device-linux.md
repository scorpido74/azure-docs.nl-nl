---
title: Voor beeld van een IoT Plug en Play preview-apparaat code koppelen aan IoT Hub (Linux) | Microsoft Docs
description: Maak IoT Plug en Play preview-voorbeeld code op Linux en voer deze uit om verbinding te maken met een IoT-hub. Gebruik de Azure CLI om de informatie weer te geven die door het apparaat naar de hub is verzonden.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586668"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>Quick Start: een voor beeld van een IoT Plug en Play preview-Device toepassing die op Linux wordt uitgevoerd, verbinden met IoT Hub

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

Controleer of de versie van `cmake` hoger is dan **2.8.12** en of de versie van **gcc** hoger is dan **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!IMPORTANT]
> Tijdens de open bare preview zijn IoT-Plug en Play-functies alleen beschikbaar voor IoT-hubs die zijn gemaakt in de regio's **VS-centraal**, **Europa-Noord**en **Japan-Oost** .

Als u de Azure CLI lokaal gebruikt, moet de `az` versie **2.0.73** of hoger zijn, de Azure Cloud Shell maakt gebruik van de nieuwste versie. Gebruik de `az --version` opdracht om de versie te controleren die op uw computer is geïnstalleerd.

Voeg de Microsoft Azure IoT-extensie voor Azure CLI toe:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Voor de stappen in deze Snelstartgids is versie **0.8.5** of hoger van de extensie vereist. Gebruik de `az extension list` opdracht om de versie te controleren die u hebt geïnstalleerd en de `az extension update` opdracht zo nodig bij te werken.

Als u CLI lokaal gebruikt, meldt u zich aan bij uw Azure-abonnement met de volgende opdracht:

```bash
az login
```

Als u Azure Cloud Shell gebruikt, bent u al automatisch aangemeld.

Als u geen IoT Hub hebt, volgt u [deze instructies om er een te maken](../iot-hub/iot-hub-create-using-cli.md). Tijdens de open bare preview is IoT Plug en Play beschikbaar in de regio's Europa-noord, centraal, VS en Japan Oost. Zorg ervoor dat u uw hub in een van deze regio's maakt.

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduiding **YourIoTHubName** door de naam van uw echte IOT-hub:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

Voer de volgende opdrachten uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick start gaat u een ontwikkel omgeving voorbereiden die u kunt gebruiken om de Azure IoT C-SDK te klonen en te bouwen.

Open een opdrachtprompt. Voer de volgende opdracht uit voor het klonen van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-opslagplaats:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Het volt ooien van deze opdracht duurt enkele minuten.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de opgenomen voorbeeld code te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een `cmake`-submap in de hoofdmap van de apparaat-SDK en navigeer naar die map:

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

1. Selecteer de bestands `SampleDevice.capabilitymodel.json` in de map `digitaltwin_client/samples` in de hoofdmap van de apparaat-SDK. Selecteer **openen** en vervolgens **Opslaan** om het model bestand te uploaden naar uw opslag plaats.

1. Selecteer **bedrijfs opslagplaats** en vervolgens de **interfaces**.

1. Selecteer **Nieuw** en **Upload**vervolgens.

1. Selecteer de bestands `EnvironmentalSensor.interface.json` in de map `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` in de hoofdmap van de apparaat-SDK. Selecteer **openen** en vervolgens **Opslaan** om het interface bestand te uploaden naar de opslag plaats.

1. Selecteer **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**. Noteer de eerste opslagplaats van het bedrijfs model connection string u deze later in deze Quick Start gebruikt.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Voer een voorbeeld toepassing in de SDK uit om een IoT-Plug en Play apparaat te simuleren dat telemetrie verzendt naar uw IoT-hub. De voorbeeld toepassing uitvoeren:

1. Navigeer in de map `cmake` naar de map met het uitvoer bare bestand:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Voer het uitvoer bare bestand uit:

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

Het gesimuleerde apparaat begint met het verzenden van telemetrie, het Luis teren naar opdrachten en Luis teren naar eigenschaps updates.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>De code valideren met behulp van de Azure IoT CLI

Nadat het voor beeld van de apparaatclient is gestart, controleert u of deze werkt met de Azure CLI.

Gebruik de volgende opdracht om de telemetrie te bekijken waarvan het voor beeld-apparaat wordt verzonden. Mogelijk moet u een minuut of twee wachten voordat u een telemetrie in de uitvoer ziet:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

Gebruik de volgende opdracht om de eigenschappen weer te geven die zijn verzonden door het apparaat:

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt met een IoT-hub. Zie voor meer informatie over het bouwen van een oplossing die samenwerkt met uw IoT Plug en Play-apparaten:

> [!div class="nextstepaction"]
> [Instructies: verbinding maken met en interactie met een apparaat](howto-develop-solution.md)

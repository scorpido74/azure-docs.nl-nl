---
title: Een Azure IoT Plug en Play preview-apparaat (Linux) maken | Microsoft Docs
description: Gebruik een mogelijkheidsprofiel om de apparaatcode te genereren. Voer vervolgens de apparaatcode uit en Bekijk het apparaat verbinding maken met uw IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 087f1d76aaab4b05425262e0c1fb87b168c99b95
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931220"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Snelstartgids: een mogelijkheidsprofiel gebruiken voor het maken van een IoT Plug en Play preview-apparaat (Linux)

Een _mogelijkheidsprofiel_ (DCM) beschrijft de mogelijkheden van een IOT Plug en Play-apparaat. Een DCM is vaak gekoppeld aan een product-SKU. De mogelijkheden die zijn gedefinieerd in DCM, zijn ingedeeld in herbruikbare interfaces. U kunt skelet apparaatcode genereren vanuit een DCM. In deze Quick start ziet u hoe u VS code gebruikt op Ubuntu Linux om een IoT Plug en Play-apparaat te maken met een DCM.

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u Ubuntu Linux met een desktop omgeving gebruikt. De stappen in deze zelf studie zijn getest met Ubuntu 18,04.

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale Linux-computer:

* Installeer **gcc**, **Git**, **cmake**en alle afhankelijkheden met behulp van de `apt-get` opdracht:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Controleer of de versie van `cmake` hoger is dan **2.8.12** en of de versie van **gcc** hoger is dan **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT-Hulpprogram Ma's installeren

Gebruik de volgende stappen voor het installeren [van de Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek naar **Azure IOT-Hulpprogram ma's**.
1. Selecteer **Installeren**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De connection string voor uw bedrijfs model opslagplaats ophalen

Als u zich aanmeldt met een micro soft-werk-of school account, of uw micro soft-partner-ID als u deze hebt, kunt u uw _bedrijfsmodel opslagplaats Connection String_ vinden in de Portal Portal van [Azure Certified voor IOT](https://preview.catalog.azureiotsolutions.com) . Nadat u zich hebt aangemeld, selecteert u **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. Als u geen IoT hub hebt, zijn er stappen die hieronder worden gemaakt.

Als u de Azure CLI lokaal gebruikt, moet de `az` versie **2.0.75** of hoger zijn, de Azure Cloud Shell maakt gebruik van de nieuwste versie. Gebruik de `az --version` opdracht om de versie te controleren die op uw computer is geïnstalleerd.

Voer de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI toe te voegen aan uw Cloud Shell-exemplaar:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Voor de stappen in deze Snelstartgids is versie **0.8.5** of hoger van de extensie vereist. Gebruik de `az extension list` opdracht om de versie te controleren die u hebt geïnstalleerd en de `az extension update` opdracht zo nodig bij te werken.

Als u geen IoT-hub hebt, maakt u er een met de volgende opdrachten, waarbij u `<YourIoTHubName>` vervangt door een unieke naam van uw keuze. Als u deze opdrachten lokaal uitvoert, meldt u zich eerst aan bij uw Azure-abonnement met behulp van `az login`. Als u deze opdrachten uitvoert in de Azure Cloud shell, bent u automatisch aangemeld:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name <YourIoTHubName> \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Met de vorige opdrachten maakt u een resource groep met de naam `pnpquickstarts_rg` en een IoT-hub in de regio VS-centraal.

> [!IMPORTANT]
> Tijdens de open bare preview zijn IoT-Plug en Play-functies alleen beschikbaar voor IoT-hubs die zijn gemaakt in de regio's **VS-centraal**, **Europa-Noord**en **Japan-Oost** .

Voer de volgende opdracht uit om een apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduidingen **YourIoTHubName** en **YourDevice** door uw werkelijke namen.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDevice>
```

Voer de volgende opdrachten uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="author-your-model"></a>Uw model ontwerpen

In deze Quick Start gebruikt u een bestaand voor beeld-mogelijkheidsprofiel en de bijbehorende interfaces.

1. Maak een `pnp_app` Directory in uw lokale station. U gebruikt deze map voor de model bestanden van het apparaat en de stub van het apparaat.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Down load het functionaliteits model en de voorbeeld bestanden voor de interface naar de map `pnp_app`.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Open `pnp_app` map met VS code. U kunt de bestanden weer geven met IntelliSense:

    ![Mogelijkheidsprofiel](media/quickstart-create-pnp-device-linux/dcm.png)

1. In de bestanden die u hebt gedownload, vervangt u `<YOUR_COMPANY_NAME_HERE>` in de velden `@id` en `schema` met een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen. Zie [Digital Identifier-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u een DCM en de bijbehorende interfaces hebt, kunt u de apparaatcode genereren waarmee het model wordt geïmplementeerd. De C-code Souche in VS code genereren:

1. Open de map `pnp_app` in VS code en gebruik **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub voor de **apparaatcode**te selecteren.

    > [!NOTE]
    > De eerste keer dat u het hulp programma IoT Plug en Play code generator gebruikt, duurt het enkele seconden om automatisch te downloaden en te installeren.

1. Kies het bestand **SampleDevice. capabilitymodel. json** dat moet worden gebruikt voor het genereren van de code-stub van het apparaat.

1. Voer de naam van het project in **sample_device**. Dit is de naam van uw apparaat-app.

1. Kies **ANSI C** als uw taal.

1. Kies **Via IOT hub apparaat Connection String** als verbindings methode.

1. Kies **cmake-project in Linux** als uw project sjabloon.

1. Kies **via bron code** als de manier waarop u de SDK van het apparaat wilt toevoegen.

1. Er wordt een nieuwe map met de naam **sample_device** gemaakt op dezelfde locatie als het DCM-bestand en in het de gegenereerde code stub-bestanden. VS code opent een nieuw venster om deze weer te geven.
    ![apparaatcode](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U gebruikt de SDK-bron code van het apparaat om de gegenereerde stub voor de apparaatcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Voer de volgende opdrachten uit om de SDK-bron code van het apparaat te downloaden:

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. Maak een **cmake** -build-map voor de toepassing **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Voer CMake uit om uw app te bouwen met de SDK:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. Nadat de build is voltooid, voert u uw toepassing uit met het IoT hub-apparaat connection string als para meter.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<device connection string>"
    ```

1. De toepassing wordt gestart met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app wordt uitgevoerd](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Model bestanden van het apparaat publiceren naar model opslagplaats

Als u de apparaatcode met **AZ** cli wilt valideren, moet u de bestanden naar de model opslagplaats publiceren.

1. Met de map `pnp_app` open in VS code, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen. Typ en selecteer **IOT plug & Play: bestanden verzenden naar model opslagplaats**.

1. Selecteer `SampleDevice.capabilitymodel.json` en `EnvironmentalSensor.interface.json` bestanden.

1. Voer uw bedrijfs model opslagplaats connection string in.

    > [!NOTE]
    > De connection string is alleen vereist wanneer u voor het eerst verbinding maakt met de opslag plaats.

1. In het venster voor het uitvoeren van VS code en meldingen kunt u controleren of de bestanden zijn gepubliceerd.

    > [!NOTE]
    > Als er fouten optreden bij het publiceren van de model bestanden van het apparaat, kunt u gebruikmaken van de opdracht **IoT Plug en Play: model opslagplaats afmelden** om u af te melden en de stappen opnieuw door te lopen.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>De code valideren met behulp van de Azure IoT CLI

Nadat het voor beeld van de apparaatclient is gestart, kunt u controleren of deze werkt met de Azure CLI.

Gebruik de volgende opdracht om de telemetrie te bekijken waarvan het voor beeld-apparaat wordt verzonden. Mogelijk moet u een minuut of twee wachten voordat u een telemetrie in de uitvoer ziet:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDevice>
```

Gebruik de volgende opdracht om alle eigenschappen weer te geven die zijn verzonden door het apparaat:

```azurecli-interactive
az iot dt list-properties --device-id <YourDevice> --hub-name <YourIoTHubNme> --source private --repo-login "<Your company model repository connection string>"
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat kunt maken met behulp van een DCM.

Ga verder met de zelf studie voor meer informatie over DCMs en het maken van uw eigen modellen:

> [!div class="nextstepaction"]
> [Zelf studie: een mogelijkheidsprofiel maken en testen met Visual Studio code](tutorial-pnp-visual-studio-code.md)

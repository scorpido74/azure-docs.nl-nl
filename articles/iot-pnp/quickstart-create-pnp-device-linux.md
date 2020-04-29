---
title: Een Azure IoT Plug en Play preview-apparaat (Linux) maken | Microsoft Docs
description: Gebruik een mogelijkheidsprofiel om de apparaatcode te genereren. Voer vervolgens de apparaatcode uit en Bekijk het apparaat verbinding maken met uw IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75550478"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Snelstartgids: een mogelijkheidsprofiel gebruiken voor het maken van een IoT Plug en Play preview-apparaat (Linux)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Een _mogelijkheidsprofiel_ (DCM) beschrijft de mogelijkheden van een IOT Plug en Play-apparaat. Een DCM is vaak gekoppeld aan een product-SKU. De mogelijkheden die zijn gedefinieerd in DCM, zijn ingedeeld in herbruikbare interfaces. U kunt skelet apparaatcode genereren vanuit een DCM. In deze Quick start ziet u hoe u VS code gebruikt op Ubuntu Linux om een IoT Plug en Play-apparaat te maken met een DCM.

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u Ubuntu Linux met een desktop omgeving gebruikt. De stappen in deze zelf studie zijn getest met Ubuntu 18,04.

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale Linux-computer:

* Installeer **gcc**, **Git**, **cmake**en alle afhankelijkheden met behulp van de `apt-get` opdracht:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Controleer of de versie `cmake` van is boven **2.8.12** en de versie van **gcc** hoger is dan **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT-Hulpprogram Ma's installeren

Gebruik de volgende stappen voor het installeren [van de Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek naar **Azure IOT-Hulpprogram ma's**.
1. Selecteer **Installeren**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De connection string voor uw bedrijfs model opslagplaats ophalen

Als u zich aanmeldt met een micro soft-werk-of school account, of uw micro soft-partner-ID als u deze hebt, kunt u uw _bedrijfsmodel opslagplaats Connection String_ vinden in de Portal Portal van [Azure Certified voor IOT](https://preview.catalog.azureiotsolutions.com) . Nadat u zich hebt aangemeld, selecteert u **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick Start gebruikt u de [Vcpkg](https://github.com/microsoft/vcpkg) -bibliotheek beheerder om de Azure IOT C-SDK in uw ontwikkel omgeving te installeren.

Open een shell. Voer de volgende opdracht uit om Vcpkg te installeren:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="author-your-model"></a>Uw model ontwerpen

In deze Quick Start gebruikt u een bestaand voor beeld-mogelijkheidsprofiel en de bijbehorende interfaces.

1. Maak een `pnp_app` map op uw lokale station. U gebruikt deze map voor de model bestanden van het apparaat en de stub van het apparaat.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Down load het functionaliteits model en de voorbeeld bestanden voor `pnp_app` de interface naar de map.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Open `pnp_app` de map met VS code. U kunt de bestanden weer geven met IntelliSense:

    ![Mogelijkheidsprofiel](media/quickstart-create-pnp-device-linux/dcm.png)

1. Vervang `<YOUR_COMPANY_NAME_HERE>` in de bestanden die u hebt gedownload in `@id` de `schema` velden en een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen. Zie [Digital Identifier-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u een DCM en de bijbehorende interfaces hebt, kunt u de apparaatcode genereren waarmee het model wordt geïmplementeerd. De C-code Souche in VS code genereren:

1. Open de `pnp_app` map in VS code en gebruik **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren**in te scha kelen.

    > [!NOTE]
    > De eerste keer dat u het hulp programma IoT Plug en Play code generator gebruikt, duurt het enkele seconden om automatisch te downloaden en te installeren.

1. Kies het bestand **SampleDevice. capabilitymodel. json** dat moet worden gebruikt voor het genereren van de code-stub van het apparaat.

1. Voer de naam van het project in **sample_device**. Dit is de naam van uw apparaat-app.

1. Kies **ANSI C** als uw taal.

1. Kies **Via IOT hub apparaat Connection String** als verbindings methode.

1. Kies **cmake-project in Linux** als uw project sjabloon.

1. Kies **via Vcpkg** als de manier waarop u de SDK van het apparaat wilt toevoegen.

1. Er wordt een nieuwe map met de naam **sample_device** gemaakt op dezelfde locatie als het DCM-bestand en in het de gegenereerde code stub-bestanden. VS code opent een nieuw venster om deze weer te geven.
    ![Apparaatcode](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U gebruikt de SDK-bron code van het apparaat om de gegenereerde stub voor de apparaatcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een **cmake** -build-map voor de toepassing **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Voer CMake uit om uw app te bouwen met de SDK. Bij de volgende opdracht wordt ervan uitgegaan dat u **vcpkg** hebt geïnstalleerd in uw basismap:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Nadat de build is voltooid, voert u uw toepassing uit met het IoT hub-apparaat connection string als para meter.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. De toepassing wordt gestart met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app wordt uitgevoerd](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Model bestanden van het apparaat publiceren naar model opslagplaats

Als u de apparaatcode met **AZ** cli wilt valideren, moet u de bestanden naar de model opslagplaats publiceren.

1. Als de `pnp_app` map is geopend in VS code, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen. Typ en selecteer **IOT plug & Play: bestanden verzenden naar model opslagplaats**.

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
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Gebruik de volgende opdracht om alle eigenschappen weer te geven die zijn verzonden door het apparaat:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat kunt maken met behulp van een DCM.

Ga verder met de zelf studie voor meer informatie over DCMs en het maken van uw eigen modellen:

> [!div class="nextstepaction"]
> [Zelf studie: een mogelijkheidsprofiel maken en testen met Visual Studio code](tutorial-pnp-visual-studio-code.md)

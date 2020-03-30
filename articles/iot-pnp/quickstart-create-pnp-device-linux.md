---
title: Een Azure IoT Plug and Play Preview-apparaat (Linux) maken | Microsoft Documenten
description: Gebruik een apparaatcapaciteitsmodel om apparaatcode te genereren. Voer vervolgens de apparaatcode uit en zie het apparaat verbinding maken met uw IoT-hub.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550478"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Snelstart: gebruik een apparaatcapaciteitsmodel om een IoT Plug and Play Preview-apparaat (Linux) te maken

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Een _apparaatmogelijkheidsmodel_ (DCM) beschrijft de mogelijkheden van een IoT Plug and Play-apparaat. Een DCM wordt vaak geassocieerd met een product SKU. De mogelijkheden die in de DCM worden gedefinieerd, zijn ingedeeld in herbruikbare interfaces. U de code van het skeletapparaat van een DCM genereren. Deze quickstart laat zien hoe je VS Code op Ubuntu Linux gebruiken om een IoT Plug and Play-apparaat te maken met behulp van een DCM.

## <a name="prerequisites"></a>Vereisten

Deze quickstart gaat ervan uit dat je Ubuntu Linux gebruikt met een desktopomgeving. De stappen in deze tutorial zijn getest met Ubuntu 18.04.

Om deze quickstart te voltooien, moet u de volgende software op uw lokale Linux-machine installeren:

* Installeer **GCC,** **Git,** **cmake**en alle `apt-get` afhankelijkheden met de opdracht:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Controleer of `cmake` de versie van is boven **2.8.12** en de versie van **GCC** is boven **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT-hulpprogramma's installeren

Gebruik de volgende stappen om het [uitbreidingspakket azure IoT-hulpprogramma's voor VS-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) te installeren:

1. Selecteer in VS-code het tabblad **Extensies.**
1. Zoeken naar **Azure IoT-hulpprogramma's**.
1. Selecteer **Installeren**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De verbindingstekenreeks voor uw bedrijfsmodelrepository opvragen

U uw _bedrijfsmodelverbindingstekenreeks_ vinden in de [Azure Certified for IoT-portal](https://preview.catalog.azureiotsolutions.com) wanneer u zich aanmeldt met een Microsoft-werk- of schoolaccount of uw Microsoft Partner ID als u er een hebt. Nadat u zich hebt aangemeld, selecteert u **Bedrijfsopslagplaats** en vervolgens **Verbindingstekenreeksen**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart gebruikt u de [Vcpkg-bibliotheekbeheerder](https://github.com/microsoft/vcpkg) om de Azure IoT C-apparaat SDK in uw ontwikkelomgeving te installeren.

Open een shell. Voer de volgende opdracht uit om Vcpkg te installeren:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Deze bewerking kan enkele minuten in beslag nemen.

## <a name="author-your-model"></a>Uw model schrijven

In deze quickstart gebruikt u een bestaand voorbeeldmodel voor het apparaaten en bijbehorende interfaces.

1. Maak `pnp_app` een map in uw lokale schijf. U gebruikt deze map voor de apparaatmodelbestanden en apparaatcode.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Download het apparaatcapaciteitsmodel en interface `pnp_app` voorbeeldbestanden naar de map.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Map `pnp_app` openen met VS-code. U de bestanden bekijken met IntelliSense:

    ![Apparaatcapaciteitsmodel](media/quickstart-create-pnp-device-linux/dcm.png)

1. Vervang `<YOUR_COMPANY_NAME_HERE>` in de bestanden die `@id` u `schema` hebt gedownload in de velden en velden met een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderbied. Zie [Digital Twin-id-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u een DCM en de bijbehorende interfaces hebt, u de apparaatcode genereren die het model implementeert. Ga als bedoeld als u de C-codestub in VS-code wilt genereren:

1. Als `pnp_app` de map is geopend in VS-code, gebruikt u **Ctrl+Shift+P** om het opdrachtpalet te openen, **IoT Plug and Play**in te voeren en **apparaatcode stub genereren**in te schakelen.

    > [!NOTE]
    > De eerste keer dat u de IoT Plug and Play Code Generator utility gebruikt, duurt het een paar seconden om automatisch te downloaden en te installeren.

1. Kies het bestand **SampleDevice.capabilitymodel.json** dat u wilt gebruiken voor het genereren van de apparaatcode.stub.

1. Voer de projectnaam **sample_device**in . Dit is de naam van uw apparaattoepassing.

1. Kies **ANSI C** als taal.

1. Kies **Via IoT Hub-verbindingstekenreeks** als verbindingsmethode.

1. Kies **CMake Project op Linux** als projectsjabloon.

1. Kies **Via Vcpkg** als de manier om het apparaat SDK op te nemen.

1. Er wordt een nieuwe map met **sample_device** gemaakt op dezelfde locatie als het DCM-bestand en daarin de gegenereerde apparaatcode-stub-bestanden. VS Code opent een nieuw venster om deze weer te geven.
    ![Apparaatcode](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U gebruikt de SDK-broncode van het apparaat om de gegenereerde apparaatcode te bouwen. De toepassing die u bouwt, simuleert een apparaat dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een **CMake-buildmap** voor de **sample_device-toepassing:**

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Voer CMake uit om uw app te bouwen met de SDK. Met de volgende opdracht wordt ervan uitgegaan dat u **vcpkg** in uw thuismap hebt geïnstalleerd:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Nadat de build is voltooid, voert u de toepassing uit die de verbindingstekenreeks voor iot-hub-apparaten als parameter passeert.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. De apparaattoepassing begint met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app actief](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Apparaatmodelbestanden publiceren om opslagplaats te modelleren

Om de apparaatcode met de **az** CLI te valideren, moet u de bestanden publiceren naar de modelopslagplaats.

1. Als `pnp_app` de map is geopend in VS-code, gebruikt u **Ctrl+Shift+P** om het opdrachtpalet te openen, het opdrachtpalet te typen en te selecteren **& Afspelen: Bestanden verzenden naar Model Repository**.

1. Selecteer `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` en bestanden.

1. Voer de tekenreeks van uw bedrijfsmodelrepository-verbindingsreeks in.

    > [!NOTE]
    > De verbindingstekenreeks is alleen vereist wanneer u voor het eerst verbinding maakt met de repository.

1. In het uitvoervenster en de melding vs-code u controleren of de bestanden zijn gepubliceerd.

    > [!NOTE]
    > Als er fouten worden gemaakt bij het publiceren van de apparaatmodelbestanden, u proberen de opdracht **IoT Plug and Play: Sign out Model Repository** te gebruiken om u af te melden en de stappen opnieuw te doorlopen.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>De Azure IoT CLI gebruiken om de code te valideren

Nadat het voorbeeld van de apparaatclient is gestart, u controleren of het werkt met de Azure CLI.

Gebruik de volgende opdracht om de telemetrie weer te geven die het voorbeeldapparaat verzendt. Het kan nodig zijn om een minuut of twee te wachten voordat u telemetrie in de uitvoer ziet:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Gebruik de volgende opdracht om alle eigenschappen weer te geven die door het apparaat worden verzonden:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat maakt met behulp van een DCM.

Ga voor meer informatie over DCM's en het maken van uw eigen modellen verder naar de zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Een apparaatcapaciteitsmodel maken en testen met Visual Studio Code](tutorial-pnp-visual-studio-code.md)

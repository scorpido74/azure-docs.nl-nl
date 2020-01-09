---
title: Een IoT Plug en Play preview-apparaat (Windows) maken | Microsoft Docs
description: Gebruik een mogelijkheidsprofiel om de apparaatcode te genereren. Voer vervolgens de apparaatcode uit en Bekijk het apparaat verbinding maken met uw IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b447a0328de210caeb23a1beb91c532853c180bc
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550414"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Quick Start: een mogelijkheidsprofiel gebruiken om een IoT Plug en Play preview-apparaat te maken (Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Een _mogelijkheidsprofiel_ (DCM) beschrijft de mogelijkheden van een IOT Plug en Play-apparaat. Een DCM is vaak gekoppeld aan een product-SKU. De mogelijkheden die zijn gedefinieerd in DCM, zijn ingedeeld in herbruikbare interfaces. U kunt skelet apparaatcode genereren vanuit een DCM. In deze Quick start ziet u hoe u VS code kunt gebruiken in Windows om een IoT Plug en Play-apparaat te maken met behulp van een DCM.

## <a name="prerequisites"></a>Vereisten

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale computer:

* [Bouw hulpprogram ma's voor Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met  **C++ build tools** en **NuGet package manager-onderdeel** workloads. Of als u [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 of 2015 al hebt geïnstalleerd met dezelfde workloads.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT-Hulpprogram Ma's installeren

Gebruik de volgende stappen voor het installeren [van de Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack:

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek naar **Azure IOT-Hulpprogram ma's**.
1. Selecteer **Installeren**.

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer de nieuwste versie van **Azure IOT Explorer** vanaf de [bibliotheek](https://github.com/Azure/azure-iot-explorer/releases) pagina van het hulp programma door het MSI-bestand te selecteren onder ' assets ' voor de meest recente update.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De connection string voor uw bedrijfs model opslagplaats ophalen

Als u zich aanmeldt met een micro soft-werk-of school account, of uw micro soft-partner-ID als u deze hebt, kunt u uw _bedrijfsmodel opslagplaats Connection String_ vinden in de Portal Portal van [Azure Certified voor IOT](https://preview.catalog.azureiotsolutions.com) . Nadat u zich hebt aangemeld, selecteert u **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IOT hub-Connection String_ voor uw hub op te halen (Let op later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze Quick Start gebruikt u de [Vcpkg](https://github.com/microsoft/vcpkg) -bibliotheek beheerder om de Azure IOT C-SDK in uw ontwikkel omgeving te installeren.

1. Open een opdrachtprompt. Voer de volgende opdracht uit om Vcpkg te installeren:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ga vervolgens als volgt te werk om de [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)van de gebruikers naam te verbreden (Opmerking: hiervoor moet de beheerder het eerste gebruik gebruiken):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installeer de Azure IoT C Device SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Uw model ontwerpen

In deze Quick Start gebruikt u een bestaand voor beeld-mogelijkheidsprofiel en de bijbehorende interfaces.

1. Maak een `pnp_app` Directory in uw lokale station. U gebruikt deze map voor de model bestanden van het apparaat en de stub van het apparaat.

1. Down load het mogelijkheidsprofiel en de [Interface voorbeeld bestanden](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) en [Interface voorbeeld](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) en sla bestanden op in `pnp_app` map.

    > [!TIP]
    > Als u een bestand van GitHub wilt downloaden, gaat u naar het bestand, klikt u met de rechter muisknop op **RAW**en selecteert u vervolgens **Koppeling opslaan als**.

1. Open `pnp_app` map met VS code. U kunt de bestanden weer geven met IntelliSense:

    ![Mogelijkheidsprofiel](media/quickstart-create-pnp-device/dcm.png)

1. In de bestanden die u hebt gedownload, vervangt u `<YOUR_COMPANY_NAME_HERE>` in de velden `@id` en `schema` met een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen. Zie [Digital Identifier-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u een DCM en de bijbehorende interfaces hebt, kunt u de apparaatcode genereren waarmee het model wordt geïmplementeerd. De C-code Souche in VS code genereren:

1. Open de map `pnp_app` in VS code en gebruik **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub voor de **apparaatcode**te selecteren.

    > [!NOTE]
    > De eerste keer dat u IoT Plug en Play CodeGen CLI gebruikt, duurt het enkele seconden om automatisch te downloaden en te installeren.

1. Kies het bestand **SampleDevice. capabilitymodel. json** dat moet worden gebruikt voor het genereren van de code-stub van het apparaat.

1. Voer de naam van het project in **sample_device**. Dit is de naam van uw apparaat-app.

1. Kies **ANSI C** als uw taal.

1. Kies **Via IOT hub apparaat Connection String** als verbindings methode.

1. Kies **cmake project in Windows** als uw project sjabloon.

1. Kies **via Vcpkg** als de manier waarop u de SDK van het apparaat wilt toevoegen.

1. Er wordt een nieuwe map met de naam **sample_device** gemaakt op dezelfde locatie als het DCM-bestand en in het de gegenereerde code stub-bestanden. VS code opent een nieuw venster om deze weer te geven.
    ![apparaatcode](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U gebruikt de SDK-bron code van het apparaat om de gegenereerde stub voor de apparaatcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een `cmake`-submap in de map `sample_device` en navigeer naar die map:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de gegenereerde code-stub te maken (Vervang de tijdelijke aanduiding door de map van uw Vcpkg opslag plaats):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Als u Visual Studio 2017 of 2015 gebruikt, moet u de CMake-Generator opgeven op basis van de build-hulpprogram ma's die u gebruikt:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Als cmake uw C++ compiler niet kan vinden, krijgt u tijdens het uitvoeren van de vorige opdracht fouten bij het bouwen. Als dat het geval is, kunt u proberen deze opdracht uit te voeren op de [Visual Studio-opdracht prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Nadat de build is voltooid, voert u uw toepassing uit, waarbij u de IoT hub-apparaat connection string als een para meter.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. De toepassing wordt gestart met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app wordt uitgevoerd](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Model bestanden van het apparaat publiceren naar model opslagplaats

Als u de apparaatcode met **Azure IOT Explorer**wilt valideren, moet u de bestanden naar de model opslagplaats publiceren.

1. Met de map `pnp_app` open in VS code, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen. Typ en selecteer **IOT plug & Play: bestanden verzenden naar model opslagplaats**.

1. Selecteer `SampleDevice.capabilitymodel.json` en `EnvironmentalSensor.interface.json` bestanden.

1. Voer uw bedrijfs model opslagplaats connection string in.

    > [!NOTE]
    > De connection string is alleen vereist wanneer u voor het eerst verbinding maakt met de opslag plaats.

1. In het venster voor het uitvoeren van VS code en meldingen kunt u controleren of de bestanden zijn gepubliceerd.

    > [!NOTE]
    > Als er fouten optreden bij het publiceren van de model bestanden van het apparaat, kunt u gebruikmaken van de opdracht **IoT Plug en Play: model opslagplaats afmelden** om u af te melden en de stappen opnieuw door te lopen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Als u uw bedrijfs opslagplaats wilt toevoegen, selecteert u **instellingen**en vervolgens een **module definitie bron toevoegen**en vervolgens **bedrijfs opslagplaats**. Voeg uw bedrijfs model opslagplaats connection string toe en selecteer **opslaan en verbinden**.

1. Ga terug naar de overzichts pagina **apparaten** en zoek de apparaat-id die u eerder hebt gemaakt. Wanneer de toepassing nog steeds wordt uitgevoerd in de opdracht prompt, controleert u of de **verbindings status** van het apparaat in azure IOT Explorer wordt gerapporteerd als _verbonden_ (als dat niet het geval is, kunt u op **vernieuwen** drukken totdat dit is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de ID **urn: < YOUR_INTERFACE_NAME >: EnvironmentalSensor: 1** om de IOT Plug en Play-primitieven-eigenschappen, opdrachten en telemetrie weer te geven. De interface naam die wordt weer gegeven, is de naam die u hebt ingevoerd bij het ontwerpen van uw model.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat kunt maken met behulp van een DCM.

Ga verder met de zelf studie voor meer informatie over DCMs en het maken van uw eigen modellen:

> [!div class="nextstepaction"]
> [Zelf studie: een mogelijkheidsprofiel maken en testen met Visual Studio code](tutorial-pnp-visual-studio-code.md)

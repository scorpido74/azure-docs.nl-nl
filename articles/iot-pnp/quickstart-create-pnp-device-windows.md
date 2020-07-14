---
title: Een IoT Plug and Play-apparaat maken (preview in Windows) | Microsoft Docs
description: Lees hoe u een apparaatondersteuningsprofiel gebruikt om apparaatcode te genereren. Vervolgens voert u de apparaatcode uit en ziet u hoe het apparaat verbinding maakt met uw IoT Hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b02455c153c6205b87a64a388d5588c3c431e0c9
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963492"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Quickstart: Een IoT Plug and Play-apparaat maken met behulp van een apparaatondersteuningsprofiel (preview in Windows)

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

In een _apparaatondersteuningsprofiel_ (Device Capability Model of DCM) worden de mogelijkheden van een IoT-Plug and Play-apparaat beschreven. Een DCM is vaak gekoppeld aan een product-SKU. De mogelijkheden die zijn gedefinieerd in het DCM, zijn georganiseerd in herbruikbare interfaces. U kunt skeleton-apparaatcode genereren vanuit een DCM. In deze quickstart ziet u hoe u VS Code kunt gebruiken in Windows om een IoT Plug and Play-apparaat te maken met behulp van een DCM.

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, moet u de volgende software installeren op uw lokale computer:

* [Build Tools for Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met **C++ Build Tools** en werkbelastingen voor **NuGet package manager component**. Als u al beschikt over [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/), 2019, 2017 of 2015 met dezelfde werkbelastingen geïnstalleerd.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT Tools installeren

Gebruik de volgende stappen voor het installeren van het Extension Pack [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools):

1. Selecteer in VS Code het tabblad **Extensions**.
1. Zoek naar **Azure IoT Tools**.
1. Selecteer **Installeren**.

### <a name="install-the-azure-iot-explorer"></a>Azure IoT Explorer installeren

Download en installeer de nieuwste versie van **Azure IoT Explorer-** via de pagina [repository](https://github.com/Azure/azure-iot-explorer/releases) van de tool door onder Assets het MSI-bestand te selecteren voor de meest recente update.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De verbindingsreeks ophalen voor uw bedrijfsmodelopslagplaats

Als u zich aanmeldt met een Microsoft-werk- of schoolaccount, of met een Microsoft Partner-id als u die hebt, kunt u de _verbindingsreeks voor uw bedrijfsmodel opslagplaats_ vinden in de [portal van Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com). Meld u aan en selecteer **Company repository** en vervolgens **Connection strings**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _verbindingsreeks voor IoT Hub_ op te halen voor uw hub (dit is voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart gebruikt u [Vcpkg](https://github.com/microsoft/vcpkg) (een programma voor bibliotheekbeheer) om de Azure IoT C Device SDK te installeren in uw ontwikkelomgeving.

1. Open een opdrachtprompt. Voer de volgende opdracht uit om Vcpkg te installeren:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Voer vervolgens deze opdracht uit om [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) voor alle gebruikers in te stellen (hiervoor is beheerdersbevoegdheid vereist bij het eerste gebruik):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installeer de Azure IoT C Device SDK:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use-prov-client]
    ```

## <a name="author-your-model"></a>Het model ontwerpen

In deze quickstart gebruikt u een bestaand voorbeeld van een apparaatondersteuningsprofiel en de bijbehorende interfaces.

1. Maak een `pnp_app` map op het lokale station. U gebruikt deze map voor de bestanden van het apparaatmodel en de codestubs.

1. Download de [apparaatondersteuningsmodel](https://github.com/Azure/opendigitaltwins-dtdl/blob/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/SampleDevice.capabilitymodel.json)- en [interfacevoorbeeld](https://github.com/Azure/opendigitaltwins-dtdl/blob/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensor.interface.json)bestanden en sla ze op in de map `pnp_app`.

    > [!TIP]
    > Als u een bestand wilt downloaden van GitHub, gaat u naar het bestand, klikt u er met de rechtermuisknop op, selecteert u **Raw** en selecteert u vervolgens **Koppeling opslaan als** (of een vergelijkbare opdracht).

1. Open de map `pnp_app` met VS Code. U kunt de bestanden weergeven met IntelliSense:

    ![Apparaatondersteuningsprofiel](media/quickstart-create-pnp-device/dcm.png)

1. In de bestanden die u hebt gedownload, vervangt u `<YOUR_COMPANY_NAME_HERE>` in de velden `@id` en `schema` door een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepingstekens. Zie [Digital Twin Definition Language](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format) voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-codestub genereren

U beschikt nu over een DCM en de bijbehorende interfaces en dus kunt u de apparaatcode gaan genereren waarmee het model wordt geïmplementeerd. De C-codestub genereren in VS Code:

1. Zorg dat de map `pnp_app` geopend is in VS Code en druk op **Ctrl+Shift+P** om het opdrachtenpalet te openen, voer **IoT Plug and Play** in en selecteer **Generate Device Code Stub**.

    > [!NOTE]
    > De eerste keer dat u IoT Plug and Play CodeGen CLI gebruikt, duurt het enkele seconden om de CLI te downloaden en automatisch te installeren.

1. Kies het bestand **SampleDevice.capabilitymodel.json** dat u wilt gebruiken voor het genereren van de codestub voor het apparaat.

1. Geef de projectnaam **sample_device** op. Dit is de naam van de apparaattoepassing.

1. Kies **ANSI C** als de taal.

1. Kies **Via IoT Hub device connection string** als de verbindingsmethode.

1. Kies **CMake Project on Windows** als de projectsjabloon.

1. Kies **Via Vcpkg** als de manier om de apparaat-SDK toe te voegen.

1. Er wordt een nieuwe map met de naam **sample_device** gemaakt op dezelfde locatie als het DCM-bestand. De map bevat de gegenereerde codestubbestanden voor het apparaat. In VS Code wordt een nieuw venster geopend om de stubs weer te geven.
    ![Apparaatcode](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U gebruikt het Vcpkg-pakket om de gegenereerde codestub voor een apparaat te bouwen. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak een submap `cmake` in de map `sample_device` en navigeer naar die map:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de gegenereerde codestub te maken (vervang de tijdelijke aanduiding door de map van uw Vcpkg-opslagplaats):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Als u Visual Studio 2017 of 2015 gebruikt, moet u de CMake-generator opgeven op basis van de Build Tools die u gebruikt:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Als Cmake uw C++-compiler niet kan vinden, treden er build-fouten op wanneer u de vorige opdracht uitvoert. Als dit gebeurt, voert u deze opdracht uit bij de [opdrachtprompt van Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Als de build is voltooid, kunt u de toepassing uitvoeren, waarbij de verbindingsreeks voor het IoT Hub-apparaat wordt doorgegeven als een parameter.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. De apparaattoepassing begint met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app die wordt uitgevoerd](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Apparaatmodelbestanden publiceren naar modelopslagplaats

Als u de apparaatcode wilt valideren met **Azure IoT Explorer-** , moet u de bestanden naar de modelopslagplaats publiceren.

1. Zorg dat de map `pnp_app` geopend is in VS Code en druk op **Ctrl+Shift+P** om het opdrachtenpalet te openen, en typ en selecteer **IoT Plug & Play: Submit files to Model Repository**.

1. Selecteer de bestanden `SampleDevice.capabilitymodel.json` en `EnvironmentalSensor.interface.json`.

1. Voer de verbindingsreeks voor uw bedrijfsmodelopslagplaats in.

    > [!NOTE]
    > De verbindingsreeks is alleen vereist wanneer u voor het eerst verbinding maakt met de opslagplaats.

1. In het uitvoervenster van VS Code en in de melding kunt u controleren of de bestanden zijn gepubliceerd.

    > [!NOTE]
    > Als er fouten optreden bij het publiceren van de modelbestanden van het apparaat, kunt u proberen om de opdracht **IoT Plug and Play: Sign out Model Repository** uit te voeren om u af te melden en de stappen opnieuw te doorlopen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Code valideren met de Azure IoT Explorer

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Als u uw bedrijfsopslagplaats wilt toevoegen, selecteert u achtereenvolgens **Settings**, **+ Add module definition source** en **Company repository**. Voeg de verbindingsreeks van uw bedrijfsmodelopslagplaats toe en selecteer **Save and Connect**.

1. Zoek op de overzichtspagina **Devices** de apparaat-id die u eerder hebt gemaakt. Controleer terwijl de apparaattoepassing nog steeds wordt uitgevoerd bij de opdrachtprompt of bij **Connection state** in Azure IoT Explorer de status van het apparaat wordt weergegeven als _Connected_ (als dat niet zo is, selecteert u **Refresh** tot dat wel het geval is). Selecteer het apparaat om meer details weer te geven.

1. Vouw de interface met de id **urn:<NAAM_VAN_UW_INTERFACE>:EnvironmentalSensor:1** uit om de primitieven (eigenschappen, opdrachten en telemetrie) van IoT Plug and Play te bekijken. De interfacenaam die wordt weergegeven, is de naam die u hebt ingevoerd bij het ontwerpen van uw model.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een IoT-Plug and Play-apparaat kunt maken met behulp van een DCM.

Als u meer wilt weten over DCM's en het maken van uw eigen modellen, gaat u verder met de zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Een apparaatondersteuningsprofiel maken en testen met Visual Studio Code](tutorial-pnp-visual-studio-code.md)

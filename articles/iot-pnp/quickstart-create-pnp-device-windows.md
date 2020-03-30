---
title: Een IoT-stekker- en afspeelvoorbeeldapparaat maken (Windows) | Microsoft Documenten
description: Gebruik een apparaatcapaciteitsmodel om apparaatcode te genereren. Voer vervolgens de apparaatcode uit en zie het apparaat verbinding maken met uw IoT-hub.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e80194f53a406b8b378d0fb787df627937125a27
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75867489"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Snelstart: gebruik een apparaatcapaciteitsmodel om een IoT Plug and Play Preview-apparaat (Windows) te maken

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Een _apparaatmogelijkheidsmodel_ (DCM) beschrijft de mogelijkheden van een IoT Plug and Play-apparaat. Een DCM wordt vaak geassocieerd met een product SKU. De mogelijkheden die in de DCM worden gedefinieerd, zijn ingedeeld in herbruikbare interfaces. U de code van het skeletapparaat van een DCM genereren. Deze quickstart laat zien hoe u VS-code op Windows gebruiken om een IoT Plug and Play-apparaat te maken met behulp van een DCM.

## <a name="prerequisites"></a>Vereisten

Om deze quickstart te voltooien, moet u de volgende software op uw lokale machine installeren:

* [Bouw tools voor Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met **C++ buildtools** en **NuGet package manager component** workloads. Of als u al [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 of 2015 hebt met dezelfde workloads geïnstalleerd.
* [Git.](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Azure IoT-hulpprogramma's installeren

Gebruik de volgende stappen om het [uitbreidingspakket azure IoT-hulpprogramma's voor VS-code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) te installeren:

1. Selecteer in VS-code het tabblad **Extensies.**
1. Zoeken naar **Azure IoT-hulpprogramma's**.
1. Selecteer **Installeren**.

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT-verkenner installeren

Download en installeer de nieuwste versie van **Azure IoT explorer** van de [repository-pagina](https://github.com/Azure/azure-iot-explorer/releases) van de tool door het MSI-bestand onder 'Assets' te selecteren voor de meest recente update.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De verbindingstekenreeks voor uw bedrijfsmodelrepository opvragen

U uw _bedrijfsmodelverbindingstekenreeks_ vinden in de [Azure Certified for IoT-portal](https://preview.catalog.azureiotsolutions.com) wanneer u zich aanmeldt met een Microsoft-werk- of schoolaccount of uw Microsoft Partner ID als u er een hebt. Nadat u zich hebt aangemeld, selecteert u **Bedrijfsopslagplaats** en vervolgens **Verbindingstekenreeksen**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Voer de volgende opdracht uit om de _IoT-hubverbindingstekenreeks_ voor uw hub te krijgen (opmerking voor later gebruik):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze quickstart gebruikt u de [Vcpkg-bibliotheekbeheerder](https://github.com/microsoft/vcpkg) om de Azure IoT C-apparaat SDK in uw ontwikkelomgeving te installeren.

1. Open een opdrachtprompt. Voer de volgende opdracht uit om Vcpkg te installeren:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Voer vervolgens het volgende uit om gebruikersbrede [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)aan te sluiten (opmerking: vereist admin bij het eerste gebruik):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installeer Azure IoT C-apparaat SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>Uw model schrijven

In deze quickstart gebruikt u een bestaand voorbeeldmodel voor het apparaaten en bijbehorende interfaces.

1. Maak `pnp_app` een map in uw lokale schijf. U gebruikt deze map voor de apparaatmodelbestanden en apparaatcode.

1. Download het [apparaatcapaciteitsmodel en interfacevoorbeeldbestanden](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) en [interfacevoorbeeld](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) `pnp_app` en sla de bestanden op in de map.

    > [!TIP]
    > Als u een bestand van GitHub wilt downloaden, navigeert u naar het bestand, klikt u met de rechtermuisknop op **Raw**en selecteert u **Koppeling opslaan als**.

1. Map `pnp_app` openen met VS-code. U de bestanden bekijken met IntelliSense:

    ![Apparaatcapaciteitsmodel](media/quickstart-create-pnp-device/dcm.png)

1. Vervang `<YOUR_COMPANY_NAME_HERE>` in de bestanden die `@id` u `schema` hebt gedownload in de velden en velden met een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderbied. Zie [Digital Twin-id-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u een DCM en de bijbehorende interfaces hebt, u de apparaatcode genereren die het model implementeert. Ga als bedoeld als u de C-codestub in VS-code wilt genereren:

1. Als `pnp_app` de map is geopend in VS-code, gebruikt u **Ctrl+Shift+P** om het opdrachtpalet te openen, **IoT Plug and Play**in te voeren en **apparaatcode stub genereren**in te schakelen.

    > [!NOTE]
    > De eerste keer dat u de IoT Plug and Play CodeGen CLI gebruikt, duurt het een paar seconden om automatisch te downloaden en te installeren.

1. Kies het bestand **SampleDevice.capabilitymodel.json** dat u wilt gebruiken voor het genereren van de apparaatcode.stub.

1. Voer de projectnaam **sample_device**in . Dit is de naam van uw apparaattoepassing.

1. Kies **ANSI C** als taal.

1. Kies **Via IoT Hub-verbindingstekenreeks** als verbindingsmethode.

1. Kies **CMake Project in Windows** als projectsjabloon.

1. Kies **Via Vcpkg** als de manier om het apparaat SDK op te nemen.

1. Er wordt een nieuwe map met **sample_device** gemaakt op dezelfde locatie als het DCM-bestand en daarin de gegenereerde apparaatcode-stub-bestanden. VS Code opent een nieuw venster om deze weer te geven.
    ![Apparaatcode](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>De code bouwen en uitvoeren

U gebruikt het Vcpkg-pakket om de gegenereerde apparaatcode te bouwen. De toepassing die u bouwt, simuleert een apparaat dat verbinding maakt met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak `cmake` een submap `sample_device` in de map en navigeer naar die map:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de gegenereerde codestomp te bouwen (de tijdelijke aanduiding vervangen door de map van uw Vcpkg repo):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Als u Visual Studio 2017 of 2015 gebruikt, moet u de CMake-generator opgeven op basis van de buildtools die u gebruikt:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Als cmake uw C++ compiler niet kan vinden, krijg je buildfouten wanneer je de vorige opdracht uitvoert. Als dat gebeurt, probeert u deze opdracht uit te voeren op de [opdrachtprompt van Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Nadat de build is voltooid, voert u uw toepassing uit en passeert u de verbindingstekenreeks voor iot-hub-apparaten als parameter.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. De apparaattoepassing begint met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app actief](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Apparaatmodelbestanden publiceren om opslagplaats te modelleren

Als u de apparaatcode wilt valideren met **Azure IoT Explorer,** moet u de bestanden publiceren naar de modelopslagplaats.

1. Als `pnp_app` de map is geopend in VS-code, gebruikt u **Ctrl+Shift+P** om het opdrachtpalet te openen, het opdrachtpalet te typen en te selecteren **& Afspelen: Bestanden verzenden naar Model Repository**.

1. Selecteer `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` en bestanden.

1. Voer de tekenreeks van uw bedrijfsmodelrepository-verbindingsreeks in.

    > [!NOTE]
    > De verbindingstekenreeks is alleen vereist wanneer u voor het eerst verbinding maakt met de repository.

1. In het uitvoervenster en de melding vs-code u controleren of de bestanden zijn gepubliceerd.

    > [!NOTE]
    > Als er fouten worden gemaakt bij het publiceren van de apparaatmodelbestanden, u proberen de opdracht **IoT Plug and Play: Sign out Model Repository** te gebruiken om u af te melden en de stappen opnieuw te doorlopen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT-verkenner gebruiken om de code te valideren

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Als u uw bedrijfsopslagplaats wilt toevoegen, selecteert u **Instellingen**en **vervolgens moduledefinitiebron toevoegen**en vervolgens De opslagplaats van het **bedrijf**. Voeg de verbindingstekenreeks van uw bedrijfsmodelrepository toe en selecteer **Opslaan en verbinden**.

1. Zoek terug op de **overzichtspagina Apparaten** de apparaatidentiteit die u eerder hebt gemaakt. Controleer of de **verbindingsstatus** van het apparaat in Azure IoT-verkenner wordt gemeld als _Verbonden_ (zo niet, druk op **Vernieuwen** totdat dit het geval is). Selecteer het apparaat om meer details weer te geven.

1. Breid de interface uit met **ID-urn: <YOUR_INTERFACE_NAME>:EnvironmentalSensor:1** om de IoT Plug and Play-primitieven te bekijken - eigenschappen, opdrachten en telemetrie. De interfacenaam die wordt weergegeven, is de naam die u invoert bij het ontwerpen van uw model.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een IoT Plug and Play-apparaat maakt met behulp van een DCM.

Ga voor meer informatie over DCM's en het maken van uw eigen modellen verder naar de zelfstudie:

> [!div class="nextstepaction"]
> [Zelfstudie: Een apparaatcapaciteitsmodel maken en testen met Visual Studio Code](tutorial-pnp-visual-studio-code.md)

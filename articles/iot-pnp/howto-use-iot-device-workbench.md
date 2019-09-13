---
title: De Azure IoT Device Workbench gebruiken om IoT Plug en Play preview-apparaten te bouwen | Microsoft Docs
description: Gebruik de Azure IoT Device Workbench-extensie in Visual Studio code om het ontwerpen van IoT Plug en Play apparaten te versnellen en de code van het apparaat te implementeren.
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b9f091caaaf85cd2b999db7781b14a497474f564
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881254"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>De Azure IoT Device Workbench-extensie gebruiken in Visual Studio code

De Azure IoT Device Workbench Visual Studio code-extensie biedt een geïntegreerde omgeving voor het maken van mogelijkheden voor het maken van modellen (DCM) en-interfaces, voor het publiceren naar model opslagplaatsen en genereren van skelet C-code om de apparaat-app te implementeren.

In dit artikel wordt beschreven hoe u:

- Genereer de code van het apparaat en het toepassings project.
- Gebruik de gegenereerde code in het project van uw apparaat.
- Herhaal door het opnieuw genereren van de skelet code.

Zie [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)voor meer informatie over het gebruik van de extensie Device Workbench voor het ontwikkelen van IOT-apparaten.

## <a name="prerequisites"></a>Vereisten

Installeer [Visual Studio Code](https://code.visualstudio.com/).

Gebruik de volgende stappen om de uitbrei ding in VS code te installeren.

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek en Installeer **Azure IOT Device Workbench** via Marketplace.

## <a name="generate-device-code-and-project"></a>Apparaatcode en-project genereren

In VS code gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren** te selecteren om de skelet code en het project type te configureren. In de volgende lijst wordt elke stap in detail beschreven:

- **DCM-bestand dat moet worden gebruikt voor het genereren van de code**. Als u de apparaatcode van het skelet wilt genereren, opent u de map die de DCM-en interface bestanden bevat die deze implementeert. Als de code generator geen interface kan vinden die is vereist voor een DCM, wordt deze indien nodig gedownload van de model opslagplaats.

- **Taal van de toestel code**. Op dit moment ondersteunt de code generator alleen ANSI C.

- **Project naam**. De project naam wordt gebruikt als de naam van de map voor de gegenereerde code en andere project bestanden. De map is standaard geplaatst naast het DCM-bestand. Als u wilt voor komen dat de gegenereerde servermap hand matig wordt gekopieerd wanneer u uw DCM bijwerkt en de apparaatcode opnieuw genereert, moet u uw DCM-bestand in dezelfde map als de projectmap houden.

- **Project type**. De code generator genereert ook een project bestand, zodat u de code kunt integreren in uw eigen project of in het SDK-project van het apparaat. Momenteel zijn de ondersteunde project typen:

    - **Cmake-project**: voor een apparaat-project dat gebruikmaakt van [cmake](https://cmake.org/) als build system. Met deze optie wordt `CMakeLists.txt` een bestand gegenereerd in dezelfde map als de C-code.
    - **MXChip IOT DevKit-project**: voor een Device-project dat wordt uitgevoerd op een [MXChip IOT DevKit](https://aka.ms/iot-devkit) -apparaat. Met deze optie wordt een Arduino-project gegenereerd dat u kunt [gebruiken in VS code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) of in de Arduino IDE om te bouwen en uit te voeren op een IOT DevKit-apparaat.

- **Methode om verbinding te maken met Azure IOT**. De gegenereerde bestanden bevatten ook code om het apparaat te configureren om verbinding te maken met Azure IoT Hub. U kunt ervoor kiezen om rechtstreeks verbinding te maken met [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) of de [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps)te gebruiken.

    - **Via IOT hub apparaat Connection String**: geef de Connection String op waarmee de apparaatgegevens rechtstreeks verbinding kunnen maken met IOT hub.
    - **Via door DPS symmetrische sleutel**: Geef de **bereik-ID**, **registratie-id**en **SAS-sleutel** op voor de apparaat-app die vereist is om verbinding te maken met IOT hub of IOT Central met behulp van DPS.

De code generator probeert DCM-en interface bestanden te gebruiken die zich in de lokale map bevinden. Als de interface bestanden zich niet in de lokale map bevinden, zoekt de code generator deze in de open bare model opslagplaats of de bedrijfs model-opslag plaats. [Veelgebruikte interface bestanden](./concepts-common-interfaces.md) worden opgeslagen in de open bare model opslagplaats.

Wanneer het genereren van code is voltooid, wordt met de uitbrei ding een nieuw versus code venster geopend met de code. Als u een gegenereerd bestand opent, zoals **Main. c**, is het mogelijk dat IntelliSense rapporteert dat de c SDK-bron bestanden niet kunnen worden geopend. Als u de juiste IntelliSense-en code navigatie wilt inschakelen, gebruikt u de volgende stappen om de C SDK-bron op te nemen:

1. In VS code gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, typt en **selecteert uC++C/: Bewerk configuraties (JSON)** om het bestand **c_cpp_properties. json** te openen.

1. Voeg het pad van de SDK van het apparaat `includePath` toe aan de sectie:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Sla het bestand op.

## <a name="use-the-generated-code"></a>De gegenereerde code gebruiken

In de volgende instructies wordt beschreven hoe u de gegenereerde code in uw eigen Device-project kunt gebruiken op verschillende ontwikkel computer platforms. In deze instructies wordt ervan uitgegaan dat u een IoT Hub apparaat gebruikt connection string met de gegenereerde code:

### <a name="linux"></a>Linux

Als u de apparaatcode samen met de apparaat C SDK wilt bouwen met behulp van CMake in een Linux-omgeving, zoals Ubuntu of Debian:

1. Open een Terminal-toepassing.

1. Installeer **gcc**, **Git**, `cmake`en alle afhankelijkheden met behulp van de `apt-get` opdracht:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Controleer of de versie `cmake` van is boven **2.8.12** en de versie van **gcc** hoger is dan **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. De [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c) -opslag plaats klonen:

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Kopieer de map met de gegenereerde code naar de basismap van de apparaat-SDK.

1. Open het `CMakeLists.txt` bestand in VS code in de hoofdmap van de apparaat-SDK.

1. Voeg aan het einde van het `CMakeLists.txt` bestand de volgende regel toe voor het opnemen van de stub-map voor het apparaat bij het compileren van de SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Maak een map met `cmake` de naam in de hoofdmap van de apparaat-SDK en navigeer naar die map.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om CMake te gebruiken om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. Nadat de build is voltooid, voert u de toepassing uit die het IoT Hub apparaat connection string als para meter opgeven.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Als u de apparaatcode samen met de apparaat C SDK op Windows wilt bouwen met CMake en de Visual StudioC++ C/compilers op de opdracht regel, raadpleegt u de [IOT Plug en Play Quick](./quickstart-create-pnp-device.md)start. De volgende stappen laten zien hoe u de apparaatcode samen met de SDK van het apparaat C als CMake-project in Visual Studio kunt bouwen.

1. Installeer [Visual Studio 2019 (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) . Zorg ervoor dat u het onderdeel **NuGet package manager** en de **Desktop ontwikkeling met C++**  Workload opneemt.

1. Open Visual Studio en selecteer op de pagina **aan de slag** de optie **kloon of uitchecken code**:

1. In de locatie van de **opslag plaats**kloont u de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c) -opslag plaats:

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Het kan enkele minuten duren voordat deze bewerking is voltooid, kunt u de voortgang bekijken in het deel venster **team Explorer** .

1. Open de **Azure-IOT-SDK-c-** opslag plaats in de **team Verkenner**, selecteer **vertakkingen**, zoek naar de vertakking **Public-Preview** en Bekijk deze.

    ![Openbare preview](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. Kopieer de map met de gegenereerde code naar de basismap van de apparaat-SDK.

1. Open de `azure-iot-sdk-c` map in VS.

1. Open het `CMakeLists.txt` bestand in de hoofdmap van de apparaat-SDK.

1. Voeg aan het einde van het `CMakeLists.txt` bestand de volgende regel toe voor het opnemen van de stub-map voor het apparaat bij het compileren van de SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Zoek de vervolg keuzelijst **configuraties** op de werk balk **Algemeen** . Selecteer **configuratie beheren** om de cmake-instelling voor uw project toe te voegen.

    ![Configuratie beheren](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. Voeg in de **cmake-instellingen**een nieuwe configuratie toe en selecteer **x64-versie** als doel.

1. Voeg in **cmake opdracht argumenten**de volgende regel toe:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Sla het bestand op.

1. Klik in de **Solution Explorer**met de rechter muisknop op `CMakeLists.txt` de hoofdmap en selecteer vervolgens **bouwen** in het context menu om de SDK van het apparaat en de gegenereerde code-stub te maken.

1. Nadat de build is geslaagd, voert u bij de opdracht prompt de toepassing uit die het IoT Hub apparaat connection string als een para meter opgeven.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Zie [Build cmake project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) (Engelstalig) voor meer informatie over het gebruik van cmake in Visual Studio.

### <a name="macos"></a>macOS

De volgende stappen laten zien hoe u de apparaatcode samen met de SDK van het apparaat C op macOS kunt bouwen met behulp van CMake:

1. Open Terminal-toepassing.

1. Gebruik [homebrew](https://homebrew.sh) om alle afhankelijkheden te installeren:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Controleer of [cmake](https://cmake.org/) ten minste versie **2.8.12**is:

    ```bash
    cmake --version
    ```

1. [Patch krul](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) naar de meest recente versie die beschikbaar is.

1. De [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c) -opslag plaats klonen:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Kopieer de map met de gegenereerde code naar de basismap van de apparaat-SDK.

1. Open het `CMakeLists.txt` bestand in VS code in de hoofdmap van de apparaat-SDK.

1. Voeg aan het einde van het `CMakeLists.txt` bestand de volgende regel toe voor het opnemen van de stub-map voor het apparaat bij het compileren van de SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Maak een map met `cmake` de naam in de hoofdmap van de apparaat-SDK en navigeer naar die map.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om CMake te gebruiken om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Nadat de build is voltooid, voert u de toepassing uit die het IoT Hub apparaat connection string als para meter opgeven.

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Herhaalde door het opnieuw genereren van de skelet code

De code generator kan de code opnieuw genereren als u uw DCM-of interface-bestanden bijwerkt. Ervan uitgaande dat u uw apparaatcode al hebt gegenereerd vanuit een DCM-bestand om de code opnieuw te genereren:

1. Als de map met DCM-bestanden geopend is, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren**in te scha kelen.

1. Kies het DCM-bestand dat u hebt bijgewerkt.

1. Selecteer **code opnieuw genereren voor {project naam}** .

1. De code generator maakt gebruik van de vorige instelling die u hebt geconfigureerd en genereert de code opnieuw. Er worden echter geen bestanden overschreven die gebruikers code kunnen bevatten, zoals `main.c` en `{project_name}_impl.c`.

> [!NOTE]
> Als u de URN-id in uw interface bestand bijwerkt, wordt deze als een nieuwe interface beschouwd. Wanneer u de code opnieuw genereert, genereert de code generator code voor de interface, maar overschrijft de oorspronkelijke niet in `{project_name}_impl.c` het bestand.

## <a name="problems-and-feedback"></a>Problemen en feedback

De uitbrei ding Azure IoT Device Workbench is een open-source project op GitHub. U kunt voor alle problemen en functie aanvragen [een probleem maken op github](https://github.com/microsoft/vscode-iot-workbench/issues).

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u de Azure IoT Device Workbench kunt gebruiken om DCM-en interface bestanden te ontwerpen. U hebt ook geleerd hoe u de code van een skelet C kunt genereren om de apparaat-app te implementeren. Een voorgestelde volgende stap is om te leren hoe u het hulp programma [Azure IOT Explorer kunt installeren en gebruiken](./howto-install-iot-explorer.md) .

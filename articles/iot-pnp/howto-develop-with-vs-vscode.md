---
title: Visual Studio en Visual Studio Code gebruiken om IoT Plug and Play Preview-apparaten te bouwen | Microsoft Documenten
description: Gebruik Visual Studio en Visual Studio Code om het ontwerpen van IoT Plug and Play-apparaatmodellen te versnellen en de apparaatcode te implementeren.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159231"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Visual Studio en Visual Studio Code gebruiken om IoT-plug- en play-apparaten te bouwen

De Azure IoT-hulpprogramma's voor Visuele Studio-code bieden een geïntegreerde omgeving voor het ontwerpen van apparaatcapaciteitsmodellen (DCM) en interfaces, publiceren naar het modelleren van repositories en het genereren van skeletC-code om de apparaattoepassing te implementeren.

In dit artikel leest u informatie over:

- Apparaatcode en toepassingsproject genereren.
- Gebruik de gegenereerde code in uw apparaatproject.
- Herhalen door het regenereren van de skeletcode.

Zie [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)voor meer informatie over het gebruik van de VS-code om IoT-apparaten te ontwikkelen.

## <a name="prerequisites"></a>Vereisten

Installeer [Visual Studio Code](https://code.visualstudio.com/).

Gebruik de volgende stappen om het uitbreidingspakket in VS-code te installeren.

1. Selecteer het tabblad **Extensies** in VS-code.
1. Azure **IoT-hulpprogramma's** zoeken en installeren vanuit de marketplace.

## <a name="generate-device-code-and-project"></a>Apparaatcode en -project genereren

Gebruik in VS-code **Ctrl+Shift+P** om het opdrachtpalet te openen, **IoT Plug and Play in**te voeren en **apparaatcode-stub genereren** te selecteren om de skeletcode en het projecttype te configureren. In de volgende lijst wordt elke stap in detail beschreven:

- **DCM-bestand dat moet worden gebruikt voor het genereren van de code**. Als u de code van het skeletapparaat wilt genereren, opent u de map met de DCM- en interfacebestanden die het implementeert. Als de codegenerator geen interface kan vinden die een DCM vereist, downloadt deze deze indien nodig uit de modelopslagplaats.

- **Apparaatcodetaal**. Momenteel ondersteunt de codegenerator alleen ANSI C.

- **Projectnaam**. De projectnaam wordt gebruikt als mapnaam voor de gegenereerde code en andere projectbestanden. De map wordt standaard naast het DCM-bestand geplaatst. Om te voorkomen dat u de gegenereerde codemap handmatig moet kopiëren wanneer u uw DCM bijwerkt en de apparaatcode opnieuw genereert, bewaart u uw DCM-bestand in dezelfde map als de projectmap.

- **Methode om verbinding te maken met Azure IoT**. De gegenereerde bestanden bevatten ook code om het apparaat te configureren om verbinding te maken met Azure IoT Hub. U ervoor kiezen om rechtstreeks verbinding te maken met [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) of de [Service Apparaatinrichting te](https://docs.microsoft.com/azure/iot-dps)gebruiken.

    - **Via IoT Hub-apparaatverbindingstekenreeks:** geef de apparaatverbindingstekenreeks op voor de apparaattoepassing om rechtstreeks verbinding te maken met IoT Hub.
    - **Via dps symmetrische sleutel:** geef de **ID-scope,** **symmetrische sleutel** en **apparaat-id** op voor de apparaattoepassing die nodig zijn om verbinding te maken met IoT Hub of IoT Central via DPS.

- **Projecttype**. De codegenerator genereert ook een CMake- of Arduino-project. Momenteel zijn de ondersteunde projecttypen:

    - **CMake Project op Windows:** voor een apparaatproject dat [CMake](https://cmake.org/) als buildsysteem op Windows gebruikt. Deze optie `CMakeLists.txt` genereert met apparaat SDK-configuraties in dezelfde map als de C-code.
    - **CMake Project op Linux:** voor een apparaatproject dat [CMake](https://cmake.org/) gebruikt als buildsysteem op Linux. Deze optie `CMakeLists.txt` genereert met apparaat SDK-configuraties in dezelfde map als de C-code.
    - **MXChip IoT DevKit-project:** voor een apparaatproject dat wordt uitgevoerd op een [MXChip IoT DevKit-apparaat.](https://aka.ms/iot-devkit) Met deze optie genereert u een Arduino-project dat u [gebruiken in VS-code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) of in de Arduino IDE om een IoT DevKit-apparaat te bouwen en uit te voeren.

- **Apparaat SDK-type**. Als u CMake als projecttype selecteert, is dit de stap om te configureren `CMakeLists.txt`hoe gegenereerde code Azure IoT C-apparaat SDK opneemt in :

    - **Via Broncode**: de gegenereerde code is gebaseerd op het [apparaat SDK broncode](https://github.com/Azure/azure-iot-sdk-c) op te nemen in en samen te bouwen met het. Dit wordt aanbevolen wanneer u de SDK-broncode van het apparaat hebt aangepast.
    - **Via Vcpkg**: de gegenereerde code is gebaseerd op het [apparaat SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) op te nemen in en samen te bouwen met het. Dit is de aanbevolen manier voor apparaten met Windows, Linux of macOS.

    > [!NOTE]
    > macOS-ondersteuning voor Azure IoT C-apparaat SDK Vcpkg werkt in volle gang.

De codegenerator probeert DCM- en interfacebestanden in de lokale map te gebruiken. Als de interfacebestanden zich niet in de lokale map bevinden, zoekt de codegenerator ernaar in de openbare modelopslagplaats of bedrijfsmodelopslagplaats. [Algemene interfacebestanden](./concepts-common-interfaces.md) worden opgeslagen in de openbare modelopslagplaats.

Nadat de codegeneratie is voltooid, opent de extensie een nieuw VS-codevenster met de code. Als u een gegenereerd bestand opent, zoals **main.c,** u merken dat IntelliSense meldt dat het de C SDK-bronbestanden niet kan openen. Als u de juiste IntelliSense- en codenavigatie wilt inschakelen, gebruikt u de volgende stappen om de C SDK-bron op te nemen:

1. Gebruik **Ctrl+Shift+P in VS-code** om het opdrachtpalet te openen, **C/C++ te selecteren: Configuraties bewerken (JSON)** om het **bestand c_cpp_properties.json** te openen.

1. Voeg het pad van de `includePath` SDK van het apparaat toe in de sectie:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Sla het bestand op.

## <a name="use-the-generated-code"></a>De gegenereerde code gebruiken

In de volgende instructies wordt beschreven hoe u de gegenereerde code gebruiken in uw eigen apparaatproject op verschillende platformen voor ontwikkelingsmachines. De instructies gaan ervan uit dat u een verbindingstekenreeks voor het IoT Hub-apparaat gebruikt met de gegenereerde code:

### <a name="linux"></a>Linux

Om de apparaatcode samen met het apparaat C SDK Vcpkg te bouwen met CMake in een Linux-omgeving zoals Ubuntu of Debian:

1. Open een terminaltoepassing.

1. Installeer **GCC,** **Git**en `cmake`alle afhankelijkheden met de `apt-get` opdracht:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Controleer of `cmake` de versie van is boven **2.8.12** en de versie van **GCC** is boven **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Vcpkg installeren:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Vervolgens, aan te sluiten gebruiker-brede [integratie,](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)uitvoeren:

    ```bash
    ./vcpkg integrate install
    ```

1. Installeer Azure IoT C-apparaat SDK Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Maak `cmake` een submap in de map die de gegenereerde code-stub bevat en navigeer naar die map:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om CMake te gebruiken om de SDK van het apparaat en de gegenereerde codestomp te bouwen:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Voer de toepassing uit die de verbindingstekenreeks van het IoT Hub-apparaat als parameter opgeeft.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Zie de QuickStart van de [IoT Plug and Play](./quickstart-create-pnp-device-windows.md)voor het bouwen van de apparaatcode samen met de C SDK op Windows met CMake en de Visual Studio C/C++-compilers op de opdrachtregel. In de volgende stappen ziet u hoe u de apparaatcode samen met het c SDK Vcpkg-apparaat als CMake-project in Visual Studio bouwen.

1. Volg de stappen in de [quickstart](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) om de Azure IoT-apparaat SDK voor C te installeren via Vcpkg.

1. Installeer [Visual Studio 2019 (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/) - zorg ervoor dat u de **nuget-pakketbeheercomponent** en de **desktopontwikkeling opneemt met C++-workload.**

1. Open Visual Studio, kies Bestand > Open `CMakeLists.txt` > **CMake...** om de in de map te openen die gegenereerde code bevat.

1. Zoek op de werkbalk **Algemeen** de vervolgkeuzelijst **Configuraties.** Selecteer **Configuratie beheren** om de CMake-instelling voor uw project toe te voegen.

    ![Configuratie beheren](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Voeg in de **CMake-instellingen**een nieuwe configuratie toe en selecteer **x86-Debug** als doel.

1. Voeg in **CMake Command Arguments**de volgende regel toe:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Sla het bestand op.

1. Overschakelen naar **x86-Foutopsporing** in de vervolgkeuzelijst **Configuraties.** Het heeft een paar seconden nodig voor de CMake om de cache voor te genereren. Bekijk het venster Uitvoer om de voortgang te zien.

    ![CMake-uitvoer](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. Klik in de **Solution Explorer** `CMakeLists.txt` met de rechtermuisknop op de map in de hoofdmap en selecteer **Bouwen** in het contextmenu om de gegenereerde codestub met de SDK van het apparaat te bouwen.

1. Nadat de build is geslaagd, voert u bij de opdrachtprompt de toepassing uit waarin de verbindingstekenreeks van het IoT Hub-apparaat als parameter wordt opgegeven.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Zie [CMake-project bouwen](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) voor meer informatie over het gebruik van CMake in Visual Studio.

### <a name="macos"></a>macOS

In de volgende stappen ziet u hoe u de apparaatcode samen met de C SDK-broncode van het apparaat op macOS bouwen met CMake:

1. Open terminal applicatie.

1. Gebruik [Homebrew](https://homebrew.sh) om alle afhankelijkheden te installeren:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Controleer of [CMake](https://cmake.org/) ten minste versie **2.8.12**is:

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) naar de nieuwste versie beschikbaar.

1. Kloon de [Azure IoT C SDK-repository](https://github.com/Azure/azure-iot-sdk-c) in de map die de gegenereerde code bevat:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak een `cmake` map die onder de map wordt aangeroepen en navigeer naar die map.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om CMake te gebruiken om de SDK van het apparaat en de gegenereerde codestomp te bouwen:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Voer de toepassing uit die de verbindingstekenreeks van het IoT Hub-apparaat als parameter opgeeft.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Herhalen door het regenereren van de skeletcode

De codegenerator kan de code regenereren als u uw DCM- of interfacebestanden bijwerkt. Ervan uitgaande dat u uw apparaatcode al hebt gegenereerd uit een DCM-bestand, om de code opnieuw te genereren:

1. Als de map met DCM-bestanden is geopend, gebruikt u **Ctrl+Shift+P** om het opdrachtpalet te openen, **IoT Plug and Play**in te voeren en **apparaatcode stub genereren**in te selecteren.

1. Kies het DCM-bestand dat u hebt bijgewerkt.

1. Selecteer **Code opnieuw genereren voor {projectnaam}**.

1. De codegenerator gebruikt de vorige instelling die u hebt geconfigureerd en regenereert de code. Het overschrijft echter niet de bestanden die gebruikerscode `main.c` `{project_name}_impl.c`kunnen bevatten, zoals en .

> [!NOTE]
> Als u de URN-id in uw interfacebestand bijwerkt, wordt deze behandeld als een nieuwe interface. Wanneer u de code opnieuw genereert, genereert de codegenerator code voor de `{project_name}_impl.c` interface, maar overschrijft deze niet in het bestand.

## <a name="problems-and-feedback"></a>Problemen en feedback

Azure IoT Tools is een open-source project op GitHub. Voor problemen en functieaanvragen u [een probleem maken op GitHub.](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)

## <a name="next-steps"></a>Volgende stappen

In dit how-to-artikel hebt u geleerd hoe u de Visual Studio en Visual Studio Code gebruiken om skelet C-code te genereren om de apparaattoepassing te implementeren. Een voorgestelde volgende stap is om te leren hoe u [azure IoT explorer-hulpprogramma installeren en gebruiken.](./howto-install-iot-explorer.md)

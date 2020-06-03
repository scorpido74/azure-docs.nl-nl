---
title: Visual Studio en Visual Studio code gebruiken om IoT Plug en Play preview-apparaten te bouwen | Microsoft Docs
description: Gebruik Visual Studio en Visual Studio code om het ontwerpen van IoT Plug en Play apparaten te versnellen en de code van het apparaat te implementeren.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 038d9ff39f388d1ef7b09b951c09dbe3420858b7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298224"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Visual Studio en Visual Studio code gebruiken om IoT Plug en Play-apparaten te bouwen

De Azure IoT-Hulpprogram Ma's voor Visual Studio code biedt een geïntegreerde omgeving voor het ontwerpen van mogelijkheden voor apparaten en interfaces, het publiceren naar model opslagplaatsen en het genereren van skelet C-code om de apparaat-app te implementeren.

In dit artikel leest u informatie over:

- Genereer de code van het apparaat en het toepassings project.
- Gebruik de gegenereerde code in het project van uw apparaat.
- Herhaal door het opnieuw genereren van de skelet code.

Zie voor meer informatie over het gebruik van de VS code voor het ontwikkelen van IoT-apparaten [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench) .

## <a name="prerequisites"></a>Vereisten

Installeer [Visual Studio Code](https://code.visualstudio.com/).

Gebruik de volgende stappen om het uitbreidings pakket in VS code te installeren.

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek en Installeer **Azure IOT-Hulpprogram ma's** op Marketplace.

## <a name="generate-device-code-and-project"></a>Apparaatcode en-project genereren

In VS code gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren** te selecteren om de skelet code en het project type te configureren. In de volgende lijst wordt elke stap in detail beschreven:

- **DCM-bestand dat moet worden gebruikt voor het genereren van de code**. Als u de apparaatcode van het skelet wilt genereren, opent u de map die de DCM-en interface bestanden bevat die deze implementeert. Als de code generator geen interface kan vinden die is vereist voor een DCM, wordt deze indien nodig gedownload van de model opslagplaats.

- **Taal van de toestel code**. Op dit moment ondersteunt de code generator alleen ANSI C.

- **Project naam**. De project naam wordt gebruikt als de naam van de map voor de gegenereerde code en andere project bestanden. De map is standaard geplaatst naast het DCM-bestand. Als u wilt voor komen dat de gegenereerde servermap hand matig wordt gekopieerd wanneer u uw DCM bijwerkt en de apparaatcode opnieuw genereert, moet u uw DCM-bestand in dezelfde map als de projectmap houden.

- **Methode om verbinding te maken met Azure IOT**. De gegenereerde bestanden bevatten ook code om het apparaat te configureren om verbinding te maken met Azure IoT Hub. U kunt ervoor kiezen om rechtstreeks verbinding te maken met [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) of de [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps)te gebruiken.

    - **Via IOT hub apparaat Connection String**: geef de Connection String op waarmee de apparaatgegevens rechtstreeks verbinding kunnen maken met IOT hub.
    - **Via DPS symmetrische sleutel**: Geef de **id-Scope**, de **symmetrische sleutel** en de **apparaat-id** op voor de apparaat-app die vereist is om verbinding te maken met IOT hub of IOT Central met behulp van DPS.

- **Project type**. De code generator genereert ook een CMake-of Arduino-project. Momenteel zijn de ondersteunde project typen:

    - **Cmake-project in Windows**: voor een apparaat-project dat gebruikmaakt van [cmake](https://cmake.org/) als build-systeem in Windows. Met deze optie worden de `CMakeLists.txt` SDK-configuraties van apparaten in dezelfde map als de C-code gegenereerd.
    - **Cmake-project in Linux**: voor een apparaat-project dat gebruikmaakt van [cmake](https://cmake.org/) als build system op Linux. Met deze optie worden de `CMakeLists.txt` SDK-configuraties van apparaten in dezelfde map als de C-code gegenereerd.
    - **MXChip IOT DevKit-project**: voor een Device-project dat wordt uitgevoerd op een [MXChip IOT DevKit](https://aka.ms/iot-devkit) -apparaat. Met deze optie wordt een Arduino-project gegenereerd dat u kunt [gebruiken in VS code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) of in de Arduino IDE om te bouwen en uit te voeren op een IOT DevKit-apparaat.

- **Type apparaat-SDK**. Als u CMake als project type selecteert, is dit de stap om te configureren hoe gegenereerde code de Azure IoT C-SDK bevat in de `CMakeLists.txt` :

    - **Via bron code**: de gegenereerde code is afhankelijk van de [apparaat-SDK-bron code](https://github.com/Azure/azure-iot-sdk-c) die u wilt insluiten en samen te bouwen. Dit wordt aanbevolen wanneer u de SDK-bron code van het apparaat hebt aangepast.
    - **Via Vcpkg**: de gegenereerde code is afhankelijk van de [SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) van het apparaat om samen te voegen en te bouwen. Dit is de aanbevolen manier voor apparaten met Windows, Linux of macOS.

    > [!NOTE]
    > macOS-ondersteuning voor Azure IoT C Device SDK Vcpkg wordt uitgevoerd.

De code generator probeert DCM-en interface bestanden te gebruiken die zich in de lokale map bevinden. Als de interface bestanden zich niet in de lokale map bevinden, zoekt de code generator deze in de open bare model opslagplaats of de bedrijfs model-opslag plaats. [Veelgebruikte interface bestanden](./concepts-common-interfaces.md) worden opgeslagen in de open bare model opslagplaats.

Wanneer het genereren van code is voltooid, wordt met de uitbrei ding een nieuw versus code venster geopend met de code. Als u een gegenereerd bestand opent, zoals **Main. c**, is het mogelijk dat IntelliSense rapporteert dat de c SDK-bron bestanden niet kunnen worden geopend. Als u de juiste IntelliSense-en code navigatie wilt inschakelen, gebruikt u de volgende stappen om de C SDK-bron op te nemen:

1. In VS code gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen. Typ en selecteer **C/C++: Edit configurations (JSON)** om het bestand **c_cpp_properties. json** te openen.

1. Voeg het pad van de SDK van het apparaat toe aan de `includePath` sectie:

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

Als u de apparaatcode samen met het apparaat C SDK Vcpkg wilt maken met behulp van CMake in een Linux-omgeving, zoals Ubuntu of Debian:

1. Open een Terminal-toepassing.

1. Installeer **gcc**, **Git**, `cmake` en alle afhankelijkheden met behulp van de `apt-get` opdracht:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Controleer of de versie van `cmake` is boven **2.8.12** en de versie van **gcc** hoger is dan **4.4.7**.

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

    Voer vervolgens de volgende handelingen uit om de [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)met de hele gebruiker te koppelen:

    ```bash
    ./vcpkg integrate install
    ```

1. Installeer de Azure IoT C Device SDK Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Maak een submap `cmake` in de map met de gegenereerde code-stub en navigeer naar die map:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om CMake te gebruiken om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Nadat de build is voltooid, voert u de toepassing uit die het IoT Hub apparaat connection string als para meter opgeven.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Als u de apparaatcode samen met de SDK van het apparaat C op Windows wilt bouwen met behulp van CMake en de Visual Studio C/C++ compilers op de opdracht regel, raadpleegt u de [IoT Plug en Play Quick](./quickstart-create-pnp-device-windows.md)start. De volgende stappen laten zien hoe u de apparaatcode samen met het apparaat C SDK Vcpkg als CMake-project in Visual Studio kunt bouwen.

1. Volg de stappen in de [Quick](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) start om de Azure IOT Device SDK voor C te installeren via Vcpkg.

1. Installeer [Visual Studio 2019 (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) . Zorg ervoor dat u het **NuGet package manager** -onderdeel en de **Desktop ontwikkeling met** de werk belasting van C++ opneemt.

1. Open Visual Studio, kies **bestand > Open > cmake...** om de `CMakeLists.txt` in de map bevat gegenereerde code te openen.

1. Zoek de vervolg keuzelijst **configuraties** op de werk balk **Algemeen** . Selecteer **configuratie beheren** om de cmake-instelling voor uw project toe te voegen.

    ![Configuratie beheren](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Voeg in de **cmake-instellingen**een nieuwe configuratie toe en selecteer **x86-debug** als doel.

1. Voeg in **cmake opdracht argumenten**de volgende regel toe:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Sla het bestand op.

1. Schakel over naar **x86-fout opsporing** in de vervolg keuzelijst **configuraties** . Het kan een paar seconden duren voordat de CMake de cache heeft gegenereerd. Bekijk het uitvoer venster om de voortgang te bekijken.

    ![CMake uitvoer](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. Klik in de **Solution Explorer**met de rechter muisknop op de `CMakeLists.txt` hoofdmap en selecteer vervolgens **bouwen** in het context menu om de gegenereerde code-STUB te bouwen met de SDK van het apparaat.

1. Nadat de build is geslaagd, voert u bij de opdracht prompt de toepassing uit die het IoT Hub apparaat connection string als een para meter opgeven.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Zie [Build cmake project](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) (Engelstalig) voor meer informatie over het gebruik van cmake in Visual Studio.

### <a name="macos"></a>macOS

De volgende stappen laten zien hoe u de apparaatcode samen met de bron code van het apparaat C SDK op macOS kunt bouwen met behulp van CMake:

1. Open Terminal-toepassing.

1. Gebruik [homebrew](https://brew.sh) om alle afhankelijkheden te installeren:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Controleer of [cmake](https://cmake.org/) ten minste versie **2.8.12**is:

    ```bash
    cmake --version
    ```

1. [Patch krul](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) naar de meest recente versie die beschikbaar is.

1. In de map met de gegenereerde code kloont u de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c) -opslag plaats:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak een map `cmake` met de naam onder de map die de gegenereerde code bevat en navigeer naar die map.

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
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Herhaalde door het opnieuw genereren van de skelet code

De code generator kan de code opnieuw genereren als u uw DCM-of interface-bestanden bijwerkt. Ervan uitgaande dat u uw apparaatcode al hebt gegenereerd vanuit een DCM-bestand om de code opnieuw te genereren:

1. Als de map met DCM-bestanden geopend is, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren**in te scha kelen.

1. Kies het DCM-bestand dat u hebt bijgewerkt.

1. Selecteer **code opnieuw genereren voor {project naam}**.

1. De code generator maakt gebruik van de vorige instelling die u hebt geconfigureerd en genereert de code opnieuw. Er worden echter geen bestanden overschreven die gebruikers code kunnen bevatten, zoals `main.c` en `{project_name}_impl.c` .

> [!NOTE]
> Als u de URN-id in uw interface bestand bijwerkt, wordt deze als een nieuwe interface beschouwd. Wanneer u de code opnieuw genereert, genereert de code generator code voor de interface, maar overschrijft de oorspronkelijke niet in het `{project_name}_impl.c` bestand.

## <a name="problems-and-feedback"></a>Problemen en feedback

Azure IoT-Hulpprogram Ma's is een open-source project op GitHub. U kunt voor alle problemen en functie aanvragen [een probleem maken op github](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u de Visual Studio en Visual Studio code kunt gebruiken om de code van de toepassing te implementeren. Een voorgestelde volgende stap is om te leren hoe u het hulp programma [Azure IOT Explorer kunt installeren en gebruiken](./howto-install-iot-explorer.md) .

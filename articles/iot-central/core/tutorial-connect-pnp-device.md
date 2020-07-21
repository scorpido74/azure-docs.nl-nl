---
title: 'Zelfstudie: een IoT Plug and Play-apparaat (preview-versie) verbinden met Azure IoT Central'
description: In deze zelfstudie ziet u hoe u een apparaatondersteuningsprofiel gebruikt om apparaatcode te genereren. Voer vervolgens de apparaatcode uit, kijk of het apparaat verbinding maakt met uw IoT Central-toepassing en gebruik de automatisch gegenereerde weergaven.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 6727a2c45187e0e6bb583bb65e176024067d81e2
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86219981"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Zelfstudie: Een IoT Plug and Play-apparaat (preview-versie) maken met behulp van een apparaatondersteuningsprofiel en verbinden met uw IoT Central-toepassing

In een _apparaatondersteuningsprofiel_ (Device Capability Model of DCM) worden de mogelijkheden van een [IoT Plug and Play-apparaat (preview-versie)](../../iot-pnp/overview-iot-plug-and-play.md) beschreven. Een DCM kan in IoT Central worden gebruikt om een apparaatsjabloon en visualisaties voor een apparaat te maken wanneer het apparaat voor het eerst verbinding maakt.

Ondersteuning voor [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) is in preview en wordt alleen ondersteund in bepaalde regio's.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Visual Studio Code gebruiken om een IoT Plug and Play-apparaat (preview-versie) te maken met behulp van een DCM.
> * De apparaatcode uitvoeren in Windows en kijken hoe deze verbinding maakt met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie bekijken die vanaf het apparaat wordt verzonden.

## <a name="prerequisites"></a>Vereisten

Voltooi de quickstart [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) om een IoT Central-toepassing te maken met behulp van de toepassing **Aangepaste app > Aangepaste sjabloon**.

Als u deze zelfstudie wilt voltooien, moet u de volgende software installeren op uw lokale computer:

* [Build Tools for Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met **C++ Build Tools** en workloads voor **NuGet package manager component**. Als u al beschikt over [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/), 2019, 2017 of 2015 met dezelfde werkbelastingen geïnstalleerd.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/): wanneer u **CMake** installeert, selecteert u de optie **CMake toevoegen aan het systeempad**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Het hulpprogramma `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Azure IoT Tools installeren

Gebruik de volgende stappen voor het installeren van het Azure IoT Tools Extension Pack in VS Code:

1. Selecteer in VS Code het tabblad **Extensions**.
1. Zoek naar **Azure IoT Tools**.
1. Selecteer **Installeren**.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze zelfstudie gebruikt u [Vcpkg](https://github.com/microsoft/vcpkg) (een programma voor bibliotheekbeheer) om de Azure IoT C Device SDK te installeren in uw ontwikkelomgeving.

1. Open een opdrachtprompt. Voer de volgende opdracht uit om Vcpkg te installeren:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Voer vervolgens deze opdracht uit om [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) voor alle gebruikers in te stellen. Wanneer u deze opdracht voor de eerste keer uitvoert, hebt u beheerdersrechten nodig:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installeer de Azure IoT C Device SDK:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Apparaatsleutel genereren

U hebt een apparaatsleutel nodig om een apparaat met een IoT Central-toepassing te verbinden. U genereert een apparaatsleutel als volgt:

1. Meld u aan bij de IoT Central-toepassing die u hebt gemaakt met de sjabloon **Aangepaste toepassing** in de quickstart [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md).

1. Ga naar de **beheerpagina** en selecteer **Apparaatverbinding**.

1. Noteer het **Id-bereik**. U gebruikt deze waarde verderop in deze zelfstudie.

1. Selecteer de registratiegroep **SAS-IoT-Devices**. Noteer de **Primaire sleutel**. U gebruikt deze waarde verderop in deze zelfstudie.

    ![Apparaatverbinding](./media/tutorial-connect-pnp-device/device-connection.png)

1. Open een opdrachtprompt en voer de volgende opdracht uit om een apparaatsleutel te maken:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Noteer de gegenereerde _apparaatsleutel_. U gebruikt deze waarde in een latere stap in deze zelfstudie.

## <a name="download-your-model"></a>Uw model downloaden

In deze zelfstudie gebruikt u de openbare DCM voor een MxChip IoT DevKit-apparaat. U hebt geen daadwerkelijk DevKit-apparaat nodig om de code uit te voeren; in deze zelfstudie compileert u de code die in Windows moet worden uitgevoerd.

1. Maak een map met de naam `central_app` en open deze in VS Code.

1. Druk op **Ctrl+Shift+P** om het opdrachtenpalet te openen, voer **IoT Plug and Play** in en selecteer **Modelopslagplaats openen**. Selecteer **Openbare opslagplaats**. In VS Code wordt een lijst weergegeven met de DCM's in de openbare modelopslagplaats.

1. Selecteer de DCM **MXChip IoT DevKit** met id `urn:mxchip:mxchip_iot_devkit:1`. Selecteer vervolgens **Downloaden**. U beschikt nu over een kopie van de DCM in de map `central_app`.

![Modelopslagplaats en DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Als u een apparaatondersteuningsprofiel wilt kunnen gebruiken in IoT Central, moeten alle interfaces inline zijn gedefinieerd in hetzelfde bestand.

## <a name="generate-the-c-code-stub"></a>De C-codestub genereren

Nu u over de DCM **MXChip IoT DevKit** en de bijbehorende interfaces beschikt, kunt u de apparaatcode gaan genereren waarmee het model wordt geïmplementeerd. De C-codestub genereren in VS Code:

1. Zorg dat de map met DCM-bestanden is geopend en druk op **Ctrl+Shift+P** om het opdrachtenpalet te openen, voer **IoT Plug and Play** in en selecteer **Generate Device Code Stub**.

    > [!NOTE]
    > De eerste keer dat u het hulpprogramma IoT Plug and Play Code Generator gebruikt, duurt het enkele seconden om dit te downloaden.

1. Selecteer het DCM-bestand van de **MXChip IoT DevKit** dat u zojuist hebt gedownload.

1. Geef de projectnaam **devkit_device** op.

1. Kies **ANSI C** als de taal.

1. Kies **Via DPS (Device Provisioning Service) symmetrische sleutel** als verbindingsmethode.

1. Kies **CMake Project on Windows** als projecttype. Kies niet **MXChip IoT DevKit Project**; deze optie is bedoeld voor wanneer u een echt DevKit-apparaat hebt.

1. Kies **Via Vcpkg** als de manier om de SDK toe te voegen.

1. In VS Code wordt een nieuw venster geopend met de gegenereerde apparaatcodestubbestanden in de map `devkit_device`.

![Gegenereerde apparaatcode](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>De code bouwen

U gebruikt de apparaat-SDK om de gegenereerde codestub voor een apparaat te bouwen. In de toepassing die u bouwt, wordt een **MXChip IoT DevKit**-apparaat gesimuleerd en wordt verbinding gemaakt met uw IoT Central-toepassing. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak bij de opdrachtprompt een submap `cmake` in de map `devkit_device` en navigeer naar die map:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de gegenereerde codestub te bouwen. Vervang de tijdelijke aanduiding `<directory of your Vcpkg repo>` door het pad naar uw kopie van de **Vcpkg**-opslagplaats:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Als u Visual Studio 2017 of 2015 gebruikt, moet u de CMake-generator opgeven op basis van de Build Tools die u gebruikt:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Zodra de build is voltooid, voert u bij dezelfde opdrachtprompt uw toepassing uit. Vervang `<scopeid>` en `<devicekey>` door de waarden die u eerder hebt genoteerd:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. De apparaattoepassing begint met het verzenden van gegevens naar IoT Hub. In sommige gevallen ziet u de fout `Error registering device for DPS` wanneer u de vorige opdracht voor het eerst uitvoert. Voer de opdracht opnieuw uit als deze fout wordt weergegeven.

## <a name="view-the-device"></a>Het apparaat weergeven

Zodra uw apparaatcode verbinding heeft gemaakt met uw IoT Central, kunt u de eigenschappen en telemetrie weergeven die hierdoor wordt verzonden:

1. Ga in uw IoT Central-toepassing naar de pagina **Apparaten** en selecteer het apparaat **mxchip-01**. Dit apparaat is automatisch toegevoegd toen de apparaatcode verbinding heeft gemaakt:

    ![Overzichtspagina](./media/tutorial-connect-pnp-device/overview-page.png)

    Na enkele minuten worden op deze pagina grafieken weergegeven van de telemetrie die door het apparaat wordt verzonden.

1. Selecteer de pagina **Info** om de eigenschapswaarden weer te geven die door het apparaat worden verzonden.

1. Selecteer de pagina **Opdrachten** om opdrachten op het apparaat aan te roepen. U ziet dat het apparaat reageert op de opdrachtprompt waardoor de apparaatcode wordt uitgevoerd.

1. Ga naar de pagina **Apparaatsjablonen** om de sjabloon weer te geven die door IoT Central in de openbare opslagplaats is gemaakt op basis van de DCM:

    ![De pagina Apparaatsjablonen](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play-apparaat (preview-versie) verbindt dat op basis van een DCM is gegenereerd in de openbare modelopslagplaats.

Als u meer wilt weten over DCM's en het maken van uw eigen modellen, gaat u verder met de instructiegids:

> [!div class="nextstepaction"]
> [Een nieuw IoT-apparaattype definiëren](./howto-set-up-template.md)

---
title: 'Zelf studie: een IoT Plug en Play-apparaat (preview) verbinden met Azure IoT Central'
description: In deze zelf studie leert u hoe u een mogelijkheidsprofiel kunt gebruiken om een apparaatcode te genereren. Vervolgens voert u de apparaatcode uit, raadpleegt u het apparaat verbinding maken met uw IoT Central-toepassing en gebruikt u de automatisch gegenereerde weer gaven.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 84db3996b49a95d1ef6a9f1e80299605cc31d669
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602596"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Zelf studie: een mogelijkheidsprofiel gebruiken om een IoT Plug en Play-apparaat (preview) te maken en dit te verbinden met uw IoT Central toepassing

Een _mogelijkheidsprofiel_ (DCM) beschrijft de mogelijkheden van een [IOT Plug en Play-apparaat (preview-versie)](../../iot-pnp/overview-iot-plug-and-play.md) . IoT Central kunt een DCM gebruiken om een sjabloon en visualisaties voor een apparaat te maken wanneer het apparaat voor de eerste keer verbinding maakt.

Ondersteuning voor [IoT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) is in de preview-versie en wordt alleen ondersteund in geselecteerde regio's.

> [!NOTE]
> Neem contact op met de technische ondersteuning voor toegang tot het maken van een preview-toepassing met ondersteuning voor IoT Plug en Play-apparaten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik Visual Studio code om een IoT Plug en Play (preview)-apparaat te maken met behulp van een DCM.
> * Voer de apparaatcode in Windows uit en zie verbinding maken met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie weer geven die het apparaat verzendt.

## <a name="prerequisites"></a>Vereisten

Voltooi de Snelstartgids [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md) om een IOT Central toepassing te maken met behulp van de aangepaste **app > aangepaste toepassings** sjabloon.

Als u deze zelf studie wilt volt ooien, moet u de volgende software installeren op uw lokale computer:

* [Bouw hulpprogram ma's voor Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met  **C++ build tools** en **Nuget package manager-onderdeel** workloads. Of als u [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 of 2015 al hebt geïnstalleerd met dezelfde workloads.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/) : als u **cmake**installeert, selecteert u de optie **cmake toevoegen aan het**systeempad.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Het hulp programma `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Azure IoT-Hulpprogram Ma's installeren

Voer de volgende stappen uit om de Azure IoT-Hulpprogram Ma's extensie Pack in VS code te installeren:

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek naar **Azure IOT-Hulpprogram ma's**.
1. Selecteer **Installeren**.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze zelf studie gebruikt u de [Vcpkg](https://github.com/microsoft/vcpkg) -bibliotheek beheerder om de Azure IOT C-SDK in uw ontwikkel omgeving te installeren.

1. Open een opdrachtprompt. Voer de volgende opdracht uit om Vcpkg te installeren:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Voer vervolgens de volgende opdracht uit om de [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)met de hele gebruiker te koppelen. De eerste keer dat u deze opdracht uitvoert, hebt u beheerders rechten nodig:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installeer de Azure IoT C Device SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Apparaatcode genereren

Als u een apparaat wilt verbinden met een IoT Central-toepassing, hebt u een apparaatcode nodig. Een apparaatcode genereren:

1. Meld u aan bij de IoT Central toepassing die u hebt gemaakt met behulp van de **aangepaste toepassings** sjabloon in de Snelstartgids [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md) .

1. Ga naar de **beheer** pagina en selecteer **apparaat-verbinding**.

1. Noteer het **id-bereik** en de **primaire sleutel** die u ziet wanneer u **sleutels weer geven**selecteert. U gebruikt deze waarden later in deze zelf studie.

    ![Apparaat-verbinding](./media/tutorial-connect-pnp-device/device-connection.png)

1. Open een opdracht prompt en voer de volgende opdracht uit om een apparaatcode te genereren:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Noteer de gegenereerde _apparaatcode_. u gebruikt deze waarde in een latere stap in deze zelf studie.

## <a name="download-your-model"></a>Uw model downloaden

In deze zelf studie gebruikt u de open bare DCM voor een MxChip IoT DevKit-apparaat. U hebt geen werkelijk DevKit-apparaat nodig om de code uit te voeren. in deze zelf studie compileert u de code om uit te voeren op Windows.

1. Maak een map met de naam `central_app` en open deze in VS code.

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en **open model opslagplaats**te selecteren. Selecteer de **open bare opslag plaats**. VS code toont een lijst van de DCMs in de open bare model opslagplaats.

1. Selecteer de **MXChip IOT DevKit** DCM met de ID-`urn:mxchip:mxchip_iot_devkit:1`. Selecteer vervolgens **downloaden**. U hebt nu een kopie van de DCM in de map `central_app`.

![Model opslagplaats en DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Als u met IoT Central wilt werken, moeten in het mogelijkheidsprofiel alle interfaces in hetzelfde bestand zijn gedefinieerd.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u de **MXChip IOT DevKit** DCM en de bijbehorende interfaces hebt, kunt u de apparaatcode genereren waarmee het model wordt geïmplementeerd. De C-code Souche in VS code genereren:

1. Als de map met DCM-bestanden geopend is, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren**in te scha kelen.

    > [!NOTE]
    > De eerste keer dat u het hulp programma IoT Plug en Play code generator gebruikt, duurt het enkele seconden om te downloaden.

1. Selecteer het **MXChip IOT DevKit** DCM-bestand dat u zojuist hebt gedownload.

1. Voer de naam van het project in **devkit_device**.

1. Kies **ANSI C** als uw taal.

1. Kies **via DPS (Device Provisioning Service) symmetrische sleutel** als de verbindings methode.

1. Kies **cmake project in Windows** als uw project type. Kies niet **MXChip IOT DevKit-project**. deze optie is voor wanneer u een echt DevKit-apparaat hebt.

1. Kies **via Vcpkg** als de manier waarop u de SDK wilt toevoegen.

1. VS code opent een nieuw venster met gegenereerde code stub-bestanden in de map `devkit_device`.

![Gegenereerde apparaatcode](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de gegenereerde stub voor de apparaatcode te maken. De toepassing die u bouwt, simuleert een **MXChip IOT DevKit** -apparaat en maakt verbinding met uw IOT Central-toepassing. De toepassing verzendt telemetrie en eigenschappen, en ontvangt opdrachten.

1. Maak een `cmake`-submap in de map `devkit_device` van een opdracht prompt en navigeer naar die map:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de gegenereerde code-stub te maken. Vervang de tijdelijke aanduiding `<directory of your Vcpkg repo>` door het pad naar uw kopie van de **Vcpkg** -opslag plaats:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Als u Visual Studio 2017 of 2015 gebruikt, moet u de CMake-Generator opgeven op basis van de build-hulpprogram ma's die u gebruikt:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Wanneer de build is voltooid, wordt de toepassing uitgevoerd op dezelfde opdracht prompt. Vervang `<scopeid>` en `<primarykey>` door de waarden die u eerder hebt genoteerd:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. De toepassing wordt gestart met het verzenden van gegevens naar IoT Hub. Soms ziet u de fout `Error registering device for DPS` de eerste keer dat u de vorige opdracht uitvoert. Als u deze fout ziet, voert u de opdracht opnieuw uit.

## <a name="view-the-device"></a>Het apparaat weer geven

Nadat de apparaatcode verbinding maakt met uw IoT Central, kunt u de eigenschappen en telemetrie die worden verzonden, bekijken:

1. Ga in uw IoT Central-toepassing naar de pagina **apparaten** en selecteer het **mxchip-01-** apparaat. Dit apparaat is automatisch toegevoegd tijdens de verbinding met de apparaatcode:

    ![Overzichts pagina](./media/tutorial-connect-pnp-device/overview-page.png)

    Na een paar minuten toont deze pagina grafieken van de telemetrie die het apparaat verzendt.

1. Selecteer de pagina **over** om de eigenschaps waarden te zien die het apparaat heeft verzonden.

1. Selecteer de pagina **opdrachten** om opdrachten op het apparaat aan te roepen. U kunt zien dat het apparaat reageert op de opdracht prompt die de apparaatcode uitvoert.

1. Ga naar de pagina met **Apparaatinstellingen** om de sjabloon te bekijken die IOT Central gemaakt vanuit de DCM in de open bare opslag plaats:

    ![Pagina met Apparaatinstellingen](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een IoT Plug en Play-apparaat (preview) verbindt dat is gegenereerd op basis van een DCM in de open bare model opslagplaats.

Voor meer informatie over DCMs en het maken van uw eigen modellen gaat u naar de hand leiding:

> [!div class="nextstepaction"]
> [Een nieuw IoT-apparaattype definiëren](./howto-set-up-template.md)

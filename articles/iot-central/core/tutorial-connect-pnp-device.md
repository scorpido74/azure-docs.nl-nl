---
title: Zelfstudie - Een IoT-stekker en afspelen (voorbeeld) koppelen aan Azure IoT Central
description: In deze zelfstudie ziet u hoe u een apparaatcapaciteitsmodel gebruiken om apparaatcode te genereren. Voer vervolgens de apparaatcode uit, zie het apparaat verbinding maken met uw IoT Central-toepassing en gebruik de automatisch gegenereerde weergaven.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80064423"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Zelfstudie: Gebruik een apparaatcapaciteitsmodel om een IoT Plug and Play -apparaat (voorbeeld) te maken en deze aan te sluiten op uw IoT Central-toepassing

Een _apparaat (DCM)_ beschrijft de mogelijkheden van een [IoT Plug and Play -apparaat (preview).](../../iot-pnp/overview-iot-plug-and-play.md) IoT Central kan een DCM gebruiken om een apparaatsjabloon en visualisaties voor een apparaat te maken wanneer het apparaat voor de eerste keer verbinding maakt.

Ondersteuning voor [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) is in preview en wordt alleen ondersteund in geselecteerde regio's.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruik Visual Studio Code om een IoT Plug and Play (preview) apparaat te maken met behulp van een DCM.
> * Voer de apparaatcode uit in Windows en zie deze verbinding maken met uw IoT Central-toepassing.
> * Bekijk de gesimuleerde telemetrie die het apparaat verzendt.

## <a name="prerequisites"></a>Vereisten

Voltooi de [snel start--toepassing Een Azure IoT Central](./quick-deploy-iot-central.md) maken om een IoT Central-toepassing te maken met de **aangepaste app > aangepaste toepassingssjabloon.**

Als u deze zelfstudie wilt voltooien, moet u de volgende software op uw lokale machine installeren:

* [Bouw tools voor Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met **C++ buildtools** en **Nuget package manager component** workloads. Of als u al [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 of 2015 hebt met dezelfde workloads geïnstalleerd.
* [Git.](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/) - wanneer u **CMake**installeert, selecteert u de optie **CMake toevoegen aan het systeemPAD**.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Het `dps-keygen` nut:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Azure IoT-hulpprogramma's installeren

Gebruik de volgende stappen om het uitbreidingspakket van Azure IoT Tools in VS-code te installeren:

1. Selecteer in VS-code het tabblad **Extensies.**
1. Zoeken naar **Azure IoT-hulpprogramma's**.
1. Selecteer **Installeren**.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

In deze zelfstudie gebruikt u de [Vcpkg-bibliotheekbeheerder](https://github.com/microsoft/vcpkg) om de Azure IoT C-apparaat SDK in uw ontwikkelomgeving te installeren.

1. Open een opdrachtprompt. Voer de volgende opdracht uit om Vcpkg te installeren:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Voer vervolgens de volgende opdracht uit om gebruikersbrede [integratie](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)aan te sluiten. De eerste keer dat u deze opdracht uitvoert, zijn administratieve rechten vereist:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installeer Azure IoT C-apparaat SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Apparaatsleutel genereren

Als u een apparaat wilt aansluiten op een IoT Central-toepassing, hebt u een apparaatsleutel nodig. Ga als lid van het werk om een apparaatsleutel te genereren:

1. Meld u aan bij de IoT Central-toepassing die u hebt gemaakt met de **sjabloon Aangepaste toepassing** in de snelstart van de toepassing Een Azure [IoT Central](./quick-deploy-iot-central.md) maken.

1. Ga naar de pagina **Beheer** en selecteer **Apparaatverbinding**.

1. Noteer de **id-scope** en de **primaire sleutel die** u ziet wanneer u Toetsen **weergeven**selecteert. U gebruikt deze waarden later in deze zelfstudie.

    ![Apparaatverbinding](./media/tutorial-connect-pnp-device/device-connection.png)

1. Open een opdrachtprompt en voer de volgende opdracht uit om een apparaatsleutel te genereren:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Maak een notitie van de gegenereerde _apparaatsleutel,_ u gebruikt deze waarde in een latere stap in deze zelfstudie.

## <a name="download-your-model"></a>Uw model downloaden

In deze zelfstudie gebruik je de openbare DCM voor een MxChip IoT DevKit-apparaat. Je hebt geen echt DevKit-apparaat nodig om de code uit te voeren, in deze zelfstudie compileer je de code die je op Windows moet uitvoeren.

1. Maak een `central_app` map genaamd en open deze in VS-code.

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen, **IoT Plug and Play**in te voeren en **Modelrepository openen**te selecteren . Selecteer **Openbare opslagplaats**. VS Code toont een lijst met de DcM's in de openbare modelopslagplaats.

1. Selecteer de **MXChip IoT DevKit** DCM met ID `urn:mxchip:mxchip_iot_devkit:1`. Selecteer vervolgens **Downloaden**. Je hebt nu een kopie van `central_app` de DCM in de map.

![Modelrepository en DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Om met IoT Central te werken, moet het apparaatcapaciteitsmodel alle interfaces inline in hetzelfde bestand hebben gedefinieerd.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u de **MXChip IoT DevKit** DCM en de bijbehorende interfaces hebt, u de apparaatcode genereren die het model implementeert. Ga als u de C-codestub in VS-code wilt genereren:

1. Als de map met DCM-bestanden is geopend, gebruikt u **Ctrl+Shift+P** om het opdrachtpalet te openen, **IoT Plug and Play**in te voeren en **apparaatcode stub genereren**in te selecteren.

    > [!NOTE]
    > De eerste keer dat u de IoT Plug and Play Code Generator utility, het duurt een paar seconden om te downloaden.

1. Selecteer het **MXChip IoT DevKit** DCM-bestand dat u zojuist hebt gedownload.

1. Voer de projectnaam **devkit_device**in .

1. Kies **ANSI C** als taal.

1. Kies **de symmetrische toets Via DPS (Device Provisioning Service)** als verbindingsmethode.

1. Kies **CMake Project op Windows** als projecttype. Kies niet voor **MXChip IoT DevKit Project,** deze optie is voor wanneer je een echt DevKit-apparaat hebt.

1. Kies **Via Vcpkg** als de manier om de SDK op te nemen.

1. VS Code opent een nieuw venster met `devkit_device` gegenereerde apparaatcode stub-bestanden in de map.

![Gegenereerde apparaatcode](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de gegenereerde apparaatcode te bouwen. De applicatie die u bouwt simuleert een **MXChip IoT DevKit-apparaat** en maakt verbinding met uw IoT Central-toepassing. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Maak bij een opdrachtprompt een `cmake` `devkit_device` submap in de map en navigeer naar die map:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de gegenereerde codestomp te bouwen. Vervang `<directory of your Vcpkg repo>` de tijdelijke aanduiding door het pad naar uw exemplaar van de **Vcpkg-opslagplaats:**

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Als u Visual Studio 2017 of 2015 gebruikt, moet u de CMake-generator opgeven op basis van de buildtools die u gebruikt:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Nadat de build is voltooid, voert u bij dezelfde opdrachtprompt uw toepassing uit. Vervang `<scopeid>` `<devicekey>` en met de eerder opmerkte waarden:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. De apparaattoepassing begint met het verzenden van gegevens naar IoT Hub. Soms ziet u `Error registering device for DPS` de fout wanneer u de vorige opdracht voor het eerst uitvoert. Als u deze fout ziet, probeert u de opdracht opnieuw.

## <a name="view-the-device"></a>Het apparaat weergeven

Nadat uw apparaatcode verbinding heeft met uw IoT Central, u de eigenschappen en telemetrie bekijken die het verzendt:

1. Ga in uw IoT Central-toepassing naar de pagina **Apparaten** en selecteer het **mxchip-01-apparaat.** Dit apparaat werd automatisch toegevoegd toen de apparaatcode is aangesloten:

    ![Overzichtspagina](./media/tutorial-connect-pnp-device/overview-page.png)

    Na een paar minuten toont deze pagina grafieken van de telemetrie die het apparaat verzendt.

1. Selecteer de pagina **Info** om de eigenschapswaarden te zien die het apparaat heeft verzonden.

1. Selecteer de pagina **Opdrachten** om opdrachten op het apparaat aan te roepen. U zien dat het apparaat reageert op de opdrachtprompt waarop de apparaatcode wordt uitgevoerd.

1. Ga naar de pagina **Apparaatsjablonen** om de sjabloon te zien die IoT Central vanuit de DCM in de openbare opslagplaats heeft gemaakt:

    ![Pagina Apparaatsjablonen](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een IoT Plug and Play -apparaat (preview) aansluiten dat is gegenereerd vanuit een DCM in de openbare modelopslagplaats.

Ga voor meer informatie over DCM's en het maken van uw eigen modellen verder naar de handleiding:

> [!div class="nextstepaction"]
> [Een nieuw IoT-apparaattype definiëren](./howto-set-up-template.md)

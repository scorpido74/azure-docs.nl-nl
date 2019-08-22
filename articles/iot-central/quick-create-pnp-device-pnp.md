---
title: Een Azure IoT Plug en Play preview-apparaat verbinden met IoT Central | Microsoft Docs
description: Gebruik een mogelijkheidsprofiel om de apparaatcode te genereren. Vervolgens voert u de apparaatcode uit, raadpleegt u het apparaat verbinding maken met uw IoT Central-toepassing en gebruikt u de automatisch gegenereerde weer gaven.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 152e373f3a340a8abe3d8bc54d6515296d95efba
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878318"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Quickstart: Een mogelijkheidsprofiel gebruiken om een IoT Plug en Play-apparaat te maken en dit te verbinden met uw IoT Central toepassing

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Een _mogelijkheidsprofiel_ (DCM) beschrijft de mogelijkheden van een [IOT Plug en Play](https://aka.ms/iot-pnp-docs) -apparaat. IoT Central kunt een DCM gebruiken om een sjabloon en visualisaties voor een apparaat te maken wanneer het apparaat voor de eerste keer verbinding maakt. In deze snelstartgids leert u het volgende:

* Gebruik Visual Studio code om een IoT Plug en Play-apparaat te maken met een DCM.
* Voer de apparaatcode in Windows uit en zie verbinding maken met uw IoT Central-toepassing.
* De gesimuleerde telemetrie weer geven die het apparaat verzendt.

## <a name="prerequisites"></a>Vereisten

Voltooi de Snelstartgids [een Azure IOT Central-toepassing maken (preview-functies)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) om een IOT Central toepassing te maken met behulp van de sjabloon **Preview-toepassing** .

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale computer:

* [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) : Zorg ervoor dat u het onderdeel **NuGet package manager** en de **Desktop ontwikkeling C++ met** werk belasting opneemt tijdens de installatie van Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/) : als u **cmake**installeert, selecteert u de optie **cmake toevoegen aan het**systeempad.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* Het `dps-keygen` hulp programma:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-device-workbench"></a>Azure IoT Device Workbench installeren

Gebruik de volgende stappen om de Azure IoT Device Workbench-extensie in VS code te installeren:

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek naar **Azure IOT Device Workbench**.
1. Selecteer **Installeren**.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

### <a name="get-azure-iot-device-sdk-for-c"></a>Azure IoT Device SDK ophalen voor C

Een ontwikkel omgeving voorbereiden die u kunt gebruiken om de Azure IoT C-SDK voor apparaten te maken.

1. Open een opdrachtprompt. Voer de volgende opdracht uit voor het klonen van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-opslagplaats:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak een `central_app` map in de hoofdmap van de lokale kloon van de opslag plaats. U gebruikt deze map voor de model bestanden van het apparaat en de stub van het apparaat.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>Apparaatcode genereren

Als u een apparaat wilt verbinden met een IoT Central-toepassing, hebt u een apparaatcode nodig. Een apparaatcode genereren:

1. Meld u aan bij de IoT Central-toepassing die u in de vorige Snelstartgids hebt gemaakt.

1. Ga naar de **beheer** pagina en selecteer **apparaat-verbinding**.

1. Noteer de **bereik-id** en de **primaire sleutel**. U gebruikt deze waarden later in deze snelstart.

    ![Apparaat-verbinding](./media/quick-create-pnp-device-pnp/device-connection.png)

1. Open een opdracht prompt en voer de volgende opdracht uit om een apparaatcode te genereren:

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    Noteer de gegenereerde apparaatcode. u gebruikt deze waarde in een latere stap in deze Quick Start.

## <a name="download-your-model"></a>Uw model downloaden

In deze Quick Start gebruikt u de open bare DCM voor een MxChip IoT DevKit-apparaat. U hebt geen werkelijk DevKit-apparaat nodig om de code uit te voeren. in deze Snelstartgids kunt u de code compileren om uit te voeren op Windows.

1. Open `azure-iot-sdk-c\central_app` de map met VS code.

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en **open model opslagplaats**te selecteren. Selecteer de **open bare opslag plaats**. VS code toont een lijst van de DCMs in de open bare model opslagplaats.

1. Selecteer de **MXChip IOT DevKit** DCM met id `urn:mxchip:mxchip_iot_devkit:1`. Selecteer vervolgens **downloaden**. U hebt nu een kopie van de DCM in de `central_app` map.

![Model opslagplaats en DCM](./media/quick-create-pnp-device-pnp/public-repository.png)

> [!NOTE]
> Als u met IoT Central wilt werken, moeten in het mogelijkheidsprofiel alle interfaces in hetzelfde bestand zijn gedefinieerd.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u de **MXChip IOT DevKit** DCM en de bijbehorende interfaces hebt, kunt u de apparaatcode genereren waarmee het model wordt geïmplementeerd. De C-code Souche in VS code genereren:

1. Als de map met DCM-bestanden geopend is, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren**in te scha kelen.

    > [!NOTE]
    > De eerste keer dat u het hulp programma IoT Plug en Play code generator gebruikt, duurt het enkele seconden om te downloaden.

1. Selecteer het **MXChip IOT DevKit** DCM-bestand dat u zojuist hebt gedownload.

1. Voer de project naam **devkit_device**in.

1. Kies **ANSI C** als uw taal.

1. Kies **cmake project** als uw project type. Kies niet **MXChip IOT DevKit-project**. deze optie is voor wanneer u een echt DevKit-apparaat hebt.

1. Kies **via DPS (Device Provisioning Service) symmetrische sleutel** als de verbindings methode.

1. VS code opent een nieuw venster met gegenereerde code stub-bestanden in `devkit_device` de map.

![Gegenereerde apparaatcode](./media/quick-create-pnp-device-pnp/generated-code.png)

De verbindings gegevens toevoegen aan de gegenereerde apparaatcode:

1. In het VS code venster met de gegenereerde C-code. Open het `main.c`-bestand.

1. Vervang `[DPS Id Scope]` door de **bereik-id** die u eerder hebt genoteerd.

1. Vervang `[DPS symmetric key]` door de apparaatcode die u in een vorige stap hebt gegenereerd.

1. Vervang `[device registration Id]` door `mxchip-01`.

1. Sla uw wijzigingen op.

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de gegenereerde stub voor de apparaatcode te maken. De toepassing die u bouwt, simuleert een **MXChip IOT DevKit** -apparaat en maakt verbinding met uw IOT Central-toepassing. De toepassing verzendt telemetrie en eigenschappen, en ontvangt opdrachten.

1. Open het `CMakeLists.txt` bestand in de `azure-iot-sdk-c` map in VS code. Zorg ervoor dat u het `CMakeLists.txt` bestand opent in `azure-iot-sdk-c` de map, `devkit_device` niet in de map.

1. Voeg de volgende regel aan de onderkant van het `CMakeLists.txt` bestand toe om de map voor de code van het apparaat op te Neem bij het compileren:

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. Maak een `cmake` map in de `azure-iot-sdk-c` map en navigeer naar deze map op de opdracht regel:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Nadat de build is voltooid, voert u op dezelfde opdracht prompt uw toepassing uit:

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe
    ```

1. De toepassing wordt gestart met het verzenden van gegevens naar uw IoT Central-toepassing.

## <a name="view-the-device"></a>Het apparaat weer geven

Nadat de apparaatcode verbinding maakt met uw IoT Central, kunt u de eigenschappen en telemetrie die worden verzonden, bekijken:

1. Ga in uw IoT Central-toepassing naar de pagina **apparaten** en selecteer het **mxchip-01-** apparaat. Dit apparaat is automatisch toegevoegd tijdens de verbinding met de apparaatcode:

    ![Overzichts pagina](./media/quick-create-pnp-device-pnp/overview-page.png)

    Na een paar minuten toont deze pagina grafieken van de telemetrie die het apparaat verzendt.

1. Selecteer de pagina **over** om de eigenschaps waarden te zien die het apparaat heeft verzonden.

1. Selecteer de pagina **opdrachten** om opdrachten op het apparaat aan te roepen. U kunt zien dat het apparaat reageert op de opdracht prompt die de apparaatcode uitvoert.

1. Ga naar de pagina met **Apparaatinstellingen** om de sjabloon te bekijken die IOT Central gemaakt vanuit de DCM in de open bare opslag plaats:

    ![Pagina met Apparaatinstellingen](./media/quick-create-pnp-device-pnp/device-template.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat verbindt dat is gegenereerd op basis van een DCM in de open bare model opslagplaats.

Voor meer informatie over DCMs en het maken van uw eigen modellen gaat u naar de hand leiding:

> [!div class="nextstepaction"]
> [Een sjabloon voor een apparaat instellen en beheren](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

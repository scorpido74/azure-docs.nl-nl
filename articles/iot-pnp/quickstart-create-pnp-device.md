---
title: Een IoT Plug en Play preview-apparaat maken | Microsoft Docs
description: Gebruik een mogelijkheidsprofiel om de apparaatcode te genereren. Voer vervolgens de apparaatcode uit en Bekijk het apparaat verbinding maken met uw IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 386c2fa23e8d01f696ef3cf6078bac5fcec58f05
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050136"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Quickstart: Een mogelijkheidsprofiel gebruiken om een IoT Plug en Play-apparaat te maken

Een _mogelijkheidsprofiel_ (DCM) beschrijft de mogelijkheden van een IOT Plug en Play-apparaat. Een DCM is vaak gekoppeld aan een product-SKU. De mogelijkheden die zijn gedefinieerd in DCM, zijn ingedeeld in herbruikbare interfaces. U kunt skelet apparaatcode genereren vanuit een DCM. In deze Quick start ziet u hoe u VS code kunt gebruiken om een IoT Plug en Play-apparaat te maken met een DCM.

## <a name="prerequisites"></a>Vereisten

Om deze Quick Start te volt ooien, moet u de volgende software installeren op uw lokale computer:

* [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) : Zorg ervoor dat u het onderdeel **NuGet package manager** en de **Desktop ontwikkeling C++ met** werk belasting opneemt tijdens de installatie van Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-device-workbench"></a>Azure IoT Device Workbench installeren

Gebruik de volgende stappen om de Azure IoT Device Workbench-extensie in VS code te installeren:

1. Selecteer in VS code het tabblad **extensies** .
1. Zoek naar **Azure IOT Device Workbench**.
1. Selecteer **Installeren**.

### <a name="install-the-azure-iot-explorer"></a>De Azure IoT Explorer installeren

Down load en installeer het hulp programma Azure IoT Explorer vanaf de pagina [nieuwste release](https://github.com/Azure/azure-iot-explorer/releases) .

### <a name="get-the-connection-string-for-your-company-model-repository"></a>De connection string voor uw bedrijfs model opslagplaats ophalen

Als u zich aanmeldt met een micro soft-werk-of school account, of uw micro soft-partner-ID als u deze hebt, kunt u uw _bedrijfsmodel opslagplaats Connection String_ vinden in de Portal Portal van [Azure Certified voor IOT](https://preview.catalog.azureiotsolutions.com) . Nadat u zich hebt aangemeld, selecteert u **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Een IoT-hub voorbereiden

U hebt ook een Azure IoT hub in uw Azure-abonnement nodig om deze Quick Start te volt ooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voeg de Microsoft Azure IoT-extensie voor Azure CLI toe:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Voer de volgende opdracht uit om de apparaat-id in uw IoT-hub te maken. Vervang de tijdelijke aanduidingen **YourIoTHubName** en **YourDevice** door uw werkelijke namen. Als u geen IoT Hub hebt, volgt u [deze instructies om er een te maken](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Voer de volgende opdrachten uit om de _apparaat-Connection String_ op te halen voor het apparaat dat u zojuist hebt geregistreerd:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Voer de volgende opdrachten uit om de _IOT hub-Connection String_ voor uw hub op te halen:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

### <a name="get-azure-iot-device-sdk-for-c"></a>Azure IoT Device SDK ophalen voor C

In deze Quick start gaat u een ontwikkel omgeving voorbereiden die u kunt gebruiken om de Azure IoT C-SDK te klonen en te bouwen.

1. Open een opdrachtprompt. Voer de volgende opdracht uit voor het klonen van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-opslagplaats:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak een `pnp_app` submap in de hoofdmap van de lokale kloon van de opslag plaats. U gebruikt deze map voor de model bestanden van het apparaat en de stub van het apparaat.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>Uw model ontwerpen

In deze Quick Start gebruikt u een bestaand voor beeld-mogelijkheidsprofiel en de bijbehorende interfaces.

1. Down load het [mogelijkheidsprofiel](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) en de [interface voor beeld](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) en sla de `pnp_app` bestanden op in de map.

    > [!TIP]
    > Als u een bestand van GitHub wilt downloaden, gaat u naar het bestand, klikt u met de rechter muisknop op **RAW**en selecteert u vervolgens **Koppeling opslaan als**.

1. Open `pnp_app` de map met VS code. U kunt de bestanden weer geven met IntelliSense:

    ![Mogelijkheidsprofiel](media/quickstart-create-pnp-device/dcm.png)

1. Vervang `<YOUR_COMPANY_NAME_HERE>` in de bestanden die u hebt gedownload in `@id` de `schema` velden en een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen. Zie [Digital Identifier-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie.

## <a name="generate-the-c-code-stub"></a>De C-code-stub genereren

Nu u een DCM en de bijbehorende interfaces hebt, kunt u de apparaatcode genereren waarmee het model wordt geïmplementeerd. De C-code Souche in VS code genereren:

1. Als de map met DCM-bestanden geopend is, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen, **IOT Plug en Play**in te voeren en de stub van de **apparaatcode genereren**in te scha kelen.

    > [!NOTE]
    > De eerste keer dat u het hulp programma IoT Plug en Play code generator gebruikt, duurt het enkele seconden om te downloaden.

1. Kies het DCM-bestand dat u wilt gebruiken voor het genereren van de stub voor het apparaat.

1. Voer de project naam **sample_device**in. Dit is de naam van uw apparaat-app.

1. Kies **ANSI C** als uw taal.

1. Kies **cmake project** als uw project type.

1. Kies **Via IOT hub apparaat Connection String** als verbindings methode.

1. VS code opent een nieuw venster met gegenereerde code stub-bestanden.
    ![Apparaatcode](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>De code bouwen

U gebruikt de SDK van het apparaat om de gegenereerde stub voor de apparaatcode te maken. De toepassing die u bouwt, simuleert een apparaat dat is verbonden met een IoT-hub. De toepassing verzendt telemetrie en eigenschappen en ontvangt opdrachten.

1. Open `CMakeLists.txt` in VS code de hoofdmap van de apparaat-SDK.

1. Voeg de volgende regel aan de onderkant van het `CMakeLists.txt` bestand toe om de map voor de code van het apparaat op te Neem bij het compileren:

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. Maak een submap cmake in de hoofdmap van de apparaat-SDK en navigeer naar die map:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Voer de volgende opdrachten uit om de SDK van het apparaat en de gegenereerde code-stub te maken:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Als cmake uw C++ compiler niet kan vinden, krijgt u tijdens het uitvoeren van de vorige opdracht fouten bij het bouwen. Als dat het geval is, kunt u proberen deze opdracht uit te voeren op de [Visual Studio-opdracht prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Nadat de build is voltooid, voert u uw toepassing uit met het IoT hub-apparaat connection string als para meter.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. De toepassing wordt gestart met het verzenden van gegevens naar IoT Hub.

    ![Apparaat-app wordt uitgevoerd](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>De code valideren

### <a name="publish-device-model-files-to-model-repository"></a>Model bestanden van het apparaat publiceren naar model opslagplaats

Als u de apparaatcode met **Azure IOT Explorer**wilt valideren, moet u de bestanden naar de model opslagplaats publiceren.

1. Als de map met DCM-bestanden geopend is, gebruikt u **CTRL + SHIFT + P** om het opdracht palet te openen **. Typ en selecteer IOT plug & Play: Bestanden verzenden naar model opslagplaats**.

1. Selecteer `SampleDevice.capabilitymodel.json` en`EnvironmentalSensor.interface.json` bestanden.

1. Voer uw bedrijfs model opslagplaats connection string in.

    > [!NOTE]
    > De connection string is alleen vereist wanneer u voor het eerst verbinding maakt met de opslag plaats.

1. In het venster voor het uitvoeren van VS code en meldingen kunt u controleren of de bestanden correct zijn gepubliceerd.

    > [!NOTE]
    > Als u fouten krijgt bij het publiceren van de model bestanden van het apparaat, kunt **u de opdracht IOT Plug en Play gebruiken: Meld u aan bij** de model opslagplaats om u af te melden en de stappen opnieuw door te lopen.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>De Azure IoT Explorer gebruiken om de code te valideren

1. Open Azure IoT Explorer. u ziet de pagina **app-configuraties** .

1. Voer uw IoT Hub connection string in en klik op **verbinding maken**.

1. Nadat u verbinding hebt gemaakt, wordt de overzichts pagina van het apparaat weer gegeven.

1. Als u uw bedrijfs opslagplaats wilt toevoegen, selecteert u **instellingen**, vervolgens **+ Nieuw**en vervolgens **bedrijfs opslagplaats**.

1. Voeg uw bedrijfs model opslagplaats connection string toe. Selecteer **Verbinden**.

1. Zoek op de pagina overzicht van apparaten de apparaat-id die u eerder hebt gemaakt en selecteer deze om meer details weer te geven.

1. Vouw de interface met de ID **urn: azureiot: EnvironmentalSensor: 1** uit om de IOT Plug en Play-primitieven-eigenschappen, opdrachten en telemetrie weer te geven.

1. Selecteer de telemetrie-pagina om de telemetriegegevens weer te geven die het apparaat verzendt.

1. Selecteer de pagina **Eigenschappen (niet-schrijfbaar)** om de niet-Beschrijf bare eigenschappen weer te geven die door het apparaat worden gerapporteerd.

1. Selecteer de pagina **Eigenschappen (schrijfbaar)** om de Beschrijf bare eigenschappen die u kunt bijwerken weer te geven.

1. Vouw de **naam**van de eigenschap uit, update met een nieuwe naam en selecteer **Beschrijf bare eigenschap bijwerken**. 
2. Voor een overzicht van de nieuwe naam wordt weer gegeven in de kolom **gemelde eigenschap** , klikt u op de knop **vernieuwen** boven op de pagina.

1. Selecteer de **opdracht** pagina om alle opdrachten weer te geven die het apparaat ondersteunt.

1. Vouw de **knip** opdracht uit en stel een nieuw Knipper tijds interval in. Selecteer **opdracht verzenden** om de opdracht op het apparaat aan te roepen.

1. Ga naar het gesimuleerde apparaat om te controleren of de opdracht wordt uitgevoerd zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een IoT-Plug en Play apparaat kunt maken met behulp van een DCM.

Ga verder met de zelf studie voor meer informatie over IoT Plug en Play:

> [!div class="nextstepaction"]
> [Een mogelijkheidsprofiel maken en testen met Visual Studio code](tutorial-pnp-visual-studio-code.md)

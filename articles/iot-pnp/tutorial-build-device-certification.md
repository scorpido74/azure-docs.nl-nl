---
title: Een IoT Plug and Play Preview-apparaat bouwen dat klaar is voor certificering | Microsoft Documenten
description: Als apparaatontwikkelaar leest u hoe u een IoT Plug and Play Preview-apparaat bouwen dat klaar is voor certificering.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239905"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>Een IoT Plug and Play Preview-apparaat bouwen dat klaar is voor certificering

In deze zelfstudie wordt beschreven hoe u als apparaatontwikkelaar een IoT Plug and Play Preview-apparaat bouwen dat klaar is voor certificering.

De certificeringstests controleren of:

- De code van uw IoT Plug and Play-apparaat is geïnstalleerd op uw apparaat.
- Uw IoT Plug and Play-apparaatcode is gebouwd met Azure IoT SDK.
- Uw apparaatcode ondersteunt de [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
- Uw apparaatcode implementeert de interface apparaatinformatie.
- Het capaciteitsmodel en de apparaatcode werken met IoT Central.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- [Visual Studio-code](https://code.visualstudio.com/download)
- [Azure IoT-hulpprogramma's voor VS-code-uitbreidingspakket](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

U moet ook het [model Voor apparaatfunctionaliteit](quickstart-create-pnp-device-windows.md) gebruiken voltooien om een apparaat snelstart voor Windows te maken. De quickstart laat u zien hoe u uw ontwikkelomgeving instelt met Vcpkg en een voorbeeldproject maakt.

## <a name="store-a-capability-model-and-interfaces"></a>Een capaciteitsmodel en interfaces opslaan

Voor IoT Plug and Play-apparaten moet u een capaciteitsmodel en interfaces maken die de mogelijkheden van het apparaat definiëren als JSON-bestanden.

U deze JSON-bestanden op drie verschillende locaties opslaan:

- De openbare modelrepository.
- Uw bedrijfsmodel repository.
- Op je apparaat.

Om uw apparaat te certificeren, moeten de bestanden momenteel worden opgeslagen in uw bedrijfsmodelopslagplaats of in de openbare modelopslagplaats.

## <a name="include-the-required-interfaces"></a>De vereiste interfaces opnemen

Als u het certificeringsproces wilt doorstaan, moet u de interface **apparaatinformatie** opnemen en implementeren in uw capaciteitsmodel. Deze interface heeft de volgende identificatie:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Als u de [Quickstart hebt voltooid: gebruik een apparaatmogelijkheidmodel om een apparaat te maken,](quickstart-create-pnp-device-windows.md)hebt u de interface **Apparaatgegevens** al in uw model opgenomen.

Als u de interface **Apparaatinformatie** in uw apparaatmodel `implements` wilt opnemen, voegt u de interface-id toe aan de eigenschap van het capaciteitsmodel:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Ga als bedoeld als u de interface **Apparaatinformatie** in VS-code weergeeft:

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer de opdracht **IoT Plug and Play Open Model Repository.** Kies **Openbare modelopslagplaats openen**. De openbare modelopslagplaats wordt geopend in VS-code.

1. Selecteer in de openbare modelopslagplaats het tabblad **Interfaces,** selecteer het filterpictogram en voer **Apparaatgegevens** in het filterveld in.

1. Als u een lokale kopie van de interface **Apparaatgegevens wilt** maken, selecteert u deze in de gefilterde lijst en selecteert u **Downloaden**. VS-code geeft het interfacebestand weer.

Ga als een overzicht van de interface **apparaatgegevens** met Azure CLI:

1. [Installeer de Azure IoT CLI-extensie](howto-install-pnp-cli.md).

1. Gebruik de volgende opdracht Azure CLI om een interface met de interface-id van apparaatgegevens weer te geven:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

Zie [De Azure IoT-extensie voor Azure CLI installeren en gebruiken voor](howto-install-pnp-cli.md)meer informatie.

## <a name="update-device-code"></a>Apparaatcode bijwerken

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Apparaatinrichting inschakelen via de Azure IoT Device Provisioning Service (DPS)

Om het apparaat te certificeren, moet het inrichten via de [Azure IoT Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)inschakelen. Als u de mogelijkheid wilt toevoegen om DPS te gebruiken, u de C-code-stub in VS-code genereren. Volg deze stappen:

1. Open de map met DCM-bestand in VS-code, gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen, voer **IoT Plug and Play in**en selecteer Device Code Stub **genereren**.

1. Kies het DCM-bestand dat u wilt gebruiken om de apparaatcode-stub te genereren.

1. Voer de projectnaam in, zoals **sample_device**. Dit is de naam van uw apparaattoepassing.

1. Kies **ANSI C** als taal.

1. Kies **de symmetrische sleutel via DPS (Device Provisioning Service)** als verbindingsmethode.

1. Kies **CMake Project in Windows** als projectsjabloon.

1. Kies **Via Vcpkg** als de manier om het apparaat SDK op te nemen.

1. VS Code opent een nieuw venster met gegenereerde apparaatcode stub-bestanden.

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

1. Nadat de build is voltooid, voert u de**DPS-referenties (DPS ID-scope**, **DPS Symmetric Key**, **Device Id**) in als parameters voor de toepassing. Zie [Uw IoT Plug and Play-apparaat verbinden en testen](tutorial-certification-test.md#connect-and-discover-interfaces)als u de referenties van de certificeringsportal wilt ophalen.

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Standaardinterfaces implementeren

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>De interfaces ModelInformatie en SDK-informatie implementeren

De Azure IoT-apparaat SDK implementeert de modelinformatie- en SDK-informatie-interfaces. Als u de functie voor het genereren van code in VS-code gebruikt, gebruikt uw apparaatcode de SDK voor Het Plug and Play-apparaat van IoT.

Als u ervoor hebt gekozen de SDK van Azure IoT-apparaat niet te gebruiken, u de SDK-broncode als referentie gebruiken voor uw eigen implementatie.

#### <a name="implement-the-device-information-interface"></a>De interface apparaatgegevens implementeren

Implementeer de **interface apparaatinformatie** op uw apparaat en geef apparaatspecifieke informatie van het apparaat tijdens de uitvoering.

U een voorbeeldimplementatie van de **apparaatinformatie-interface** voor [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) als referentie gebruiken.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>Implementeer alle mogelijkheden die in uw model zijn gedefinieerd

Tijdens de certificering wordt uw apparaat programmatisch getest om ervoor te zorgen dat het mogelijkheden implementeert die in de interfaces zijn gedefinieerd. Gebruik HTTP-statuscode 501 om te reageren op eigenschap- en opdrachtaanvragen voor lezen en opdrachtaanvragen als uw apparaat deze niet implementeert.

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Plug and Play-apparaat hebt gebouwd dat klaar is voor certificering, is de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Meer informatie over het certificeren van uw apparaat](tutorial-certification-test.md)

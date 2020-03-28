---
title: Een IoT Plug and Play Preview-apparaat maken en testen | Microsoft Documenten
description: Als apparaatontwikkelaar leest u over het gebruik van VS-code om een nieuw apparaatmodel voor een IoT Plug and Play Preview-apparaat te maken en te testen.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719718"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Zelfstudie: Een apparaatcapaciteitsmodel maken en testen met Visual Studio Code

In deze zelfstudie ziet u hoe u als apparaatontwikkelaar Visual Studio Code gebruiken om een _apparaatcapaciteitsmodel_te maken. U het model gebruiken om skeletcode te genereren om te worden uitgevoerd op een apparaat dat verbinding maakt met een Azure IoT Hub-instantie in de cloud.

De sectie in deze zelfstudie waarin wordt beschreven hoe u de gegenereerde skeletcode bouwen, gaat ervan uit dat u Windows gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatcapaciteitsmodel maken
> * Skeletapparaatcode genereren van het model
> * De stubs in de gegenereerde code implementeren
> * De code uitvoeren om de interacties met een IoT-hub te testen

## <a name="prerequisites"></a>Vereisten

Als u in deze zelfstudie met het apparaatcapaciteitsmodel wilt werken, moet u het andere doen:

* [Visual Studio Code](https://code.visualstudio.com/download): VS Code is beschikbaar voor meerdere platforms
* [Azure IoT-hulpprogramma's voor VS-code-uitbreidingspakket.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Gebruik de volgende stappen om het uitbreidingspakket in VS-code te installeren:

    1. Selecteer in VS-code het tabblad **Extensies.**
    1. Zoeken naar **Azure IoT-hulpprogramma's**.
    1. Selecteer **Installeren**.

Als u de gegenereerde C-code in Windows wilt bouwen in deze zelfstudie, moet u het andere doen:

* [Bouw tools voor Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met **C++ buildtools** en **NuGet package manager component** workloads. Of als u al [Visual Studio (Community, Professional of Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 of 2015 hebt met dezelfde workloads geïnstalleerd.
* [Git](https://git-scm.com/download)
* [Cmake](https://cmake.org/download/)

Als u uw apparaatcode in deze zelfstudie wilt testen, moet u het andere doen:

* De [Azure IoT-verkenner](https://github.com/Azure/azure-iot-explorer/releases).
* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Uw apparaat modelleren

U gebruikt de _digitale tweedefinitietaal_ om een apparaatcapaciteitsmodel te maken. Een model bestaat meestal uit meerdere _interfacedefinitiebestanden_ en één modelbestand. De **Azure IoT-hulpprogramma's voor VS-code** bevatten hulpprogramma's waarmee u deze JSON-bestanden maken en bewerken.

### <a name="create-the-interface-file"></a>Het interfacebestand maken

Ga als het gaat om het maken van een interfacebestand dat de mogelijkheden van uw IoT-apparaat definieert in VS-code:

1. Een map maken met de naam **devicemodel**.

1. Start VS-code en gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer vervolgens de opdracht **IoT-stekker & Afspelen: Interface maken.**

1. Blader naar en selecteer de **apparaatmodelmap die** u hebt gemaakt.

1. Voer **vervolgens EnvironmentalSensor** in als de naam van de interface en druk op **Enter**. VS Code maakt een voorbeeldinterfacebestand met de naam **EnvironmentalSensor.interface.json**.

1. Vervang de inhoud van dit bestand door `{your name}` de `@id` volgende JSON en vervang in het veld een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderbied. Zie [Digital Twin-id-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie. De interface-id moet uniek zijn om de interface in de repository op te slaan:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Deze interface definieert apparaateigenschappen zoals **klantnaam,** telemetrietypen zoals **Temperatuur**en opdrachten zoals **turnon**.

1. Voeg aan het einde van dit interfacebestand een opdrachtmogelijkheid toe die **knipperen** wordt genoemd. Zorg ervoor dat u een komma toevoegt voordat u de opdracht toevoegt. Probeer de definitie te typen om te zien hoe intellisense, automatisch aanvullen en validatie u kunnen helpen bij het bewerken van een interfacedefinitie:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Sla het bestand op.

### <a name="create-the-model-file"></a>Het modelbestand maken

Het modelbestand geeft de interfaces op die uw IoT Plug and Play-apparaat implementeert. Er zijn meestal ten minste twee interfaces in een model - een of meer die de specifieke mogelijkheden van uw apparaat definiëren en een standaardinterface die alle IoT Plug and Play-apparaten moeten implementeren.

Ga als het gaat om het maken van een modelbestand dat de interfaces opgeeft die uw IoT Plug and Play-apparaat implementeert in VS-code:

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer vervolgens de opdracht **IoT-stekker & Spelen: Maak het model maken.** Voer vervolgens **SensorboxModel** in als de naam van het model. VS-code maakt een voorbeeldinterfacebestand met de naam **SensorboxModel.capabilitymodel.json**.

1. Vervang de inhoud van dit bestand door `{your name}` de `@id` volgende JSON en vervang in het veld en in de `EnvironmentalSensor` interface met dezelfde waarde die u hebt gebruikt in het bestand **EnvironmentalSensor.interface.json.** De interface-id moet uniek zijn om de interface in de repository op te slaan:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Het model definieert een apparaat dat uw **EnvironmentalSensor-interface** en de standaard **Interface DeviceInformation** implementeert.

1. Sla het bestand op.

### <a name="download-the-deviceinformation-interface"></a>De interface DeviceInformation downloaden

Voordat u skeletcode uit het model genereren, moet u een lokale kopie van de **Apparaatinformatie** maken vanuit de *openbare modelopslagplaats.* De openbare modelrepository bevat al de **DeviceInformation-interface.**

Ga als het gaat om het downloaden van de **DeviceInformation-interface** van de openbare modelopslagplaats met VS-code:

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play**in, selecteer de opdracht **Modelopslagplaats** openen en selecteer Vervolgens Openbare **modelopslagplaats openen**.

1. Selecteer **Interfaces,** selecteer vervolgens de `urn:azureiot:DeviceManagement:DeviceInformation:1`apparaatinformatie-interface met ID en selecteer **Vervolgens Downloaden**.

U hebt nu de drie bestanden die deel uitmaken van uw apparaat mogelijkheid model:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Het model publiceren

Als u het Azure IoT Explorer-hulpprogramma wilt lezen, moet u het publiceren in uw bedrijfsopslagplaats. Als u wilt publiceren vanuit VS-code, hebt u de verbindingstekenreeks voor de bedrijfsopslagplaats nodig:

1. Navigeer naar de [Azure Certified for IoT-portal.](https://aka.ms/ACFI)

1. Gebruik uw _Microsoft-werkaccount_ om u aan te melden bij de portal.

1. Selecteer **Bedrijfsopslagplaats** en vervolgens **Verbindingstekenreeksen**.

1. Kopieer de verbindingstekenreeks.

Ga als bedoeld als het gaat om het openen van uw bedrijfsrepository in VS Code:

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer vervolgens de opdracht **IoT Plug & Play: Open Model Repository.**

1. Selecteer **Repository openen en** plak in uw verbindingstekenreeks.

1. Druk op **Enter** om uw bedrijfsopslagplaats te openen.

Ga als het gaat om het publiceren van uw apparaatcapaciteitsmodel en interfaces naar uw bedrijfsopslagplaats:

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer vervolgens de opdracht **IoT Plug & Play: Bestanden verzenden naar Model Repository.**

1. Selecteer de **bestanden EnvironmentalSensor.interface.json** en **SensorboxModel.capabilitymodel.json** en selecteer **OK**.

Uw bestanden worden nu opgeslagen in uw bedrijfsopslagplaats.

## <a name="generate-code"></a>Code genereren

U de **Azure IoT-hulpprogramma's voor VS-code** gebruiken om skelet C-code van uw model te genereren. Ga als bedoeld als gevolg van het genereren van de skeletcode in VS-code:

1. Gebruik **Ctrl+Shift+P** om het opdrachtpalet te openen.

1. Voer **Plug and Play in** en selecteer vervolgens de opdracht **IoT-stekker & Play: Genereer apparaatcode.**

1. Selecteer het modelbestand **SensorboxModel.capabilitymodel.json** capability.

1. Voer **sensorbox_app** in als de projectnaam.

1. Kies **ANSI C** als taal.

1. Kies **via IoT Hub-verbindingstekenreeks** als de manier om verbinding te maken.

1. Kies **CMake Project in Windows** als projectsjabloon.

1. Kies **Via Vcpkg** als manier om het apparaat SDK op te nemen.

VS Code genereert de skeleton C-code en slaat de bestanden op in de **map sensorbox_app** in de **map modelcode.** VS Code opent een nieuw venster met de gegenereerde codebestanden.

## <a name="update-the-generated-code"></a>De gegenereerde code bijwerken

Voordat u de code bouwen en uitvoeren, moet u de stopige eigenschappen, telemetrie en opdrachten implementeren.

Ga als bedoeld als bedoeld bij implementaties van de stopcode in VS Code:

1. Open het **SensorboxModel_impl.c-bestand.**

1. Voeg code toe om de stopfuncties te implementeren.

1. Sla uw wijzigingen op.

## <a name="build-the-code"></a>De code bouwen

Voordat u de code uitvoert om uw IoT Plug and Play-apparaat te testen met een Azure IoT-hub, moet u de code compileren.

Volg de instructies in het **Readme.md-bestand** in de **map sensorbox_app** om de code op Windows te bouwen en uit te voeren. De volgende sectie bevat instructies voor het ophalen van een apparaatverbindingstekenreeks die u wilt gebruiken wanneer u de apparaatcode uitvoert.

## <a name="test-the-code"></a>De code testen

Wanneer u de code uitvoert, maakt deze verbinding met IoT Hub en wordt begonnen met het verzenden van voorbeeldtelemetrie- en eigenschapwaarden. Het apparaat reageert ook op opdrachten die vanuit de IoT Hub worden verzonden. Ga als volgt te werk om dit gedrag te verifiëren:

1. Een IoT Hub maken:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Voeg een apparaat toe aan uw IoT-hub en haal de verbindingstekenreeks op:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Noteer de verbindingstekenreeks.

1. Navigeer bij een opdrachtprompt naar de map **azure-iot-sdk-c** waar u de SDK en voorbeelden hebt gebouwd. Navigeer vervolgens naar de map **cmake\\\\sensorbox_app Release.**

1. Voer de volgende opdracht uit:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Gebruik het hulpprogramma azure IoT explorer om te communiceren met het IoT Plug and Play-apparaat dat is verbonden met uw IoT-hub. Zie [Azure IoT explorer installeren en gebruiken](./howto-install-iot-explorer.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Plug and Play hebt gebouwd die klaar is voor certificering, leert u hoe u:

> [!div class="nextstepaction"]
> [Een apparaat bouwen dat gereed is voor certificering](tutorial-build-device-certification.md)

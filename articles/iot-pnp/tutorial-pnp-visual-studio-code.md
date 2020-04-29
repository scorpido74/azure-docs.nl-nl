---
title: Een IoT Plug en Play preview-apparaat maken en testen | Microsoft Docs
description: Meer informatie over het gebruik van VS code voor het maken en testen van een nieuw mogelijkheidsprofiel voor een IoT Plug en Play preview-apparaat.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76719718"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Zelf studie: een mogelijkheidsprofiel maken en testen met Visual Studio code

In deze zelf studie ziet u hoe, als een ontwikkelaar van een apparaat, Visual Studio code moet gebruiken om een _mogelijkheidsprofiel_te maken. U kunt het model gebruiken om skelet code te genereren voor uitvoering op een apparaat dat verbinding maakt met een Azure IoT Hub-exemplaar in de Cloud.

In het gedeelte in deze zelf studie waarin wordt beschreven hoe u de gegenereerde skelet code bouwt, wordt ervan uitgegaan dat u Windows gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een mogelijkheidsprofiel maken
> * Een apparaatcode van een skelet genereren op basis van het model
> * De stubs in de gegenereerde code implementeren
> * De code uitvoeren om de interacties te testen met een IoT-hub

## <a name="prerequisites"></a>Vereisten

Als u in deze zelf studie wilt werken met het mogelijkheidsprofiel, hebt u het volgende nodig:

* [Visual Studio code](https://code.visualstudio.com/download): VS code is beschikbaar voor meerdere platformen
* [Azure IOT-Hulpprogram ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -uitbreidings pakket. Gebruik de volgende stappen om het uitbreidings pakket in VS code te installeren:

    1. Selecteer in VS code het tabblad **extensies** .
    1. Zoek naar **Azure IOT-Hulpprogram ma's**.
    1. Selecteer **Installeren**.

Als u in deze zelf studie de gegenereerde C-code in Windows wilt maken, hebt u het volgende nodig:

* [Bouw Hulpprogram ma's voor Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) met **C++ build tools** en **NuGet package manager-onderdeel** workloads. Of als u [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 of 2015 al hebt geïnstalleerd met dezelfde workloads.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Als u uw apparaatcode in deze zelf studie wilt testen, hebt u het volgende nodig:

* [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Uw apparaat model leren

U gebruikt de _Digital-dubbele definitie taal_ om een mogelijkheidsprofiel te maken. Een model bestaat meestal uit meerdere _Interface_ definitie bestanden en één model bestand. De **Azure IOT-Hulpprogram ma's voor VS code** bevat hulpprogram ma's waarmee u deze json-bestanden kunt maken en bewerken.

### <a name="create-the-interface-file"></a>Het interface bestand maken

Een interface bestand maken waarmee de mogelijkheden van uw IoT-apparaat in VS code worden gedefinieerd:

1. Maak een map met de naam **devicemodel**.

1. Start VS code en gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer de opdracht **IOT plug & Play: Create interface** .

1. Blader naar en selecteer de **devicemodel** -map die u hebt gemaakt.

1. Voer **EnvironmentalSensor** in als de naam van de interface en druk op **Enter**. VS code maakt een voor beeld-interface bestand met de naam **EnvironmentalSensor. interface. json**.

1. Vervang de inhoud van dit bestand door de volgende JSON en vervang `{your name}` in het `@id` veld door een unieke waarde. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen. Zie [Digital Identifier-indeling](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)voor meer informatie. De interface-ID moet uniek zijn voor het opslaan van de interface in de opslag plaats:

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

    Deze interface definieert eigenschappen van apparaten, zoals de naam van de **klant**, de typen telemetrie, zoals de **Tempe ratuur**en opdrachten zoals **TurnOn**.

1. Voeg aan het einde van dit interface bestand een opdracht functie toe met de naam **Blink** . Zorg ervoor dat u een komma toevoegt voordat u de opdracht toevoegt. Typ de definitie om te zien hoe IntelliSense, automatisch aanvullen en valideren u kunt helpen bij het bewerken van een interface definitie:

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

### <a name="create-the-model-file"></a>Het model bestand maken

In het model bestand worden de interfaces opgegeven die uw IoT Plug en Play-apparaat implementeert. Er zijn meestal ten minste twee interfaces in een model, een of meer, waarmee de specifieke mogelijkheden van uw apparaat worden gedefinieerd, en een standaard interface die door alle IoT Plug en Play-apparaten moet worden geïmplementeerd.

Een model bestand maken dat de interfaces specificeert die uw IoT Plug en Play-apparaat implementeert in VS code:

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer de opdracht **IOT plug & Play: Create Capability model** . Voer **SensorboxModel** in als de naam van het model. VS code maakt een voor beeld-interface bestand met de naam **SensorboxModel. capabilitymodel. json**.

1. Vervang de inhoud van dit bestand door de volgende JSON en vervang `{your name}` in het `@id` veld en in de `EnvironmentalSensor` interface met dezelfde waarde die u hebt gebruikt in het bestand **EnvironmentalSensor. interface. json** . De interface-ID moet uniek zijn voor het opslaan van de interface in de opslag plaats:

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

    Het model definieert een apparaat dat uw **EnvironmentalSensor** -interface en de standaard **DeviceInformation** -interface implementeert.

1. Sla het bestand op.

### <a name="download-the-deviceinformation-interface"></a>De DeviceInformation-interface downloaden

Voordat u een skelet code van het model kunt genereren, moet u een lokale kopie van de **DeviceInformation** maken uit de *open bare model opslagplaats*. De open bare model opslagplaats bevat al de **DeviceInformation** -interface.

De **DeviceInformation** -interface vanuit de open bare model opslagplaats downloaden met behulp van VS code:

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play**in, selecteer de opdracht **model opslagplaats openen** en selecteer vervolgens **Open Public model-opslag**.

1. Selecteer **interfaces**, selecteer de apparaatgegevens interface met de id `urn:azureiot:DeviceManagement:DeviceInformation:1`en selecteer vervolgens **downloaden**.

U hebt nu de drie bestanden waaruit het mogelijkheidsprofiel kan worden gemaakt:

* urn_azureiot_DeviceManagement_DeviceInformation_1. interface. json
* EnvironmentalSensor. interface. json
* SensorboxModel. capabilitymodel. json

## <a name="publish-the-model"></a>Het model publiceren

Als u het hulp programma Azure IoT Explorer wilt gebruiken om uw mogelijkheidsprofiel te lezen, moet u dit publiceren in de opslag plaats van uw bedrijf. Als u wilt publiceren vanuit VS code, hebt u de connection string nodig voor de bedrijfs opslagplaats:

1. Ga naar de [Azure Certified voor IOT-Portal](https://aka.ms/ACFI).

1. Gebruik uw micro soft- _werk account_ om u aan te melden bij de portal.

1. Selecteer **bedrijfs opslagplaats** en vervolgens **verbindings reeksen**.

1. Kopieer de connection string.

De opslag plaats van uw bedrijf in VS code openen:

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer de optie **IOT plug & Play: open model repository** .

1. Selecteer **opslag plaats organisatie model openen** en plak de Connection String.

1. Druk op **Enter** om de opslag plaats van uw bedrijf te openen.

Uw mogelijkheidsprofiel en interfaces publiceren naar uw bedrijfs opslagplaats:

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer vervolgens de **IOT plug & Play: bestanden verzenden naar model opslagplaats** opdracht.

1. Selecteer de bestanden **EnvironmentalSensor. interface. json** en **SensorboxModel. capabilitymodel. json** en selecteer **OK**.

Uw bestanden worden nu opgeslagen in de opslag plaats van uw bedrijf.

## <a name="generate-code"></a>Code genereren

U kunt de **Azure IOT-Hulpprogram ma's voor VS code** gebruiken voor het genereren van een skelet C-code van uw model. De skelet code in VS code genereren:

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer vervolgens de **IOT plug & Play: apparaatcode stub** -opdracht genereren.

1. Selecteer het **SensorboxModel. capabilitymodel. json** -bestand met het capaciteits model.

1. Voer **sensorbox_app** in als de project naam.

1. Kies **ANSI C** als taal.

1. Kies **Via IOT hub apparaat Connection String** als de manier om verbinding te maken.

1. Kies **cmake project in Windows** als project sjabloon.

1. Kies **via Vcpkg** als manier om de SDK voor het apparaat in te voegen.

VS code genereert de code van de skelet C en slaat de bestanden op in de map **sensorbox_app** in de map **modelcode** . VS code opent een nieuw venster met de gegenereerde code bestanden.

## <a name="update-the-generated-code"></a>De gegenereerde code bijwerken

Voordat u de code kunt bouwen en uitvoeren, moet u de stubbed-eigenschappen, telemetrie en opdrachten implementeren.

Voor het leveren van implementaties voor de stubbed-code in VS code:

1. Open het bestand **SensorboxModel_impl. c** .

1. Voeg code toe om de stubbed-functies te implementeren.

1. Sla uw wijzigingen op.

## <a name="build-the-code"></a>De code bouwen

Voordat u de code uitvoert om uw IoT Plug en Play-apparaat te testen met een Azure IoT hub, moet u de code compileren.

Volg de instructies in het **README.MD** -bestand in de map **sensorbox_app** om de code op Windows te bouwen en uit te voeren. De volgende sectie bevat instructies voor het ophalen van een apparaat connection string dat moet worden gebruikt wanneer u de apparaatcode uitvoert.

## <a name="test-the-code"></a>De code testen

Wanneer u de code uitvoert, maakt deze verbinding met IoT Hub en wordt het verzenden van voor beeld-telemetrie en eigenschaps waarden gestart. Het apparaat reageert ook op opdrachten die vanuit IoT Hub worden verzonden. U kunt dit gedrag als volgt controleren:

1. Een IoT Hub maken:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Voeg een apparaat toe aan uw IoT-hub en haal de connection string op:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Noteer de connection string.

1. Ga bij een opdracht prompt naar de map **Azure-IOT-SDK-c** waarin u de SDK en voor beelden hebt gemaakt. Ga vervolgens naar de **map\\cmake\\sensorbox_app release** .

1. Voer de volgende opdracht uit:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Gebruik het hulp programma Azure IoT Explorer om te communiceren met de IoT-Plug en Play apparaat dat is verbonden met uw IoT-hub. Zie voor meer informatie [Azure IOT Explorer installeren en gebruiken](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Plug en Play hebt gemaakt voor certificering, leert u het volgende:

> [!div class="nextstepaction"]
> [Een apparaat bouwen dat gereed is voor certificering](tutorial-build-device-certification.md)

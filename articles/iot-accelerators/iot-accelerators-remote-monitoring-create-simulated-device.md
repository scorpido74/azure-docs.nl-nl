---
title: Apparaatsimulatie met IoT-bewaking op afstand - Azure | Microsoft Documenten
description: Deze handleiding laat u zien hoe u de apparaatsimulator gebruiken met de versneller van de bewakingsoplossing op afstand.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt
ms.openlocfilehash: d31e520dac1c7e2a13fbd9e24a0cd3167f69e904
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682012"
---
# <a name="create-and-test-a-new-simulated-device"></a>Een nieuw gesimuleerd apparaat maken en testen

Met de Remote Monitoring-oplossingsversneller u uw eigen gesimuleerde apparaten definiëren. In dit artikel ziet u hoe u een nieuw gesimuleerd gloeilampapparaat definieert en vervolgens lokaal testen. De oplossingsversneller omvat gesimuleerde apparaten zoals koelmachines en vrachtwagens. U echter uw eigen gesimuleerde apparaten definiëren om uw IoT-oplossingen te testen voordat u echte apparaten implementeert.

> [!NOTE]
> In dit artikel wordt beschreven hoe gesimuleerde apparaten worden gebruikt die worden gehost in de apparaatsimulatieservice. Als u een echt apparaat wilt maken, [raadpleegt u Uw apparaat verbinden met de versneller van de oplossing voor externe bewaking.](iot-accelerators-connecting-devices.md)

Deze handleiding laat u zien hoe u de microservice voor apparaatsimulatie aanpassen. Deze microservice maakt deel uit van de Remote Monitoring oplossingsversneller. Om de mogelijkheden voor apparaatsimulatie weer te geven, gebruikt deze handleiding twee scenario's in de Contoso IoT-toepassing:

In het eerste scenario voegt u een nieuw telemetrietype toe aan het bestaande **Chiller-apparaattype van** Contoso.

In het tweede scenario wil Contoso een nieuw slim gloeilampapparaat testen. Als u de tests wilt uitvoeren, maakt u een nieuw gesimuleerd apparaat met de volgende kenmerken:

*Eigenschappen*

| Naam                     | Waarden                      |
| ------------------------ | --------------------------- |
| Kleur                    | Wit, Rood, Blauw            |
| Helderheid               | 0 tot 100                    |
| Geschatte resterende levensduur | Aftellen vanaf 10.000 uur |

*Telemetrie*

In de volgende tabel worden de gegevens weergegeven die de gloeilampen naar de cloud rapporteert als een gegevensstroom:

| Naam   | Waarden      |
| ------ | ----------- |
| Status | "aan", "off" |
| Temperatuur | Graden F |
| online | de waarde True, false |

> [!NOTE]
> De **online** telemetriewaarde is verplicht voor alle gesimuleerde typen.

*Methoden*

In de volgende tabel worden de acties weergegeven die het nieuwe apparaat ondersteunt:

| Naam        |
| ----------- |
| Inschakelen   |
| Schakel  |

*Initiële status*

In de volgende tabel ziet u de oorspronkelijke status van het apparaat:

| Naam                     | Waarden |
| ------------------------ | -------|
| Initiële kleur            | Wit  |
| Initiële helderheid       | 75     |
| Initiële resterende levensduur   | 10.000 |
| Initiële telemetriestatus | "op"   |
| Initiële telemetrietemperatuur | 200   |

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Om deze handleiding te volgen, moet u het volgende volgen:

* Visual Studio Code. U [Visual Studio Code voor Mac, Linux en Windows downloaden.](https://code.visualstudio.com/download)
* .NET Core. U [.NET Core voor Mac, Linux en Windows](https://www.microsoft.com/net/download)downloaden.
* [C# voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. U [Postman voor Mac, Windows of Linux](https://www.getpostman.com/apps)downloaden.
* Een [IoT-hub die is geïmplementeerd voor uw Azure-abonnement.](../../articles/iot-hub/iot-hub-create-through-portal.md) U hebt de verbindingstekenreeks van de IoT-hub nodig om de stappen in deze handleiding te voltooien. U de verbindingstekenreeks ophalen via de Azure-portal.
* Een Cosmos DB-database die de SQL-API gebruikt en die is geconfigureerd voor [een sterke consistentie.](../../articles/cosmos-db/how-to-manage-database-account.md) U hebt de verbindingstekenreeks van de Cosmos DB-database nodig om de stappen in deze handleiding uit te voeren. U de verbindingstekenreeks ophalen via de Azure-portal.

## <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Voltooi de volgende taken om uw ontwikkelomgeving voor te bereiden:

* Download de bron voor de microservice van apparaatsimulatie.
* Download de bron voor de microservice van de opslagadapter.
* Voer de microservice van de opslagadapter lokaal uit.

De instructies in dit artikel gaan ervan uit dat u Windows gebruikt. Als u een ander besturingssysteem gebruikt, moet u mogelijk een aantal bestandspaden en opdrachten aanpassen aan uw omgeving.

### <a name="download-the-microservices"></a>De microservices downloaden

Download en rits de [microservices voor externe bewaking](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) van GitHub uit naar een geschikte locatie op uw lokale machine. Het artikel gaat ervan uit dat de naam van deze map **remote-monitoring-services-dotnet-master**is.

Download en rits de microservice van de [apparaatsimulatie](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) van GitHub uit naar een geschikte locatie op uw lokale machine. Het artikel gaat ervan uit dat de naam van deze map **apparaatsimulatie-dotnet-master**is.

### <a name="run-the-storage-adapter-microservice"></a>De microservice van de opslagadapter uitvoeren

Open de map **remote-monitoring-services-dotnet-master\storage-adapter** in Visual Studio Code. Klik **op de knoppen Herstel** om onopgeloste afhankelijkheden op te lossen.

Open het **bestand storage-adapter/WebService/appsettings.ini** en wijs de Cosmos DB-verbindingstekenreeks toe aan de variabele **documentDBConnectionString.**

Als u de microservice lokaal wilt uitvoeren, klikt u op **Foutopsporing > Foutopsporing starten**.

Het **venster Terminal** in Visual Studio Code toont de uitvoer van de [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status)uitvoerende microservice, inclusief een URL voor de statuscontrole van de webservice: . Wanneer u naar dit adres navigeert, moet de status "OK: Levend en wel" zijn.

Laat de microservice van de opslagadapter draaien in dit geval van Visual Studio Code terwijl u de volgende stappen uitvoert.

## <a name="modify-the-chiller"></a>De koeler wijzigen

In deze sectie voegt u een nieuw telemetrietype **interne temperatuur** toe aan het bestaande **type Chiller-apparaat:**

1. Maak een nieuwe map **C:\temp\devicemodels** op uw lokale machine.

1. Kopieer de volgende bestanden naar uw nieuwe map uit de gedownloade kopie van de microservice voor apparaatsimulatie:

    | Bron | Doel |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Open het **c:\temp\devicemodels\chiller-01.json-bestand.**

1. Voeg in de sectie **InitialState** de volgende twee definities toe:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Voeg in de array **Telemetrie** de volgende definitie toe:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Sla het **c:\temp\devicemodels\chiller-01.json-bestand op.**

1. Open het **c:\temp\devicemodels\scripts\chiller-01-state.js-bestand.**

1. Voeg de volgende velden toe aan de **statusvariabele:**

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Werk de **hoofdfunctie** als volgt bij:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Sla het **c:\temp\devicemodels\scripts\chiller-01-state.js-bestand op.**

## <a name="create-the-lightbulb"></a>De gloeilamp maken

In deze sectie definieert u een nieuw **type lampapparaat:**

1. Maak een bestand **C:\temp\devicemodels\lightbulb-01.json** en voeg de volgende inhoud toe:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Sla de wijzigingen op in **C:\temp\devicemodels\lightbulb-01.json**.

1. Maak een bestand **C:\temp\devicemodels\scripts\lightbulb-01-state.js** en voeg de volgende inhoud toe:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Sla de wijzigingen op in **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Maak een bestand **C:\temp\devicemodels\scripts\SwitchOn-method.js** en voeg de volgende inhoud toe:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Sla de wijzigingen op in **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Maak een bestand **C:\temp\devicemodels\scripts\SwitchOff-method.js** en voeg de volgende inhoud toe:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Sla de wijzigingen op in **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

U hebt nu een aangepaste versie van het type **Chiller-apparaat** gemaakt en een nieuw **type gloeilampapparaat** gemaakt.

## <a name="test-the-devices"></a>De apparaten testen

In deze sectie test u de apparaattypen die u in de vorige secties lokaal hebt gemaakt.

### <a name="run-the-device-simulation-microservice"></a>De microservice voor apparaatsimulatie uitvoeren

Open de **apparaatsimulatie-dotnet-mastermap** die u van GitHub hebt gedownload in een nieuw exemplaar van Visual Studio Code. Klik **op de knoppen Herstel** om onopgeloste afhankelijkheden op te lossen.

Open het **bestand WebService/appsettings.ini** en wijs de Cosmos DB-verbindingstekenreeks toe aan de **documentdb_connstring** variabele en wijzig ook de instellingen als volgt:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Als u de microservice lokaal wilt uitvoeren, klikt u op **Foutopsporing > Foutopsporing starten**.

Het **venster Terminal** in Visual Studio Code toont de uitvoer van de hardloopmicroservice.

Laat de microservice voor apparaatsimulatie draaien in dit geval van Visual Studio Code terwijl u de volgende stappen uitvoert.

### <a name="set-up-a-monitor-for-device-events"></a>Een monitor instellen voor apparaatgebeurtenissen

In deze sectie gebruikt u de Azure CLI om een gebeurtenismonitor in te stellen om de telemetrie weer te geven die is verzonden vanaf de apparaten die zijn verbonden met uw IoT-hub.

Het volgende script gaat ervan uit dat de naam van uw **IoT-hub apparaatsimulatie-test**is.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Laat de gebeurtenismonitor draaien terwijl u de gesimuleerde apparaten test.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Een simulatie maken met het bijgewerkte type koelapparaat

In deze sectie gebruikt u het gereedschap Postman om de microservice voor apparaatsimulatie te vragen om een simulatie uit te voeren met behulp van het bijgewerkte type koelapparaat. Postman is een tool waarmee u REST-verzoeken naar een webservice sturen. De Postman configuratiebestanden die u nodig hebt, staan in uw lokale kopie van de **apparaatsimulatie-dotnet-repository.**

Postman instellen:

1. Open postbode op je lokale machine.

1. Klik **op Bestand > importeren**. Klik vervolgens op **Bestanden kiezen**.

1. Navigeer naar de **map device-simulation-dotnet-master/docs/postman.** Selecteer **Azure IoT Device Simulation solution accelerator.postman_collection** en **Azure IoT Device Simulation solution accelerator.postman_environment** en klik op **Openen**.

1. Vouw de **azure IoT Device Simulation-oplossingsversneller** uit naar de aanvragen die u verzenden.

1. Klik **op Geen omgeving** en selecteer Azure **IoT Device Simulation solution accelerator**.

U hebt nu een verzameling en omgeving geladen in uw Postman-werkruimte die u gebruiken om te communiceren met de microservice voor apparaatsimulatie.

Ga als het gaat om het configureren en uitvoeren van de simulatie:

1. Selecteer in de postmanverzameling de optie **Gewijzigde koelersimulatie maken** en klik op **Verzenden**. Met deze aanvraag worden vier exemplaren van het gesimuleerde type koelapparaat gebruikt.

1. De uitvoer van gebeurtenismonitor in het Azure CLI-venster toont de **internal_temperature** telemetrie van de gesimuleerde apparaten, inclusief de nieuwe internal_temperature-waarden.

Als u de simulatie wilt stoppen, selecteert u de **simulatieaanvraag stoppen** in Postman en klikt u op **Verzenden**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Een simulatie maken met het type gloeilampapparaat

In deze sectie gebruikt u de tool Postman om de microservice voor apparaatsimulatie te vragen om een simulatie uit te voeren met behulp van het type gloeilampapparaat. Postman is een tool waarmee u REST-verzoeken naar een webservice sturen.

Ga als het gaat om het configureren en uitvoeren van de simulatie:

1. Selecteer **gloeilampsimulatie maken** in de verzameling Postbode en klik op **Verzenden**. Met deze aanvraag worden twee exemplaren van het gesimuleerde type gloeilampapparaat ingeschakeld.

1. De uitvoer van gebeurtenismonitor in het Azure CLI-venster toont de telemetrie van de gesimuleerde gloeilampen.

Als u de simulatie wilt stoppen, selecteert u de **simulatieaanvraag stoppen** in Postman en klikt u op **Verzenden**.

## <a name="clean-up-resources"></a>Resources opschonen

U de twee lokaal draaiende microservices stoppen in hun visual studiocode-instanties **(Foutopsporing > Stoppen met foutopsporing).**

Als u de IoT Hub- en Cosmos DB-exemplaren niet meer nodig hebt, verwijdert u deze uit uw Azure-abonnement om onnodige kosten te voorkomen.

## <a name="next-steps"></a>Volgende stappen

Deze handleiding liet u zien hoe u een aangepaste gesimuleerde apparaattypen maken en deze testen door de microservice voor apparaatsimulatie lokaal uit te voeren.

De voorgestelde volgende stap is om te leren hoe u uw aangepaste gesimuleerde apparaattypen implementeert in de [remote monitoring oplossingsversneller.](iot-accelerators-remote-monitoring-deploy-simulated-device.md)

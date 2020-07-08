---
title: Simulatie van apparaten met IoT-externe controle-Azure | Microsoft Docs
description: In deze hand leiding wordt uitgelegd hoe u de Device Simulator kunt gebruiken met de oplossings versneller voor externe controle.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt
ms.openlocfilehash: d31e520dac1c7e2a13fbd9e24a0cd3167f69e904
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81682012"
---
# <a name="create-and-test-a-new-simulated-device"></a>Een nieuw gesimuleerd apparaat maken en testen

Met de oplossings versneller voor externe controle kunt u uw eigen gesimuleerde apparaten definiëren. Dit artikel laat u zien hoe u een nieuw gesimuleerd gloeilamp-apparaat definieert en dit vervolgens lokaal kunt testen. De oplossings versneller bevat gesimuleerde apparaten, zoals koelers en vracht wagens. U kunt echter uw eigen gesimuleerde apparaten definiëren om uw IoT-oplossingen te testen voordat u echte apparaten implementeert.

> [!NOTE]
> In dit artikel wordt beschreven hoe u gesimuleerde apparaten gebruikt die worden gehost in de Device simulatie service. Als u een echt apparaat wilt maken, raadpleegt u [uw apparaat aansluiten op de oplossings versneller voor externe controle](iot-accelerators-connecting-devices.md).

In deze hand leiding wordt uitgelegd hoe u de micro service Device simulatie kunt aanpassen. Deze micro service maakt deel uit van de oplossings versneller voor externe controle. Voor het weer geven van de functies voor simulatie van apparaten worden in deze hand leiding twee scenario's in de toepassing contoso IoT gebruikt:

In het eerste scenario voegt u een nieuw type telemetrie toe aan het bestaande **Chiller** -apparaattype van contoso.

In het tweede scenario wil Contoso een nieuw Smart gloeilamp-apparaat testen. Als u de tests wilt uitvoeren, maakt u een nieuw gesimuleerd apparaat met de volgende kenmerken:

*Eigenschappen*

| Name                     | Waarden                      |
| ------------------------ | --------------------------- |
| Kleur                    | Wit, rood, blauw            |
| Helderheid               | 0 tot 100                    |
| Geschatte resterende levens duur | Aftel tijd van 10.000 uur |

*Telemetrie*

In de volgende tabel ziet u de gegevens die de gloeilamp rapporteert aan de Cloud als een gegevens stroom:

| Name   | Waarden      |
| ------ | ----------- |
| Status | ' aan ', ' uit ' |
| Temperatuur | Graden F |
| online | de waarde True, false |

> [!NOTE]
> De **online** -telemetrie-waarde is verplicht voor alle gesimuleerde typen.

*Methoden*

In de volgende tabel ziet u de acties die het nieuwe apparaat ondersteunt:

| Name        |
| ----------- |
| Scha kelen   |
| Uitschakelen  |

*Begin status*

In de volgende tabel wordt de begin status van het apparaat weer gegeven:

| Name                     | Waarden |
| ------------------------ | -------|
| Eerste kleur            | Wit  |
| Eerste helderheid       | 75     |
| Eerste resterende levens duur   | 10.000 |
| Initiële telemetrie-status | waarop   |
| Initiële telemetrische Tempe ratuur | 200   |

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen, hebt u het volgende nodig:

* Visual Studio Code. U kunt [Visual Studio code voor Mac, Linux en Windows downloaden](https://code.visualstudio.com/download).
* .NET core. U kunt [.net core voor Mac, Linux en Windows](https://www.microsoft.com/net/download)downloaden.
* [C# voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. U kunt [postman downloaden voor Mac, Windows of Linux](https://www.getpostman.com/apps).
* Een [IOT-hub die is geïmplementeerd in uw Azure-abonnement](../../articles/iot-hub/iot-hub-create-through-portal.md). U hebt de connection string van de IoT-hub nodig om de stappen in deze hand leiding uit te voeren. U kunt de connection string ophalen via de Azure Portal.
* Een Cosmos DB-Data Base die gebruikmaakt van de SQL-API en die is geconfigureerd voor [sterke consistentie](../../articles/cosmos-db/how-to-manage-database-account.md). U hebt de connection string van de Cosmos DB-data base nodig om de stappen in deze hand leiding uit te voeren. U kunt de connection string ophalen via de Azure Portal.

## <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Voer de volgende taken uit om uw ontwikkel omgeving voor te bereiden:

* Down load de bron voor de micro service Device simulatie.
* Down load de bron voor de micro service Storage Adapter.
* Voer de micro service Storage Adapter lokaal uit.

In de instructies in dit artikel wordt ervan uitgegaan dat u Windows gebruikt. Als u een ander besturings systeem gebruikt, moet u mogelijk enkele bestands paden en opdrachten aanpassen aan uw omgeving.

### <a name="download-the-microservices"></a>De micro services downloaden

Down load en pak de [externe bewakings micro Services](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) van github naar een geschikte locatie op de lokale computer. In dit artikel wordt ervan uitgegaan dat de naam van deze map **extern wordt bewaakt-Services-DotNet-Master**.

Down load en pak de [device simulatie micro service](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) vanuit github naar een geschikte locatie op de lokale computer. In dit artikel wordt ervan uitgegaan dat de naam van deze map **device-simulatie-DotNet-Master**is.

### <a name="run-the-storage-adapter-microservice"></a>De micro service Storage Adapter uitvoeren

Open de map **Remote-Monitoring-Services-DotNet-master\storage-adapter** in Visual Studio code. Klik op alle **herstel** knoppen om eventuele onopgeloste afhankelijkheden op te lossen.

Open het bestand **Storage-Adapter/webservice/appsettings.ini** en wijs uw Cosmos DB connection string toe aan de **documentDBConnectionString** -variabele.

Als u de micro service lokaal wilt uitvoeren, klikt u op **fouten opsporen > fout opsporing starten**.

Het **Terminal** venster in Visual Studio code toont uitvoer van de actieve micro service, inclusief een URL voor de status controle van de webservice: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status) . Wanneer u naar dit adres navigeert, moet de status ' OK: Alive en well ' zijn.

Verlaat de micro service voor de opslag adapter die wordt uitgevoerd in dit exemplaar van Visual Studio code terwijl u de volgende stappen uitvoert.

## <a name="modify-the-chiller"></a>De chiller wijzigen

In deze sectie voegt u een nieuw type telemetrie voor **intern temperatuur** toe aan het bestaande **Chiller** -apparaattype:

1. Maak een nieuwe map **C:\temp\devicemodels** op uw lokale machine.

1. Kopieer de volgende bestanden naar de nieuwe map vanuit de gedownloade kopie van de micro service Device simulatie:

    | Bron | Doel |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.jsop | C:\temp\devicemodels\chiller-01.jsop |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Open de **C:\temp\devicemodels\chiller-01.jsin** het bestand.

1. Voeg in de sectie **InitialState** de volgende twee definities toe:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Voeg in de **telemetrie** -matrix de volgende definitie toe:

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

1. Sla de **C:\temp\devicemodels\chiller-01.jsop in** het bestand.

1. Open het **C:\temp\devicemodels\scripts\chiller-01-state.js** -bestand.

1. Voeg de volgende velden toe aan de variabele **status** :

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Werk de **hoofd** functie als volgt bij:

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

1. Sla het **C:\temp\devicemodels\scripts\chiller-01-state.js** bestand op.

## <a name="create-the-lightbulb"></a>De gloeilamp maken

In deze sectie definieert u een nieuw **gloeilamp** -apparaattype:

1. Maak een bestand **C:\temp\devicemodels\lightbulb-01.jsop** en voeg de volgende inhoud toe:

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

    Sla de wijzigingen in **C:\temp\devicemodels\lightbulb-01.jsop**.

1. Maak een bestands **C:\temp\devicemodels\scripts\lightbulb-01-state.js** en voeg de volgende inhoud toe:

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

    Sla de wijzigingen in **C:\temp\devicemodels\scripts\lightbulb-01-state.js**op.

1. Maak een bestands **C:\temp\devicemodels\scripts\SwitchOn-method.js** en voeg de volgende inhoud toe:

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

    Sla de wijzigingen in **C:\temp\devicemodels\scripts\SwitchOn-method.js**op.

1. Maak een bestands **C:\temp\devicemodels\scripts\SwitchOff-method.js** en voeg de volgende inhoud toe:

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

    Sla de wijzigingen in **C:\temp\devicemodels\scripts\SwitchOff-method.js**op.

U hebt nu een aangepaste versie van het **Chiller** -apparaattype gemaakt en een nieuw **gloeilamp** -apparaattype gemaakt.

## <a name="test-the-devices"></a>De apparaten testen

In deze sectie test u de apparaattypen die u hebt gemaakt in de vorige gedeelten lokaal.

### <a name="run-the-device-simulation-microservice"></a>De Device simulatie micro service uitvoeren

Open de map **device-simulatie-DotNet-Master** die u hebt gedownload van github in een nieuw exemplaar van Visual Studio code. Klik op alle **herstel** knoppen om eventuele onopgeloste afhankelijkheden op te lossen.

Open het bestand **WebService appsettings.ini** en wijs uw Cosmos DB connection string toe aan de variabele **documentdb_connstring** en wijzig de instellingen als volgt.

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Als u de micro service lokaal wilt uitvoeren, klikt u op **fouten opsporen > fout opsporing starten**.

In het **Terminal** venster in Visual Studio code wordt uitvoer van de actieve micro service weer gegeven.

Zorg ervoor dat de micro service Device simulatie niet wordt uitgevoerd in dit exemplaar van Visual Studio code terwijl u de volgende stappen uitvoert.

### <a name="set-up-a-monitor-for-device-events"></a>Een monitor instellen voor faxgebeurtenissen

In deze sectie gebruikt u de Azure CLI om een gebeurtenis controle in te stellen voor het weer geven van de telemetrie die is verzonden vanaf de apparaten die zijn verbonden met uw IoT-hub.

In het volgende script wordt ervan uitgegaan dat de naam van uw IoT hub **apparaat-simulatie-test**is.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Zorg ervoor dat de controle van gebeurtenissen actief blijft terwijl u de gesimuleerde apparaten test.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Een simulatie maken met het bijgewerkte Chiller-apparaattype

In deze sectie gebruikt u het hulp programma postman om de Device simulatie micro service aan te vragen om een simulatie uit te voeren met het bijgewerkte Chiller-apparaattype. Postman is een hulp programma waarmee u REST-aanvragen kunt verzenden naar een webservice. De Postman-configuratie bestanden die u nodig hebt, bevinden zich in uw lokale kopie van de ' **device-simulatie-DotNet-** opslag plaats.

Postman instellen:

1. Open postman op uw lokale machine.

1. Klik op **bestand > importeren**. Klik vervolgens op **bestanden kiezen**.

1. Navigeer naar de map **device-simulatie-DotNet-Master/docs/postman** . Selecteer **Azure IOT Device simulatie Solution Accelerator. postman_collection** en **Azure IOT Device simulatie solution Accelerator. postman_environment** en klik op **openen**.

1. Vouw de **Azure IOT Device simulatie-oplossings versneller** uit voor de aanvragen die u kunt verzenden.

1. Klik op **geen omgeving** en selecteer **Azure IOT Device simulatie Solution Accelerator**.

U hebt nu een verzameling en omgeving geladen in uw postman-werk ruimte die u kunt gebruiken om te communiceren met de micro service Device simulatie.

De simulatie configureren en uitvoeren:

1. Selecteer in de verzameling postman de optie **gewijzigde Chiller simulatie maken** en klik op **verzenden**. Met deze aanvraag worden vier exemplaren van het gesimuleerde Chiller-apparaattype gemaakt.

1. De gebeurtenis monitor uitvoer in het venster Azure CLI toont de telemetrie van de gesimuleerde apparaten, met inbegrip van de nieuwe **internal_temperature** waarden.

Als u de simulatie wilt stoppen, selecteert u de aanvraag voor het stoppen van de **simulatie** in postman en klikt u op **verzenden**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Een simulatie maken met het gloeilamp

In deze sectie gebruikt u het hulp programma postman om de Device simulatie micro service aan te vragen om een simulatie uit te voeren met behulp van het apparaattype van het type gloeilamp. Postman is een hulp programma waarmee u REST-aanvragen kunt verzenden naar een webservice.

De simulatie configureren en uitvoeren:

1. Selecteer in de verzameling postman de optie **gloeilamp simulatie maken** en klik op **verzenden**. Met deze aanvraag worden twee exemplaren van het gesimuleerde gloeilamp-apparaattype gemaakt.

1. De gebeurtenis monitor uitvoer in het Azure CLI-venster toont de telemetrie van de gesimuleerde Lightbulbs.

Als u de simulatie wilt stoppen, selecteert u de aanvraag voor het stoppen van de **simulatie** in postman en klikt u op **verzenden**.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de twee lokaal uitgevoerde micro Services in hun Visual Studio code-instanties stoppen (**fout opsporing > fout opsporing stoppen**).

Als u de IoT Hub en Cosmos DB instanties niet meer nodig hebt, verwijdert u deze uit uw Azure-abonnement om overbodige kosten te voor komen.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding wordt uitgelegd hoe u een aangepaste gesimuleerde apparaattypen maakt en deze test door de micro service Device simulatie lokaal uit te voeren.

De voorgestelde volgende stap is om te leren hoe u uw aangepaste gesimuleerde apparaattypen kunt implementeren naar de [oplossings versneller voor externe controle](iot-accelerators-remote-monitoring-deploy-simulated-device.md).

---
title: Zelfstudie - Een algemene client-app Node.js verbinden met Azure IoT Central | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u als apparaatontwikkelaar een apparaat met een Node.js-client-app verbinden met uw Azure IoT Central-toepassing. U maakt een apparaatsjabloon door een apparaatcapaciteitsmodel te importeren en weergaven toe te voegen waarmee u communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624541"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Zelfstudie: Een Clienttoepassing Node.js maken en verbinden met uw Azure IoT Central-toepassing (Node.js)

In deze zelfstudie ziet u hoe u als apparaatontwikkelaar een Clienttoepassing Node.js verbinden met uw Azure IoT Central-toepassing. De Node.js applicatie simuleert het gedrag van een echt apparaat. U gebruikt een model voor _voorbeeldapparaten_ voor een omgevingssensorom een _apparaatsjabloon_ te maken in IoT Central. U voegt weergaven toe aan de apparaatsjabloon om apparaattelemetrie te visualiseren, apparaateigenschappen te beheren en opdrachten te gebruiken om uw apparaten te bedienen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Importeer een apparaatcapaciteitsmodel om een apparaatsjabloon te maken.
> * Standaardweergaven en aangepaste weergaven toevoegen aan een apparaatsjabloon.
> * Publiceer een apparaatsjabloon en voeg een echt apparaat toe aan uw IoT Central-toepassing.
> * Maak en voer de node.js-apparaatcode uit en zie deze verbinding maken met uw IoT Central-toepassing.
> * Bekijk de gesimuleerde telemetrie die het apparaat verzendt.
> * Gebruik een weergave om apparaateigenschappen te beheren.
> * Oproepopdrachten om het apparaat te bedienen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing die is gemaakt met de sjabloon **Aangepaste toepassing **. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een ontwikkelmachine met [Node.js](https://nodejs.org/) versie 10.0.0 of hoger geïnstalleerd. U kunt `node --version` in de opdrachtregel worden uitgevoerd om uw versie te controleren. Node.js is beschikbaar voor een groot aantal verschillende besturingssystemen. De instructies in deze zelfstudie gaan ervan uit dat u de opdracht **knooppunt** uitvoert bij de opdrachtprompt van Windows. U Node.js gebruiken op verschillende besturingssystemen.

## <a name="create-a-device-template"></a>Een apparaatsjabloon maken

Maak een `environmental-sensor` map die op uw lokale machine wordt aangeroepen.

Download het [JSON-bestand met omgevingssensormogelijkheden](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) `environmental-sensor` en sla het op in de map.

Gebruik een teksteditor om de `{YOUR_COMPANY_NAME_HERE}` twee exemplaren van `EnvironmentalSensorInline.capabilitymodel.json` uw bedrijfsnaam te vervangen door uw bedrijfsnaam in het bestand dat u hebt gedownload.

Maak in uw Azure IoT Central-toepassing een apparaatsjabloon `EnvironmentalSensorInline.capabilitymodel.json` met de naam *Omgevingssensor* door het modelbestand voor apparaatmogelijkheden te importeren:

![Apparaatsjabloon met geïmporteerd apparaatcapaciteitsmodel](./media/tutorial-connect-device/device-template.png)

Het apparaatvermogenmodel bevat twee interfaces: de standaard **interface voor apparaatinformatie** en de aangepaste **interface voor omgevingssensoren.** De **interface omgevingssensor** definieert de volgende mogelijkheden:

| Type | Weergavenaam | Beschrijving |
| ---- | ------------ | ----------- |
| Eigenschap | Apparaatstatus     | De toestand van het apparaat. Twee staten online / offline zijn beschikbaar. |
| Eigenschap | Naam van de klant    | De naam van de klant die momenteel het apparaat gebruikt. |
| Eigenschap | Helderheidsniveau | Het helderheidsniveau voor het licht op het apparaat. Kan worden opgegeven als 1 (hoog), 2 (medium), 3 (laag). |
| Telemetrie | Temperatuur | Huidige temperatuur op het apparaat. |
| Telemetrie | Vochtigheid    | Huidige vochtigheid op het apparaat. |
| Opdracht | Knipperen          | Begin met het knipperen van de LED voor een bepaald tijdsinterval. |
| Opdracht | turnon         | Schakel het LED-lampje op het apparaat in. |
| Opdracht | Afslag        | Schakel het LED-lampje op het apparaat uit. |
| Opdracht | rundiagnostics | Met deze opdracht wordt een diagnostische run gestart. |

Als u wilt aanpassen hoe de eigenschap **Apparaatstatus** wordt weergegeven in uw IoT Central-toepassing, selecteert u **Aanpassen** in de apparaatsjabloon. Vouw de **vermelding apparaatstatus** uit, voer _Online_ in als **de echte naam** en _Offline_ als de **valse naam**. Sla vervolgens de wijzigingen op:

![De apparaatsjabloon aanpassen](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Weergaven maken

Met weergaven u communiceren met apparaten die zijn verbonden met uw IoT Central-toepassing. U bijvoorbeeld weergaven hebben die telemetrie weergeven, weergaven die eigenschappen weergeven en weergaven waarmee u schrijfbare en cloudeigenschappen bewerken. Weergaven maken deel uit van een apparaatsjabloon.

Als u een aantal standaardweergaven wilt toevoegen aan de apparaatsjabloon **voor de omgevingssensor,** navigeert u naar de apparaatsjabloon, selecteert u **Weergaven**en selecteert u de tegel **Standaardweergaven genereren.** Controleer of **Overzicht** en **Informatie** **aan**staan en selecteer **vervolgens Standaarddashboardweergaven genereren**. Er zijn nu twee standaardweergaven gedefinieerd in uw sjabloon.

De **interface omgevingssensor** bevat twee schrijfbare eigenschappen - **klantnaam** en **helderheidsniveau**. Als u een weergave wilt maken, u deze eigenschappen bewerken:

1. Selecteer **Weergaven** en selecteer vervolgens het bewerkingsapparaat en de **tegel cloudgegevens.**

1. Voer _Eigenschappen_ in als formuliernaam.

1. Selecteer de eigenschappen **Helderheidsniveau** en **klantnaam.** Selecteer vervolgens **Sectie Toevoegen**.

1. Sla uw wijzigingen op.

![Een weergave toevoegen om eigenschapsbewerking in te schakelen](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat toevoegt aan uw IoT Central-toepassing die gebruikmaakt van de sjabloon **omgevingssensorapparaat,** moet u de sjabloon publiceren.

Selecteer **Publiceren**in de apparaatsjabloon . Selecteer **Publiceren**in het deelvenster met de te publiceren wijzigingen .

Als u wilt controleren of de sjabloon klaar is voor gebruik, navigeert u naar de pagina **Apparaten** in uw IoT Central-toepassing. In de sectie **Apparaten** wordt een lijst weergegeven met de gepubliceerde apparaten in de toepassing:

![Gepubliceerde sjablonen op de pagina apparaten](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe aan de apparaatsjabloon die u in de vorige sectie hebt gemaakt:

1. Selecteer **op** de pagina Apparaten de sjabloon **omgevingssensor.**

1. Selecteer **+ Nieuw**.

1. Zorg ervoor dat **gesimuleerd** is **uitgeschakeld.** Selecteer vervolgens **Maken**.

Klik op de naam van het apparaat en selecteer **Verbinding maken**. Noteer de apparaatverbindingsgegevens op de pagina **Apparaatverbinding** - **ID-bereik,** **apparaat-id**en **primaire sleutel**. U hebt deze waarden nodig wanneer u uw apparaatcode maakt:

![Apparaatverbindingsgegevens](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

In de volgende stappen ziet u hoe u een Clienttoepassing Node.js maakt die het echte apparaat implementeert dat u aan de toepassing hebt toegevoegd. Deze Node.js applicatie simuleert het gedrag van een echt apparaat.

1. Navigeer in de opdrachtregelomgeving `environmental-sensor` naar de map die u eerder hebt gemaakt.

1. Als u uw Node.js-project wilt initialiseren en de vereiste afhankelijkheden wilt installeren, `npm init`voert u de volgende opdrachten uit - accepteert u alle standaardopties wanneer u dit uitvoert:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Maak een bestand met de `environmental-sensor` naam **environmentalSensor.js** in de map.

1. Voeg de `require` volgende instructies toe aan het begin van het **bestand environmentalSensor.js:**

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Voeg de volgende variabelendeclaraties aan het bestand toe:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    var ledOn = true;
    ```

    Werk de `{your Scope ID}`tijdelijke `{your Device ID}`aanduidingen en `{your Primary Key}` met de waarden waar u eerder een notitie van hebt gemaakt. In dit voorbeeld `targetTemperature` u tot nul initialiseren, u de huidige meting van het apparaat of een waarde van de apparaattweeling gebruiken.

1. Als u telemetrie naar uw Azure IoT Central-toepassing wilt verzenden, voegt u de volgende functie toe aan het bestand:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Als u apparaateigenschappen naar uw Azure IoT Central-toepassing wilt verzenden, voegt u de volgende functie toe aan uw bestand:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Als u de schrijfbare eigenschappen wilt definiëren en verwerken waarop uw apparaat reageert, voegt u de volgende code toe:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Voeg de volgende code toe om de opdrachten te verwerken die vanuit de IoT Central-toepassing worden verzonden:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
    function turnOn(request, response) {
      console.log('Received synchronous call to turn on LED');
      if(!ledOn){
        console.log('Turning on the LED');
        ledOn = true;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle LED turn off command
    function turnOff(request, response) {
      console.log('Received synchronous call to turn off LED');
      if(ledOn){
        console.log('Turning off the LED');
        ledOn = false;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. Voeg de volgende code toe voor het voltooien van de verbinding met Azure IoT Central en het aansluiten van de functies in de clientcode:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>De Node.js-toepassing uitvoeren

Als u de apparaatclienttoepassing wilt starten, voert u de volgende opdracht uit in de opdrachtregelomgeving:

```cmd/sh
node environmentalSensor.js
```

U zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en telemetrie gaat verzenden:

![De clienttoepassing uitvoeren](media/tutorial-connect-device/run-application.png)

Als operator in uw Azure IoT Central-toepassing u:

* Bekijk de telemetrie die door het apparaat wordt verzonden op de **overzichtspagina:**

    ![Telemetrie bekijken](media/tutorial-connect-device/view-telemetry.png)

* Schrijfbare eigenschapswaarden bijwerken op de pagina **Eigenschappen:**

    ![Eigenschappen bijwerken](media/tutorial-connect-device/update-properties.png)

    ![Apparaat eigenschappen bijwerken](media/tutorial-connect-device/update-properties-device.png)

* Roep de opdrachten aan op de pagina **Opdrachten:**

    ![Oproep knipperen, opdracht](media/tutorial-connect-device/call-command.png)

    ![Opdrachtapparaat voor knipperen bellen](media/tutorial-connect-device/call-command-device.png)

* Bekijk de apparaateigenschappen op de pagina **Info:**

    ![Eigenschappen weergeven](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over apparaatcapaciteitsmodellen en het maken van uw eigen apparaatsjablonen verder naar de handleiding:

> [!div class="nextstepaction"]
> [Een nieuw IoT-apparaattype definiëren](./howto-set-up-template.md)

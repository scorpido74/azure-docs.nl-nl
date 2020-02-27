---
title: 'Zelf studie: een algemene node. js-client-app verbinden met Azure IoT Central | Microsoft Docs'
description: In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat een apparaat met een node. js-client-app verbindt met uw Azure IoT Central-toepassing. U maakt een sjabloon voor een apparaat door een mogelijkheidsprofiel te importeren en weer gaven toe te voegen waarmee u kunt communiceren met een verbonden apparaat
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624541"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Zelf studie: een node. js-client toepassing maken en verbinden met uw Azure IoT Central-toepassing (node. js)

In deze zelf studie wordt uitgelegd hoe u als een ontwikkelaar van een apparaat een node. js-client toepassing verbindt met uw Azure IoT Central-toepassing. Met de node. js-toepassing wordt het gedrag van een echt apparaat gesimuleerd. U gebruikt een voor beeld van een _apparaat_ voor het maken van een _sjabloon_ voor een omgevings sensor in IOT Central. U kunt weer gaven toevoegen aan de sjabloon voor het apparaat om telemetrie van apparaten te visualiseren, apparaateigenschappen te beheren en opdrachten te gebruiken voor het beheren van uw apparaten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Importeer een mogelijkheidsprofiel om een sjabloon voor een apparaat te maken.
> * Standaard-en aangepaste weer gaven toevoegen aan een sjabloon voor een apparaat.
> * Een sjabloon voor een apparaat publiceren en een echt apparaat toevoegen aan uw IoT Central-toepassing.
> * Maak de apparaatcode van node. js en voer deze uit om verbinding te maken met uw IoT Central-toepassing.
> * De gesimuleerde telemetrie weer geven die het apparaat verzendt.
> * Gebruik een weer gave om apparaateigenschappen te beheren.
> * Roep opdrachten aan om het apparaat te beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met de sjabloon * * aangepaste toepassing * *. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Een ontwikkelings machine waarop de [node. js](https://nodejs.org/) -versie 10.0.0 of hoger is geïnstalleerd. U kunt `node --version` uitvoeren op de opdracht regel om uw versie te controleren. Node.js is beschikbaar voor een groot aantal verschillende besturingssystemen. In de instructies in deze zelf studie wordt ervan uitgegaan dat u de **knoop punt** opdracht uitvoert vanaf de Windows-opdracht prompt. U kunt node. js gebruiken op verschillende besturings systemen.

## <a name="create-a-device-template"></a>Een sjabloon voor een apparaat maken

Maak een map met de naam `environmental-sensor` op uw lokale machine.

Down load het JSON-bestand van het [Environmental Capability model](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) en sla dit op in de map `environmental-sensor`.

Gebruik een tekst editor om de twee exemplaren van `{YOUR_COMPANY_NAME_HERE}` te vervangen door de naam van uw bedrijf in het `EnvironmentalSensorInline.capabilitymodel.json` bestand dat u hebt gedownload.

Maak in uw Azure IoT Central-toepassing een Device-sjabloon met de naam *omgevings sensor* door het bestand met het capaciteits model van `EnvironmentalSensorInline.capabilitymodel.json` te importeren:

![De sjabloon apparaat met het model voor het importeren van het apparaat](./media/tutorial-connect-device/device-template.png)

Het functionaliteits model van het apparaat bevat twee interfaces: de standaard interface voor **apparaatgegevens** en de interface voor aangepaste **omgevings sensor** . De **omgevings sensor** interface definieert de volgende mogelijkheden:

| Type | Weergavenaam | Beschrijving |
| ---- | ------------ | ----------- |
| Eigenschap | Apparaatstatus     | De status van het apparaat. Er zijn twee statussen online/offline beschikbaar. |
| Eigenschap | Klant naam    | De naam van de klant die momenteel op het apparaat wordt uitgevoerd. |
| Eigenschap | Helderheids niveau | Het helderheids niveau voor het licht op het apparaat. Kan worden opgegeven als 1 (hoog), 2 (gemiddeld), 3 (laag). |
| Telemetrie | Temperatuur | Huidige Tempe ratuur op het apparaat. |
| Telemetrie | Vochtigheid    | Huidige vochtigheid op het apparaat. |
| Opdracht | gaat          | De LED voor het opgegeven tijds interval laten knip peren. |
| Opdracht | turnon         | Schakel het LED-lampje op het apparaat in. |
| Opdracht | turnoff        | Schakel het LED-lampje op het apparaat uit. |
| Opdracht | rundiagnostics | Met deze opdracht wordt een diagnostische uitvoering gestart. |

Als u wilt aanpassen hoe de eigenschap **Apparaatstatus** wordt weer gegeven in uw IOT Central-toepassing, selecteert u **aanpassen** in de sjabloon voor het apparaat. Vouw de vermelding voor de **apparaats status** uit en voer _online_ in als de **werkelijke naam** en _offline_ als de **naam onwaar**. Sla de wijzigingen vervolgens op:

![De sjabloon voor het apparaat aanpassen](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Weergaven aanmaken

Met weer gaven kunt u communiceren met apparaten die zijn verbonden met uw IoT Central-toepassing. U kunt bijvoorbeeld weer gaven hebben waarin telemetrie wordt weer gegeven, weer gaven die eigenschappen weer geven en weer gaven waarmee u Beschrijf bare en Cloud eigenschappen kunt bewerken. Weer gaven maken deel uit van een sjabloon voor een apparaat.

Als u een aantal standaard weergaven wilt toevoegen aan de sjabloon **omgevings sensor** apparaat, gaat u naar de sjabloon voor het apparaat, selecteert u **weer gaven**en selecteert u de tegel **Standaard weergaven genereren** . Zorg ervoor dat **overzicht** en **over** zijn **ingeschakeld**en selecteer **standaard dashboard weergaven genereren**. U hebt nu twee standaard weergaven gedefinieerd in uw sjabloon.

De **omgevings sensor** interface bevat twee Beschrijf bare eigenschappen: **klant naam** en **helderheids niveau**. Als u een weer gave wilt maken, kunt u deze gebruiken om deze eigenschappen te bewerken:

1. Selecteer **weer gaven** en selecteer vervolgens de tegel **apparaat en Cloud gegevens bewerken** .

1. Geef _Eigenschappen_ op als formulier naam.

1. Selecteer de eigenschappen van het **niveau helderheid** en de naam van de **klant** . Selecteer vervolgens **sectie toevoegen**.

1. Sla uw wijzigingen op.

![Een weer gave toevoegen om het bewerken van eigenschappen in te scha kelen](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat toevoegt aan uw IoT Central-toepassing die gebruikmaakt van de sjabloon voor de **omgevings sensor** , moet u de sjabloon publiceren.

Selecteer in de sjabloon voor het apparaat **publiceren**. Selecteer in het deel venster waarin de wijzigingen worden weer gegeven die moeten worden gepubliceerd, de optie **publiceren**.

Als u wilt controleren of de sjabloon klaar is voor gebruik, gaat u naar de pagina **apparaten** in uw IOT Central-toepassing. De sectie **apparaten** bevat een lijst van de gepubliceerde apparaten in de toepassing:

![Gepubliceerde sjablonen op de pagina apparaten](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe aan de Device-sjabloon die u in de vorige sectie hebt gemaakt:

1. Selecteer op de pagina **apparaten** de sjabloon **omgevings sensor** .

1. Selecteer **+ Nieuw**.

1. Zorg ervoor dat **gesimuleerd** is **uitgeschakeld**. Selecteer vervolgens **Maken**.

Klik op de naam van het apparaat en selecteer vervolgens **verbinding maken**. Noteer de verbindings gegevens van het apparaat op de pagina met de verbindings- **id**van het **apparaat** , de **apparaat-id**en de **primaire sleutel**. U hebt deze waarden nodig wanneer u uw apparaatcode maakt:

![Verbindings gegevens apparaat](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

De volgende stappen laten zien hoe u een node. js-client toepassing maakt die het echte apparaat implementeert dat u aan de toepassing hebt toegevoegd. Met deze node. js-toepassing wordt het gedrag van een echt apparaat gesimuleerd.

1. Ga in de opdracht regel omgeving naar de map `environmental-sensor` die u eerder hebt gemaakt.

1. Als u het node. js-project wilt initialiseren en de vereiste afhankelijkheden wilt installeren, voert u de volgende opdrachten uit: accepteer alle standaard opties wanneer u `npm init`uitvoert.

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Maak een bestand met de naam **environmentalSensor. js** in de map `environmental-sensor`.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **environmentalSensor. js** :

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

    De tijdelijke aanduidingen `{your Scope ID}`, `{your Device ID}`en `{your Primary Key}` bijwerken met de waarden die u eerder hebt genoteerd. In dit voor beeld initialiseert u `targetTemperature` op nul, kunt u de huidige lees bewerking van het apparaat of een waarde van het apparaat dubbele gebruiken.

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

1. Als u apparaateigenschappen wilt verzenden naar uw Azure IoT Central-toepassing, voegt u de volgende functie toe aan het bestand:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Voeg de volgende code toe om de Beschrijf bare eigenschappen te definiëren en te verwerken waarop uw apparaat reageert:

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

1. Voeg de volgende code toe om de opdrachten te verwerken die zijn verzonden vanuit de IoT Central-toepassing:

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

## <a name="run-your-nodejs-application"></a>Uw node. js-toepassing uitvoeren

Voer de volgende opdracht uit in de opdracht regel omgeving om de client toepassing van het apparaat te starten:

```cmd/sh
node environmentalSensor.js
```

U kunt zien dat het apparaat verbinding maakt met uw Azure IoT Central-toepassing en dat telemetrie wordt verzonden:

![De client toepassing uitvoeren](media/tutorial-connect-device/run-application.png)

Als operator in uw Azure IoT Central-toepassing kunt u het volgende doen:

* De telemetrie weer geven die is verzonden door het apparaat op de pagina **overzicht** :

    ![Telemetrie bekijken](media/tutorial-connect-device/view-telemetry.png)

* Schrijf bare eigenschaps waarden bijwerken op de pagina **Eigenschappen** :

    ![Eigenschappen bijwerken](media/tutorial-connect-device/update-properties.png)

    ![Apparaat voor update-eigenschappen](media/tutorial-connect-device/update-properties-device.png)

* Roep de opdrachten op de pagina **opdrachten** :

    ![Opdracht Blink aanroepen](media/tutorial-connect-device/call-command.png)

    ![Apparaat voor knipperend opdracht gesprek](media/tutorial-connect-device/call-command-device.png)

* De apparaateigenschappen weer geven op de pagina **over** :

    ![Eigenschappen weergeven](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over apparaatfuncties en het maken van uw eigen Device-sjablonen gaat u verder met de hand leiding:

> [!div class="nextstepaction"]
> [Een nieuw IoT-apparaattype definiëren](./howto-set-up-template.md)

---
title: Een algemene node. js-client-app verbinden met Azure IoT Central | Microsoft Docs
description: Als ontwikkelaar van een apparaat kunt u een algemeen knoop punt. js-apparaat verbinden met uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1ddb947e9bcbdde655b5c83cffb67e8fc13d9d0e
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048682"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Een algemene client toepassing verbinden met uw Azure IoT Central-toepassing (node. js)

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een ontwikkelaar van een apparaat verbinding maakt met een algemene node. js-toepassing die een echt apparaat vertegenwoordigt aan uw Microsoft Azure IoT Central toepassing.

## <a name="before-you-begin"></a>Voordat u begint

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

- Een Azure IoT Central-toepassing. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
- Een ontwikkelings machine waarop de [node. js](https://nodejs.org/) -versie 4.0.0 of hoger is geïnstalleerd. U kunt `node --version` uitvoeren op de opdracht regel om uw versie te controleren. Node.js is beschikbaar voor een groot aantal verschillende besturingssystemen.

## <a name="create-a-device-template"></a>Een sjabloon voor een apparaat maken

In uw Azure IoT Central-toepassing hebt u een sjabloon nodig met de volgende metingen, apparaateigenschappen, instellingen en opdrachten.

Zie voor meer informatie over geldige eigenschapnamen de [indeling Tags en eigenschappen](../../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

### <a name="telemetry-measurements"></a>Telemetrische metingen

Voeg de volgende telemetrie toe op de pagina **metingen** :

| Weergavenaam | Veldnaam  | Eenheden | Min. | Max. | Aantal decimalen |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatuur  | temperatuur | F     | 60  | 110 | 0              |
| Vochtigheid     | vochtigheid    | %     | 0   | 100 | 0              |
| Tegen     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Het gegevens type van de telemetrie-meting is een getal met drijvende komma.

Voer de veld namen precies in zoals weer gegeven in de tabel in de sjabloon voor het apparaat. Als de veld namen niet overeenkomen met de naam van de eigenschap in de bijbehorende apparaatcode, kan de telemetrie niet worden weer gegeven in de toepassing.

### <a name="state-measurements"></a>Status metingen

Voeg de volgende status toe op de pagina **metingen** :

| Weergavenaam | Veldnaam  | Waarde 1 | Weergavenaam | Waarde 2 | Weergavenaam |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Ventilatormodus     | ventilatormodus     | 1       | In uitvoering      | 0       | Gestopt      |

> [!NOTE]
> Het gegevens type van de status meting is teken reeks.

Voer de veld namen precies in zoals weer gegeven in de tabel in de sjabloon voor het apparaat. Als de veld namen niet overeenkomen met de naam van de eigenschap in de bijbehorende apparaatcode, kan de status niet worden weer gegeven in de toepassing.

### <a name="event-measurements"></a>Gebeurtenis metingen

Voeg de volgende gebeurtenis toe op de pagina **metingen** :

| Weergavenaam | Veldnaam  | Severity |
| ------------ | ----------- | -------- |
| Oververhitting  | overheat    | Fout    |

> [!NOTE]
> Het gegevens type van de gebeurtenis meting is teken reeks.

### <a name="location-measurements"></a>Locatie metingen

Voeg de volgende locatie meting toe op de pagina **metingen** :

| Weergavenaam | Veldnaam  |
| ------------ | ----------- |
| Locatie     | locatie    |

Het gegevens type van de locatie meting bestaat uit twee drijvende-komma nummers voor lengte graad en breedte graad, en een optioneel drijvende-komma nummer voor hoogte.

Voer de veld namen precies in zoals weer gegeven in de tabel in de sjabloon voor het apparaat. Als de veld namen niet overeenkomen met de naam van de eigenschap in de bijbehorende apparaatcode, kan de locatie niet worden weer gegeven in de toepassing.

### <a name="device-properties"></a>Apparaateigenschappen

Voeg de volgende apparaateigenschappen toe op de pagina **Eigenschappen** :

| Weergavenaam        | Veldnaam        | Gegevenstype |
| ------------------- | ----------------- | --------- |
| Serienummer       | Serienummer      | tekst      |
| Fabrikant van apparaat | Fabricage      | tekst      |

Voer de veld namen precies in zoals in de tabel in de sjabloon voor het apparaat wordt weer gegeven. Als de veld namen niet overeenkomen met de naam van de eigenschap in de bijbehorende apparaatcode, kunnen de eigenschappen niet worden weer gegeven in de toepassing.

### <a name="settings"></a>Instellingen

Voeg de volgende **nummer** instellingen toe op de pagina **instellingen** :

| Weergavenaam    | Veldnaam     | Eenheden | decimalen | Min. | Max.  | Itiaal |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Snelheid van ventilator       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Temperatuur instellen | temperatuurInstellen | F     | 0        | 20  | 200  | 80      |

Voer de veld naam precies in zoals weer gegeven in de tabel in de sjabloon voor het apparaat. Als de veld namen niet overeenkomen met de naam van de eigenschap in de bijbehorende apparaatcode, kan het apparaat de waarde van de instelling niet ontvangen.

### <a name="commands"></a>Opdrachten

Voeg de volgende opdracht toe op de pagina **opdrachten** :

| Weergavenaam    | Veldnaam     | Standaardtime-out | Gegevenstype |
| --------------- | -------------- | --------------- | --------- |
| Geteld       | Geteld      | 30              | getal    |

Voeg het volgende invoer veld toe aan de opdracht Aftel tijd:

| Weergavenaam    | Veldnaam     | Gegevenstype | Waarde |
| --------------- | -------------- | --------- | ----- |
| Aantal van      | countFrom      | getal    | 10    |

Voer de veld namen precies in zoals weer gegeven in de tabellen in de sjabloon voor het apparaat. Als de veld namen niet overeenkomen met de naam van de eigenschap in de bijbehorende apparaatcode, kan het apparaat de opdracht niet verwerken.

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe aan de Device-sjabloon die u in de vorige sectie hebt gemaakt.

Noteer de verbindings gegevens van het apparaat op de pagina **apparaat-verbinding** : **Scope-ID**, **apparaat-id**en **primaire sleutel**. U voegt deze waarden later in deze hand leiding toe aan de apparaatcode:

![Verbindings gegevens apparaat](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Een Node.js-toepassing maken

De volgende stappen laten zien hoe u een client toepassing kunt maken die het werkelijke apparaat implementeert dat u aan de toepassing hebt toegevoegd. Hier geeft de node. js-toepassing het echte apparaat aan.

1. Maak de map `connected-air-conditioner-adv` op uw computer. Navigeer naar de map in uw opdracht regel omgeving.

1. Voer de volgende opdrachten uit om het node. js-project te initialiseren:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Maak een bestand met de naam **connectedAirConditionerAdv. js** in de map `connected-air-conditioner-adv`.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **connectedAirConditionerAdv. js** :

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    De tijdelijke aanduidingen `{your Scope ID}`, `{your Device ID}`en `{your Primary Key}` bijwerken met de waarden die u eerder hebt genoteerd. In dit voor beeld initialiseert u `targetTemperature` op nul, kunt u de huidige lees bewerking van het apparaat of een waarde van het apparaat dubbele gebruiken.

1. Als u een telemetrie-, status-, gebeurtenis-en locatie meting naar uw Azure IoT Central-toepassing wilt verzenden, voegt u de volgende functie toe aan het bestand:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
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

1. Als u de instellingen wilt definiëren waarop uw apparaat reageert, voegt u de volgende definitie toe:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Als u de bijgewerkte instellingen van uw Azure IoT Central-toepassing wilt verwerken, voegt u het volgende toe aan het bestand:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
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
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Voeg de volgende code toe voor het afhandelen van een Aftel opdracht die wordt verzonden vanuit de IoT Central-toepassing:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
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

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
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

Voer de volgende opdracht uit in de opdracht regel omgeving:

```cmd/sh
node connectedAirConditionerAdv.js
```

Als operator in uw Azure IoT Central-toepassing kunt u voor uw echte apparaat het volgende doen:

* Bekijk de telemetrie op de pagina **metingen** :

    ![Telemetrie bekijken](media/howto-connect-nodejs/viewtelemetry.png)

* Bekijk de locatie op de pagina **metingen** :

    ![Locatie metingen weer geven](media/howto-connect-nodejs/viewlocation.png)

* De waarden van de eigenschappen van uw apparaat weer geven op de pagina **Eigenschappen** . De tegels van de apparaateigenschappen worden bijgewerkt wanneer het apparaat verbinding maakt:

    ![Apparaateigenschappen weer geven](media/howto-connect-nodejs/viewproperties.png)

* Stel de snelheid van de ventilator en doel temperatuur in op de pagina **instellingen** :

    ![Ventilator snelheid instellen](media/howto-connect-nodejs/setfanspeed.png)

* Roep de aftel opdracht aan op de pagina **opdrachten** :

    ![Opdracht voor aftellen van oproep](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een algemene node. js-client verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een sjabloon voor aangepaste apparaten](howto-set-up-template.md) voor uw eigen IOT-apparaat.

---
title: Aan de slag met Azure IoT Hub-apparaattweeling (knooppunt) | Microsoft Documenten
description: Azure IoT Hub-apparaattweelingen gebruiken om tags toe te voegen en vervolgens een IoT Hub-query te gebruiken. U gebruikt de Azure IoT-SDK's voor Node.js om de gesimuleerde apparaat-app en een service-app te implementeren die de tags toevoegt en de IoT Hub-query uitvoert.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.openlocfilehash: 55dc7f73a3e5bbff2e6e331ba0bd7d4088a86536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110827"
---
# <a name="get-started-with-device-twins-nodejs"></a>Aan de slag met apparaattweelingen (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Aan het einde van deze zelfstudie heb je twee Node.js-console-apps:

* **AddTagsAndQuery.js**, een Node.js back-end app, die tags en query's apparaat tweelingen toevoegt.

* **TwinSimulatedDevice.js**, een Node.js-app, die een apparaat simuleert dat verbinding maakt met uw IoT-hub met de apparaatidentiteit die eerder is gemaakt, en rapporteert de verbindingsconditie.

> [!NOTE]
> Het artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) bevat informatie over de Azure IoT SDK's die u gebruiken om zowel apparaat- als back-end-apps te bouwen.
>

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Node.js versie 10.0.x of hoger.

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>De service-app maken

In deze sectie maakt u een Node.js-console-app die locatiemetagegevens toevoegt aan de apparaattweeling die is gekoppeld aan **myDeviceId.** Vervolgens wordt het apparaat twins opgevraagd die zijn opgeslagen in de IoT-hub en selecteert u de apparaten in de VS en vervolgens de apparaten die een mobiele verbinding melden.

1. Maak een nieuwe lege map genaamd **addtagsandqueryapp**. Maak in de map **addtagsandqueryapp** een nieuw package.json-bestand met de volgende opdracht bij de opdrachtprompt. De `--yes` parameter accepteert alle standaardinstellingen.

    ```cmd/sh
    npm init --yes
    ```

2. Voer bij de opdrachtprompt in de map **addtagsandqueryapp** de volgende opdracht uit om het **azure-iothub-pakket** te installeren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Maak met behulp van een teksteditor een nieuw **addtagsandquery.js-bestand** in de map **addtagsandqueryapp.**

4. Voeg de volgende code toe aan het bestand **AddTagsAndQuery.js.** Vervang `{iot hub connection string}` de IE-verbindingstekenreeks van de IoT-hub die u hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub](#get-the-iot-hub-connection-string).

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    Het **object Register** onthult alle methoden die nodig zijn om te communiceren met apparaattweelingen van de service. De vorige code initialiseert eerst het **object Registry,** haalt vervolgens de apparaattweeling voor **myDeviceId**op en werkt de tags ten slotte bij met de gewenste locatie-informatie.

    Na het bijwerken van de tags wordt de functie **queryTwins** aanroept.

5. Voeg de volgende code toe aan het einde van **AddTagsAndQuery.js** om de functie **queryTwins** te implementeren:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    De vorige code voert twee query's uit: de eerste selecteert alleen de apparaattweeling van apparaten in de **Redmond43-fabriek** en de tweede verfijnt de query om alleen de apparaten te selecteren die ook via het mobiele netwerk zijn verbonden.

    Wanneer de code het **queryobject** maakt, geeft deze het maximumaantal geretourneerde documenten in de tweede parameter op. Het **queryobject** bevat een booleaanse eigenschap **hasMoreResults** die u gebruiken om de **nextAsTwin-methoden** meerdere keren aan te roepen om alle resultaten op te halen. Een methode die **de volgende methode** wordt genoemd, is beschikbaar voor resultaten die geen apparaattweeling zijn, bijvoorbeeld de resultaten van aggregatiequery's.

6. Voer de toepassing uit met:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   U ziet één apparaat in de resultaten voor de query waarin wordt gevraagd naar alle apparaten in **Redmond43** en geen voor de query die de resultaten beperkt tot apparaten die een mobiel netwerk gebruiken.

   ![Zie het ene apparaat in de queryresultaten](media/iot-hub-node-node-twin-getstarted/service1.png)

In de volgende sectie maakt u een apparaat-app die de verbindingsinformatie rapporteert en het resultaat van de query in de vorige sectie wijzigt.

## <a name="create-the-device-app"></a>De apparaat-app maken

In deze sectie maakt u een Node.js-console-app die verbinding maakt met uw hub als **myDeviceId**en vervolgens de gerapporteerde eigenschappen van de apparaattweeling bijwerkt om de informatie te bevatten die is verbonden met een mobiel netwerk.

1. Een nieuwe lege map maken met de naam **reportconnectivity**. Maak in de map **reportconnectivity** een nieuw package.json-bestand met de volgende opdracht bij de opdrachtprompt. De `--yes` parameter accepteert alle standaardinstellingen.

    ```cmd/sh
    npm init --yes
    ```

2. Voer bij de opdrachtprompt in de map **reportconnectivity** de volgende opdracht uit om het **azure-iot-apparaat**en **azure-iot-device-mqtt-pakketten** te installeren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Maak met behulp van een teksteditor een nieuw **ReportConnectivity.js-bestand** in de map **reportconnectivity.**

4. Voeg de volgende code toe aan het bestand **ReportConnectivity.js.** Vervang `{device connection string}` de tekenreeks voor apparaatverbinding die u hebt gekopieerd toen u de identiteit van het **myDeviceId-apparaat** hebt gemaakt in [Een nieuw apparaat registreren in de IoT-hub](#register-a-new-device-in-the-iot-hub).

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    Het **clientobject** onthult alle methoden die u nodig hebt om vanaf het apparaat met apparaattweelingen te communiceren. De vorige code, nadat het **clientobject** is geïnitialiseerde, haalt de apparaattweeling voor **myDeviceId** op en werkt de gerapporteerde eigenschap bij met de verbindingsinformatie.

5. De apparaat-app uitvoeren

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Als het goed is, ziet u nu het bericht `twin state reported`.

6. Nu het apparaat zijn connectiviteitsinformatie heeft gerapporteerd, moet het in beide query's worden weergegeven. Ga terug in de map **addtagsandqueryapp** en voer de query's opnieuw uit:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Deze keer **moet myDeviceId** worden weergegeven in beide queryresultaten.

    ![MyDeviceId weergeven in beide queryresultaten](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u een nieuwe IoT-hub geconfigureerd in Azure Portal en vervolgens een apparaat-id gemaakt in het id-register van de IoT-hub. U hebt apparaatmetagegevens toegevoegd als tags van een back-end-app en een gesimuleerde apparaat-app geschreven om informatie over apparaatconnectiviteit in de apparaattweeling te rapporteren. U hebt ook geleerd hoe u deze informatie opvragen met de SQL-achtige IoT Hub-querytaal.

Gebruik de volgende bronnen om te leren hoe u:

* telemetrie verzenden vanaf apparaten met de [zelfstudie van IoT Hub,](quickstart-send-telemetry-node.md)

* apparaten configureren met behulp van de gewenste eigenschappen van device twin met de gewenste eigenschappen gebruiken om de zelfstudie [van apparaten te configureren,](tutorial-device-twins.md)

* apparaten interactief bedienen (zoals het inschakelen van een ventilator vanuit een door de gebruiker bestuurde app), met de zelfstudie [Direct methoden](quickstart-control-device-node.md) gebruiken.

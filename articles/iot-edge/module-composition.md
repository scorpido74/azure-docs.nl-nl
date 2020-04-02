---
title: Module & routes implementeren met implementatiemanifesten - Azure IoT Edge
description: Lees hoe een implementatiemanifest verklaart welke modules moeten worden geïmplementeerd, hoe u ze moet implementeren en hoe u berichtroutes tussen deze modules maken.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a4b90d8b6fe67de26c8e652e0dc5b62cc27023f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545630"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Meer informatie over het implementeren van modules en het vaststellen van routes naar IoT Edge

Elk IoT Edge-apparaat draait ten minste twee modules: $edgeAgent en $edgeHub, die deel uitmaken van de IoT Edge-runtime. IoT Edge-apparaat kan meerdere extra modules uitvoeren voor een willekeurig aantal processen. Gebruik een implementatiemanifest om uw apparaat te vertellen welke modules u moet installeren en hoe u ze configureren om samen te werken.

Het *implementatiemanifest* is een JSON-document dat beschrijft:

* De **IoT Edge-agentmoduletwin,** die drie componenten bevat:
  * De containerafbeelding voor elke module die op het apparaat wordt uitgevoerd.
  * De referenties voor toegang tot privécontainerregisters die moduleafbeeldingen bevatten.
  * Instructies voor hoe elke module moet worden gemaakt en beheerd.
* De **IoT Edge hub** module twin, die omvat hoe berichten stromen tussen modules en uiteindelijk naar IoT Hub.
* De gewenste eigenschappen van een extra module tweeling (optioneel).

Alle IoT Edge-apparaten moeten zijn geconfigureerd met een implementatiemanifest. Een nieuw geïnstalleerde IoT Edge runtime rapporteert een foutcode totdat deze is geconfigureerd met een geldig manifest.

In de Azure IoT Edge-zelfstudies bouwt u een implementatiemanifest door een wizard in de Azure IoT Edge-portal te doorlopen. U een implementatiemanifest ook programmatisch toepassen met REST of de IoT Hub Service SDK. Zie [IoT Edge-implementaties begrijpen](module-deployment-monitoring.md)voor meer informatie .

## <a name="create-a-deployment-manifest"></a>Een implementatiemanifest maken

Op een hoog niveau is een implementatiemanifest een lijst van moduletweelingen die zijn geconfigureerd met de gewenste eigenschappen. Een implementatiemanifest vertelt een IoT Edge-apparaat (of een groep apparaten) welke modules moeten worden geïnstalleerd en hoe deze moeten worden geconfigureerd. Implementatiemanifesten bevatten de *gewenste eigenschappen* voor elke moduletwin. IoT Edge-apparaten rapporteren de *gerapporteerde eigenschappen* voor elke module.

In elk implementatiemanifest zijn `$edgeAgent`twee `$edgeHub`modules vereist: , en . Deze modules maken deel uit van de IoT Edge-runtime die het IoT Edge-apparaat beheert en de modules die erop draaien. Zie [De runtime van IoT Edge en de architectuur ervan begrijpen](iot-edge-runtime.md)voor meer informatie over deze modules.

Naast de twee runtime-modules u maximaal 20 eigen modules toevoegen om op een IoT Edge-apparaat te draaien.

Een implementatiemanifest dat alleen de IoT Edge-runtime (edgeAgent en edgeHub) bevat, is geldig.

Implementatiemanifesten volgen deze structuur:

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Modules configureren

Definieer hoe de IoT Edge-runtime de modules in uw implementatie installeert. De IoT Edge-agent is de runtime-component die installatie-, updates- en statusrapportage voor een IoT Edge-apparaat beheert. Daarom bevat de $edgeAgent moduletwin de configuratie- en beheerinformatie voor alle modules. Deze informatie bevat de configuratieparameters voor de IoT Edge-agent zelf.

Zie [Eigenschappen van de IoT Edge-agent en de IoT Edge-hub](module-edgeagent-edgehub.md)voor een volledige lijst met eigenschappen die kunnen of moeten worden opgenomen.

De $edgeAgent eigenschappen volgen deze structuur:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Routes declareren

De IoT Edge-hub beheert de communicatie tussen modules, IoT Hub en alle bladapparaten. Daarom bevat de $edgeHub moduletwin een gewenste eigenschap die *routes* wordt genoemd, die aangeeft hoe berichten binnen een implementatie worden doorgegeven. U meerdere routes binnen dezelfde implementatie hebben.

Routes worden aangegeven in de **$edgeHub** gewenste eigenschappen met de volgende syntaxis:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Elke route heeft een bron en een gootsteen nodig, maar de voorwaarde is een optioneel stuk dat u gebruiken om berichten te filteren.

### <a name="source"></a>Bron

De bron geeft aan waar de berichten vandaan komen. IoT Edge kan berichten van modules of bladapparaten routeren.

Met behulp van de IoT SDKs kunnen modules specifieke uitvoerwachtrijen voor hun berichten declareren met behulp van de klasse ModuleClient. Uitvoerwachtrijen zijn niet nodig, maar zijn wel handig voor het beheren van meerdere routes. Leaf-apparaten kunnen de klasse DeviceClient van de IoT-SDK's gebruiken om berichten naar IoT Edge-gatewayapparaten te verzenden op dezelfde manier als ze berichten naar IoT Hub zouden verzenden. Zie [Azure IoT Hub SDKs begrijpen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md)voor meer informatie.

De eigenschap source kan een van de volgende waarden zijn:

| Bron | Beschrijving |
| ------ | ----------- |
| `/*` | Alle device-to-cloud berichten of dubbele wijzigingmeldingen van elke module of leaf-apparaat |
| `/twinChangeNotifications` | Elke dubbele verandering (gerapporteerde eigenschappen) afkomstig van een module of bladapparaat |
| `/messages/*` | Elk apparaat-naar-cloud bericht dat door een module wordt verzonden via een of geen uitvoer, of door een leaf-apparaat |
| `/messages/modules/*` | Elk apparaat-naar-cloud bericht dat door een module wordt verzonden via een of andere uitvoer |
| `/messages/modules/<moduleId>/*` | Elk apparaat-naar-cloud bericht dat door een specifieke module wordt verzonden via een of andere uitvoer |
| `/messages/modules/<moduleId>/outputs/*` | Elk apparaat-naar-cloud bericht dat door een specifieke module wordt verzonden via een |
| `/messages/modules/<moduleId>/outputs/<output>` | Elk device-to-cloud bericht dat door een specifieke module wordt verzonden via een specifieke uitvoer |

### <a name="condition"></a>Voorwaarde

De voorwaarde is optioneel in een routeaangifte. Als u wilt alle berichten van de bron doorgeven aan de gootsteen, gewoon weglaten van de **WHERE-clausule** volledig. U de [IoT Hub-querytaal](../iot-hub/iot-hub-devguide-routing-query-syntax.md) ook gebruiken om te filteren op bepaalde berichten of berichttypen die aan de voorwaarde voldoen. IoT Edge-routes ondersteunen geen filterberichten op basis van dubbele tags of eigenschappen.

De berichten die tussen modules in IoT Edge worden verzonden, worden opgemaakt op dezelfde manier als de berichten die tussen uw apparaten en Azure IoT Hub worden verzonden. Alle berichten zijn opgemaakt als JSON en hebben **systemProperties,** **appProperties**en **body** parameters.

U query's maken rond een van de drie parameters met de volgende syntaxis:

* Systeemeigenschappen: `$<propertyName>` of`{$<propertyName>}`
* Toepassingseigenschappen:`<propertyName>`
* Eigenschappen van het lichaam:`$body.<propertyName>`

Zie [Device-to-cloud berichtroutes queryexpressies](../iot-hub/iot-hub-devguide-routing-query-syntax.md)voor voorbeelden over het maken van query's voor berichteigenschappen.

Een voorbeeld dat specifiek is voor IoT Edge is wanneer u wilt filteren op berichten die vanaf een leaf-apparaat op een gateway-apparaat zijn aangekomen. Berichten die afkomstig zijn van modules bevatten een systeemeigenschap genaamd **connectionModuleId**. Dus als u berichten rechtstreeks naar IoT Hub wilt routeren van leaf-apparaten, gebruikt u de volgende route om moduleberichten uit te sluiten:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink

De gootsteen bepaalt waar de berichten worden verzonden. Alleen modules en IoT Hub kunnen berichten ontvangen. Berichten kunnen niet worden doorgestuurd naar andere apparaten. Er zijn geen wildcardopties in de eigenschap sink.

De eigenschap sink kan een van de volgende waarden zijn:

| Sink | Beschrijving |
| ---- | ----------- |
| `$upstream` | Het bericht verzenden naar IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Het bericht verzenden naar een specifieke invoer van een specifieke module |

IoT Edge biedt ten minste eenmaal garanties. De IoT Edge-hub slaat berichten lokaal op voor het geval een route het bericht niet naar de gootsteen kan brengen. Als de IoT Edge-hub bijvoorbeeld geen verbinding kan maken met IoT Hub of als de doelmodule niet is verbonden.

IoT Edge-hub slaat de berichten op `storeAndForwardConfiguration.timeToLiveSecs` tot de tijd die is opgegeven in de eigenschap van de gewenste eigenschappen van de [IoT Edge-hub.](module-edgeagent-edgehub.md)

## <a name="define-or-update-desired-properties"></a>Gewenste eigenschappen definiëren of bijwerken

Het implementatiemanifest geeft de gewenste eigenschappen op voor elke module die is geïmplementeerd op het IoT Edge-apparaat. Gewenste eigenschappen in het implementatiemanifest overschrijven alle gewenste eigenschappen die momenteel in de moduletwee zitten.

Als u de gewenste eigenschappen van een moduletweeling niet opgeeft in het implementatiemanifest, wijzigt IoT Hub de moduletweeling op geen enkele manier. In plaats daarvan u de gewenste eigenschappen programmatisch instellen.

Dezelfde mechanismen waarmee u apparaattweelingen wijzigen, worden gebruikt om moduletweelingen te wijzigen. Zie voor meer informatie de [handleiding voor twee ontwikkelaars van de module.](../iot-hub/iot-hub-devguide-module-twins.md)

## <a name="deployment-manifest-example"></a>Voorbeeld van implementatiemanifest

In het volgende voorbeeld ziet u hoe een geldig implementatiemanifestdocument eruit kan zien.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
            }
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "env": {
              "tempLimit": {"value": "100"}
            },
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Eigenschappen van de IoT Edge-agent en de IoT Edge-hub](module-edgeagent-edgehub.md)voor een volledige lijst met eigenschappen die in $edgeAgent en $edgeHub kunnen of moeten worden opgenomen.

* Nu u weet hoe IoT Edge-modules worden gebruikt, [begrijpt u de vereisten en tools voor het ontwikkelen van IoT Edge-modules.](module-development.md)

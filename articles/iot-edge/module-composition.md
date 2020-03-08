---
title: Module & routes implementeren met implementatie manifesten-Azure IoT Edge
description: Leer hoe een manifest van de implementatie wordt gedeclareerd welke modules te implementeren, om ze te implementeren, en hoe u berichtroutes daartussen maken.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8eb24fe878638853cd8519c08045552a91f0c190
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379412"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Meer informatie over het implementeren van modules en routes instellen in IoT Edge

Elke IoT Edge-apparaat wordt uitgevoerd ten minste twee modules: $edgeAgent en $edgeHub, die deel uitmaken van de IoT Edge-runtime. IoT Edge apparaat kan meerdere extra modules uitvoeren voor elk aantal processen. Gebruik een implementatie manifest om uw apparaat te laten weten welke modules moeten worden geïnstalleerd en hoe ze moeten worden geconfigureerd om samen te werken.

Het *implementatie manifest* is een JSON-document met een beschrijving van:

* De **IOT Edge agent** module, die drie onderdelen omvat:
  * De container installatie kopie voor elke module die op het apparaat wordt uitgevoerd.
  * De referenties voor toegang tot persoonlijke container registers die module-installatie kopieën bevatten.
  * Instructies voor het maken en beheren van elke module.
* De **IOT Edge hub** -module, met inbegrip van hoe berichten stromen tussen modules en uiteindelijk naar IOT hub.
* De gewenste eigenschappen van een extra module apparaatdubbels (optioneel).

Alle IoT Edge-apparaten moeten worden geconfigureerd met een manifest van de implementatie. Een nieuw geïnstalleerde IoT Edge-runtime rapporteert een foutcode totdat geconfigureerd met een manifest.

In de Azure IoT Edge-zelfstudies bouwt u een manifest van de implementatie door te gaan met een wizard in de portal voor Azure IoT Edge. U kunt ook een implementatie-manifest programmatisch met behulp van REST- of de SDK voor IoT Hub-Service. Zie [IOT Edge-implementaties begrijpen](module-deployment-monitoring.md)voor meer informatie.

## <a name="create-a-deployment-manifest"></a>Maken van een manifest van de implementatie

Op hoog niveau is een manifest van de implementatie van een lijst met moduledubbels die zijn geconfigureerd met de gewenste eigenschappen. Een manifest van de implementatie wordt een IoT Edge-apparaat (of een groep apparaten) aangegeven welke modules te installeren en configureren. Implementatie manifesten bevatten de *gewenste eigenschappen* voor elke module dubbele. IoT Edge-apparaten melden de *gerapporteerde eigenschappen* voor elke module terug.

In elk implementatie manifest zijn twee modules vereist: `$edgeAgent`en `$edgeHub`. Deze modules zijn onderdeel van de IoT Edge-runtime die u beheert de IoT Edge-apparaat en de modules die erop worden uitgevoerd. Zie voor meer informatie over deze modules [inzicht in de runtime van IOT Edge en de bijbehorende architectuur](iot-edge-runtime.md).

Naast de twee runtimemodules, kunt u maximaal 20 modules van uw eigen om uit te voeren op een IoT Edge-apparaat toevoegen.

Een implementatie-manifest dat alleen de IoT Edge-runtime (edgeAgent en edgeHub bevat) is geldig.

Implementatie manifesten volgen deze structuur:

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

Definiëren hoe de modules in IoT Edge-runtime wordt geïnstalleerd in uw implementatie. De IoT Edge-agent is de runtime-onderdelen die installatie, updates en rapportage voor een IoT Edge-apparaat de status beheerd. Daarom bevat de $edgeAgent-module twee informatie over de configuratie en het beheer van alle modules. Deze informatie bevat de configuratie parameters voor de IoT Edge agent zelf.

Zie [Eigenschappen van de IOT Edge agent en IOT Edge hub](module-edgeagent-edgehub.md)voor een volledige lijst met eigenschappen die kunnen of moeten worden opgenomen.

De eigenschappen $edgeAgent volgen deze structuur:

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

De IoT Edge hub beheert de communicatie tussen modules, IoT-Hub en een leaf-apparaten. Daarom bevat de $edgeHub-module twee een gewenste eigenschap met de naam *routes* die declareert hoe berichten worden door gegeven in een implementatie. U kunt meerdere routes binnen dezelfde implementatie hebben.

Routes worden in de gewenste **$edgeHub** eigenschappen gedeclareerd met de volgende syntaxis:

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

Elke route heeft een bron en sink, maar de voorwaarde is een optioneel onderdeel dat u gebruiken kunt voor het filteren van berichten.

### <a name="source"></a>Bron

De bron geeft aan waar de berichten afkomstig zijn uit. IoT Edge kunt berichten van modules of blad apparaten routeren.

Met behulp van de IoT-Sdk's kunnen modules specifieke uitvoer wachtrijen voor hun berichten declareren met behulp van de ModuleClient-klasse. Uitvoer wachtrijen zijn niet nodig, maar zijn handig voor het beheren van meerdere routes. Blade apparaten kunnen de DeviceClient-klasse van de IoT-Sdk's gebruiken om berichten te verzenden naar IoT Edge gateway-apparaten op dezelfde manier als waarop ze berichten verzenden naar IoT Hub. Zie voor meer informatie [Azure IOT hub-Sdk's begrijpen en gebruiken](../iot-hub/iot-hub-devguide-sdks.md).

De broneigenschap kan een van de volgende waarden zijn:

| Bron | Beschrijving |
| ------ | ----------- |
| `/*` | Alle apparaat-naar-cloud-berichten of dubbele wijzigingsmeldingen vanaf elk apparaat module of leaf |
| `/twinChangeNotifications` | Elke wijziging met dubbele (gerapporteerde eigenschappen) die afkomstig zijn van een module of leaf-apparaat |
| `/messages/*` | Een apparaat-naar-Cloud-bericht dat door een module wordt verzonden via een of geen uitvoer of door een Leaf-apparaat |
| `/messages/modules/*` | Een apparaat-naar-cloud bericht verzonden door een module door bepaalde of geen uitvoer |
| `/messages/modules/<moduleId>/*` | Een apparaat-naar-cloud bericht verzonden door een specifieke module via bepaalde of geen uitvoer |
| `/messages/modules/<moduleId>/outputs/*` | Een apparaat-naar-cloud bericht verzonden door een specifieke module door sommige uitvoer |
| `/messages/modules/<moduleId>/outputs/<output>` | Een apparaat-naar-cloud bericht verzonden door een specifieke module door een specifieke uitvoer |

### <a name="condition"></a>Voorwaarde

De voorwaarde is optioneel in de declaratie van een route. Als u alle berichten van de bron wilt door geven aan de sink, laat u de **where** -component gewoon weg. U kunt ook de [IOT hub query taal](../iot-hub/iot-hub-devguide-routing-query-syntax.md) gebruiken om te filteren op bepaalde berichten of bericht typen die voldoen aan de voor waarde. IoT Edge-routes bieden geen ondersteuning voor filters gebruiken om berichten op basis van de apparaatdubbel-tags of eigenschappen.

De berichten die modules in IoT Edge tussen worden opgemaakt gelijk zijn aan de berichten die de apparaten en Azure IoT Hub tussen. Alle berichten worden ingedeeld als JSON en hebben **systemProperties**-, **appProperties**-en **Body** -para meters.

U kunt query's om een van de drie parameters op in de volgende syntaxis bouwen:

* Systeem eigenschappen: `$<propertyName>` of `{$<propertyName>}`
* Toepassings eigenschappen: `<propertyName>`
* Eigenschappen van hoofd tekst: `$body.<propertyName>`

Zie voor beelden van het maken van query's voor bericht eigenschappen [apparaat-naar-Cloud-bericht routes query-expressies](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Een voorbeeld die specifiek is voor IoT Edge is als u wilt filteren op berichten dat is ontvangen op een gateway-apparaat vanaf een leaf-apparaat. Berichten die afkomstig zijn uit modules, bevatten een systeem eigenschap met de naam **connectionModuleId**. Dus als u routeren van berichten van het leaf-apparaten rechtstreeks naar IoT Hub wilt, gebruikt u de volgende route wilt uitsluiten van berichten van de module:

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Sink

De sink definieert waar de berichten worden verzonden. Alleen modules en IoT-Hub ontvangen berichten. Berichten kunnen niet worden doorgestuurd naar andere apparaten. Er zijn geen opties jokertekens in de sink-eigenschap.

De eigenschap sink is een van de volgende waarden:

| Sink | Beschrijving |
| ---- | ----------- |
| `$upstream` | Het bericht verzenden naar IoT Hub |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | Het bericht verzenden naar een specifieke invoer van een specifieke module |

IoT Edge biedt op-één keer garanties. De IoT Edge hub slaat berichten lokaal op als een route het bericht niet aan de Sink kan verzenden. Bijvoorbeeld, als de IoT Edge hub geen verbinding kan maken met IoT Hub of de doel module niet is verbonden.

IoT Edge hub worden de berichten opgeslagen tot de tijd die is opgegeven in de eigenschap `storeAndForwardConfiguration.timeToLiveSecs` van de [gewenste eigenschappen van de IOT Edge hub](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Gewenste eigenschappen bijwerken of definiëren

Het manifest implementatie Hiermee geeft u de gewenste eigenschappen voor elke module die is geïmplementeerd op het IoT Edge-apparaat. Gewenste eigenschappen in het manifest van de implementatie van overschreven de gewenste eigenschappen die momenteel in de moduledubbel.

Als u de gewenste eigenschappen van de moduledubbel in het manifest van de implementatie niet opgeeft, niet de moduledubbel op geen enkele manier gewijzigd in IoT Hub. In plaats daarvan kunt u de gewenste eigenschappen programmatisch instellen.

De dezelfde mechanismen waarmee u kunt wijzigen van apparaatdubbels worden gebruikt voor het wijzigen van moduledubbels. Zie de [module dubbele ontwikkelaars handleiding](../iot-hub/iot-hub-devguide-module-twins.md)voor meer informatie.

## <a name="deployment-manifest-example"></a>Voorbeeld van de implementatie-manifest

Het volgende voorbeeld laat zien hoe een geldige implementatie manifest document eruit kan zien.

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

* Zie [Eigenschappen van de IOT Edge agent en IOT Edge hub](module-edgeagent-edgehub.md)voor een volledige lijst met eigenschappen die kunnen of moeten worden opgenomen in $edgeAgent en $edgeHub.

* Nu u weet hoe IoT Edge-modules worden gebruikt, [begrijpt u de vereisten en hulpprogram ma's voor het ontwikkelen van IOT Edge modules](module-development.md).

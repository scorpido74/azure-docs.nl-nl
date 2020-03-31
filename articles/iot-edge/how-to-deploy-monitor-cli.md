---
title: Modules op schaal implementeren met Azure CLI - Azure IoT Edge
description: De IoT-extensie voor Azure CLI gebruiken om automatische implementaties te maken voor groepen IoT Edge-apparaten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271471"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>IoT Edge-modules op schaal implementeren en bewaken met de Azure CLI

Maak een **automatische implementatie van IoT Edge** met behulp van de Azure-command-line-interface om lopende implementaties voor veel apparaten tegelijk te beheren. Automatische implementaties voor IoT Edge maken deel uit van de [functie voor automatisch apparaatbeheer](/azure/iot-hub/iot-hub-automatic-device-management) van IoT Hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten implementeren, de status en status van de modules bijhouden en waar nodig wijzigingen aanbrengen.

Zie [IoT Edge-automatische implementaties voor afzonderlijke apparaten of op schaal begrijpen](module-deployment-monitoring.md)voor meer informatie.

In dit artikel stelt u Azure CLI en de IoT-extensie in. Vervolgens leert u hoe u modules implementeert naar een set IoT Edge-apparaten en de voortgang controleren met behulp van de beschikbare CLI-opdrachten.

## <a name="cli-prerequisites"></a>CLI-vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [IoT Edge-apparaten](how-to-register-device.md#prerequisites-for-the-azure-cli) met de IoT Edge-runtime geïnstalleerd.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. Zie [Meer informatie over het implementeren van modules en het instellen van routes in IoT Edge](module-composition.md)voor meer informatie.

Als u modules wilt implementeren met Azure CLI, slaat u het implementatiemanifest lokaal op als een .txt-bestand. U gebruikt het bestandspad in de volgende sectie wanneer u de opdracht uitvoert om de configuratie op uw apparaat toe te passen.

Hier is een basisimplementatiemanifest met één module als voorbeeld:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
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
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="layered-deployment"></a>Gelaagde implementatie

Gelaagde implementaties zijn een type automatische implementatie dat bovenop elkaar kan worden gestapeld. Zie [IoT Edge-automatische implementaties voor afzonderlijke apparaten of op schaal begrijpen voor](module-deployment-monitoring.md)meer informatie over gelaagde implementaties.

Gelaagde implementaties kunnen worden gemaakt en beheerd met de Azure CLI zoals elke automatische implementatie, met slechts een paar verschillen. Zodra een gelaagde implementatie is gemaakt, werkt hetzelfde Azure CLI-werk voor gelaagde implementaties hetzelfde als elke implementatie. Als u een gelaagde `--layered` implementatie wilt maken, voegt u de vlag toe aan de opdracht Maken.

Het tweede verschil zit hem in de constructie van het implementatiemanifest. Hoewel standaard automatische implementatie de runtimemodules van het systeem moet bevatten naast gebruikersmodules, kunnen gelaagde implementaties alleen gebruikersmodules bevatten. In plaats daarvan hebben gelaagde implementaties ook een standaard automatische implementatie op een apparaat nodig, om de vereiste componenten van elk IoT Edge-apparaat te leveren, zoals de runtime-modules van het systeem.

Hier is een basisgelaagd implementatiemanifest met één module als voorbeeld:

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

In het vorige voorbeeld werd `properties.desired` een gelaagde implementatie getoond die de instelling voor een module instelt. Als deze gelaagde implementatie gericht was op een apparaat waarbij dezelfde module al is toegepast, zou dit alle bestaande gewenste eigenschappen overschrijven. Om de gewenste eigenschappen bij te werken, in plaats van te overschrijven, u een nieuwe onderafdeling definiëren. Bijvoorbeeld:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Zie [Gelaagde implementatie](module-deployment-monitoring.md#layered-deployment) voor meer informatie over het configureren van moduletweelingen in gelaagde implementaties

## <a name="identify-devices-using-tags"></a>Apparaten identificeren met behulp van tags

Voordat u een implementatie maken, moet u kunnen opgeven welke apparaten u wilt beïnvloeden. Azure IoT Edge identificeert apparaten met behulp van **tags** in de apparaattweeling. Elk apparaat kan meerdere tags hebben die u definieert op een manier die zinvol is voor uw oplossing. Als u bijvoorbeeld een campus van slimme gebouwen beheert, u de volgende tags aan een apparaat toevoegen:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie over apparaattweelingen en tags.

## <a name="create-a-deployment"></a>Een implementatie maken

U implementeert modules naar uw doelapparaten door een implementatie te maken die bestaat uit het implementatiemanifest en andere parameters.

Gebruik de opdracht voor het maken van een implementatie [door AZ IOT Edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) om een implementatie te maken:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Gebruik dezelfde opdracht `--layered` met de vlag om een gelaagde deploymet te maken.

De opdracht Implementatie-maken neemt de volgende parameters:

* **--layered** - Een optionele vlag om de implementatie te identificeren als een gelaagde implementatie.
* **--deployment-id** - De naam van de implementatie die wordt gemaakt in de IoT-hub. Geef uw implementatie een unieke naam die maximaal 128 kleine letters bedraagt. Vermijd spaties en de `& ^ [ ] { } \ | " < > /`volgende ongeldige tekens: . Vereiste parameter.
* **--inhoud** - Filepath naar het implementatiemanifest JSON. Vereiste parameter.
* **--hub-name** - Naam van de IoT-hub waarin de implementatie wordt gemaakt. De hub moet zich in het huidige abonnement bevinden. Wijzig uw huidige `az account set -s [subscription name]` abonnement met de opdracht.
* **--labels** - Labels toevoegen om uw implementaties bij te houden. Labels zijn Naam, Waardeparen die uw implementatie beschrijven. Labels nemen JSON-opmaak voor de namen en waarden. Bijvoorbeeld: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-conditie** - Voer een doelvoorwaarde in om te bepalen welke apparaten met deze implementatie worden getarget.De voorwaarde is gebaseerd op apparaat twin tags of apparaat twin gerapporteerde eigenschappen en moet overeenkomen met de expressie-indeling.Bijvoorbeeld `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--prioriteit** - Een positief geheel getal. In het geval dat twee of meer implementaties op hetzelfde apparaat zijn gericht, is de implementatie met de hoogste numerieke waarde voor Prioriteit van toepassing.
* **--metrics** - Maak statistieken die de edgeHub gerapporteerde eigenschappen opvragen om de status van een implementatie bij te houden. Statistieken nemen JSON-invoer of een bestandspad. Bijvoorbeeld `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Een implementatie controleren

Gebruik de [opdracht az iot edge-implementatie](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) om de details van één implementatie weer te geven:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht Implementatieshow neemt de volgende parameters:

* **--deployment-id** - De naam van de implementatie die in de IoT-hub bestaat. Vereiste parameter.
* **--hub-naam** - Naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement bevinden. Overschakelen naar het gewenste abonnement met de opdracht`az account set -s [subscription name]`

Controleer de implementatie in het opdrachtvenster.De eigenschap **metrics** bevat een telling voor elke statistiek die door elke hub wordt geëvalueerd:

* **targetedCount** - Een systeemstatistiek die het aantal apparaattweelingen in IoT-hub opgeeft dat overeenkomt met de targetingvoorwaarde.
* **appliedCount** - Een systeemstatistiek geeft het aantal apparaten op waarop de implementatie-inhoud is toegepast op hun moduletweeling in IoT Hub.
* **gerapporteerdESuccesvolletelling** - een apparaatstatistiek die het aantal IoT Edge-apparaten in de implementatierapportage van de runtime van de IoT Edge-client opgeeft.
* **gerapporteerdEOntrische aantal** - een apparaatstatistiek die het aantal IoT Edge-apparaten in de implementatierapportagefout van de runtime van de IoT Edge-client opgeeft.

U een lijst met apparaat-geïdentificeerde gegevens of objecten voor elk van de statistieken weergeven met behulp van de showmetrische opdracht [az iot-implementatie:](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

De opdracht metmetrische functie voor implementatietoont neemt de volgende parameters:

* **--deployment-id** - De naam van de implementatie die in de IoT-hub bestaat.
* **--metric-id** - De naam van de statistiek waarvoor u bijvoorbeeld de lijst `reportedFailedCount`met apparaat-id's wilt zien.
* **--hub-naam** - Naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement bevinden. Schakel met de opdracht `az account set -s [subscription name]`over naar het gewenste abonnement.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle beoogde apparaten.

Als u de doelvoorwaarde bijwerkt, vinden de volgende updates plaats:

* Als een apparaat niet voldoet aan de oude doelvoorwaarde, maar voldoet aan de nieuwe doelvoorwaarde en deze implementatie de hoogste prioriteit heeft voor dat apparaat, wordt deze implementatie toegepast op het apparaat.
* Als een apparaat dat deze implementatie uitvoert niet meer voldoet aan de doelvoorwaarde, wordt deze implementatie ongedaan en wordt de volgende implementatie met de hoogste prioriteit uitgevoerd.
* Als een apparaat dat momenteel deze implementatie uitvoert niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van andere implementaties, vindt er geen wijziging plaats op het apparaat. Het apparaat blijft de huidige modules in hun huidige staat uitvoeren, maar wordt niet meer beheerd als onderdeel van deze implementatie. Zodra deze voldoet aan de doelvoorwaarde van een andere implementatie, verwijdert het deze implementatie en neemt het de nieuwe aan.

U de inhoud van een implementatie niet bijwerken, waaronder de modules en routes die zijn gedefinieerd in het implementatiemanifest. Als u de inhoud van een implementatie wilt bijwerken, u dit doen door een nieuwe implementatie te maken die zich richt op dezelfde apparaten met een hogere prioriteit. U bepaalde eigenschappen van een bestaande module wijzigen, waaronder de doelvoorwaarde, labels, statistieken en prioriteit.

Gebruik de opdracht [voor het bijwerken van de az-implementatievan de RAND](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) om een implementatie bij te werken:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

De opdracht implementatie-update voert de volgende parameters uit:

* **--deployment-id** - De naam van de implementatie die in de IoT-hub bestaat.
* **--hub-naam** - Naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement bevinden. Overschakelen naar het gewenste abonnement met de opdracht`az account set -s [subscription name]`
* **--set** - Een eigenschap bijwerken in de implementatie. U de volgende eigenschappen bijwerken:
  * targetCondition - bijvoorbeeld`targetCondition=tags.location.state='Oregon'`
  * labels
  * priority
* **--toevoegen** - Voeg een nieuwe eigenschap toe aan de implementatie, inclusief doelvoorwaarden of labels.
* **--verwijderen** - Verwijder een bestaande eigenschap, inclusief doelvoorwaarden of labels.

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, nemen alle apparaten hun volgende implementatie met de hoogste prioriteit op zich. Als uw apparaten niet voldoen aan de doelvoorwaarde van een andere implementatie, worden de modules niet verwijderd wanneer de implementatie wordt verwijderd.

Gebruik de opdracht voor het verwijderen van de [implementatie van AZ IOT-edge](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) om een implementatie te verwijderen:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht Implementatie verwijderen neemt de volgende parameters:

* **--deployment-id** - De naam van de implementatie die in de IoT-hub bestaat.
* **--hub-naam** - Naam van de IoT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement bevinden. Overschakelen naar het gewenste abonnement met de opdracht`az account set -s [subscription name]`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules naar IoT Edge-apparaten](module-deployment-monitoring.md).

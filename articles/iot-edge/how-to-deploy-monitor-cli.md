---
title: Modules op schaal implementeren met behulp van Azure CLI-Azure IoT Edge
description: De IoT-extensie voor Azure CLI gebruiken voor automatische implementaties voor groepen van IoT Edge-apparaten maken
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893022"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>Implementeren en bewaken van IoT Edge-modules op schaal met behulp van de Azure CLI

Maak een **IOT Edge automatische implementatie** met behulp van de Azure-opdracht regel interface voor het beheren van doorlopende implementaties voor veel apparaten tegelijk. Automatische implementaties voor IoT Edge maken deel uit van de functie voor het [automatisch beheren van apparaten](/azure/iot-hub/iot-hub-automatic-device-management) van IOT hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren, de status en status van de modules kunt volgen en waar nodig wijzigingen kunt aanbrengen.

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

In dit artikel hebt instellen u Azure CLI en de IoT-extensie. U leert hoe u modules implementeert op een set IoT Edge-apparaten en de voortgang kunt controleren met behulp van de beschik bare CLI-opdrachten.

## <a name="cli-prerequisites"></a>CLI-vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* [IOT edge apparaten](how-to-register-device.md#prerequisites-for-the-azure-cli) waarop de IOT Edge-runtime is geïnstalleerd.
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

Sla het manifest van implementatie lokaal als een txt-bestand voor het implementeren van modules met behulp van Azure CLI. U gebruikt het bestandspad in de volgende sectie wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat.

Hier volgt een manifest eenvoudige implementatie met één module als een voorbeeld:

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

Gelaagde implementaties zijn een type automatische implementatie dat boven op elkaar kan worden geplaatst. Zie voor meer informatie over gelaagde implementaties [begrijpen IOT Edge automatische implementaties voor afzonderlijke apparaten of op schaal](module-deployment-monitoring.md).

U kunt gelaagde implementaties maken en beheren met de Azure CLI, zoals elke automatische implementatie, met slechts enkele verschillen. Zodra een gelaagde implementatie is gemaakt, werkt dezelfde Azure CLI voor gelaagde implementaties hetzelfde als elke implementatie. Als u een gelaagde implementatie wilt maken, moet u de vlag `--layered` toevoegen aan de opdracht Create.

Het tweede verschil bevindt zich in de constructie van het implementatie manifest. Hoewel standaard automatische implementatie de System runtime-modules moet bevatten naast eventuele gebruikers modules, kunnen gelaagde implementaties alleen gebruikers modules bevatten. In plaats daarvan moet er ook een standaard automatische implementatie op een apparaat worden uitgevoerd om de vereiste onderdelen van elk IoT Edge apparaat op te geven, zoals de System runtime-modules.

Hier volgt een eenvoudig gelaagd implementatie manifest met een module als voor beeld:

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

In het vorige voor beeld is een gelaagde implementatie ingesteld voor de `properties.desired` van een module. Als deze gelaagde implementatie gericht is op een apparaat waarop dezelfde module al was toegepast, worden de bestaande gewenste eigenschappen overschreven. Als u een update wilt gebruiken in plaats van overschrijven, gewenste eigenschappen, kunt u een nieuwe Subsectie definiëren. Bijvoorbeeld:

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Zie [gelaagde implementatie](module-deployment-monitoring.md#layered-deployment) voor meer informatie over het configureren van module apparaatdubbels in gelaagde implementaties.

## <a name="identify-devices-using-tags"></a>Identificatie van apparaten met behulp van tags

Voordat u een implementatie maken kunt, moet u opgeven welke apparaten die u wilt toepassen. Azure IoT Edge identificeert apparaten met behulp van **Tags** op het apparaat. Elk apparaat kan meerdere labels hebben die u op een wille keurige manier definieert voor uw oplossing. Als u een campus van slimme gebouwen beheert, kunt u bijvoorbeeld de volgende codes toevoegen aan een apparaat:

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

Zie voor meer informatie over apparaatdubbels en tags voor apparaten [inzicht in de apparaatdubbels in IOT hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Een implementatie maken

U implementeren modules naar uw apparaten door het maken van een implementatie die uit een manifest voor de implementatie, evenals andere parameters bestaat.

Gebruik de opdracht [AZ IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) om een implementatie te maken:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

Gebruik dezelfde opdracht met de vlag `--layered` om een gelaagd gelaagde deploymet te maken.

De opdracht voor het maken van de implementatie heeft de volgende para meters:

* **--laagst** : een optionele vlag voor het identificeren van de implementatie als een gelaagde implementatie.
* **--implementatie-id** : de naam van de implementatie die wordt gemaakt in de IOT-hub. Geef uw implementatie een unieke naam die maximaal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /`. Vereiste parameter.
* **--Content** -filepath naar de JSON van het implementatie manifest. Vereiste parameter.
* **--hub-naam** -naam van de IOT-hub waarin de implementatie wordt gemaakt. De hub moet zich in het huidige abonnement. Wijzig uw huidige abonnement met de opdracht `az account set -s [subscription name]`.
* **--labels** : Voeg labels toe om uw implementaties bij te houden. Labels zijn naam, waarde-paren die uw implementatie te beschrijven. Labels maken de JSON-indeling voor de namen en waarden. Bijvoorbeeld: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--doel-condition** : Voer een doel voorwaarde in om te bepalen welke apparaten worden bedoeld voor deze implementatie. De voor waarde is gebaseerd op apparaatspecifieke Tags of dubbele gerapporteerde eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test' and properties.reported.devicemodel='4000x'`.
* **--Priority** : een positief geheel getal. In het geval dat twee of meer implementaties zijn gericht op hetzelfde apparaat, wordt de implementatie met de hoogste numerieke waarde voor prioriteit wordt toegepast.
* **--metrische gegevens** : Maak metrische gegevens die de edgeHub-gerapporteerde eigenschappen doorzoeken om de status van een implementatie bij te houden. Metrische gegevens hebben een JSON-invoer of een filepath. Bijvoorbeeld `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`.

## <a name="monitor-a-deployment"></a>Controleer de implementatie van een

Gebruik de opdracht [AZ IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) om de details van één implementatie weer te geven:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht show voor de implementatie heeft de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat. Vereiste parameter.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

Controleer de implementatie in het opdrachtvenster. De eigenschap **Metrics** bevat een aantal voor elke metrische waarde die door elke hub wordt geëvalueerd:

* **targetedCount** : een systeem metriek waarmee het aantal apparaatdubbels van het apparaat in IOT hub wordt opgegeven dat overeenkomt met de doel voorwaarde.
* **appliedCount** : met een systeem metriek geeft u het aantal apparaten op waarop de implementatie-inhoud is toegepast op de bijbehorende module apparaatdubbels in IOT hub.
* **reportedSuccessfulCount** : een metrische waarde van het apparaat waarmee het aantal IOT edge-apparaten wordt opgegeven in het geslaagde implementatie rapport van de IOT Edge-client runtime.
* **reportedFailedCount** : een metrische waarde van het apparaat waarmee het aantal IOT edge-apparaten wordt opgegeven in de implementatie rapportage fout van de IOT Edge-client runtime.

U kunt een lijst met apparaat-Id's of objecten voor elk van de metrische gegevens weer geven met behulp van de opdracht [AZ IOT Edge Deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) :

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

De opdracht show-meet waarde accepteert de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--metrisch-id** : de naam van de metriek waarvoor u de lijst met apparaat-id's wilt zien, bijvoorbeeld `reportedFailedCount`.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`.

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle apparaten uit de doelgroep.

Als u de doelvoorwaarde bijwerkt, gebeuren de volgende updates:

* Als een apparaat niet voldoet aan de oude doelvoorwaarde, maar voldoet aan de nieuwe doelvoorwaarde en deze implementatie de hoogste prioriteit voor het apparaat is, wordt klikt u vervolgens deze implementatie toegepast op het apparaat.
* Als een apparaat op dit moment met deze implementatie niet meer voldoet aan de doelvoorwaarde, verwijdert deze implementatie en neemt de volgende implementatie met hoogste prioriteit.
* Als een apparaat op dit moment met deze implementatie niet meer voldoet aan de doelvoorwaarde en niet voldoet aan de doelvoorwaarde van alle andere implementaties, treedt er geen wijziging op op het apparaat. Het apparaat wordt uitgevoerd de huidige modules in hun huidige status, maar niet als onderdeel van deze implementatie niet meer wordt beheerd. Als deze voldoet aan de doelvoorwaarde van elke andere implementatie, deze implementatie wordt verwijderd en wordt op de nieuwe computer.

U kunt de inhoud van een implementatie niet bijwerken, die de modules en routes bevat die in het implementatie manifest zijn gedefinieerd. Als u de inhoud van een implementatie wilt bijwerken, kunt u dit doen door een nieuwe implementatie te maken die gericht is op dezelfde apparaten met een hogere prioriteit. U kunt bepaalde eigenschappen van een bestaande module wijzigen, met inbegrip van de doel voorwaarde, labels, metrische gegevens en prioriteit.

Gebruik de opdracht [AZ IOT Edge Deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) om een implementatie bij te werken:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

De implementatie-update opdracht heeft de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`
* **--set** -een eigenschap in de implementatie bijwerken. U kunt de volgende eigenschappen bijwerken:
  * targetCondition-bijvoorbeeld `targetCondition=tags.location.state='Oregon'`
  * labels
  * priority
* **--Voeg** een nieuwe eigenschap toe aan de implementatie, met inbegrip van de doel voorwaarden of labels.
* **--Remove** -een bestaande eigenschap verwijderen, met inbegrip van de doel voorwaarden of labels.

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, worden alle apparaten op de volgende implementatie met hoogste prioriteit. Als uw apparaten niet voldoen aan de doelvoorwaarde van elke andere implementatie, klikt u vervolgens de modules niet verwijderd wanneer de implementatie wordt verwijderd.

Gebruik de opdracht [AZ IOT Edge Deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) om een implementatie te verwijderen:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht voor het verwijderen van de implementatie heeft de volgende para meters:

* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules voor het IOT Edge van apparaten](module-deployment-monitoring.md).

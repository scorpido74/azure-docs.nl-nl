---
title: Modules op schaal implementeren met behulp van Azure CLI-Azure IoT Edge
description: Gebruik de IoT-extensie voor Azure CLI om automatische implementaties te maken voor groepen IoT Edge apparaten
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 14c4ddd5d95abb223fb30e2ce07496e7f2773257
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666015"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>IoT Edge modules op schaal implementeren en bewaken met behulp van de Azure CLI

Maak een **IOT Edge automatische implementatie** met behulp van de Azure-opdracht regel interface voor het beheren van doorlopende implementaties voor veel apparaten tegelijk. Automatische implementaties voor IoT Edge maken deel uit van de functie voor het [automatisch beheren van apparaten](/azure/iot-hub/iot-hub-automatic-device-management) van IOT hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten kunt implementeren, de status en status van de modules kunt volgen en waar nodig wijzigingen kunt aanbrengen. 

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

In dit artikel stelt u Azure CLI en de IoT-uitbrei ding in. U leert hoe u modules implementeert op een set IoT Edge-apparaten en de voortgang kunt controleren met behulp van de beschik bare CLI-opdrachten.

## <a name="cli-prerequisites"></a>CLI-vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement. 
* [IOT edge apparaten](how-to-register-device.md#prerequisites-for-the-azure-cli) waarop de IOT Edge-runtime is geïnstalleerd.
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet mini maal 2.0.24 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen. 
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. Zie [informatie over het implementeren van modules en het tot stand brengen van routes in IOT Edge](module-composition.md)voor meer informatie.

Als u modules wilt implementeren met behulp van Azure CLI, slaat u het manifest van de implementatie lokaal op als een. txt-bestand. U gebruikt het bestandspad in de volgende sectie wanneer u de opdracht uitvoert om de configuratie toe te passen op uw apparaat. 

Hier volgt een basis implementatie manifest met een module als voor beeld:

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
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
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
                "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>Apparaten identificeren met behulp van Tags

Voordat u een implementatie kunt maken, moet u kunnen opgeven welke apparaten u wilt beïnvloeden. Azure IoT Edge identificeert apparaten met behulp van **Tags** op het apparaat. Elk apparaat kan meerdere labels hebben die u op een wille keurige manier definieert voor uw oplossing. Als u bijvoorbeeld een universiteit van Smart gebouwen beheert, kunt u de volgende Tags toevoegen aan een apparaat:

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

U implementeert modules op uw doel apparaten door een implementatie te maken die bestaat uit het implementatie manifest en andere para meters. 

Gebruik de opdracht [AZ IOT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) om een implementatie te maken:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

De opdracht voor het maken van de implementatie heeft de volgende para meters: 

* **--implementatie-id** : de naam van de implementatie die wordt gemaakt in de IOT-hub. Geef uw implementatie een unieke naam van Maxi maal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /`.
* **--hub-naam** -naam van de IOT-hub waarin de implementatie wordt gemaakt. De hub moet zich in het huidige abonnement benemen. Wijzig uw huidige abonnement met de opdracht `az account set -s [subscription name]`.
* **--Content** -filepath naar de JSON van het implementatie manifest. 
* **--labels** : Voeg labels toe om uw implementaties bij te houden. Labels zijn naam-, waardeparen die uw implementatie beschrijven. Labels maken de JSON-indeling voor de namen en waarden. Bijvoorbeeld: `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--doel-condition** : Voer een doel voorwaarde in om te bepalen welke apparaten worden bedoeld voor deze implementatie. De voor waarde is gebaseerd op apparaatspecifieke Tags of dubbele gerapporteerde eigenschappen van het apparaat en moet overeenkomen met de indeling van de expressie. Bijvoorbeeld `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--Priority** : een positief geheel getal. In het geval van twee of meer implementaties op hetzelfde apparaat is de implementatie met de hoogste numerieke waarde voor prioriteit van toepassing.

## <a name="monitor-a-deployment"></a>Een implementatie bewaken

Gebruik de opdracht [AZ IOT Edge Deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) om de details van één implementatie weer te geven:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

De opdracht show voor de implementatie heeft de volgende para meters:
* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

Inspecteer de implementatie in het opdracht venster. De eigenschap **Metrics** bevat een aantal voor elke metrische waarde die door elke hub wordt geëvalueerd:

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
* **--metric-id** : de naam van de metriek waarvoor u de lijst met apparaat-id's wilt zien, bijvoorbeeld `reportedFailedCount`
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

## <a name="modify-a-deployment"></a>Een implementatie wijzigen

Wanneer u een implementatie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle doel apparaten. 

Als u de doel voorwaarde bijwerkt, worden de volgende updates uitgevoerd:

* Als een apparaat niet voldoet aan de oude doel voorwaarde, maar voldoet aan de nieuwe doel voorwaarde en deze implementatie de hoogste prioriteit voor dat apparaat is, wordt deze implementatie toegepast op het apparaat. 
* Als een apparaat waarop deze implementatie momenteel wordt uitgevoerd niet meer voldoet aan de doel voorwaarde, wordt deze implementatie verwijderd en neemt de implementatie van de volgende met de hoogste prioriteit in beslag. 
* Als een apparaat dat deze implementatie momenteel uitvoert niet meer voldoet aan de doel voorwaarde en niet voldoet aan de doel voorwaarde van andere implementaties, vindt er geen wijzigingen plaats op het apparaat. Het apparaat blijft de huidige modules in hun huidige staat uitvoeren, maar wordt niet meer beheerd als onderdeel van deze implementatie. Zodra de app aan de doel voorwaarde van een andere implementatie voldoet, wordt deze implementatie verwijderd en wordt de nieuwe geïmplementeerd. 

Gebruik de opdracht [AZ IOT Edge Deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) om een implementatie bij te werken:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

De implementatie-update opdracht heeft de volgende para meters:
* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`
* **--set** -een eigenschap in de implementatie bijwerken. U kunt de volgende eigenschappen bijwerken:
  * targetCondition-bijvoorbeeld `targetCondition=tags.location.state='Oregon'`
  * Labels 
  * priority


## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Wanneer u een implementatie verwijdert, nemen alle apparaten de volgende implementatie van de hoogste prioriteit. Als uw apparaten niet voldoen aan de doel voorwaarde van een andere implementatie, worden de modules niet verwijderd wanneer de implementatie wordt verwijderd. 

Gebruik de opdracht [AZ IOT Edge Deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) om een implementatie te verwijderen:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

De opdracht voor het verwijderen van de implementatie heeft de volgende para meters: 
* **--implementatie-id** : de naam van de implementatie die in de IOT-hub bestaat.
* **--hub-name** -naam van de IOT-hub waarin de implementatie bestaat. De hub moet zich in het huidige abonnement benemen. Schakel over naar het gewenste abonnement met de opdracht `az account set -s [subscription name]`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules voor het IOT Edge van apparaten](module-deployment-monitoring.md).

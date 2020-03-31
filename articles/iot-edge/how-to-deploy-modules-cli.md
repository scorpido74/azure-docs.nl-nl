---
title: Modules implementeren vanuit de opdrachtregel Azure CLI - Azure IoT Edge
description: Gebruik de Azure CLI met de Azure IoT-extensie om een IoT Edge-module van uw IoT-hub naar uw IoT Edge-apparaat te pushen, zoals geconfigureerd door een implementatiemanifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 619ba7cb2d99e0137fd1834096dd5b66ffcd6ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240387"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure IoT Edge-modules implementeren met Azure CLI

Zodra u IoT Edge-modules hebt gemaakt met uw bedrijfslogica, wilt u deze implementeren op uw apparaten om aan de rand te werken. Als u meerdere modules hebt die samenwerken om gegevens te verzamelen en te verwerken, u ze allemaal tegelijk implementeren en de routeringsregels declareren die deze verbinden.

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open-source cross-platform command-line tool voor het beheren van Azure-bronnen zoals IoT Edge. Hiermee u Azure IoT Hub-resources, apparaatinrichtingsservice-exemplaren en gekoppelde hubs out-of-the-box beheren. De nieuwe IoT-extensie verrijkt Azure CLI met functies zoals apparaatbeheer en volledige IoT Edge-mogelijkheden.

In dit artikel ziet u hoe u een JSON-implementatiemanifest maakt en gebruikt u dat bestand om de implementatie naar een IoT Edge-apparaat te pushen. Zie [IoT Edge-modules op schaal implementeren en bewaken](how-to-deploy-monitor-cli.md) voor informatie over het maken van een implementatie die zich richt op meerdere apparaten op basis van hun gedeelde tags.

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device.md#register-with-the-azure-cli) met de IoT Edge-runtime geïnstalleerd.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet minimaal 2.0.70 of hoger zijn. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. Zie [Begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en hergebruikt](module-composition.md)voor meer informatie over hoe implementatiemanifesten werken en hoe u deze maken.

Als u modules wilt implementeren met azure cli, slaat u het implementatiemanifest lokaal op als een .json-bestand. U gebruikt het bestandspad in de volgende sectie wanneer u de opdracht uitvoert om de configuratie op uw apparaat toe te passen.

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

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

U implementeert modules op uw apparaat door het implementatiemanifest toe te passen dat u hebt geconfigureerd met de modulegegevens.

Wijzig mappen in de map waarin uw implementatiemanifest wordt opgeslagen. Als u een van de VS Code IoT Edge-sjablonen hebt gebruikt, gebruikt u het `deployment.json` bestand in de **config-map** van uw oplossingsmap en niet in het `deployment.template.json` bestand.

Gebruik de volgende opdracht om de configuratie toe te passen op een IoT Edge-apparaat:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

De parameter apparaat-ID is hoofdlettergevoelig. De inhoudsparameter verwijst naar het manifestbestand dat u hebt opgeslagen.

   ![output az iot edge set-modules](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Zodra u modules op uw apparaat hebt geïmplementeerd, u ze allemaal bekijken met de volgende opdracht:

De modules op uw IoT Edge-apparaat bekijken:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

De parameter apparaat-ID is hoofdlettergevoelig.

   ![uitvoer van az iot-hubmodule-identiteiten](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal](how-to-deploy-monitor.md)

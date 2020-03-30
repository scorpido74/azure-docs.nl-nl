---
title: Opc Twin-module implementeren voor Azure vanaf nul | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u OPC Twin vanaf nul implementeert met het IoT Edge-blad van de Azure-portal en ook met AZ CLI.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241059"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Opc Twin-module en afhankelijkheden vanaf nul implementeren

De OPC Twin-module draait op IoT Edge en biedt verschillende edge-services aan de opc-apparaattweeling- en registerservices. 

Er zijn verschillende opties om modules te implementeren voor uw [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, waaronder

- [Implementeren vanuit het IoT Edge-blade van Azure-portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Implementeren met AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Zie de [GitHub-repository](https://github.com/Azure/azure-iiot-components)voor meer informatie over implementatiegegevens en -instructies.

## <a name="deployment-manifest"></a>Distributiemanifest

Alle modules worden geïmplementeerd met behulp van een implementatiemanifest.  Hieronder vindt u een voorbeeldmanifest voor het implementeren van zowel [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) als [OPC Twin.](https://github.com/Azure/azure-iiot-opc-twin-module)

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Implementeren vanuit Azure-portal

De eenvoudigste manier om de modules te implementeren op een Azure IoT Edge-gateway-apparaat is via de Azure-portal.  

### <a name="prerequisites"></a>Vereisten

1. Implementeer de [OPC Twin-afhankelijkheden](howto-opc-twin-deploy-dependencies.md) `.env` en verkregen het resulterende bestand. Let op `hub name` de `PCS_IOTHUBREACT_HUB_NAME` geïmplementeerde variabele `.env` in het resulterende bestand.

2. Registreer en start [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) een [Linux-](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) of Windows `device id`IoT Edge-gateway en noteer de .

### <a name="deploy-to-an-edge-device"></a>Implementeren op een randapparaat

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en navigeer naar uw IoT-hub.

2. Selecteer **IoT Edge** in het linkermenu.

3. Klik op de id van het doelapparaat in de lijst met apparaten.

4. Selecteer **Modules instellen**.

5. Selecteer **Toevoegen** en **IoT Edge Module** in het gedeelte **Implementatiemodules** van de pagina.

6. Geef in het dialoogvenster Aangepaste `opctwin` **IoT Edge-module** de naam voor de module op en geef vervolgens de container Image *URI* op als

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Gebruik als *opties voor het maken van containers*de volgende JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Vul indien nodig de optionele velden in. Zie [EdgeAgent gewenste eigenschappen](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)voor meer informatie over opties voor het maken van containers, het beleid opnieuw starten en de gewenste status. Zie Gewenste eigenschappen definiëren [of bijwerken voor](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)meer informatie over de moduletwin.

7. Selecteer **Opslaan** en herhalen stap **5**.  

8. Gebruik in het dialoogvenster Aangepaste `opcpublisher` IoT Edge-module als naam voor de module en de *containerafbeelding URI* als 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Gebruik als *opties voor het maken van containers*de volgende JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selecteer **Opslaan** en vervolgens **Volgende** om door te gaan naar de sectie routes.

10. Plak op het tabblad Routes het volgende 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    en selecteer **Volgende**

11. Bekijk uw implementatiegegevens en manifest.  Het moet lijken op de bovenstaande inzet manifest.  Selecteer **Indienen**.

12. Zodra u modules op uw apparaat hebt geïmplementeerd, u ze allemaal bekijken op de pagina **Apparaatdetails** van de portal. Op deze pagina wordt de naam van elke geïmplementeerde module weergegeven, evenals nuttige informatie zoals de implementatiestatus en afsluitcode.

## <a name="deploying-using-azure-cli"></a>Implementeren met Azure CLI

### <a name="prerequisites"></a>Vereisten

1. Installeer vanaf [hier](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)de nieuwste versie van de [Azure command line interface (AZ).](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

### <a name="quickstart"></a>Snelstartgids

1. Sla het bovenstaande implementatiemanifest op in een `deployment.json` bestand.  

2. Gebruik de volgende opdracht om de configuratie toe te passen op een IoT Edge-apparaat:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   De `device id` parameter is hoofdlettergevoelig. De inhoudsparameter verwijst naar het manifestbestand dat u hebt opgeslagen. 
    ![az IoT Edge set-modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Zodra u modules op uw apparaat hebt geïmplementeerd, u ze allemaal bekijken met de volgende opdracht:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   De parameter apparaat-ID is hoofdlettergevoelig. ![uitvoer van az iot-hubmodule-identiteiten](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Twin vanaf nul implementeren, is hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [OPC Twin implementeren in een bestaand project](howto-opc-twin-deploy-existing.md)

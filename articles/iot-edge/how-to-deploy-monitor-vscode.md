---
title: Modules op schaal implementeren met Visual Studio Code - Azure IoT Edge
description: Gebruik de IoT-extensie voor Visual Studio Code om automatische implementaties te maken voor groepen IoT Edge-apparaten.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774131"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>IoT Edge-modules op schaal implementeren met Visual Studio Code

U een **automatische implementatie van IoT Edge** maken met Visual Studio Code om lopende implementaties voor veel apparaten tegelijk te beheren. Automatische implementaties voor IoT Edge maken deel uit van de [functie voor automatisch apparaatbeheer](/azure/iot-hub/iot-hub-automatic-device-management) van IoT Hub. Implementaties zijn dynamische processen waarmee u meerdere modules op meerdere apparaten implementeren. U ook de status en status van de modules bijhouden en indien nodig wijzigingen aanbrengen.

Zie [IoT Edge-automatische implementaties voor afzonderlijke apparaten of op schaal begrijpen](module-deployment-monitoring.md)voor meer informatie.

In dit artikel stelt u Visual Studio Code en de IoT-extensie in. U leert vervolgens hoe u modules implementeert op een set IoT Edge-apparaten.

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device.md#register-with-visual-studio-code) met de IoT Edge-runtime geïnstalleerd.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) voor Visual Studio-code.

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan om toegang te krijgen tot uw IoT-hub

U de Azure IoT-extensies voor Visual Studio Code gebruiken om bewerkingen uit te voeren met uw Hub. Als deze bewerkingen werken, moet u zich aanmelden bij uw Azure-account en de IoT-hub selecteren waaraan u werkt.

1. Open in Visual Studio Code de **explorer-weergave.**

1. Vouw onder aan de Explorer de sectie **Azure IoT Hub** uit.

1. Klik op de **sectiekop van** de **Azure IoT Hub.** Als u de ellips niet ziet, zweeft u over de kop.

1. Kies **IoT-hub selecteren**.

1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen hiervoor.

1. Selecteer uw Azure-abonnement.

1. Selecteer uw IoT-hub.

## <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd. Het beschrijft ook hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de module tweelingen. Zie [Meer informatie over het implementeren van modules en het instellen van routes in IoT Edge](module-composition.md)voor meer informatie.

Als u modules wilt implementeren met Visual Studio Code, slaat u het implementatiemanifest lokaal op als een . JSON-bestand. U moet de locatie bepalen wanneer u de opdracht uitvoert om de configuratie op uw apparaat toe te passen.

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

Als u wilt bepalen welke IoT Edge-apparaten u momenteel configureren, voert u de opdracht **IoT Edge: Get Device Info** uit.

## <a name="identify-devices-with-target-conditions"></a>Apparaten identificeren met doelomstandigheden

Als u de IoT Edge-apparaten wilt identificeren die de implementatie moeten ontvangen, moet u een doelvoorwaarde opgeven. Aan een doelvoorwaarde wordt voldaan wanneer bepaalde criteria worden geëvenaard door een deviceId, tagwaarde of een gerapporteerde eigenschapswaarde.

U configureert tags in de apparaattweeling. Hier is een voorbeeld van een apparaat tweeling die tags heeft:

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

Dit apparaat ontvangt een implementatie als de doelvoorwaarde voor de implementatie een expressie bevat `tag.location.building = '20'`die overeenkomt met een van de waarden van de tag, zoals .

Als u een specifiek apparaat wilt targeten, ongeacht de `deviceId` tags of andere waarden, geeft u alleen de doelvoorwaarde op.

Hier zijn nog enkele voorbeelden:

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OF deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' EN tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' EN tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

Zie [doelvoorwaarde](module-deployment-monitoring.md#target-condition) voor meer informatie. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie over apparaattweelingen en tags.

### <a name="edit-the-device-twin"></a>De apparaattweeling bewerken

U de apparaattweeling bewerken in Visual Studio Code om de tags te configureren. Selecteer **in het** menu Weergave de optie **Opdrachtpalet** en voer de opdracht **IoT Edge: Bewerk Apparaat twee uit.** Selecteer uw IoT Edge-apparaat en de apparaattweeling wordt weergegeven.

In dit voorbeeld zijn er geen tags gedefinieerd. Vervang de huidige `"tags": {}` lege sectie door uw eigen tagsdefinitie.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Nadat u het lokale bestand hebt opgeslagen, voert u de opdracht **IoT Edge: Update Device Twin** uit.

## <a name="create-deployment-at-scale"></a>Implementatie op schaal maken

Nadat u het implementatiemanifest en de geconfigureerde tags in de apparaattweeling hebt geconfigureerd, bent u klaar om te implementeren.

1. Selecteer **in het** menu Weergave **opdrachtpalet** en selecteer de opdracht **Azure IoT Edge: Create Deployment at Scale.**

1. Navigeer naar het JSON-bestand voor implementatiemanifest dat u wilt gebruiken en klik op **Edge Deployment Manifest selecteren**.

1. Geef waarden op zoals gevraagd, te beginnen met de **implementatie-id**.

   ![Een implementatie-id opgeven](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Waarden voor deze parameters opgeven:

  | Parameter | Beschrijving |
  | --- | --- |
  | Implementatie-id | De naam van de implementatie die wordt gemaakt in de IoT-hub. Geef uw implementatie een unieke naam die maximaal 128 kleine letters bedraagt. Vermijd spaties en de `& ^ [ ] { } \ | " < > /`volgende ongeldige tekens: . |
  | Doelvoorwaarde | Voer een doelvoorwaarde in om te bepalen welke apparaten met deze implementatie worden getarget.De voorwaarde is gebaseerd op apparaat twin tags of apparaat twin gerapporteerde eigenschappen en moet overeenkomen met de expressie-indeling.Bijvoorbeeld. `tags.environment='test' and properties.reported.devicemodel='4000x'` |
  | Prioriteit |  Een positief geheel getal. Als twee of meer implementaties op hetzelfde apparaat zijn gericht, is de implementatie met de hoogste numerieke waarde voor Prioriteit van toepassing. |

  Nadat de prioriteit is opgegeven, moet de terminal de uitvoer weergeven die vergelijkbaar is met de volgende afbeelding:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Implementaties controleren en wijzigen

Gebruik de [Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment) of de [Azure-portal](how-to-deploy-monitor.md#monitor-a-deployment) om implementaties te controleren, wijzigen en verwijderen. Beide bieden statistieken over uw implementaties.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het implementeren van modules naar IoT Edge-apparaten](module-deployment-monitoring.md).

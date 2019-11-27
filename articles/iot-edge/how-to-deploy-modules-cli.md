---
title: Implementeren van modules uit vanaf de opdrachtregel - Azure IoT Edge | Microsoft Docs
description: De IoT-extensie voor Azure CLI gebruiken om te implementeren van modules voor een IoT Edge-apparaat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72535b69c81aee880eb16bf5d10e11dedb36f3a7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457470"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>Azure IoT Edge-modules met Azure CLI implementeren

Als u IoT Edge modules met uw zakelijke logica maken, wilt u deze implementeren op uw apparaten te werken aan de rand. Als u meerdere modules die samenwerken om te verzamelen en verwerken van gegevens hebt, kunt u ze in één keer implementeren en declareert de routeringsregels waarmee ze zijn verbonden.

[Azure cli](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT Edge. Hiermee kunt u voor het beheren van Azure IoT Hub-resources, device provisioning service-exemplaren en gekoppelde hubs buiten het vak. Azure CLI verrijkt de nieuwe IoT-extensie met functies zoals Apparaatbeheer en de volledige functionaliteit van IoT Edge.

In dit artikel laat zien hoe een manifest van de implementatie JSON maken en vervolgens pusht u de implementatie naar een IoT Edge-apparaat met dat bestand. Voor informatie over het maken van een implementatie die is gericht op meerdere apparaten op basis van hun gedeelde labels, Zie [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-monitor-cli.md)

## <a name="prerequisites"></a>Vereisten

* Een [IOT-hub](../iot-hub/iot-hub-create-using-cli.md) in uw Azure-abonnement.
* Een [IOT edge apparaat](how-to-register-device.md#register-with-the-azure-cli) waarop de IOT Edge-runtime is geïnstalleerd.
* [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) in uw omgeving. Uw Azure CLI-versie moet ten minste 2.0.24 of hoger. Gebruik `az --version` om de versie te valideren. In deze versie worden az-extensie-opdrachten ondersteund en is voor het eerst het Knack-opdrachtframework opgenomen.
* De [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. Zie [begrijpen hoe IOT Edge modules kunnen worden gebruikt, geconfigureerd en opnieuw worden gebruikt](module-composition.md)voor meer informatie over hoe implementatie manifesten werken en hoe u ze kunt maken.

Sla het manifest van implementatie lokaal als een .json-bestand voor het implementeren van modules met behulp van de Azure CLI. Wanneer u de opdracht uit om de configuratie van toepassing op het apparaat uitvoert, wordt u het pad in de volgende sectie.

Hier volgt een manifest eenvoudige implementatie met één module als een voorbeeld:

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
   ```

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

U implementeren modules naar uw apparaat door het toepassen van het manifest implementatie die u hebt geconfigureerd met de modulegegevens.

Wijzig de mappen in de map waarin de implementatie van het manifest is opgeslagen. Als u een van de VS code IoT Edge sjablonen hebt gebruikt, gebruikt u het `deployment.json`-bestand in de map **config** van de map met oplossingen en niet het `deployment.template.json`-bestand.

Gebruik de volgende opdracht toe te passen van de configuratie op een IoT Edge-apparaat:

   ```cli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

De para meter voor de apparaat-ID is hoofdletter gevoelig. De inhoud parameter verwijst naar de implementatie van het manifest-bestand dat u hebt opgeslagen.

   ![AZ iot edge set-modules uitvoer](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Nadat u hebt modules geïmplementeerd op uw apparaat, vindt u alle mappen met de volgende opdracht:

De modules op uw IoT Edge-apparaat bekijken:

   ```cli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

De para meter voor de apparaat-ID is hoofdletter gevoelig.

   ![AZ iot hub-module-identity-lijstuitvoer](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IOT Edge modules op schaal](how-to-deploy-monitor.md)

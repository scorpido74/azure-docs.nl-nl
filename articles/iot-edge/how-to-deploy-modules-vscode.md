---
title: Modules implementeren vanuit Visual Studio Code - Azure IoT Edge
description: Gebruik Visual Studio Code met de Azure IoT-hulpprogramma's om een IoT Edge-module van uw IoT-hub naar uw IoT Edge-apparaat te pushen, zoals geconfigureerd door een implementatiemanifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1b2e2a80670cf0409f8f8477563b9a209cc8706
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209202"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Azure IoT Edge-modules implementeren vanuit Visual Studio Code

Zodra u IoT Edge-modules hebt gemaakt met uw bedrijfslogica, wilt u deze implementeren op uw apparaten om aan de rand te werken. Als u meerdere modules hebt die samenwerken om gegevens te verzamelen en te verwerken, u ze allemaal tegelijk implementeren en de routeringsregels declareren die deze verbinden.

In dit artikel ziet u hoe u een JSON-implementatiemanifest maakt en gebruikt u dat bestand om de implementatie naar een IoT Edge-apparaat te pushen. Zie [IoT Edge-modules implementeren op schaal implementeren met Visual Studio Code](how-to-deploy-monitor-vscode.md)voor informatie over het maken van een implementatie die zich richt op meerdere apparaten op basis van hun gedeelde tags.

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device.md#register-with-visual-studio-code) met de IoT Edge-runtime geïnstalleerd.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) voor Visual Studio-code.

## <a name="configure-a-deployment-manifest"></a>Een implementatiemanifest configureren

Een implementatiemanifest is een JSON-document dat beschrijft welke modules moeten worden geïmplementeerd, hoe gegevens tussen de modules stromen en de gewenste eigenschappen van de moduletweeling. Zie [Begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en hergebruikt](module-composition.md)voor meer informatie over hoe implementatiemanifesten werken en hoe u deze maken.

Als u modules wilt implementeren met Visual Studio Code, slaat u het implementatiemanifest lokaal op als een . JSON-bestand. U gebruikt het bestandspad in de volgende sectie wanneer u de opdracht uitvoert om de configuratie op uw apparaat toe te passen.

Hier is een basisimplementatiemanifest met één module als voorbeeld:

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
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
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

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan om toegang te krijgen tot uw IoT-hub

U de Azure IoT-extensies voor Visual Studio Code gebruiken om bewerkingen uit te voeren met uw IoT-hub. Als deze bewerkingen werken, moet u zich aanmelden bij uw Azure-account en de IoT-hub selecteren waaraan u werkt.

1. Open in Visual Studio Code de **explorer-weergave.**

1. Vouw onder aan de Explorer de sectie **Azure IoT Hub** uit.

   ![Azure IoT-hub uitvouwen](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klik op de **sectiekop van** de **Azure IoT Hub.** Als u de ellips niet ziet, zweeft u over de kop.

1. Kies **IoT-hub selecteren**.

1. Als u niet bent aangemeld bij uw Azure-account, volgt u de aanwijzingen hiervoor.

1. Selecteer uw Azure-abonnement.

1. Selecteer uw IoT-hub.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

U implementeert modules op uw apparaat door het implementatiemanifest toe te passen dat u hebt geconfigureerd met de modulegegevens.

1. Vouw in de weergave Visual Studio Code Explorer de sectie **Azure IoT Hub** uit en vouw vervolgens het knooppunt **Apparaten** uit.

1. Klik met de rechtermuisknop op het IoT Edge-apparaat dat u wilt configureren met het implementatiemanifest.

    > [!TIP]
    > Als u wilt bevestigen dat het apparaat dat u hebt gekozen een IoT Edge-apparaat is, selecteert u het apparaat om de lijst met modules uit te breiden en de aanwezigheid van **$edgeHub** en **$edgeAgent**te verifiëren. Elk IoT Edge-apparaat bevat deze twee modules.

1. Selecteer **Implementatie maken voor één apparaat**.

1. Navigeer naar het JSON-bestand voor implementatiemanifest dat u wilt gebruiken en klik op **Edge Deployment Manifest selecteren**.

   ![Edge Deployment Manifest selecteren](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

De resultaten van uw implementatie worden afgedrukt in de VS-code-uitvoer. Succesvolle implementaties worden binnen enkele minuten toegepast als het doelapparaat wordt uitgevoerd en verbonden met internet.

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Zodra u modules op uw apparaat hebt geïmplementeerd, u ze allemaal bekijken in de sectie **Azure IoT Hub.** Selecteer de pijl naast uw IoT Edge-apparaat om het uit te vouwen. Alle momenteel draaiende modules worden weergegeven.

Als u onlangs nieuwe modules op een apparaat hebt geïmplementeerd, plaatst u de plaats boven de sectiekop **Azure IoT Hub-apparaten** en selecteert u het vernieuwingspictogram om de weergave bij te werken.

Klik met de rechtermuisknop op de naam van een module om de moduletwee weer te geven en te bewerken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IoT Edge-modules op schaal met Behulp van Visual Studio Code](how-to-deploy-monitor.md)

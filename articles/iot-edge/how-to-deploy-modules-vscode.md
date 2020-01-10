---
title: Modules implementeren vanuit Visual Studio code-Azure IoT Edge
description: Gebruik Visual Studio code met de Azure IoT-Hulpprogram Ma's om een IoT Edge module van uw IoT Hub naar uw IoT Edge-apparaat te pushen, zoals geconfigureerd door een implementatie manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/8/2019
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8c90c2a13d111d01a1e7a1bd9b63da180621ded
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772088"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Azure IoT Edge-modules van Visual Studio Code implementeren

Als u IoT Edge modules met uw zakelijke logica maken, wilt u deze implementeren op uw apparaten te werken aan de rand. Als u meerdere modules die samenwerken om te verzamelen en verwerken van gegevens hebt, kunt u ze in één keer implementeren en declareert de routeringsregels waarmee ze zijn verbonden.

In dit artikel laat zien hoe een manifest van de implementatie JSON maken en vervolgens pusht u de implementatie naar een IoT Edge-apparaat met dat bestand. Voor informatie over het maken van een implementatie die is gericht op meerdere apparaten op basis van hun gedeelde labels, raadpleegt [u IOT Edge modules implementeren op schaal met behulp van Visual Studio code](how-to-deploy-monitor-vscode.md).

## <a name="prerequisites"></a>Vereisten

* Een [IoT-hub](../iot-hub/iot-hub-create-through-portal.md) in uw Azure-abonnement.
* Een [IoT Edge-apparaat](how-to-register-device.md#register-with-visual-studio-code) met IoT Edge-runtime geïnstalleerd.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) voor Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Een manifest van de implementatie configureren

Het manifest voor een implementatie is een JSON-document waarin wordt beschreven welke modules te implementeren, hoe gegevens stromen tussen de modules, en de gewenste eigenschappen van de moduledubbels. Zie voor meer informatie over hoe implementatie werk manifesten en hoe ze worden gemaakt, [te begrijpen hoe IoT Edge-modules kunnen worden gebruikt, geconfigureerd en opnieuw gebruikt](module-composition.md).

Voor het implementeren van modules met behulp van Visual Studio Code, sla het manifest implementatie lokaal als een. JSON-bestand. Wanneer u de opdracht uit om de configuratie van toepassing op het apparaat uitvoert, wordt u het pad in de volgende sectie.

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

## <a name="sign-in-to-access-your-iot-hub"></a>Meld u aan voor toegang tot uw IoT-hub

De Azure IoT-uitbreidingen voor Visual Studio Code kunt u bewerkingen uitvoeren met uw IoT-hub. Voor deze bewerkingen om te werken, moet u zich aanmelden bij uw Azure-account en selecteer de IoT-hub die u bezig bent.

1. Open in Visual Studio Code, de **Explorer** weergeven.

1. Vouw de sectie **Azure-IOT hub** aan de onderkant van de Explorer uit.

   ![De sectie Azure-IoT Hub uitvouwen](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Klik in de koptekst van de Azure- **IOT hub** op de sectie **..** .. Als u het beletselteken niet ziet, Beweeg de muisaanwijzer over de header.

1. Kies **IoT-Hub selecteren**.

1. Als u niet bent aangemeld bij uw Azure-account, volg de aanwijzingen om dit te doen.

1. Selecteer uw Azure-abonnement.

1. Selecteer uw IoT-hub.

## <a name="deploy-to-your-device"></a>Uw apparaat implementeren

U implementeren modules naar uw apparaat door het toepassen van het manifest implementatie die u hebt geconfigureerd met de modulegegevens.

1. Vouw in de weer gave Visual Studio code Explorer het gedeelte **Azure IOT hub** uit en vouw vervolgens het knoop punt **apparaten** uit.

1. Klik met de rechter muisknop op het IoT Edge apparaat dat u wilt configureren met het implementatie manifest.

    > [!TIP]
    > Als u wilt controleren of het apparaat dat u hebt gekozen een IoT Edge apparaat is, selecteert u dit om de lijst met modules uit te vouwen en de aanwezigheid van **$edgeHub** en **$edgeAgent**te controleren. Elk IoT Edge apparaat bevat deze twee modules.

1. Selecteer **implementatie voor één apparaat maken**.

1. Navigeer naar de implementatie van JSON-manifestbestand die u wilt gebruiken en klikt u op **Selecteer Edge-implementatie Manifest**.

   ![Manifest van de Edge-implementatie selecteren](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

De resultaten van uw implementatie zijn vermeld in de VS Code-uitvoer. Geslaagde implementaties worden toegepast binnen een paar minuten als het doelapparaat wordt uitgevoerd en die zijn verbonden met internet.

## <a name="view-modules-on-your-device"></a>Modules weergeven op uw apparaat

Zodra u modules hebt geïmplementeerd op uw apparaat, kunt u deze bekijken in de sectie **Azure IOT hub** . Selecteer de pijl naast uw IoT Edge-apparaat uit te vouwen. De huidige actieve modules worden weergegeven.

Als u onlangs nieuwe modules geïmplementeerd op een apparaat, Beweeg de muisaanwijzer over de **Azure IoT Hub-apparaten** sectie header en selecteer het vernieuwingspictogram op de weergave bijwerken.

Met de rechtermuisknop op de naam van een module bekijken en bewerken van de moduledubbel.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren en bewaken van IOT Edge modules op schaal met Visual Studio code](how-to-deploy-monitor.md)

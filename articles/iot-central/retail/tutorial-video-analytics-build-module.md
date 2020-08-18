---
title: Zelfstudie - De modules voor livevideoanalyse van IoT Edge aanpassen
description: In deze zelf studie leert u hoe u de gatewaymodules voor livevideoanalyse kunt wijzigen en maken die door de toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse wordt gebruikt.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d21eb8d8d79ec04f0f7e766b4eeb370811553e64
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037901"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Zelfstudie: De gatewaymodules voor livevideoanalyse wijzigen en bouwen

In deze zelfstudie leert u hoe u de code van de IoT Edge-module kunt aanpassen voor de LVA-modules (Live Video Analytics).

In de voorgaande zelfstudies worden vooraf gemaakte installatiekopieën van de modules gebruikt.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van de stappen in deze zelfstudie hebt u het volgende nodig:

* [Node.js](https://nodejs.org/en/download/) V10 of hoger
* [Visual Studio Code](https://code.visualstudio.com/Download) met de [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)-extensie geïnstalleerd
* [Docker](https://www.docker.com/products/docker-desktop)-engine
* Een [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) om uw versies van de modules te hosten.
* Een [Azure Media Services](https://docs.microsoft.com/azure/media-services/)-account. Als u de vorige zelfstudies hebt voltooid, kunt u het eerder gemaakte account opnieuw gebruiken.

## <a name="clone-the-repository"></a>De opslagplaats klonen

Als u de opslagplaats nog niet hebt gekloond, gebruikt u de volgende opdracht om deze te klonen op een geschikte locatie op uw lokale computer:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Open de lokale *live-video-analytics*-opslagplaatsmap met VS Code.

## <a name="edit-the-deploymentamd64json-file"></a>Het bestand deployment.amd64.json bewerken

1. Als u dit nog niet hebt gedaan, maakt u een map met de naam *ref-apps/lva-edge-iot-central-gateway/storage* in het lokale exemplaar van de opslagplaats **lva-gateway**. Deze map wordt genegeerd door Git, zodat u niet per ongeluk vertrouwelijke informatie kunt inchecken.
1. Kopieer het bestand *deployment.amd64. json* uit de map *setup* naar de map *storage*.
1. Open in VS Code het bestand *storage/deployment.amd64.json*.
1. Bewerk de sectie `registryCredentials` om uw Azure Container Registry-referenties toe te voegen.
1. Bewerk de modulesectie `LvaEdgeGatewayModule` om de naam van uw installatiekopie en de naam van uw AMS-account toe te voegen in de `env:amsAccountName:value`.
1. Bewerk de modulesectie `lvaYolov3` en voeg de naam van uw installatiekopie toe.
1. Bewerk de modulesectie `lvaEdge` en voeg de naam van uw installatiekopie toe.
1. Zie [Een toepassing voor videoanalyse maken in Azure IoT Central](tutorial-video-analytics-create-app.md) voor meer informatie over het volt ooien van de configuratie.

## <a name="build-the-code"></a>De code bouwen

1. Voordat u de code voor de eerste keer probeert te maken, gebruikt u de VS Code-terminal om de opdracht `npm install` uit te voeren. Met deze opdracht worden de vereiste pakketten geïnstalleerd en worden de installatiescripts uitgevoerd.

    > [!TIP]
    > Als u een nieuwere versie van de GitHub opslagplaats ophaalt, verwijdert u de map *node_modules* en voert u `npm install` opnieuw uit.

1. Bewerk het bestand *./setup/imageConfig.json* om de installatiekopie met de naam op basis van de naam van het containerregister bij te werken:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Gebruik de VS Code-terminal om de opdracht `docker login [your server].azurecr.io` uit te voeren. Gebruik dezelfde referenties die u hebt opgegeven in het distributiemanifest voor de modules.

1. Gebruik de VS Code-terminal om de opdracht **npm version patch** uit te voeren. Met dit buildscript worden de installatiekopieën geïmplementeerd in het containerregister. In de uitvoer van het VS code-terminalvenster wordt weergegeven of de build is geslaagd.

1. De versie van de installatiekopie **LvaEdgeGatewayModule** wordt verhoogd telkens wanneer de build is voltooid. U moet deze versie gebruiken in het manifestbestand voor de implementatie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse en de LVA IoT Edge-modules, is de voorgestelde volgende stap meer informatie over:

> [!div class="nextstepaction"]
> [Oplossingen voor de detailhandel bouwen met Azure IoT Central](overview-iot-central-retail.md)

---
title: Remote Monitoring solution import Edge package - Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een IoT Edge-pakket importeert in de versneller van de oplossing voor externe bewaking
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442936"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Een IoT Edge-pakket importeren in de verbetering voor de externe bewakingsoplossing

In een implementatiemanifest worden de modules gedefinieerd die moeten worden geïmplementeerd op een IoT Edge-apparaat. In dit artikel wordt ervan uitgegaan dat een ontwikkelaar in uw organisatie al een implementatiemanifest heeft gemaakt. Zie een van de volgende iot edge-artikelen voor meer informatie over hoe een ontwikkelaar een manifest maakt:

- [Azure IoT Edge-modules implementeren vanuit de Azure-portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Azure IoT Edge-modules implementeren met Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Azure IoT Edge-modules implementeren vanuit Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Een ontwikkelaar maakt en test een implementatiemanifest in een ontwikkelomgeving. Wanneer u er klaar voor bent, u het manifest importeren in uw versneller met externe bewakingsoplossingen.

## <a name="export-a-manifest"></a>Een manifest exporteren

Gebruik de Azure-portal om het implementatiemanifest te exporteren vanuit uw ontwikkelomgeving:

1. Navigeer in de Azure-portal naar de IoT-hub die u gebruikt om uw IoT Edge-apparaten te ontwikkelen en te testen. Klik op **IoT Edge** en vervolgens ![op **IoT Edge-implementaties:** IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klik op de implementatie met de implementatieconfiguratie die u wilt gebruiken. De pagina **Implementatiedetails** wordt weergegeven: ![de implementatiegegevens van IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klik **op IoT Edge-manifest downloaden:** ![implementatiemanifest downloaden](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Sla het JSON-bestand op als een lokaal bestand genaamd **deploymentmanifest.json**.

Nu hebt u een bestand dat het implementatiemanifest bevat. In het volgende gedeelte importeert u dit manifest als een pakket in de oplossing voor externe bewaking.

## <a name="import-a-package"></a>Een pakket importeren

Volg de onderstaande stappen om een Edge-implementatiemanifest als pakket in uw oplossing te importeren:

1. Navigeren naar de pagina **Pakketten** op de ![pagina WebGebruikersinterface voor externe controle: pakketten](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klik **op + Nieuw pakket,** kies Edge **Manifest** als het pakkettype en klik op **Bladeren** om het **bestand deploymentmanifest.json** te selecteren dat u in de vorige sectie hebt opgeslagen: ![Manifest selecteren](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Klik **op Uploaden** om het pakket ![toe te voegen aan uw oplossing voor externe controle: geüpload pakket](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

U hebt nu een IoT Edge-implementatiemanifest als pakket geüpload. Op de pagina **Implementaties** u dit pakket implementeren op uw verbonden IoT Edge-apparaten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u modules implementeren op een IoT Edge-apparaat vanuit de oplossing voor externe bewaking, is de volgende stap om meer te weten te komen over [IoT Edge.](../iot-edge/about-iot-edge.md)

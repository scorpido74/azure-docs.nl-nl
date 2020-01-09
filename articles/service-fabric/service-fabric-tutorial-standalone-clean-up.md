---
title: Een zelfstandig cluster opschonen
description: In deze zelf studie leert u hoe u AWS of Azure-resources opschoont in uw zelfstandige Service Fabric cluster.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639017"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Zelfstudie: Zelfstandig cluster opschonen

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze zelfstudie reeks maakt u een zelfstandig cluster dat wordt gehost op AWS of Azure en hoe u een toepassing kunt installeren.

Deze zelfstudie is deel vier een serie. In dit deel van de zelf studie ziet u hoe u de AWS-of Azure-resources opschoont die u hebt gemaakt om uw Service Fabric-cluster te hosten.

In deel vier van de serie leert u het volgende:

> [!div class="checklist"]
> * Service Fabric-cluster opschonen
> * Uw AWS-of Azure-resources opschonen

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric-cluster opschonen

1. RDP in de virtuele machine die u hebt gebruikt om Service Fabric te installeren.
2. Open PowerShell.
3. Ga naar de uitgepakte map uit de tweede zelfstudie.
4. Voer de volgende opdracht uit om het Service Fabric-cluster te verwijderen:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Voer `Y` als u daarom wordt gevraagd of de uitvoer is geslaagd, met uw eigen IP-adressen die worden vervangen in:

  ```powershell
  Best Practices Analyzer completed successfully.
  Removing configuration from machine 172.31.21.141
  Removing configuration from machine 172.31.27.1
  Removing configuration from machine 172.31.20.163
  Configuration removed from machine 172.31.21.141
  Configuration removed from machine 172.31.27.1
  Configuration removed from machine 172.31.20.163
  The cluster is successfully removed.
  ```

## <a name="clean-up-aws-resources"></a>AWS-resources opschonen

1. Meld u aan bij uw AWS-account.
2. Ga naar de EC2-console.
3. Selecteer de drie knooppunten die u hebt gemaakt in deel een van de zelfstudie.
4. Klik op **acties** > **exemplaar status** > **beëindigen**.

## <a name="clean-up-azure-resources"></a>Azure-resources opschonen

1. Meld u aan bij Azure Portal.
2. Ga naar de sectie **virtual machines** .
3. Schakel de selectie vakjes in voor de drie knoop punten die u in deel één van de zelf studie hebt gemaakt.
4. Klik op **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deel vier van de reeks hebt u geleerd hoe u de resources opschoont die zijn gemaakt in eerdere stappen.

> [!div class="checklist"]
> * Uw resources opschonen

> [!div class="nextstepaction"]
> [Terug naar het begin](service-fabric-tutorial-standalone-create-infrastructure.md)

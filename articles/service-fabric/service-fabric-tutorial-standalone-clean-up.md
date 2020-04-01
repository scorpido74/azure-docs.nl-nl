---
title: Een zelfstandig cluster opschonen
description: In deze zelfstudie leert u hoe u AWS- of Azure-bronnen opschonen in uw standalone Service Fabric-cluster.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639017"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Zelfstudie: Zelfstandig cluster opschonen

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze zelfstudiereeks maakt u een zelfstandig cluster dat wordt gehost op AWS of Azure en installeert u er een toepassing in.

Deze zelfstudie is deel vier een serie. In dit deel van de zelfstudie ziet u hoe u de AWS- of Azure-resources opkunt die u hebt gemaakt om uw cluster servicestructuur te hosten.

In deel vier van de serie leert u het volgende:

> [!div class="checklist"]
> * Service Fabric-cluster opschonen
> * Uw AWS- of Azure-bronnen opschonen

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric-cluster opschonen

1. RDP in de VM die u hebt gebruikt om Service Fabric te installeren.
2. Open PowerShell.
3. Ga naar de uitgepakte map uit de tweede zelfstudie.
4. Voer de volgende opdracht uit om het Service Fabric-cluster te verwijderen:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Voer `Y` in wanneer u daarom wordt gevraagd, als deze is geslaagd, ziet uw uitvoer er als volgt uit, waarbij uw eigen IP-adressen zijn vervangen door:

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
4. Klik op **Instantiestatus Acties** > **Beëindigen** > **Terminate**.

## <a name="clean-up-azure-resources"></a>Azure-bronnen opschonen

1. Meld u aan bij Azure Portal.
2. Ga naar de sectie **Virtuele machines.**
3. Schakel selectievakjes in voor de drie knooppunten die u in deel één van de zelfstudie hebt gemaakt.
4. Klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deel vier van de reeks hebt u geleerd hoe u de resources opschoont die zijn gemaakt in eerdere stappen.

> [!div class="checklist"]
> * Uw resources opschonen

> [!div class="nextstepaction"]
> [Terug naar het begin](service-fabric-tutorial-standalone-create-infrastructure.md)

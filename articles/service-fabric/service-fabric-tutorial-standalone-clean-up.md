---
title: Zelfstandig cluster opschonen
description: In deze zelfstudie leert u hoe u AWS- of Azure-resources kunt opschonen in uw zelfstandige Service Fabric-cluster.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "75639017"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Zelfstudie: Zelfstandig cluster opschonen

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze reeks zelfstudies maakt u een zelfstandig cluster dat in AWS of Azure wordt gehost. Vervolgens installeert u een toepassing in het cluster.

Deze zelfstudie is deel vier een serie. In dit deel van de reeks wordt uitgelegd hoe u de AWS- of Azure-resources kunt opschonen die u hebt gemaakt voor het hosten van uw Service Fabric-cluster.

In deel vier van de serie leert u het volgende:

> [!div class="checklist"]
> * Service Fabric-cluster opschonen
> * Uw AWS- of Azure-resources opschonen

## <a name="clean-up-service-fabric-cluster"></a>Service Fabric-cluster opschonen

1. Ga met behulp van RDP naar de VM die u hebt gebruikt voor de installatie van Service Fabric.
2. Open PowerShell.
3. Ga naar de uitgepakte map uit de tweede zelfstudie.
4. Voer de volgende opdracht uit om het Service Fabric-cluster te verwijderen:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Voer `Y` in als dat wordt gevraagd. Als de opdracht is gelukt, ziet de uitvoer er als volgt uit, maar dan met uw eigen IP-adressen:

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
4. Klik op **Actions** > **Instance State** > **Terminate**.

## <a name="clean-up-azure-resources"></a>Azure-resources opschonen

1. Meld u aan bij Azure Portal.
2. Ga naar de sectie **Virtual Machines**.
3. Schakel de selectievakjes in bij de drie knooppunten die u hebt gemaakt in deel een van de zelfstudie.
4. Klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deel vier van de reeks hebt u geleerd hoe u de resources opschoont die zijn gemaakt in eerdere stappen.

> [!div class="checklist"]
> * Uw resources opschonen

> [!div class="nextstepaction"]
> [Terug naar het begin](service-fabric-tutorial-standalone-create-infrastructure.md)

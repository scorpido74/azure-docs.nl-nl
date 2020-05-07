---
title: De configuratie van een Azure Service Fabric-cluster upgraden
description: Meer informatie over het bijwerken van de configuratie waarmee een Service Fabric cluster in azure wordt uitgevoerd met behulp van een resource manager-sjabloon.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: c2973428354f101b5b546128b08bf67587923a8e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793071"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>De configuratie van een cluster in azure upgraden 

In dit artikel wordt beschreven hoe u de verschillende infrastructuur instellingen voor uw Service Fabric cluster kunt aanpassen. Voor clusters die worden gehost in azure, kunt u instellingen aanpassen via de [Azure Portal](https://portal.azure.com) of met behulp van een Azure Resource Manager sjabloon.

> [!NOTE]
> Niet alle instellingen zijn beschikbaar in de portal en het is een [Best practice om deze aan te passen met behulp van een Azure Resource Manager sjabloon](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Portal is alleen voor Service Fabric-scenario van Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Cluster instellingen aanpassen met behulp van Resource Manager-sjablonen
Azure-clusters kunnen worden geconfigureerd via de JSON Resource Manager-sjabloon. Zie [configuratie-instellingen voor clusters](service-fabric-cluster-fabric-settings.md)voor meer informatie over de verschillende instellingen. De volgende stappen laten zien hoe u een nieuwe instelling *MaxDiskQuotaInMB* kunt toevoegen aan de sectie *Diagnostische gegevens* met behulp van Azure resource Explorer.

1. Ga naar https://resources.azure.com
2. Navigeer naar uw abonnement door **abonnementen** -> **\< **uit te breiden> ->  **resourceGroups** -> **\<naar uw resource groep **te gaan> ->  **providers** -> van**micro soft. ServiceFabric** -> **clusters** -> **\<uw cluster naam>**
3. Selecteer in de rechter bovenhoek de optie **lezen/schrijven.**
4. Selecteer **bewerken** en werk het `fabricSettings` JSON-element bij en voeg een nieuw element toe:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

U kunt cluster instellingen ook aanpassen op een van de volgende manieren met Azure Resource Manager:

- Gebruik de [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) om de Resource Manager-sjabloon te exporteren en bij te werken.
- Gebruik [Power shell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) om de Resource Manager-sjabloon te exporteren en bij te werken.
- Gebruik de [Azure cli](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) om de Resource Manager-sjabloon te exporteren en bij te werken.
- Gebruik de opdrachten Azure PowerShell [set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) en [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) om de instelling rechtstreeks te wijzigen.
- Gebruik de opdracht Azure CLI [AZ EB cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) om de instelling rechtstreeks te wijzigen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md).
* Meer informatie over hoe u [uw cluster in-en uitschaalt](service-fabric-cluster-scale-in-out.md).

---
title: De configuratie van een Azure Service Fabric-cluster bijwerken
description: Meer informatie over het upgraden van de configuratie waarop een cluster van Servicefabric in Azure wordt uitgevoerd met behulp van een resourcemanagersjabloon.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463288"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>De configuratie van een cluster in Azure bijwerken 

In dit artikel wordt beschreven hoe u de verschillende fabric-instellingen voor uw Service Fabric-cluster aanpassen. Voor clusters die in Azure worden gehost, u instellingen aanpassen via de [Azure-portal](https://portal.azure.com) of met behulp van een Azure Resource Manager-sjabloon.

> [!NOTE]
> Niet alle instellingen zijn beschikbaar in de portal en het is een [aanbevolen manier om deze aan te passen met behulp van een Azure Resource Manager-sjabloon;](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) Portal is alleen voor Service Fabric Dev\Test scenario's.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Clusterinstellingen aanpassen met resourcebeheersjablonen
Azure-clusters kunnen worden geconfigureerd via de JSON Resource Manager-sjabloon. Zie [Configuratie-instellingen voor clusters voor](service-fabric-cluster-fabric-settings.md)meer informatie over de verschillende instellingen. In de onderstaande stappen ziet u bijvoorbeeld hoe u een nieuwe instelling *MaxDiskQuotaInMB* toevoegt aan de sectie *Diagnostische gegevens* met Azure Resource Explorer.

1. Ga naar https://resources.azure.com
2. Navigeer naar uw abonnement door **abonnementen** -> **\< **  -> uit te breiden Uw abonnement>**resourceGroepen** -> **\<Uw Resourcegroep>**  ->  **providers** -> **Microsoft.ServiceFabric-clusters** -> **clusters** -> **\<Uw clusternaam>**
3. Selecteer **lezen/schrijven** in de rechterbovenhoek.
4. Selecteer **Bewerken** en `fabricSettings` werk het JSON-element bij en voeg een nieuw element toe:

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

U ook clusterinstellingen op een van de volgende manieren aanpassen met Azure Resource Manager:

- Gebruik de [Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) om de resourcemangersjabloon te exporteren en bij te werken.
- Gebruik [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) om de resourcemanagersjabloon te exporteren en bij te werken.
- Gebruik de [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) om de sjabloon Resourcebeheer te exporteren en bij te werken.
- Gebruik de opdrachten Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) en [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) om de instelling rechtstreeks te wijzigen.
- Gebruik de azure CLI [az sf-clusterinstellingsopdrachten](https://docs.microsoft.com/cli/azure/sf/cluster/setting) om de instelling rechtstreeks te wijzigen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [clusterinstellingen van Service Fabric](service-fabric-cluster-fabric-settings.md).
* Meer informatie over het [schalen van uw cluster in en uit](service-fabric-cluster-scale-up-down.md).

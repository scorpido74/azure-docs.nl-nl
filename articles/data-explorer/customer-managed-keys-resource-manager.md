---
title: Klantbeheersleutels configureren in Azure Data Explorer met de sjabloon Azure Resource Manager
description: In dit artikel wordt beschreven hoe u versleuteling van door de klant beheerde sleutels op uw gegevens configureren in Azure Data Explorer met behulp van de sjabloon Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 454a80089b5f74d4a70015ffcd03d0212e8c08a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297922"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Door de klant beheerde sleutels configureren met de sjabloon Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sjabloon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

In deze sectie configureert u door de klant beheerde sleutels met Azure Resource Manager-sjablonen. Standaard maakt Azure Data Explorer-versleuteling gebruik van door Microsoft beheerde sleutels. Configureer in deze stap uw Azure Data Explorer-cluster om door de klant beheerde sleutels te gebruiken en geef de sleutel op die aan het cluster moet worden gekoppeld.

U de sjabloon Azure Resource Manager implementeren met behulp van de Azure-portal of met PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>De belangrijkste versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het cluster bijwerken om de nieuwe versie te gebruiken. Bel eerst `Get-AzKeyVaultKey` om de nieuwste versie van de sleutel te krijgen. Werk vervolgens de eigenschappen van de sleutelkluis van het cluster bij om de nieuwe versie van de sleutel te gebruiken, zoals wordt weergegeven in [Versleuteling configureren met door de klant beheerde sleutels.](#configure-encryption-with-customer-managed-keys)

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in Azure](security.md)
* [Beheerde identiteiten configureren voor uw Azure Data Explorer-cluster](managed-identities.md)
* [Beveilig uw cluster in Azure Data Explorer - Azure-portal](manage-cluster-security.md) door versleuteling in rust in te schakelen.
* [Door de klant beheerde sleutels configureren met C #](customer-managed-keys-csharp.md)


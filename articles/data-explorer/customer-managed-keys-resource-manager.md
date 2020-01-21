---
title: Door de klant beheerde sleutels configureren in azure Data Explorer met behulp van de Azure Resource Manager sjabloon
description: In dit artikel wordt beschreven hoe u door de klant beheerde sleutels versleuteling kunt configureren voor uw gegevens in azure Data Explorer met behulp van de Azure Resource Manager sjabloon.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d0f7085342f972f227fc549c423672296697d7de
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281254"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Door de klant beheerde sleutels configureren met behulp van de Azure Resource Manager sjabloon

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sjabloon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Versleuteling configureren met door de klant beheerde sleutels

In deze sectie configureert u door de klant beheerde sleutels met behulp van Azure Resource Manager sjablonen. Data Explorer versleuteling van Azure maakt standaard gebruik van door micro soft beheerde sleutels. In deze stap configureert u uw Azure Data Explorer-cluster voor het gebruik van door de klant beheerde sleutels en geeft u de sleutel op die u aan het cluster wilt koppelen.

U kunt de Azure Resource Manager-sjabloon implementeren met behulp van de Azure Portal of met behulp van Power shell.

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

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het cluster bijwerken voor gebruik van de nieuwe versie. Roep eerst `Get-AzKeyVaultKey` aan om de nieuwste versie van de sleutel op te halen. Werk vervolgens de sleutel kluis eigenschappen van het cluster bij om de nieuwe versie van de sleutel te gebruiken, zoals wordt weer gegeven in [versleuteling configureren met door de klant beheerde sleutels](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Explorer-clusters beveiligen in azure](security.md)
* [Beheerde identiteiten voor uw Azure Data Explorer-cluster configureren](managed-identities.md)
* [Beveilig uw cluster in Azure Data Explorer-Azure Portal](manage-cluster-security.md) door versleuteling op rest in te scha kelen.
* [Door de klant beheerde sleutels configureren metC#](customer-managed-keys-csharp.md)


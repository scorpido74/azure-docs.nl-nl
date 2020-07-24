---
title: Een sjabloon specificatie met gekoppelde sjablonen maken
description: Meer informatie over het maken van een sjabloon spec met gekoppelde sjablonen.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: c2648cb8a71be709406f314d02a226ed097be6f0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096684"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Zelf studie: een sjabloon specificatie met gekoppelde sjablonen maken (preview)

Meer informatie over het maken van een [sjabloon specificatie](template-specs.md) met een [gekoppelde sjabloon](linked-templates.md#linked-template). U kunt sjabloon specificaties gebruiken om ARM-sjablonen te delen met andere gebruikers in uw organisatie. In dit artikel wordt beschreven hoe u een sjabloon specificatie maakt voor het inpakken van een hoofd sjabloon en gekoppelde sjablonen met de nieuwe `relativePath` eigenschap van de [implementatie resource](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> De sjabloon specificaties zijn momenteel beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u [zich aanmelden voor de preview-versie](https://aka.ms/templateSpecOnboarding).

## <a name="create-linked-templates"></a>Gekoppelde sjablonen maken

Maak de hoofd sjabloon en de gekoppelde sjabloon.

Als u een sjabloon wilt koppelen, moet u een implementatie van een [resource](/azure/templates/microsoft.resources/deployments) toevoegen aan uw hoofd sjabloon. Geef in de `templateLink` eigenschap het relatieve pad van de gekoppelde sjabloon op in overeenstemming met het pad van de bovenliggende sjabloon.

De gekoppelde sjabloon heet **linkedTemplate.jsop**en wordt opgeslagen in een submap met de naam **artefacten** in het pad waar de hoofd sjabloon is opgeslagen.  U kunt een van de volgende waarden gebruiken voor de relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

Als er een andere linkedTemplate2.jsis aangeroepen vanuit linkedTemplate.jsen linkedTemplate2.jsop is opgeslagen in dezelfde submap voor artefacten, is de relativePath die is opgegeven in linkedTemplate.js, **linkedTemplate2.jsop**.

1. Maak de hoofd sjabloon met de volgende JSON. Sla de hoofd sjabloon **op alsazuredeploy.jsop** de lokale computer. In deze zelf studie wordt ervan uitgegaan dat u hebt opgeslagen in een pad **c:\Templates\linkedTS\azuredeploy.js** , maar u kunt een wille keurig pad gebruiken.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > De apiVersion van `Microsoft.Resources/deployments` moet 2020-06-01 of hoger zijn.

1. Maak een map met de naam **artefacten** in de map waarin de hoofd sjabloon is opgeslagen.
1. Maak de gekoppelde sjabloon met de volgende JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
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
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Sla de sjabloon **op alslinkedTemplate.js** in de map **artefacten** .

## <a name="create-template-spec"></a>Sjabloon specificatie maken

Sjabloon specificaties worden opgeslagen in resource groepen.  Maak een resource groep en maak vervolgens een sjabloon specificatie met het volgende script. De naam van de sjabloon specificatie is **webspec**.

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

Wanneer u klaar bent, kunt u de sjabloon specificatie bekijken vanuit de Azure Portal of met behulp van de volgende cmdlet:

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>Sjabloon specificatie implementeren

U kunt nu de sjabloon specificatie implementeren. het implementeren van de sjabloon spec is net als het implementeren van de sjabloon die het bevat, met uitzonde ring van de resource-ID van de sjabloon specificatie. U gebruikt dezelfde implementatie opdrachten, en als dat nodig is, geeft u de parameter waarden door voor de sjabloon specificatie.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon](template-specs-deploy-linked-template.md)voor meer informatie over het implementeren van een sjabloon specificatie als een gekoppelde sjabloon.

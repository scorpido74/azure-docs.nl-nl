---
title: Sjabloonspecificatie maken en implementeren
description: Meer informatie over het maken van ene sjabloonspecificatie op basis van een ARM-sjabloon. Implementeer de sjabloonspecificatie vervolgens naar een resourcegroep in uw abonnement.
author: tfitzmac
ms.date: 07/20/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: b2667e63f7cac5d1e3ad8475501ff909e8f6f3c1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101022"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Quickstart: Sjabloonspecificatie maken en implementeren (preview)

In deze quickstart wordt uitgelegd hoe u een Azure Resource Manager-sjabloon (ARM-sjabloon) inpakt in een [sjabloonspecificatie](template-specs.md) en hoe u de sjabloonspecificatie vervolgens implementeert. Uw sjabloonspecificatie bevat een ARM-sjabloon die een opslagaccount implementeert.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

> [!NOTE]
> Sjabloonspecificaties is momenteel beschikbaar als preview-versie. Als u deze functie wilt gebruiken, moet u zich [inschrijven op de wachtlijst](https://aka.ms/templateSpecOnboarding).
>
> Als u na de wachtlijst wordt goedgekeurd, ontvangt u instructies om de preview-versie van de PowerShell-module te installeren.

## <a name="create-template-spec"></a>Sjabloonspecificatie maken

De sjabloonspecificatie is een nieuw resourcetype met de naam **Microsoft.Resources/templateSpecs**. U kunt Azure PowerShell of een ARM-sjabloon gebruiken om uw sjabloonspecificatie te maken. Voor alle opties hebt u een ARM-sjabloon nodig die is verpakt in de sjabloonspecificatie.

Voor PowerShell wordt de ARM-sjabloon als parameter aan de opdracht doorgegeven. De ARM-sjabloon die in de sjabloonspecificatie wordt opgenomen, wordt ingesloten in de definitie van de sjabloonspecificatie.

Deze opties worden hieronder weergegeven.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Wanneer u een sjabloonspecificatie met PowerShell maakt, kunt u een lokale sjabloon doorgeven. Kopieer de volgende sjabloon en sla deze lokaal op in een bestand met de naam **azuredeploy.json**. In deze quickstart wordt ervan uitgegaan dat u hebt opgeslagen naar het pad **c:\Templates\azuredeploy.json**, maar u kunt elk pad gebruiken.

   :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Maak een nieuwe resourcegroep voor de sjabloonspecificatie.

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. Maak vervolgens de sjabloonspecificatie in die resourcegroep. U geeft de nieuwe sjabloonspecificatie de naam **storageSpec**.

   ```powershell
   New-AzTemplateSpec `
     -ResourceGroupName templateSpecRG `
     -Name storageSpec `
     -Version "1.0.0.0" `
     -Location westus2 `
     -TemplateJsonFile "c:\Templates\azuredeploy.json"
   ```

# <a name="arm-template"></a>[Azure VPN-gateway](#tab/azure-resource-manager)

1. Wanneer u een ARM-sjabloon gebruikt om de sjabloonspecificatie te maken, wordt de sjabloon ingesloten in de resourcedefinitie. Kopieer de volgende sjabloon en sla deze lokaal op als **azuredeploy.json**. In deze quickstart wordt ervan uitgegaan dat u hebt opgeslagen naar het pad **c:\Templates\azuredeploy.json**, maar u kunt elk pad gebruiken.

   > [!NOTE]
   > In de ingesloten sjabloon moeten alle vierkante haken links worden voorafgegaan (escaped) door een tweede vierkante haak links. Gebruik `[[` in plaats van `[`.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/templateSpecs",
               "apiVersion": "2019-06-01-preview",
               "name": "storageSpec",
               "location": "westus2",
               "properties": {
                   "displayName": "Storage template spec"
               },
               "tags": {},
               "resources": [
                   {
                       "type": "versions",
                       "apiVersion": "2019-06-01-preview",
                       "name": "1.0.0.1",
                       "location": "westus2",
                       "dependsOn": [ "storageSpec" ],
                       "properties": {
                           "template": {
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
                                       "defaultValue": "[[resourceGroup().location]",
                                       "metadata": {
                                           "description": "Location for all resources."
                                       }
                                   }
                               },
                               "variables": {
                                   "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                               },
                               "resources": [
                                   {
                                       "type": "Microsoft.Storage/storageAccounts",
                                       "apiVersion": "2019-04-01",
                                       "name": "[[variables('storageAccountName')]",
                                       "location": "[[parameters('location')]",
                                       "sku": {
                                           "name": "[[parameters('storageAccountType')]"
                                       },
                                       "kind": "StorageV2",
                                       "properties": {}
                                   }
                               ],
                               "outputs": {
                                   "storageAccountName": {
                                       "type": "string",
                                       "value": "[[variables('storageAccountName')]"
                                   }
                               }
                           }
                       },
                       "tags": {}
                   }
               ]
           }
       ],
       "outputs": {}
   }
   ```

1. Gebruik Azure CLI of PowerShell om een nieuwe resourcegroep te maken.

   ```azurecli
   az group create \
     --name templateSpecRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. Implementeer uw sjabloon met Azure CLI of PowerShell.

   ```azurecli
   az deployment group create \
     --name templateSpecRG \
     --template-file "c:\Templates\azuredeploy.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName templateSpecRG `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

---

## <a name="deploy-template-spec"></a>Sjabloonspecificatie implementeren

U kunt nu de sjabloonspecificatie implementeren. De sjabloonspecificatie wordt op dezelfde manier geïmplementeerd als de sjabloon in de sjabloonspecificatie. Het enige verschil is dat u de resource-id van de sjabloonspecificatie doorgeeft. U gebruikt dezelfde implementatieopdrachten en geeft indien nodig de parameterwaarden voor de sjabloonspecificatie door.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Eerst maakt u een resourcegroep die het nieuwe opslagaccount bevat.

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Haal de resource-id op van de sjabloonspecificatie.

   ```azurepowershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0.0.0").Version.Id
   ```

1. Implementeer de sjabloonspecificatie.

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName demoRG
   ```

# <a name="arm-template"></a>[Azure VPN-gateway](#tab/azure-resource-manager)

1. Kopieer de volgende sjabloon en sla deze lokaal op in een bestand met de naam **storage.json**.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/deployments",
               "apiVersion": "2020-06-01",
               "name": "demo",
               "properties": {
                   "templateLink": {
                       "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0.0.0')]"
                   },
                   "parameters": {
                   },
                   "mode": "Incremental"
               }
           }
       ],
       "outputs": {}
   }
   ```

1. Gebruik Azure CLI of PowerShell om een nieuwe resourcegroep te maken voor het opslagaccount.

   ```azurecli
   az group create \
     --name storageRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. Implementeer uw sjabloon met Azure CLI of PowerShell.

   ```azurecli
   az deployment group create \
     --name storageRG \
     --template-file "c:\Templates\storage.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName storageRG `
     -TemplateFile "c:\Templates\storage.json"
   ```

---

## <a name="grant-access"></a>Toegang verlenen

Als u wilt dat andere gebruikers in uw organisatie uw sjabloonspecificatie implementeren, moet u hen leestoegang verlenen. U kunt de rol van lezer toewijzen aan een Azure AD-groep voor de resourcegroep die de sjabloonspecificaties bevat die u wilt delen. Zie [Zelfstudie: Verleen een groep toegang tot Azure-resources met behulp van Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resource wilt opschonen die u in deze quickstart hebt geïmplementeerd, verwijdert u beide resourcegroepen die u hebt gemaakt.

1. Selecteer Resourcegroep in het linkermenu van Azure Portal.

1. Voer in het veld Filteren op naam de naam van de resourcegroep (templateSpecRG en storageRG) in.

1. Selecteer de naam van de resourcegroep.

1. Selecteer Resourcegroep verwijderen in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

Zie [Een sjabloonspecificatie van een gekoppelde sjabloon maken](template-specs-create-linked.md) voor meer informatie over het maken van een sjabloonspecificatie die gekoppelde sjablonen bevat.

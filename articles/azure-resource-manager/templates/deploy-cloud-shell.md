---
title: Sjablonen implementeren met Cloud Shell
description: Gebruik Azure Resource Manager en Cloud Shell om resources te implementeren in Azure. De resources worden gedefinieerd in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d0b519955cb877f5fb9640cc0cf95a898e1743ef
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681439"
---
# <a name="deploy-arm-templates-from-cloud-shell"></a>ARM-sjablonen implementeren vanuit Cloud Shell

U kunt [Cloud shell](../../cloud-shell/overview.md) gebruiken om een Azure Resource Manager sjabloon (arm-sjabloon) te implementeren. U kunt een ARM-sjabloon implementeren die extern is opgeslagen, of een ARM-sjabloon die is opgeslagen op het lokale opslag account voor Cloud Shell.

U kunt implementeren in elk bereik. In dit artikel wordt beschreven hoe u implementeert in een resource groep.

## <a name="deploy-remote-template"></a>Externe sjabloon implementeren

Als u een externe sjabloon wilt implementeren, geeft u de URI van de sjabloon op exact dezelfde manier als bij elke externe implementatie. De externe sjabloon kan zich in een GitHub-opslag plaats of een extern opslag account beslaan.

1. Open de Cloud Shell prompt.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell openen":::

1. Gebruik de volgende opdrachten om de sjabloon te implementeren:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Een lokale sjabloon implementeren

Als u een lokale sjabloon wilt implementeren, moet u eerst uw sjabloon uploaden naar het opslag account dat is verbonden met uw Cloud Shell-sessie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer de Cloud Shell-resourcegroep. Het naampatroon is `cloud-shell-storage-<region>`.

   ![Resourcegroep selecteren](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Selecteer het opslagaccount voor Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Cloud Shell openen":::

1. Selecteer **Bestands shares** .

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Cloud Shell openen":::

1. Selecteer de standaard bestands share voor Cloud Shell. De bestands share heeft de naam indeling van `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Cloud Shell openen":::

1. Voeg een nieuwe map toe om uw sjablonen te bewaren. Selecteer de map.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Cloud Shell openen":::

1. Selecteer **Uploaden** .

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Cloud Shell openen":::

1. Zoek de sjabloon en upload deze.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Cloud Shell openen":::

1. Open de Cloud Shell prompt.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell openen":::

1. Ga naar de **clouddrive** -map. Ga naar de map die u hebt toegevoegd voor het houden van de sjablonen.

1. Gebruik de volgende opdrachten om de sjabloon te implementeren:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Volgende stappen

- Zie [resources implementeren met arm-sjablonen en Azure cli](deploy-cli.md) en [resources implementeren met arm-sjablonen en Azure PowerShell](deploy-powershell.md)voor meer informatie over implementatie opdrachten.
- Zie [arm sjabloon implementatie What-if-bewerking](template-deploy-what-if.md)voor een voor beeld van wijzigingen voordat u een sjabloon implementeert.

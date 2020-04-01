---
title: Zelfstudie - Een lokale Azure Resource Manager-sjabloon implementeren
description: Meer informatie over het implementeren van een Azure Resource Manager-sjabloon vanaf uw lokale computer
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8e3eb62fa52caeaa63808b6b9ea199bdff5c4da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082256"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Zelfstudie: Een lokale Azure Resource Manager-sjabloon implementeren

Meer informatie over het implementeren van een Azure Resource Manager-sjabloon vanuit uw lokale machine. Het duurt ongeveer **8 minuten** om te voltooien.

Deze tutorial is de eerste van een serie. Naarmate u door de reeks heen gaat, modulariseert u de sjabloon door een gekoppelde sjabloon te maken, slaat u de gekoppelde sjabloon op in een opslagaccount en beveiligt u de gekoppelde sjabloon met behulp van SAS-token en leert u hoe u een DevOp-pijplijn maakt om sjablonen te implementeren. Deze serie richt zich op het implementeren van sjablonen.  Als u sjabloonontwikkeling wilt leren, raadpleegt u de [beginnershandleidingen.](./template-tutorial-create-first-template.md)

## <a name="get-tools"></a>Tools oppakken

Laten we beginnen met ervoor te zorgen dat u de tools hebt die u nodig hebt om sjablonen te implementeren.

### <a name="command-line-deployment"></a>Opdrachtregelimplementatie

U hebt Azure PowerShell of Azure CLI nodig om de sjabloon te implementeren. Zie voor de installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI op Linux installeren](/cli/azure/install-azure-cli-linux)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd, moet u zich voor de eerste keer aanmelden. Zie Aanmelden [- PowerShell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden voor](/cli/azure/get-started-with-azure-cli#sign-in)hulp voor hulp.

### <a name="editor-optional"></a>Editor (optioneel)

Sjablonen zijn JSON-bestanden. Als u sjablonen wilt bekijken/bewerken, hebt u een goede JSON-editor nodig. We raden Visual Studio Code aan met de extensie Hulpmiddelen voor Resource Manager. Zie [Visual Studio Code gebruiken om Azure Resource Manager-sjablonen te maken](use-vs-code-to-create-template.md)als u deze hulpprogramma's moet installeren.

## <a name="review-template"></a>Sjabloon bekijken

De sjabloon die in deze zelfstudie wordt gebruikt, is vergelijkbaar met de sjabloon die wordt gebruikt in de [zelfstudie over Quickstart-sjablonen.](template-tutorial-quickstart-template.md) Als u geïnteresseerd bent in het maken van de sjabloon, u die zelfstudie doorlopen. Het is echter niet vereist voor het invullen van deze tutorial.

De sjabloon implementeert een opslagaccount, app-serviceplan en web-app.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> De namen van het opslagaccount moeten tussen de 3 en 24 tekens lang zijn en alleen getallen en kleine letters gebruiken. De naam moet uniek zijn. In de sjabloon is de naam van het opslagaccount de projectnaam met 'store' toegevoegd en moet de projectnaam tussen 3 en 11 tekens liggen. De projectnaam moet dus voldoen aan de vereisten voor de naam van het opslagaccount en heeft minder dan 11 tekens.

Sla een kopie van de sjabloon op uw lokale computer op met de .json-extensie, bijvoorbeeld azuredeploy.json. U implementeert deze sjabloon later in de zelfstudie.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u wilt beginnen te werken met Azure PowerShell/Azure CLI om een sjabloon te implementeren, meldt u zich aan met uw Azure-referenties.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az login
```

---

Als u meerdere Azure-abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Een resourcegroep maken

Wanneer u een sjabloon implementeert, geeft u een resourcegroep op die de bronnen bevat. Maak de resourcegroep met Azure CLI of Azure PowerShell voordat u de opdracht Implementatie uitvoert. Selecteer de tabbladen in de volgende codesectie om te kiezen tussen Azure PowerShell en Azure CLI. De CLI-voorbeelden in dit artikel zijn geschreven voor de Bash-shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sjabloon implementeren

Gebruik een of beide implementatieopties om de sjabloon te implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](./deploy-powershell.md)voor meer informatie over het implementeren van sjabloon met Azure PowerShell.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Zie Resources implementeren [met Resource Manager-sjablonen en Azure CLI](./deploy-cli.md)voor meer informatie over het implementeren van sjabloon met Azure CLI.

---

## <a name="clean-up-resources"></a>Resources opschonen

Ruim de resources op die u hebt geïmplementeerd door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een lokale sjabloon implementeert. In de volgende zelfstudie scheidt u de sjabloon in een hoofdsjabloon en een gekoppelde sjabloon en leert u hoe u de gekoppelde sjabloon opslaan en beveiligen.

> [!div class="nextstepaction"]
> [Een gekoppelde sjabloon implementeren](./deployment-tutorial-linked-template.md)

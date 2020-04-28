---
title: Zelf studie-een lokale Azure Resource Manager-sjabloon implementeren
description: Meer informatie over het implementeren van een Azure Resource Manager-sjabloon vanaf uw lokale computer
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7f134bb836d05d006ef2e474ea48382a671957fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188821"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Zelf studie: een lokale Azure Resource Manager-sjabloon implementeren

Meer informatie over het implementeren van een Azure Resource Manager-sjabloon vanaf uw lokale computer. Het duurt ongeveer **acht minuten** om te volt ooien.

Deze zelf studie is het eerste van een reeks. Tijdens de voortgang van de reeks kunt u de sjabloon modularize door een gekoppelde sjabloon te maken, de gekoppelde sjabloon op te slaan in een opslag account en de gekoppelde sjabloon te beveiligen met behulp van SAS-token, en u leert hoe u een DevOp-pijp lijn kunt maken voor het implementeren van sjablonen. Deze reeks is gericht op het implementeren van sjablonen.  Als u meer wilt weten over het ontwikkelen van sjablonen, raadpleegt u de [zelf studies van beginners](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Hulp middelen ophalen

Laten we beginnen door ervoor te zorgen dat u beschikt over de hulpprogram ma's die u nodig hebt voor het implementeren van sjablonen.

### <a name="command-line-deployment"></a>Opdracht regel implementatie

U hebt een Azure PowerShell of Azure CLI nodig om de sjabloon te implementeren. Zie voor de installatie-instructies:

- [Azure PowerShell installeren](/powershell/azure/install-az-ps)
- [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
- [Azure CLI installeren in Linux](/cli/azure/install-azure-cli-linux)

Nadat u Azure PowerShell of Azure CLI hebt geïnstalleerd, moet u zich voor de eerste keer aanmelden. Zie [Aanmelden bij Power shell](/powershell/azure/install-az-ps#sign-in) of [Aanmelden-Azure cli](/cli/azure/get-started-with-azure-cli#sign-in)voor meer informatie.

### <a name="editor-optional"></a>Redacteur (optioneel)

Sjablonen zijn JSON-bestanden. Als u sjablonen wilt bekijken/bewerken, hebt u een goede JSON-editor nodig. We raden Visual Studio code aan met de uitbrei ding Resource Manager-Hulpprogram Ma's. Als u deze hulpprogram ma's wilt installeren, raadpleegt u [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).

## <a name="review-template"></a>Sjabloon controleren

Met de sjabloon wordt een opslag account, een app service-abonnement en een web-app geïmplementeerd. Als u geïnteresseerd bent in het maken van de sjabloon, kunt u [zelf studie over Quick](template-tutorial-quickstart-template.md)start-sjablonen door nemen. Het is echter niet vereist voor het volt ooien van deze zelf studie.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. De naam moet uniek zijn. In de sjabloon is de naam van het opslag account de project naam ' Store ' toegevoegd en de project naam moet tussen de 3 en 11 tekens lang zijn. De project naam moet dus voldoen aan de naam vereisten voor het opslag account en mag uit minder dan 11 tekens bestaan.

Sla een kopie van de sjabloon op uw lokale computer op met de extensie. json, bijvoorbeeld azuredeploy. json. U implementeert deze sjabloon later in de zelf studie.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan met uw Azure-referenties om aan de slag te gaan met Azure PowerShell/Azure CLI voor het implementeren van een sjabloon.

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

Wanneer u een sjabloon implementeert, geeft u een resource groep op die de resources zal bevatten. Voordat u de implementatie opdracht uitvoert, maakt u de resource groep met behulp van Azure CLI of Azure PowerShell. Selecteer de tabbladen in het volgende code gedeelte om te kiezen tussen Azure PowerShell en Azure CLI. De CLI-voor beelden in dit artikel zijn geschreven voor de bash-shell.

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

Gebruik een of beide implementatie opties voor het implementeren van de sjabloon.

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

Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](./deploy-powershell.md)voor meer informatie over het implementeren van een sjabloon met behulp van Azure PowerShell.

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

Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](./deploy-cli.md)voor meer informatie over het implementeren van een sjabloon met behulp van Azure cli.

---

## <a name="clean-up-resources"></a>Resources opschonen

Opschonen van de resources die u hebt geïmplementeerd door de resource groep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een lokale sjabloon implementeert. In de volgende zelf studie scheidt u de sjabloon in een hoofd sjabloon en een gekoppelde sjabloon en leert u hoe u de gekoppelde sjabloon kunt opslaan en beveiligen.

> [!div class="nextstepaction"]
> [Een gekoppelde sjabloon implementeren](./deployment-tutorial-linked-template.md)

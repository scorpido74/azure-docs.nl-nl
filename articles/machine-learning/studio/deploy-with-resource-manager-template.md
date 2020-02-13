---
title: Studio-werk ruimte (klassiek) implementeren met Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Een werk ruimte voor Azure Machine Learning Studio (klassiek) implementeren met behulp van Azure Resource Manager sjabloon
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: f05ef9472f11a5025e9856cfb207cc0859f24c3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169355"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Machine Learning Studio-werk ruimte (klassieke) implementeren met behulp van Azure Resource Manager

Met behulp van een Azure Resource Manager-sjabloon voor de implementatie u tijd bespaart doordat u een schaalbare manier om te implementeren met elkaar verbonden onderdelen met een validatie en mechanisme voor opnieuw proberen. Als u bijvoorbeeld Azure Machine Learning Studio (klassieke) werk ruimten wilt instellen, moet u eerst een Azure-opslag account configureren en vervolgens uw werk ruimte implementeren. Stel dit handmatig doen voor honderden werkruimten. Een eenvoudiger alternatief is het gebruik van een Azure Resource Manager sjabloon voor het implementeren van een studio-werk ruimte (klassiek) en alle bijbehorende afhankelijkheden. In dit artikel gaat u door dit stapsgewijze proces. Zie [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)voor een geweldig overzicht van Azure Resource Manager.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Stap voor stap: een Machine Learning-werkruimte maken
We gaan een Azure-resource groep maken en vervolgens een nieuw Azure-opslag account en een nieuwe Azure Machine Learning Studio (klassieke) werk ruimte implementeren met behulp van een resource manager-sjabloon. Zodra de implementatie voltooid is, wordt er belangrijke informatie over de werkruimten die zijn gemaakt (de primaire sleutel, de werkruimte-id en de URL van de werkruimte) afgedrukt.

### <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon maken

Een Machine Learning-werkruimte is vereist voor het opslaan van de gegevensset die is gekoppeld aan het Azure storage-account.
De volgende sjabloon maakt gebruik van de naam van de resourcegroep voor het genereren van de naam van het opslagaccount en de naam van de werkruimte.  Gebruikt ook de naam van opslagaccount als een eigenschap bij het maken van de werkruimte.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Deze sjabloon opslaan als mlworkspace.json bestand onder c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>De resourcegroep, op basis van de sjabloon implementeren

* Open PowerShell.
* Modules voor Azure Resource Manager en Azure Service Management installeren

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Deze stappen downloadt en installeert de modules die nodig zijn voor de resterende stappen uitvoeren. Dit moet slechts één keer worden uitgevoerd in de omgeving waarin u de PowerShell-opdrachten worden uitgevoerd.

* Verificatie op Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Deze stap moet worden herhaald voor elke sessie. Eenmaal is geverifieerd, moet uw abonnementsgegevens worden weergegeven.

![Azure-Account](./media/deploy-with-resource-manager-template/azuresubscription.png)

Nu we hebben toegang tot Azure, kunnen we de resourcegroep maken.

* Een resourcegroep maken

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Controleer of dat de resourcegroep correct is ingericht. **ProvisioningState** moet "geslaagd" zijn.
Naam van de resourcegroep wordt gebruikt door de sjabloon voor het genereren van de naam van het opslagaccount. De opslagaccountnaam moet tussen 3 en 24 tekens lang zijn en cijfers en kleine letters bevatten.

![Resourcegroep](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Met behulp van de implementatie van resourcegroep, een nieuwe Machine Learning-werkruimte implementeren.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Zodra de implementatie is voltooid, is het eenvoudig om de eigenschappen van de werkruimte die u hebt geïmplementeerd. Bijvoorbeeld, u kunt toegang tot de primaire sleutel Token.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

U kunt ook de opdracht invoke-AzResourceAction gebruiken om tokens van een bestaande werk ruimte op te halen. Bijvoorbeeld, kunt u de primaire en secundaire tokens van alle werkruimten weergeven.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Nadat de werk ruimte is ingericht, kunt u ook veel Azure Machine Learning Studio (klassieke) taken automatiseren met behulp [van de Power shell-module voor Azure machine learning Studio (klassiek)](https://aka.ms/amlps).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md).
* Bekijk de [opslag plaats voor Azure Quick](https://github.com/Azure/azure-quickstart-templates)start-sjablonen.
* Bekijk deze video over [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zie de [Naslag informatie voor de Resource Manager-sjabloon](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->

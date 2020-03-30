---
title: Studio-werkruimte (klassieke) werkruimte implementeren met Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Een werkruimte implementeren voor Azure Machine Learning Studio (klassiek) met azure resource manager-sjabloon
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 34333d4fe6e9b34a0c8b56cca8123f4ed93a917a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218117"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Machine Learning Studio (klassieke) werkruimte implementeren met Azure Resource Manager

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Het gebruik van een Azure Resource Manager-implementatiesjabloon bespaart u tijd dooru een schaalbare manier te geven om onderling verbonden componenten te implementeren met een validatie- en opnieuw probeermechanisme. Als u bijvoorbeeld Azure Machine Learning Studio (klassieke) werkruimten wilt instellen, moet u eerst een Azure-opslagaccount configureren en vervolgens uw werkruimte implementeren. Stel je voor dat je dit handmatig doet voor honderden werkplekken. Een eenvoudiger alternatief is het gebruik van een Azure Resource Manager-sjabloon om een Studio (klassieke) workspace en al zijn afhankelijkheden te implementeren. Dit artikel neemt u mee door dit proces stap voor stap. Zie overzicht van Azure Resource Manager voor een goed overzicht van Azure [Resource Manager.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Stap voor stap: maak een Machine Learning Workspace
We maken een Azure-brongroep en implementeren vervolgens een nieuw Azure-opslagaccount en een nieuwe Azure Machine Learning Studio (klassieke) werkruimte met behulp van een Resource Manager-sjabloon. Zodra de implementatie is voltooid, zullen we belangrijke informatie uitprinten over de werkruimten die zijn gemaakt (de primaire sleutel, de werkruimte-id en de URL naar de werkruimte).

### <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon maken

Voor een Machine Learning Workspace is een Azure-opslagaccount vereist om de aan deze gegevensgekoppelde gegevensset op te slaan.
In de volgende sjabloon wordt de naam van de resourcegroep gebruikt om de naam van het opslagaccount en de naam van de werkruimte te genereren.  Het gebruikt ook de naam van het opslagaccount als eigenschap bij het maken van de werkruimte.

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
Sla deze sjabloon op als mlworkspace.json-bestand onder c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>De resourcegroep implementeren op basis van de sjabloon

* Open PowerShell.
* Modules installeren voor Azure Resource Manager en Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Deze stappen downloaden en installeren de modules die nodig zijn om de resterende stappen te voltooien. Dit hoeft slechts één keer te gebeuren in de omgeving waarin u de PowerShell-opdrachten uitvoert.

* Verifiëren naar Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Deze stap moet voor elke sessie worden herhaald. Na verificatie moeten uw abonnementsgegevens worden weergegeven.

![Azure-account](./media/deploy-with-resource-manager-template/azuresubscription.png)

Nu we toegang hebben tot Azure, kunnen we de brongroep maken.

* Een resourcegroep maken

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Controleer of de resourcegroep correct is ingericht. **ProvisioningState** moet worden "Geslaagd."
De naam van de brongroep wordt door de sjabloon gebruikt om de naam van het opslagaccount te genereren. De naam van het opslagaccount moet tussen de 3 en 24 tekens lang zijn en alleen getallen en kleine letters gebruiken.

![Resourcegroep](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Implementeer met de implementatie van de resourcegroep een nieuwe Machine Learning Workspace.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Zodra de implementatie is voltooid, is het eenvoudig om toegang te krijgen tot eigenschappen van de werkruimte die u hebt geïmplementeerd. U hebt bijvoorbeeld toegang tot het token voor primaire sleutels.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Een andere manier om tokens van bestaande werkruimte op te halen, is door de opdracht Aanroepen-AzResourceAction te gebruiken. U bijvoorbeeld de primaire en secundaire tokens van alle werkruimten weergeven.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Nadat de werkruimte is ingericht, u ook veel Azure Machine Learning Studio-taken (klassieke) taken automatiseren met behulp van de [PowerShell-module voor Azure Machine Learning Studio (klassiek).](https://aka.ms/amlps)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-syntax.md).
* Neem een kijkje in de [Azure Quickstart Templates Repository](https://github.com/Azure/azure-quickstart-templates).
* Bekijk deze video over [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zie de [naslaghulp voor sjabloonresourcebeheer](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->

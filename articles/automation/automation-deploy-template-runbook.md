---
title: Een Azure Resource Manager-sjabloon implementeren in een Azure Automation runbook
description: Een Azure Resource Manager-sjabloon implementeren die is opgeslagen in Azure Storage vanuit een runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: Power shell, runbook, JSON, Azure Automation
ms.openlocfilehash: 1bf381499ac31fafc8aaeef2b4ee488cfa1aa5c1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994685"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-PowerShell-runbook

U kunt een [Azure Automation Power shell-runbook](automation-first-runbook-textual-powershell.md) schrijven waarmee een Azure-resource wordt geïmplementeerd met behulp van een [Azure resource management-sjabloon](../azure-resource-manager/resource-manager-create-first-template.md).

Op deze manier kunt u de implementatie van Azure-resources automatiseren. U kunt uw Resource Manager-sjablonen in een centrale, veilige locatie, zoals Azure Storage, onderhouden.

In dit artikel maken we een Power shell-runbook dat gebruikmaakt van een resource manager-sjabloon die is opgeslagen in [Azure Storage](../storage/common/storage-introduction.md) voor het implementeren van een nieuw Azure Storage-account.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u de volgende items nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/free/).
* [Automation-account](automation-sec-configure-azure-runas-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources.  Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* [Azure Storage account](../storage/common/storage-create-storage-account.md) waarin de Resource Manager-sjabloon moet worden opgeslagen
* Azure PowerShell geïnstalleerd op een lokale computer. Zie [de Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) voor meer informatie over het verkrijgen van Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

In dit voor beeld gebruiken we een resource manager-sjabloon waarmee een nieuw Azure Storage-account wordt geïmplementeerd.

Kopieer de volgende tekst in een tekst editor:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
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

Sla het bestand lokaal op als **TemplateTest. json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Sla de Resource Manager-sjabloon op in Azure Storage

Nu gebruiken we Power shell om een Azure Storage bestands share te maken en het bestand **TemplateTest. json** te uploaden.
Zie [aan de slag met Azure file storage in Windows](../storage/files/storage-dotnet-how-to-use-files.md)voor instructies over het maken van een bestands share en het uploaden van een bestand in de Azure Portal.

Start Power shell op uw lokale machine en voer de volgende opdrachten uit om een bestands share te maken en de Resource Manager-sjabloon te uploaden naar die bestands share.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Het Power shell-runbook-script maken

Nu gaan we een Power shell-script maken waarmee het bestand **TemplateTest. json** wordt opgehaald van Azure Storage en de sjabloon implementeert voor het maken van een nieuw Azure Storage-account.

Plak de volgende tekst in een tekst editor:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Sla het bestand lokaal op als **DeployTemplate. ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importeer en publiceer het runbook in uw Azure Automation-account

Nu gebruiken we Power shell om het runbook te importeren in uw Azure Automation-account en vervolgens het runbook te publiceren. Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor meer informatie over het importeren en publiceren van een runbook in de Azure Portal.

Voer de volgende Power shell-opdrachten uit om **DeployTemplate. ps1** te importeren in uw Automation-account als een Power shell-runbook:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Het runbook starten

Nu gaan we het runbook starten door de cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) aan te roepen. Zie [starten van een runbook in azure Automation](automation-starting-a-runbook.md)voor informatie over het starten van een runbook in de Azure Portal.

Voer de volgende opdrachten uit in de Power shell-console:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Het runbook wordt uitgevoerd en u kunt de status controleren door uit `$job.Status`te voeren.

Het runbook haalt de Resource Manager-sjabloon op en gebruikt deze om een nieuw Azure Storage-account te implementeren.
U kunt zien dat het nieuwe opslag account is gemaakt door de volgende opdracht uit te voeren:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Samenvatting

Dat is alles. Nu kunt u Azure Automation en Azure Storage met Resource Manager-sjablonen gebruiken om al uw Azure-resources te implementeren.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md)voor meer informatie over Resource Manager-sjablonen.
* Zie [Inleiding tot Azure Storage](../storage/common/storage-introduction.md)om aan de slag te gaan met Azure Storage.
* Zie [Runbook-en module galerieën voor Azure Automation](automation-runbook-gallery.md)om andere nuttige Azure Automation runbooks te vinden.
* Zie [Azure Quick](https://azure.microsoft.com/resources/templates/)start-sjablonen voor meer informatie over andere nuttige Resource Manager-sjablonen.
* Zie [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)(Engelstalig) voor een Power shell-cmdlet-verwijzing.

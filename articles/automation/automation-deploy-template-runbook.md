---
title: Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-runbook
description: Een Azure Resource Manager-sjabloon implementeren die is opgeslagen in Azure Storage vanuit een runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: d4adbea42cda54380ad32dce40cfa0d8391ee490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366631"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-PowerShell-runbook

U een [Azure Automation PowerShell-runbook](automation-first-runbook-textual-powershell.md) schrijven waarmee een Azure-bron wordt geïmplementeerd met behulp van een [Azure Resource Management-sjabloon.](../azure-resource-manager/resource-manager-create-first-template.md)

Op deze manier u de implementatie van Azure-resources automatiseren. U uw Resource Manager-sjablonen op een centrale, veilige locatie zoals Azure Storage behouden.

In dit artikel maken we een PowerShell-runbook die een Resource Manager-sjabloon gebruikt die is opgeslagen in [Azure Storage](../storage/common/storage-introduction.md) om een nieuw Azure Storage-account te implementeren.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende items nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/free/).
* [Automation-account](automation-sec-configure-azure-runas-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources.  Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* [Azure Storage-account](../storage/common/storage-create-storage-account.md) waarin de sjabloon Resourcemanager moet worden opgeslagen
* Azure Powershell geïnstalleerd op een lokale machine. Zie [Azure Powershell installeren en configureren](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) voor informatie over het downloaden van Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

In dit voorbeeld gebruiken we een resourcemanagersjabloon waarmee een nieuw Azure Storage-account wordt geïmplementeerd.

Kopieer in een teksteditor de volgende tekst:

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

Sla het bestand `TemplateTest.json`lokaal op als .

## <a name="save-the-resource-manager-template-in-azure-storage"></a>De sjabloon Resourcebeheer opslaan in Azure Storage

Nu gebruiken we PowerShell om een Azure `TemplateTest.json` Storage-bestandsshare te maken en het bestand te uploaden.
Zie Aan de [slag met Azure File-opslag in Windows](../storage/files/storage-dotnet-how-to-use-files.md)voor instructies voor het maken van een bestandsshare en het uploaden van een bestand in de Azure-portal.

Start PowerShell op uw lokale machine en voer de volgende opdrachten uit om een bestandsshare te maken en de resourcemanagersjabloon te uploaden naar die bestandsshare.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Het PowerShell-runbook-script maken

Nu maken we een PowerShell-script dat het `TemplateTest.json` bestand uit Azure Storage haalt en de sjabloon implementeert om een nieuw Azure Storage-account te maken.

Plak in een teksteditor de volgende tekst:

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
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Sla het bestand `DeployTemplate.ps1`lokaal op als .

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Het runbook importeren en publiceren in uw Azure Automation-account

Nu gebruiken we PowerShell om de runbook te importeren in uw Azure Automation-account en vervolgens het runbook te publiceren.
Zie [Runbooks](manage-runbooks.md)beheren in Azure Automation voor informatie over het importeren en publiceren van een runbook in de Azure-portal.

Voer `DeployTemplate.ps1` de volgende PowerShell-opdrachten uit om als PowerShell-runbook in uw Automatiseringsaccount te importeren:

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
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Het runbook starten

Nu starten we het runbook door de [cmdlet Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) te bellen.

Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md)voor informatie over het starten van een runbook in de Azure-portal.

Voer de volgende opdrachten uit in de PowerShell-console:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Het runbook wordt uitgevoerd en u `$job.Status`kunt de status controleren door .

De runbook krijgt de resourcemanagersjabloon en gebruikt deze om een nieuw Azure Storage-account te implementeren.
U zien dat het nieuwe opslagaccount is gemaakt door de volgende opdracht uit te voeren:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Samenvatting

Dat is alles. Nu u Azure Automation en Azure Storage en Resource Manager-sjablonen gebruiken om al uw Azure-bronnen te implementeren.

## <a name="next-steps"></a>Volgende stappen

* Zie overzicht van [Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie over resourcebeheersjablonen
* Zie Inleiding tot Azure [Storage](../storage/common/storage-introduction.md)om aan de slag te gaan met Azure Storage.
* Zie [Runbook- en modulegaleries voor Azure Automation](automation-runbook-gallery.md)voor het zoeken naar andere nuttige Azure Automation-runbooks.
* Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) voor het zoeken naar andere nuttige Resource Manager-sjablonen



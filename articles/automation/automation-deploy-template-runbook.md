---
title: Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-runbook
description: Een Azure Resource Manager-sjabloon implementeren die is opgeslagen in Azure Storage vanuit een runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 575f954b346edb7d682e3fd0b432a257486bbfbb
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383285"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-PowerShell-runbook

U een [Azure Automation PowerShell-runbook](automation-first-runbook-textual-powershell.md) schrijven waarmee een Azure-bron wordt geïmplementeerd met behulp van een [Azure Resource Management-sjabloon.](../azure-resource-manager/resource-manager-create-first-template.md)

Op deze manier u de implementatie van Azure-resources automatiseren. U uw Resource Manager-sjablonen op een centrale, veilige locatie zoals Azure Storage behouden.

In dit artikel maken we een PowerShell-runbook die een Resource Manager-sjabloon gebruikt die is opgeslagen in [Azure Storage](../storage/common/storage-introduction.md) om een nieuw Azure Storage-account te implementeren.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende items nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/free/).
* [Automation-account](automation-sec-configure-azure-runas-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources.  Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* [Azure Storage-account](../storage/common/storage-create-storage-account.md) waarin de sjabloon Resourcemanager moet worden opgeslagen
* Azure PowerShell geïnstalleerd op een lokale machine. Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) voor informatie over het downloaden van Azure PowerShell.

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

Sla het bestand lokaal op als **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>De sjabloon Resourcebeheer opslaan in Azure Storage

Nu gebruiken we PowerShell om een Azure Storage-bestandsshare te maken en het **TemplateTest.json-bestand** te uploaden.
Zie Aan de [slag met Azure File-opslag in Windows](../storage/files/storage-dotnet-how-to-use-files.md)voor instructies voor het maken van een bestandsshare en het uploaden van een bestand in de Azure-portal.

Start PowerShell op uw lokale machine en voer de volgende opdrachten uit om een bestandsshare te maken en de resourcemanagersjabloon te uploaden naar die bestandsshare.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

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

Nu maken we een PowerShell-script waarmee het **TemplateTest.json-bestand** uit Azure Storage wordt opgehaald en de sjabloon wordt geïmplementeerd om een nieuw Azure Storage-account te maken.

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
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Sla het bestand lokaal op als **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Het runbook importeren en publiceren in uw Azure Automation-account

Nu gebruiken we PowerShell om de runbook te importeren in uw Azure Automation-account en vervolgens het runbook te publiceren.
Zie [Runbooks](manage-runbooks.md)beheren in Azure Automation voor informatie over het importeren en publiceren van een runbook in de Azure-portal.

Voer de volgende PowerShell-opdrachten uit als **u DeployTemplate.ps1** wilt importeren in uw Automatiseringsaccount als PowerShell-runbook:

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

Nu starten we het loopboek door de [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) cmdlet te bellen. Zie [Een runbook starten in Azure Automation](automation-starting-a-runbook.md)voor informatie over het starten van een runbook in de Azure-portal.

Voer de volgende opdrachten uit in de PowerShell-console:

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

Het runbook wordt uitgevoerd en u `$job.Status`kunt de status controleren door .

De runbook krijgt de resourcemanagersjabloon en gebruikt deze om een nieuw Azure Storage-account te implementeren.
U zien dat het nieuwe opslagaccount is gemaakt door de volgende opdracht uit te voeren:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Samenvatting

Dat is alles. Nu u Azure Automation en Azure Storage gebruiken met Resource Manager-sjablonen om al uw Azure-bronnen te implementeren.

## <a name="next-steps"></a>Volgende stappen

* Zie overzicht van [Azure Resource Manager](../azure-resource-manager/management/overview.md)voor meer informatie over Resource Manager-sjablonen.
* Zie Inleiding tot Azure [Storage](../storage/common/storage-introduction.md)om aan de slag te gaan met Azure Storage.
* Zie [Runbook- en modulegaleries voor Azure Automation](automation-runbook-gallery.md)voor het zoeken naar andere nuttige Azure Automation-runbooks.
* Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/)voor het zoeken naar andere nuttige Resource Manager-sjablonen.
* Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)voor een PowerShell-cmdletreferentie.

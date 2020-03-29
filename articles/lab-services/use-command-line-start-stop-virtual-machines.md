---
title: Gebruik opdrachtregelhulpprogramma's om VM's Azure DevTest Labs te starten en te stoppen
description: Meer informatie over het gebruik van opdrachtregelgereedschappen om virtuele machines te starten en te stoppen in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169254"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Gebruik opdrachtregelgereedschappen om virtuele azure-machines van Azure DevTest Labs te starten en te stoppen
In dit artikel ziet u hoe u Azure PowerShell of Azure CLI gebruiken om virtuele machines in een lab in Azure DevTest Labs te starten of te stoppen. U PowerShell/CLI-scripts maken om deze bewerkingen te automatiseren. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
Azure DevTest Labs is een manier om snelle, eenvoudige en lean dev/testomgevingen te maken. Hiermee u kosten beheren, vm's snel inrichten en verspilling minimaliseren.  Er zijn ingebouwde functies in de Azure-portal waarmee u VM's in een lab configureren om automatisch op specifieke tijdstippen te starten en te stoppen. 

In sommige scenario's u echter het starten en stoppen van VM's uit PowerShell/CLI-scripts automatiseren. Het geeft u enige flexibiliteit met het starten en stoppen van individuele machines op elk gewenst moment in plaats van op specifieke tijdstippen. Hier zijn enkele van de situaties waarin het uitvoeren van deze taken met behulp van scripts nuttig zou zijn.

- Wanneer u een 3-laagstoepassing gebruikt als onderdeel van een testomgeving, moeten de lagen in een reeks worden gestart. 
- Schakel een virtuele machine uit wanneer aan aangepaste criteria is voldaan om geld te besparen. 
- Gebruik deze als taak binnen een CI/CD-werkstroom om aan het begin van de stroom te beginnen, de VM's te gebruiken als buildmachines, testmachines of infrastructuur en stop vervolgens de VM's wanneer het proces is voltooid. Een voorbeeld hiervan is de aangepaste afbeeldingsfabriek met Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> In het volgende script wordt de Azure PowerShell Az-module gebruikt. 

Met het volgende PowerShell-script wordt een VM gestart in een lab. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) is de primaire focus voor dit script. De parameter **ResourceId** is de volledig gekwalificeerde resource-id voor de VM in het lab. De parameter **Actie** is waar de opties **Start** of **Stop** worden ingesteld, afhankelijk van wat nodig is.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure-CLI
De [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) is een andere manier om het starten en stoppen van DevTest Labs VM's te automatiseren. Azure CLI kan op verschillende besturingssystemen worden [geïnstalleerd.](/cli/azure/install-azure-cli?view=azure-cli-latest) In het volgende script vindt u opdrachten voor het starten en stoppen van een VM in een lab. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor het gebruik van de Azure-portal om deze bewerkingen uit te voeren: [Start een VM opnieuw](devtest-lab-restart-vm.md)op.

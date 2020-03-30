---
title: Een artefact toevoegen aan een vm in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een artefact aan een virtuele machine in een lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73901314"
---
# <a name="add-an-artifact-to-a-vm"></a>Een artefact toevoegen aan een VM
Tijdens het maken van een VM u er bestaande artefacten aan toevoegen. Deze artefacten kunnen afkomstig zijn van de [openbare DevTest Labs Git repository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) of uit uw eigen Git repository. In dit artikel ziet u hoe u artefacten toevoegt in de Azure-portal en met Azure PowerShell. 

Met Azure DevTest *Labs-artefacten* u *acties* opgeven die worden uitgevoerd wanneer de VM is ingericht, zoals het uitvoeren van Windows PowerShell-scripts, het uitvoeren van Bash-opdrachten en het installeren van software. Met *artefactparameters* u het artefact aanpassen voor uw specifieke scenario.

Zie het artikel: [Aangepaste artefacten maken](devtest-lab-artifact-author.md)voor meer informatie over het maken van aangepaste artefacten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Azure Portal gebruiken 
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met labs het lab met de VM waarmee u wilt werken.  
1. Selecteer **Mijn virtuele machines**.
1. Selecteer de gewenste VM.
1. Selecteer **Artefacten beheren**. 
1. Selecteer **Artefacten toepassen**.
1. Selecteer in het deelvenster **Artefacten toepassen** het artefact dat u aan de vm wilt toevoegen.
1. Voer in het deelvenster **Artefact toevoegen** de vereiste parameterwaarden en eventuele optionele parameters in die u nodig hebt.  
1. Selecteer **Toevoegen** om het artefact toe te voegen en ga terug naar het deelvenster **Artefacten toepassen.**
1. Ga door met het toevoegen van artefacten als dat nodig is voor uw VM.
1. Zodra u uw artefacten hebt toegevoegd, u [de volgorde wijzigen waarin de artefacten worden uitgevoerd.](#change-the-order-in-which-artifacts-are-run) U ook teruggaan om een artefact te [bekijken of te wijzigen.](#view-or-modify-an-artifact)
1. Wanneer u klaar bent met het toevoegen van artefacten, selecteert u **Toepassen**

### <a name="change-the-order-in-which-artifacts-are-run"></a>De volgorde wijzigen waarin artefacten worden uitgevoerd
Standaard worden de acties van de artefacten uitgevoerd in de volgorde waarin ze aan de VM worden toegevoegd. In de volgende stappen wordt uitgelegd hoe u de volgorde wijzigen waarin de artefacten worden uitgevoerd.

1. Selecteer boven aan het deelvenster **Artefacten toepassen** de koppeling met vermelding van het aantal artefacten dat aan de vm is toegevoegd.
   
    ![Aantal artefacten dat aan VM is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Sleep en zet de artefacten in het deelvenster **Geselecteerde artefacten** in de gewenste volgorde. Als u problemen hebt met het slepen van het artefact, moet u ervoor zorgen dat u vanaf de linkerkant van het artefact sleept. 
1. Selecteer **OK** wanneer u gereed bent.  

### <a name="view-or-modify-an-artifact"></a>Een artefact weergeven of wijzigen
In de volgende stappen wordt uitgelegd hoe u de parameters van een artefact weergeven of wijzigen:

1. Selecteer boven aan het deelvenster **Artefacten toepassen** de koppeling met vermelding van het aantal artefacten dat aan de vm is toegevoegd.
   
    ![Aantal artefacten dat aan VM is toegevoegd](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Selecteer in het deelvenster **Geselecteerde artefacten** het artefact dat u wilt weergeven of bewerken.  
1. Breng **in** het deelvenster Artefact toevoegen de benodigde wijzigingen aan en selecteer **OK** om het **deelvenster Artefact toevoegen** te sluiten.
1. Selecteer **OK** om het deelvenster **Geselecteerde artefacten te** sluiten.

## <a name="use-powershell"></a>PowerShell gebruiken
In het volgende script wordt het opgegeven artefact op de opgegeven VM uitgevoerd. De opdracht [Aanroepen-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) is degene die de bewerking uitvoert.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen over artefacten:

- [Geef verplichte artefacten op voor uw lab](devtest-lab-mandatory-artifacts.md)
- [Aangepaste artefacten maken](devtest-lab-artifact-author.md)
- [Een opslagplaats voor artefacten toevoegen aan een lab](devtest-lab-artifact-author.md)
- [Problemen met artefacten vaststellen](devtest-lab-troubleshoot-artifact-failure.md)

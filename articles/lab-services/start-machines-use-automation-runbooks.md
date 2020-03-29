---
title: Machines starten met automatiseringswerkboeken in Azure DevTest Labs
description: Meer informatie over het starten van virtuele machines in een lab in Azure DevTest Labs met Azure Automation-runbooks.
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
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166316"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Virtuele machines in een lab starten in volgorde met Azure Automation-runbooks
Met de [functie automatisch starten](devtest-lab-set-lab-policy.md#set-autostart) van DevTest Labs u VM's configureren om automatisch op een bepaald tijdstip te starten. Deze functie ondersteunt machines echter niet om in een specifieke volgorde te starten. Er zijn verschillende scenario's waarin dit type automatisering nuttig zou zijn.  Een scenario is waar een Jumpbox VM in een lab eerst moet worden gestart, voordat de andere VM's, als de Jumpbox wordt gebruikt als het toegangspunt tot de andere VM's.  In dit artikel ziet u hoe u een Azure Automation-account instelt met een PowerShell-runbook waarmee een script wordt uitgevoerd. Het script maakt gebruik van tags op VM's in het lab om u in staat te stellen de opstartvolgorde te beheren zonder het script te hoeven wijzigen.

## <a name="setup"></a>Instellen
In dit voorbeeld moeten VM's in het lab de tag **StartupOrder** met de juiste waarde (0,1,2, enz.) hebben toegevoegd. Wijs een machine aan die niet als -1 hoeft te worden gestart.

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken
Maak een Azure Automation-account door instructies in [dit artikel](../automation/automation-create-standalone-account.md)te volgen. Kies de optie **Als account uitvoeren** bij het maken van het account. Zodra het automatiseringsaccount is gemaakt, opent u de pagina **Modules** en selecteert u **Azure Modules bijwerken** op de menubalk. De standaardmodules zijn meerdere versies oud en zonder de update werkt het script mogelijk niet.

## <a name="add-a-runbook"></a>Een runbook toevoegen
Als u nu een runbook wilt toevoegen aan het automatiseringsaccount, selecteert u **Runbooks** in het linkermenu. Selecteer **Een runbook toevoegen** in het menu en volg instructies om een [PowerShell-runbook te maken.](../automation/automation-first-runbook-textual-powershell.md)

## <a name="powershell-script"></a>PowerShell-script
Het volgende script neemt de naam van het abonnement, de labnaam als parameters. De stroom van het script is om alle VM's in het lab te krijgen en vervolgens de taginformatie te ontschepen om een lijst met vm-namen en hun opstartvolgorde te maken. Het script loopt door de VM's in volgorde en start de VM's. Als er meerdere VM's in een specifiek ordernummer zijn, worden ze asynchroon gestart met PowerShell-taken. Voor vm's die geen tag hebben, stel je de opstartwaarde in als laatste (10), ze worden standaard als laatste gestart.  Als het lab niet wil dat de VM automatisch wordt gestart, stelt u de tagwaarde in op 11 en wordt deze genegeerd.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Een planning maken
Als u dit script dagelijks wilt laten uitvoeren, [maakt u een planning](../automation/shared-resources/schedules.md#creating-a-schedule) in het automatiseringsaccount. Zodra de planning is gemaakt, [koppelt u deze aan het runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

In een grootschalige situatie waarin er meerdere abonnementen zijn met meerdere labs, slaat u de parameterinformatie op in een bestand voor verschillende laboratoria en geeft u het bestand door aan het script in plaats van aan de afzonderlijke parameters. Het script zou moeten worden gewijzigd, maar de kern uitvoering zou hetzelfde zijn. Hoewel dit voorbeeld de Azure Automation gebruikt om het PowerShell-script uit te voeren, zijn er andere opties, zoals het gebruik van een taak in een Build/Release-pijplijn.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over Azure Automation: [een inleiding tot Azure Automation.](../automation/automation-intro.md)

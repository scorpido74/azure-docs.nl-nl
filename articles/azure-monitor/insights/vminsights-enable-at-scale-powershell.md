---
title: Azure-monitor voor VM's inschakelen met PowerShell- of sjablonen
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt voor een of meer Azure-virtuele machines of virtuele machineschaalsets met Azure PowerShell- of Azure Resource Manager-sjablonen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480841"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Azure Monitor voor VM's inschakelen met Azure PowerShell- of Resource Manager-sjablonen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's inschakelt voor virtuele Azure-machines of virtuele machineschaalsets met Azure PowerShell- of Azure Resource Manager-sjablonen. Aan het einde van dit proces bent u met succes begonnen met het monitoren van al uw virtuele machines en leert u of er problemen zijn met prestaties of beschikbaarheid.

## <a name="set-up-a-log-analytics-workspace"></a>Een logboekanalysewerkruimte instellen

Als u geen Log Analytics-werkruimte hebt, moet u er een maken. Bekijk de methoden die worden voorgesteld in de sectie [Voorwaarden](vminsights-enable-overview.md#log-analytics) voordat u verdergaat met de stappen om deze te configureren. Vervolgens u de implementatie van Azure Monitor voor VM's voltooien met behulp van de sjabloonmethode Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>De VMInsights-oplossing installeren

Deze methode bevat een JSON-sjabloon die de configuratie opgeeft voor het inschakelen van de oplossingsonderdelen in uw Log Analytics-werkruimte.

Zie als u niet weet hoe u resources implementeert met behulp van een sjabloon:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u de Azure CLI wilt gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.27 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u de Azure CLI wilt installeren of upgraden.

1. Kopieer en plak de volgende JSON-syntaxis in het bestand:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Sla dit bestand op als *installsolutionsforvminsights.json* in een lokale map.

1. De waarden voor *WorkspaceName,* *ResourceGroupName*en *WorkspaceLocation vastleggen*. De waarde voor *WorkspaceName* is de naam van uw Log Analytics-werkruimte. De waarde voor *WorkspaceLocation* is het gebied waarin de werkruimte is gedefinieerd.

1. U kunt deze sjabloon nu implementeren.

    * Gebruik de volgende PowerShell-opdrachten in de map die de sjabloon bevat:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

        ```output
        provisioningState       : Succeeded
        ```

    * Ga als volgt te werk om de volgende opdracht uit te voeren met Azure CLI:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Inschakelen met Azure Resource Manager-sjablonen

We hebben bijvoorbeeld Azure Resource Manager-sjablonen gemaakt voor het onboarding van uw virtuele machines en virtuele machineschaalsets. Deze sjablonen bevatten scenario's die u gebruiken om bewaking op een bestaande resource in te schakelen en een nieuwe bron te maken die monitoring heeft ingeschakeld.

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resourcegroep als de resource die aan boord moet worden gebracht.

Zie als u niet weet hoe u resources implementeert met behulp van een sjabloon:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Als u de Azure CLI wilt gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.27 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u de Azure CLI wilt installeren of upgraden.

### <a name="download-templates"></a>Sjablonen downloaden

De Azure Resource Manager-sjablonen worden geleverd in een archiefbestand (.zip) dat u [downloaden](https://aka.ms/VmInsightsARMTemplates) van onze GitHub-repo. De inhoud van het bestand bevat mappen die elk implementatiescenario vertegenwoordigen met een sjabloon en parameterbestand. Voordat u ze uitvoert, wijzigt u het parametersbestand en geeft u de vereiste waarden op. Wijzig het sjabloonbestand alleen als u het moet aanpassen om uw specifieke vereisten te ondersteunen. Nadat u het parameterbestand hebt gewijzigd, u het implementeren met behulp van de volgende methoden die later in dit artikel worden beschreven.

Het downloadbestand bevat de volgende sjablonen voor verschillende scenario's:

- **Bestaande VmOnboarding-sjabloon** maakt Azure Monitor voor VM's mogelijk als de virtuele machine al bestaat.
- **NewVmOnboarding-sjabloon** maakt een virtuele machine en stelt Azure Monitor voor VM's in staat om deze te controleren.
- **Bestaande VmssOnboarding-sjabloon** maakt Azure Monitor voor VM's mogelijk als de virtuele machineschaalset al bestaat.
- **NewVmssOnboarding-sjabloon** maakt virtuele machineschaalsets en stelt Azure Monitor voor VM's in staat om deze te controleren.
- **ConfigureWorkspace-sjabloon** configureert uw Log Analytics-werkruimte om Azure Monitor voor VM's te ondersteunen door de oplossingen en verzameling van prestatiemeteritems voor het Besturingssysteem voor Linux en Windows in te schakelen.

>[!NOTE]
>Als er al virtuele machineschaalsets aanwezig waren en het upgradebeleid is ingesteld op **Handmatig,** wordt Azure Monitor voor VM's standaard niet standaard ingeschakeld nadat de sjabloon **ExistingVmssOnboarding** Azure Resource Manager is uitgevoerd. U moet de exemplaren handmatig upgraden.

### <a name="deploy-by-using-azure-powershell"></a>Implementeren met behulp van Azure PowerShell

Met de volgende stap u met Azure PowerShell worden gecontroleerd.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Het kan enkele minuten duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Implementeren met de Azure CLI

Met de volgende stap u met de Azure CLI worden gecontroleerd.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

De uitvoer lijkt op het volgende:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Inschakelen met PowerShell

Als u Azure Monitor voor VM's voor meerdere VM's of virtuele machineschaalsets wilt inschakelen, gebruikt u het [PowerShell-script Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Het is beschikbaar in de Azure PowerShell Gallery. Dit script wordt doorgestoond:

- Elke virtuele machine en virtuele machine schaal ingesteld in uw abonnement.
- De scoped resourcegroep die is opgegeven door *ResourceGroup*.
- Een enkele VM- of virtuele machineschaalset die is opgegeven op *naam.*

Voor elke vm- of virtuele machineschaalset controleert het script of de VM-extensie al is geïnstalleerd. Als de VM-extensie is geïnstalleerd, probeert het script deze opnieuw te installeren. Als de VM-extensie niet is geïnstalleerd, worden in het script de VM-extensies log-analyse en afhankelijkheidsagent geïnstalleerd.

Controleer of u Azure PowerShell-module Az-versie 1.0.0 of hoger gebruikt, waarbij `Enable-AzureRM` compatibiliteitsaliassen zijn ingeschakeld. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Voer uit om een lijst met de argumentdetails `Get-Help`en het voorbeeldgebruik van het script te krijgen.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

In het volgende voorbeeld wordt aangetoond dat u de PowerShell-opdrachten in de map gebruikt om Azure Monitor voor VM's in te schakelen en inzicht te krijgen in de verwachte uitvoer:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Volgende stappen

Nu bewaking is ingeschakeld voor uw virtuele machines, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's.

- Zie Azure Monitor for [VM-toewijzing weergeven](vminsights-maps.md)om gedetecteerde toepassingsafhankelijkheden weer te geven.

- Zie [Azure VM Performance weergeven](vminsights-performance.md)om knelpunten en algemeen gebruik met de prestaties van uw VM te identificeren.

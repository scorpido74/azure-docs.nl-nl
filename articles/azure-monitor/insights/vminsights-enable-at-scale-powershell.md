---
title: Azure Monitor voor VM's (klassiek) inschakelen met Power shell of sjablonen
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt voor een of meer virtuele machines van Azure of virtuele-machine schaal sets met behulp van Azure PowerShell-of Azure Resource Manager-sjablonen.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 5f37971e9680468c29efd5733517cb900852431f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400759"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Azure Monitor voor VM's inschakelen (preview) met behulp van Azure PowerShell-of Resource Manager-sjablonen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's (preview) voor virtuele machines van Azure of virtuele-machine schaal sets inschakelt met behulp van Azure PowerShell-of Azure Resource Manager-sjablonen. Aan het einde van dit proces hebt u al uw virtuele machines gecontroleerd en leert u meer over problemen met prestaties of Beschik baarheid.

## <a name="set-up-a-log-analytics-workspace"></a>Stel een Log Analytics-werkruimte 

Als u geen Log Analytics-werk ruimte hebt, moet u er een maken. Bekijk de methoden die in de sectie [vereisten](vminsights-enable-overview.md#log-analytics) worden voorgesteld voordat u doorgaat met de stappen om deze te configureren. Daarna kunt u de implementatie van Azure Monitor voor VM's volt ooien met behulp van de Azure Resource Manager sjabloon methode.

### <a name="enable-performance-counters"></a>Inschakelen van prestatiemeteritems

Als de Log Analytics-werkruimte waarnaar wordt verwezen door de oplossing niet is al geconfigureerd voor het verzamelen van de prestatiemeteritems die is vereist voor de oplossing, moet u ze inschakelen. U kunt dit op een van de volgende twee manieren doen:
* Handmatig, zoals beschreven in [Windows en Linux-gegevensbronnen van de prestaties die u in Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Een Power shell-script downloaden en uitvoeren dat beschikbaar is via de [Azure PowerShell galerie](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-solution"></a>De ServiceMap-oplossing installeren

Deze methode bevat een JSON-sjabloon waarmee de configuratie voor het inschakelen van de oplossingsonderdelen in uw Log Analytics-werkruimte.

Als u niet weet hoe u resources kunt implementeren met behulp van een sjabloon, raadpleegt u:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Als u Azure CLI wilt gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet worden uitgevoerd van Azure CLI versie 2.0.27 of hoger. Voor het identificeren van uw versie uitvoeren `az --version`. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Sla dit bestand als *installsolutionsforvminsights.json* naar een lokale map.

1. Leg de waarden vast voor de *werkruimte*, *ResourceGroupName*en *WorkspaceLocation*. De waarde voor *workspacenaam* is de naam van uw log Analytics-werk ruimte. De waarde voor *WorkspaceLocation* is de regio in de werkruimte is gedefinieerd.

1. U kunt deze sjabloon nu implementeren.
 
    * Gebruik de volgende PowerShell-opdrachten in de map met de sjabloon:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Het kan een paar minuten duren voordat de configuratie wijziging is voltooid. Wanneer het is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

        ```powershell
        provisioningState       : Succeeded
        ```

    * De volgende opdracht uitvoeren met behulp van de Azure CLI:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Het kan een paar minuten duren voordat de configuratie wijziging is voltooid. Wanneer de bewerking is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

        ```azurecli
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Inschakelen met Azure Resource Manager sjablonen

Er zijn voor beelden gemaakt Azure Resource Manager sjablonen voor het voorbereiden van uw virtuele machines en virtuele-machine schaal sets. Deze sjablonen bevatten scenario's die u kunt gebruiken om bewaking in te scha kelen voor een bestaande resource en om een nieuwe resource te maken waarvoor bewaking is ingeschakeld.

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep als de resource die aan het bord moet worden toegevoegd.

Als u niet weet hoe u resources kunt implementeren met behulp van een sjabloon, raadpleegt u:
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Als u Azure CLI wilt gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet worden uitgevoerd van Azure CLI versie 2.0.27 of hoger. Voor het identificeren van uw versie uitvoeren `az --version`. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u [de Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Sjablonen downloaden

De Azure Resource Manager sjablonen worden in een archief bestand (. zip) vermeld dat u kunt [downloaden](https://aka.ms/VmInsightsARMTemplates) van onze github opslag plaats. Inhoud van het bestand bevat mappen die elk implementatie scenario vertegenwoordigen met een sjabloon en een parameter bestand. Voordat u ze uitvoert, wijzigt u het parameter bestand en geeft u de vereiste waarden op. Wijzig het sjabloon bestand alleen als u het moet aanpassen om uw specifieke vereisten te ondersteunen. Nadat u het parameter bestand hebt gewijzigd, kunt u het implementeren met behulp van de volgende methoden die verderop in dit artikel worden beschreven. 

Het Download bestand bevat de volgende sjablonen voor verschillende scenario's:

- Met de **ExistingVmOnboarding** -sjabloon kunnen Azure monitor voor VM's als de virtuele machine al bestaat.
- Met de **NewVmOnboarding** -sjabloon maakt u een virtuele machine en schakelt Azure monitor voor VM's deze te controleren.
- Met de **ExistingVmssOnboarding** -sjabloon kan Azure monitor voor VM's als de schaalset van de virtuele machine al bestaat.
- Met de **NewVmssOnboarding** -sjabloon worden schaal sets voor virtuele machines gemaakt en kunnen Azure monitor voor VM's worden bewaakt.
- Met de **ConfigureWorkspace** -sjabloon configureert u uw log Analytics-werk ruimte ter ondersteuning van Azure monitor voor VM's door de oplossingen en verzameling van prestatie meter items voor Linux-en Windows-besturings systemen in te scha kelen.

>[!NOTE]
>Als de virtuele-machine schaal sets al aanwezig waren en het upgrade beleid is ingesteld op **hand matig**, wordt Azure monitor voor VM's standaard niet ingeschakeld voor instanties nadat u de **ExistingVmssOnboarding** -Azure Resource Manager sjabloon hebt uitgevoerd. U moet de exemplaren hand matig bijwerken.

### <a name="deploy-by-using-azure-powershell"></a>Implementeren met behulp van Azure PowerShell

Met de volgende stap wordt bewaking ingeschakeld met behulp van Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Het kan een paar minuten duren voordat de configuratie wijziging is voltooid. Wanneer het is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat:

```powershell
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Implementeren met behulp van de Azure CLI

Met de volgende stap wordt bewaking ingeschakeld met behulp van de Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

De uitvoer ziet er ongeveer als volgt uit:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Inschakelen met PowerShell

Als u Azure Monitor voor VM's wilt inschakelen voor meerdere Vm's of virtuele-machine schaal sets, gebruikt u het Power shell-script [install-VMInsights. ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Deze is beschikbaar in de galerie met Azure PowerShell. Dit script doorloopt over:

- Alle virtuele machines en virtuele-machine schaal sets in uw abonnement.
- Het bereik van de resource groep die is opgegeven door *ResourceGroup*. 
- Een enkele virtuele machine of VM-schaalset die is opgegeven met de *naam*.

Voor elke virtuele machine of VM-schaalset het script wordt gecontroleerd of de VM-extensie al is geïnstalleerd. Als de VM-extensie is geïnstalleerd, probeert het script deze opnieuw te installeren. Als de extensie van de virtuele machine niet is geïnstalleerd, installeert het script de Log Analytics en de VM-extensies van de afhankelijkheids agent.

Controleer of u Azure PowerShell module AZ version 1.0.0 of hoger gebruikt met `Enable-AzureRM` compatibiliteits aliassen ingeschakeld. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Uitvoeren als u een lijst van het script argument details en een voorbeeld, `Get-Help`.

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

Het volgende voorbeeld ziet u met behulp van de PowerShell-opdrachten in de map Azure Monitor inschakelen voor virtuele machines en begrijpen van de verwachte uitvoer:

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

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's.
 
- Afhankelijkheden van gedetecteerde toepassingen, Zie [weergave Azure Monitor voor virtuele machines kaart](vminsights-maps.md). 

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren. 

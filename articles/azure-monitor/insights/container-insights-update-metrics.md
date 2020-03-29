---
title: Azure-monitor bijwerken voor containers voor statistieken | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers bijwerkt om de aangepaste metrische functie in te schakelen die het verkennen en waarschuwen van geaggregeerde statistieken ondersteunt.
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: a7f40cb0523c2366c47da228e49311c2f9579212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715917"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Azure Monitor voor containers bijwerken om metrische gegevens in te schakelen

Azure Monitor voor containers introduceert ondersteuning voor het verzamelen van statistieken uit Azure Kubernetes Services (AKS) clusters knooppunten en pods en ze te schrijven naar de Azure Monitor metrics store. Deze wijziging is bedoeld om verbeterde tijdigheid te leveren bij het presenteren van geaggregeerde berekeningen (Avg, Count, Max, Min, Som) in prestatiegrafieken, ondersteuning van prestatiegrafieken in Azure-portaldashboards en ondersteuningsmetrische waarschuwingen.

>[!NOTE]
>Deze functie biedt momenteel geen ondersteuning voor Azure Red Hat OpenShift-clusters.
>

De volgende statistieken zijn ingeschakeld als onderdeel van deze functie:

| Metrische naamruimte | Gegevens | Beschrijving |
|------------------|--------|-------------|
| insights.container/nodes | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount | Dit zijn *knooppuntstatistieken* en bevatten *host* als dimensie, en ze bevatten ook de<br> de naam van het knooppunt als waarde voor de *hostdimensie.* |
| insights.container/pods | podCount | Dit zijn *pod* metrics en omvatten de volgende als dimensies - ControllerName, Kubernetes naamruimte, naam, fase. |

Het bijwerken van het cluster om deze nieuwe mogelijkheden te ondersteunen, kan worden uitgevoerd vanuit de Azure-portal, Azure PowerShell of azure CLI. Met Azure PowerShell en CLI u dit per cluster of voor alle clusters in uw abonnement inschakelen. Nieuwe implementaties van AKS bevatten automatisch deze configuratiewijziging en -mogelijkheden.

Het proces wijst de rol **Van De Uitgevers van de statistieken van** de controle aan de serviceprincipal van het cluster toe, zodat de gegevens die door de agent worden verzameld, kunnen worden gepubliceerd naar uw clustersbron. Monitoring Metrics Publisher heeft alleen toestemming om statistieken naar de bron te pushen, het kan geen status wijzigen, de bron bijwerken of gegevens lezen. Zie De rol Van [de uitgever van statistieken controleren](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)voor meer informatie over de rol Monitoring Metrics .

## <a name="prerequisites"></a>Vereisten

Bevestig voordat u begint het volgende:

* Aangepaste statistieken zijn alleen beschikbaar in een subset van Azure-regio's. Een lijst van ondersteunde regio's is [hier](../platform/metrics-custom-overview.md#supported-regions)gedocumenteerd.
* U bent lid van de rol **[Eigenaar](../../role-based-access-control/built-in-roles.md#owner)** in de AKS-clusterbron om het verzamelen van aangepaste prestatiestatistieken voor knooppunten en pod's mogelijk te maken. 

Als u ervoor kiest de Azure CLI te gebruiken, moet u de CLI eerst lokaal installeren en gebruiken. U moet de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer uit om `az --version`uw versie te identificeren. Zie [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)installeren als u de Azure CLI moet installeren of upgraden. 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Een cluster upgraden vanuit de Azure-portal

Voor bestaande AKS-clusters die worden gecontroleerd door Azure Monitor voor containers, moet u een banner boven aan de portal zien nadat u het cluster hebt geselecteerd om de status ervan te bekijken vanuit de multiclusterweergave in Azure Monitor of rechtstreeks vanuit het cluster door **Inzichten** te selecteren in het linkerdeelvenster.

![AKS-clusterbanner bijwerken in Azure-portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Als u op **Inschakelen klikt,** wordt het proces gestart om het cluster te upgraden. Dit proces kan enkele seconden duren en u de voortgang ervan bijhouden onder Meldingen in het menu.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Alle clusters upgraden met Bash in Azure Command Shell

Voer de volgende stappen uit om alle clusters in uw abonnement bij te werken met Bash in Azure Command Shell.

1. Voer de volgende opdracht uit met de Azure CLI.  Bewerk de waarde voor **abonnementId** met de waarde van de pagina **AKS-overzicht** voor het AKS-cluster.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Het kan enkele seconden duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Upgraden per cluster met Azure CLI

Voer de volgende stappen uit om een specifiek cluster in uw abonnement bij te werken met Azure CLI.

1. Voer de volgende opdracht uit met de Azure CLI. Bewerk de waarden voor **subscriptionId,** **resourceGroupName**en **clusterName** met behulp van de waarden op de pagina **AKS-overzicht** voor het AKS-cluster.  Om de waarde van **clientIdOfSPN**te krijgen, `az aks show` wordt deze geretourneerd wanneer u de opdracht uitvoert, zoals in het onderstaande voorbeeld wordt weergegeven.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ``` 

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Alle clusters upgraden met Azure PowerShell

Voer de volgende stappen uit om alle clusters in uw abonnement bij te werken met Azure PowerShell.

1. Kopieer en plak het volgende script in het bestand:

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the all AKS clusters in specified subscription        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    #>

    param(
    [Parameter(mandatory = $true)]
    [string]$SubscriptionId 
    )


    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ( $null -eq $azAksModule ) -or ($null -eq $azResourcesModule)) {

    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command if not installed already`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule)   {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }   
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule)   {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }    
            }     
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    try {
    Write-Host("")
    Write-Host("Trying to get the current Az login context...")
    $account = Get-AzContext -ErrorAction Stop
    Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
    Write-Host("")
    }
    catch {
    Write-Host("")
    Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
    Write-Host("")
    }

    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    } 

    #
    #   get all the AKS clusters in specified subscription
    #
    Write-Host("getting all aks clusters in specified subscription ...")
    $allClusters = Get-AzAks -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
    Write-Host("")
    Write-Host("Failed to get Aks clusters in specified subscription. Please make sure that you have access to the existing clusters") -ForegroundColor Red
    Write-Host("")
    Stop-Transcript
    exit
    }
    Write-Host("Successfully got all aks clusters ...") -ForegroundColor Green

    $clustersCount = $allClusters.Id.Length

    Write-Host("Adding role assignment for the clusters ...")

    for ($index = 0 ; $index -lt $clustersCount ; $index++) {  

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    $servicePrincipalClientId = $allClusters.ServicePrincipalProfile[$index].ClientId
    $clusterResourceId = $allClusters.Id[$index]
    $clusterName = $allClusters.Name[$index]


    Write-Host("Adding role assignment for the cluster: $clusterResourceId, servicePrincipalClientId: $servicePrincipalClientId ...")

  
    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

        $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     
        if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {
            Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
        }
        else { 
        
            Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
        }

    }
    else {

        Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }   

    Write-Host("Completed adding role assignment for the cluster: $clusterName ...")
        
    }

    Write-Host("Completed adding role assignment for the aks clusters in subscriptionId :$SubscriptionId")   
    ```

2. Sla dit bestand op als **onboard_metrics_atscale.ps1** in een lokale map.
3. Voer de volgende opdracht uit met de Azure PowerShell.  Bewerk de waarde voor **abonnementId** met de waarde van de pagina **AKS-overzicht** voor het AKS-cluster.

    ```powershell
    .\onboard_metrics_atscale.ps1 subscriptionId
    ```
    Het kan enkele seconden duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Upgraden per cluster met Azure PowerShell

Voer de volgende stappen uit om een specifiek cluster bij te werken met Azure PowerShell.

1. Kopieer en plak het volgende script in het bestand:

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the specified AKS cluster        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    .PARAMETER resourceGroupName
        Resource Group name that the AKS cluster is in
            
     .PARAMETER clusterName
        Name of the AKS cluster.
    #>

    param(
       [Parameter(mandatory = $true)]
       [string]$SubscriptionId,
       [Parameter(mandatory = $true)]
       [string]$resourceGroupName,
       [Parameter(mandatory = $true)]
       [string] $clusterName
    )

    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ($null -eq $azAksModule) -or ($null -eq $azResourcesModule)) {


    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule) {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule) {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }   
            }      
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
     }
    }

    try {
       Write-Host("")
       Write-Host("Trying to get the current Az login context...")
       $account = Get-AzContext -ErrorAction Stop
       Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
       Write-Host("")
    }
    catch {
      Write-Host("")
      Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
      Write-Host("")
    }


    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    #
    #   Check AKS cluster existence and access check
    #
    Write-Host("Checking aks cluster exists...")
    $cluster = Get-AzAks -ResourceGroupName $resourceGroupName -Name $clusterName  -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
       Write-Host("")
       Write-Host("Could not find Aks cluster. Please make sure that specified cluster exists: '" + $clusterName + "'is correct and you have access to the cluster") -ForegroundColor Red
       Write-Host("")
       Stop-Transcript
       exit
    }
    Write-Host("Successfully checked specified cluster exists details...") -ForegroundColor Green

    $servicePrincipalClientId = $cluster.ServicePrincipalProfile.clientId
    $clusterResourceId = $cluster.Id

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

    $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     

    if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {           
        Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
    }
    else { 
        
        Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
    }

    }
    else {

    Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }
    ```

2. Sla dit bestand op als **onboard_metrics.ps1** in een lokale map.
3. Voer de volgende opdracht uit met de Azure PowerShell. Bewerk de waarden voor **subscriptionId,** **resourceGroupName**en **clusterName** met behulp van de waarden op de pagina **AKS-overzicht** voor het AKS-cluster.

    ```powershell
    .\onboard_metrics.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Het kan enkele seconden duren voordat de configuratiewijziging is voltooid. Wanneer het is voltooid, wordt een bericht weergegeven dat vergelijkbaar is met het volgende en het resultaat bevat:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Update verifiëren 

Nadat u de update hebt gestart met een van de eerder beschreven methoden, u Azure Monitor metrics explorer gebruiken en vanuit de **metrische naamruimte** controleren of **er inzichten** worden weergegeven. Als dit het is, geeft dit aan dat u doorgaan en beginnen met het instellen van [metrische waarschuwingen](../platform/alerts-metric.md) of het vastmaken van uw grafieken aan [dashboards.](../../azure-portal/azure-portal-dashboards.md)  

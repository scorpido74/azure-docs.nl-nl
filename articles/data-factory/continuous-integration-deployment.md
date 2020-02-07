---
title: Continue integratie en levering in Azure Data Factory
description: Leer hoe u doorlopende integratie en levering kunt gebruiken om Data Factory pijp lijnen van de ene omgeving (ontwikkeling, test, productie) naar een andere te verplaatsen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7c9f4a5a4993057ef49eecf3852afa0929c49da3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061570"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Continue integratie en levering in Azure Data Factory

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het testen van elke wijziging die wordt aangebracht in uw code basis automatisch en zo snel mogelijk. Continue levering volgt de tests die optreden tijdens continue integratie en het pushen van wijzigingen in een staging-of productie systeem.

In Azure Data Factory wordt doorlopende integratie en levering (CI/CD) de mogelijkheid Data Factory pijp lijnen te verplaatsen van de ene omgeving (ontwikkeling, test, productie) naar de andere. U kunt Data Factory UX-integratie met Azure Resource Manager sjablonen gebruiken om CI/CD uit te voeren.

In de Data Factory UX kunt u een resource manager-sjabloon genereren in de vervolg keuzelijst **arm-sjabloon** . Wanneer u **arm-sjabloon exporteren**selecteert, genereert de Portal de Resource Manager-sjabloon voor de Data Factory en een configuratie bestand dat alle verbindings reeksen en andere para meters bevat. Vervolgens maakt u een configuratie bestand voor elke omgeving (ontwikkelen, testen, productie). Het sjabloon bestand van de hoofd Resource Manager blijft hetzelfde voor alle omgevingen.

Bekijk de volgende video voor een inleiding tot negen minuten voor deze functie en een demonstratie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-levens cyclus

Hieronder volgt een voor beeld van een overzicht van de CI/CD-levens cyclus in een Azure-data factory die is geconfigureerd met Azure opslag plaatsen git. Zie [broncode beheer in azure Data Factory](source-control.md)voor meer informatie over het configureren van een Git-opslag plaats.

1.  Een ontwikkelings data factory wordt gemaakt en geconfigureerd met Azure opslag plaatsen git. Alle ontwikkel aars moeten toestemming hebben om Data Factory resources als pijp lijnen en gegevens sets te schrijven.

1.  Wanneer de ontwikkel aars wijzigingen aanbrengen in hun functie vertakkingen, kunnen ze fouten opsporen in hun pijplijn uitvoeringen met hun meest recente wijzigingen. Zie [iteratieve ontwikkeling en fout opsporing met Azure Data Factory](iterative-development-debugging.md)voor meer informatie over het opsporen van fouten in een pijplijn uitvoering.

1.  Nadat de ontwikkel aars zijn tevreden met hun wijzigingen, maken ze een pull-aanvraag van hun functie vertakking in de vertakking Master of collaboration om hun wijzigingen te verkrijgen die door peers worden gecontroleerd.

1.  Nadat een pull-aanvraag is goedgekeurd en wijzigingen zijn samengevoegd in de hoofd vertakking, kunnen de wijzigingen worden gepubliceerd in de ontwikkelings-Factory.

1.  Wanneer het team gereed is voor het implementeren van de wijzigingen in de test-Factory en vervolgens naar de productie-Factory, exporteert het team de Resource Manager-sjabloon vanuit de hoofd vertakking.

1.  De geëxporteerde Resource Manager-sjabloon wordt geïmplementeerd met verschillende parameter bestanden naar de test-Factory en de productie-Factory.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Een resource manager-sjabloon maken voor elke omgeving

1. Selecteer in de lijst **arm-sjabloon** de optie **arm-sjabloon exporteren** om de Resource Manager-sjabloon voor uw Data Factory te exporteren in de ontwikkel omgeving.

   ![Een resource manager-sjabloon exporteren](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Selecteer in uw test-en productie gegevens fabrieken de optie **arm-sjabloon importeren**. Met deze actie gaat u naar de Azure Portal, waar u de geëxporteerde sjabloon kunt importeren. Selecteer **uw eigen sjabloon bouwen in de editor** om de Resource Manager-sjabloon editor te openen.

   ![Uw eigen sjabloon bouwen](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecteer **bestand laden**en selecteer vervolgens de gegenereerde Resource Manager-sjabloon.

   ![Sjabloon bewerken](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Voer in de sectie instellingen de configuratie waarden in, zoals gekoppelde service referenties. Wanneer u klaar bent, selecteert u **kopen** om de Resource Manager-sjabloon te implementeren.

   ![De sectie instellingen](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Verbindingsreeksen

Zie het artikel van de connector voor meer informatie over het configureren van verbindings reeksen. Zie bijvoorbeeld voor Azure SQL Database [gegevens kopiëren naar of van Azure SQL database met behulp van Azure Data Factory](connector-azure-sql-database.md). Als u een connection string wilt controleren, kunt u de code weergave voor de resource openen in de Data Factory UX. In de code weergave wordt het wacht woord of account sleutel gedeelte van de connection string verwijderd. Als u de code weergave wilt openen, selecteert u het pictogram dat hier is gemarkeerd:

![Open de code weergave om de connection string te bekijken](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Continue integratie automatiseren met Azure pipelines-releases

Hieronder vindt u een hand leiding voor het instellen van een Azure pipeline-release, waarmee de implementatie van een data factory naar meerdere omgevingen wordt geautomatiseerd.

![Diagram van doorlopende integratie met Azure-pijp lijnen](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Vereisten

-   Een Azure-abonnement dat is gekoppeld aan Visual Studio Team Foundation Server of Azure opslag plaatsen dat gebruikmaakt van het [Azure Resource Manager service-eind punt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Een data factory dat is geconfigureerd met Azure opslag plaatsen Git-integratie.

-   Een [Azure-sleutel kluis](https://azure.microsoft.com/services/key-vault/) die de geheimen voor elke omgeving bevat.

### <a name="set-up-an-azure-pipelines-release"></a>Een Azure pipelines-release instellen

1.  Open in [Azure DevOps](https://dev.azure.com/)het project dat is geconfigureerd met uw Data Factory.

1.  Selecteer aan de linkerkant van de pagina **pijp lijnen**en selecteer vervolgens **releases**.

    ![Selecteer pijp lijnen, releases](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecteer **nieuwe pijp lijn**of, als u bestaande pijp lijnen hebt, selecteert u **Nieuw** en vervolgens **nieuwe release pijplijn**.

1.  Selecteer de **lege taak** sjabloon.

    ![Lege taak selecteren](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Voer in het vak **naam fase** de naam van uw omgeving in.

1.  Selecteer **artefact toevoegen**en selecteer vervolgens de opslag plaats die is geconfigureerd met uw Data Factory. Selecteer **adf_publish** voor de **standaard vertakking**. Selecteer **laatste uit standaard vertakking**voor de **standaard versie**.

    ![Een artefact toevoegen](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Een Azure Resource Manager implementatie taak toevoegen:

    a.  Selecteer in de weer gave fase de optie **fase taken weer geven**.

    ![Fase weergave](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Een nieuwe taak maken. Zoek naar de implementatie van een **Azure-resource groep**en selecteer vervolgens **toevoegen**.

    c.  Selecteer in de implementatie taak het abonnement, de resource groep en de locatie voor het doel data factory. Geef indien nodig referenties op.

    d.  Selecteer in de lijst **actie** de optie **resource groep maken of bijwerken**.

    e.  Selecteer de knop met het weglatings teken ( **...** ) naast het vak **sjabloon** . Blader naar de Azure Resource Manager sjabloon die u hebt gemaakt met behulp van **arm-sjabloon importeren** in het gedeelte [een resource manager-sjabloon maken voor elke omgeving](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) van dit artikel. Zoek naar dit bestand in de map <FactoryName> van de adf_publish vertakking.

    f.  Selecteren **...** Naast het vak **sjabloon parameters** om het parameter bestand te kiezen. Het bestand dat u kiest, is afhankelijk van het feit of u een kopie hebt gemaakt of het standaard bestand ARMTemplateParametersForFactory. json gebruikt.

    g.  Selecteren **...** Naast het vak **sjabloon parameters negeren** en voer de gegevens voor de doel Data Factory in. Voer de naam van het geheim tussen dubbele aanhalings tekens in voor de referenties die afkomstig zijn van Azure Key Vault. Als de naam van het geheim bijvoorbeeld cred1 is, voert u **$ (cred1)** in voor deze waarde.

    h. Selecteer **Incrementeel** voor de **implementatie modus**.

    > [!WARNING]
    > Als u **volt ooien** voor de **implementatie modus**selecteert, kunnen bestaande resources worden verwijderd, inclusief alle resources in de doel resource groep die niet zijn gedefinieerd in de Resource Manager-sjabloon.

    ![Data Factory Prod-implementatie](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Sla de release pijplijn op.

1. Selecteer **vrijgave maken**om een release te activeren.

   ![Selecteer release maken](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Geheimen van Azure Key Vault ophalen

Als u geheimen in een Azure Resource Manager sjabloon wilt door geven, raden we u aan Azure Key Vault te gebruiken met de versie van Azure pipeline.

Er zijn twee manieren om geheimen af te handelen:

1.  Voeg de geheimen toe aan het parameter bestand. Zie [Azure Key Vault gebruiken om de waarde van een beveiligde para meter door te geven tijdens de implementatie](../azure-resource-manager/templates/key-vault-parameter.md)voor meer informatie.

    Maak een kopie van het parameter bestand dat is geüpload naar de Publish-vertakking. Stel de waarden in van de para meters die u wilt ophalen van Key Vault met behulp van de volgende indeling:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Wanneer u deze methode gebruikt, wordt het geheim automatisch opgehaald uit de sleutel kluis.

    Het parameter bestand moet ook in de publicatie vertakking staan.

-  Voeg een [Azure Key Vault-taak](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) toe vóór de Azure Resource Manager implementatie taak die in de vorige sectie wordt beschreven:

    1.  Maak een nieuwe taak op het tabblad **taken** . Zoek naar **Azure Key Vault** en voeg deze toe.

    1.  Selecteer in de taak Key Vault het abonnement waarin u de sleutel kluis hebt gemaakt. Geef indien nodig referenties op en selecteer vervolgens de sleutel kluis.

    ![Een Key Vault taak toevoegen](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Machtigingen verlenen aan de Azure pipelines-agent

   De Azure Key Vault taak kan mislukken met de fout toegang geweigerd als de juiste machtigingen niet zijn ingesteld. Down load de logboeken voor de release en zoek het. ps1-bestand dat de opdracht bevat om machtigingen te verlenen aan de Azure pipelines-agent. U kunt de opdracht rechtstreeks uitvoeren. U kunt ook de principal-ID uit het bestand kopiëren en het toegangs beleid hand matig toevoegen in de Azure Portal. `Get` en `List` zijn de mini maal vereiste machtigingen.

### <a name="update-active-triggers"></a>Actieve triggers bijwerken

De implementatie kan mislukken als u probeert actieve triggers bij te werken. Als u actieve triggers wilt bijwerken, moet u deze hand matig stoppen en opnieuw opstarten na de implementatie. U kunt dit doen met behulp van een Azure PowerShell taak:

1.  Voeg op het tabblad **taken** van de release een **Azure PowerShell** taak toe.

1.  Selecteer **Azure Resource Manager** als het verbindings type en selecteer vervolgens uw abonnement.

1.  Selecteer **in-line script** als het script type en geef vervolgens uw code op. Met de volgende code worden de triggers gestopt:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![Azure PowerShell taak](media/continuous-integration-deployment/continuous-integration-image11.png)

U kunt vergelijk bare stappen uitvoeren (met de functie `Start-AzDataFactoryV2Trigger`) om de triggers na de implementatie opnieuw te starten.

> [!IMPORTANT]
> In de CI/CD-scenario's moet het type Integration runtime (IR) in verschillende omgevingen hetzelfde zijn. Als u bijvoorbeeld een zelf-hostende IR in de ontwikkel omgeving hebt, moet dezelfde IR ook van het type zelf-hostend zijn in andere omgevingen, zoals testen en productie. En als u integratie-runtime in meerdere fasen deelt, moet u de Integration Runtimes als gekoppelde zelf-hostende in alle omgevingen configureren, zoals ontwikkeling, testen en productie.

#### <a name="sample-pre--and-post-deployment-script"></a>Voor beeld van een script vóór en na implementatie

In het volgende voorbeeld script ziet u hoe u triggers stopt voordat de implementatie wordt gestart en deze later opnieuw opstarten. Het script bevat ook code voor het verwijderen van resources die zijn verwijderd. Zie [install Azure PowerShell in Windows with PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps)(Engelstalig) om de nieuwste versie van Azure PowerShell te installeren.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -DataFactoryName miliutesteu04 -ResourceGroupName miliu
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -DataFactoryName miliutesteu04 -ResourceGroupName miliu
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Aangepaste para meters gebruiken met de Resource Manager-sjabloon

Als u zich in de GIT-modus bevindt, kunt u de standaard eigenschappen in uw Resource Manager-sjabloon onderdrukken om eigenschappen in te stellen die zijn vastgelegd in de sjabloon en eigenschappen die in een vastgelegde code zijn vastgelegd. In deze scenario's wilt u mogelijk de standaard sjabloon parameterisering overschrijven:

* U gebruikt automatische CI/CD en u wilt enkele eigenschappen wijzigen tijdens de implementatie van Resource Manager, maar de eigenschappen zijn niet standaard ingesteld op para meters.
* Uw fabriek is zo groot dat de standaard Resource Manager-sjabloon ongeldig is omdat deze meer dan de Maxi maal toegestane para meters (256) heeft.

Onder deze omstandigheden kunt u de standaard sjabloon parameterisering overschrijven door een bestand te maken met de naam arm-sjabloon-para meters-definition. json in de map die is opgegeven als de hoofdmap voor de data factory Git-integratie. U moet die exacte bestands naam gebruiken. Data Factory leest dit bestand van de vertakking waar u zich momenteel bevindt in de Azure Data Factory Portal, niet alleen vanuit de vertakking voor samen werking. U kunt het bestand maken of bewerken vanuit een persoonlijke vertakking, waar u uw wijzigingen kunt testen door **arm-sjabloon exporteren** te selecteren in de gebruikers interface. U kunt het bestand vervolgens samen voegen in de vertakking voor samen werking. Als er geen bestand wordt gevonden, wordt de standaard sjabloon gebruikt.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxis van een bestand met aangepaste para meters

Hieronder vindt u enkele richt lijnen die u moet volgen wanneer u het bestand met aangepaste para meters maakt. Het bestand bestaat uit een sectie voor elk entiteits type: trigger, pijp lijn, gekoppelde service, gegevensset, Integration runtime, enzovoort.
* Voer het pad naar de eigenschap in onder het relevante entiteits type.
* Als u de naam van een eigenschap instelt op `*`, geeft u aan dat u alle eigenschappen daaronder wilt para meters (alleen naar het eerste niveau, niet recursief). U kunt ook uitzonde ringen voor deze configuratie opgeven.
* Als u de waarde van een eigenschap instelt als een teken reeks, geeft u aan dat u de eigenschap wilt para meters. Gebruik de notatie `<action>:<name>:<stype>`.
   *  `<action>` kan een van de volgende tekens zijn:
      * `=` houdt in dat de huidige waarde wordt ingesteld als de standaard waarde voor de para meter.
      * bij `-` wordt niet de standaard waarde voor de para meter bewaard.
      * `|` is een speciaal geval voor geheimen van Azure Key Vault voor verbindings reeksen of sleutels.
   * `<name>` de naam van de para meter. Als deze leeg is, wordt de naam van de eigenschap gebruikt. Als de waarde begint met een `-` teken, wordt de naam Inge kort. `AzureStorage1_properties_typeProperties_connectionString` zou bijvoorbeeld worden inge kort tot `AzureStorage1_connectionString`.
   * `<stype>` is het type para meter. Als `<stype>` leeg is, is het standaard type `string`. Ondersteunde waarden: `string`, `bool`, `number`, `object`en `securestring`.
* Het opgeven van een matrix in het definitie bestand geeft aan dat de overeenkomende eigenschap in de sjabloon een matrix is. Data Factory doorloopt alle objecten in de matrix door gebruik te maken van de definitie die is opgegeven in het object Integration runtime van de matrix. Het tweede object, een teken reeks, wordt de naam van de eigenschap, die wordt gebruikt als de naam voor de para meter voor elke iteratie.
* Een definitie kan niet specifiek zijn voor een resource-exemplaar. Elke wille keurige definitie is van toepassing op alle resources van dat type.
* Standaard zijn alle beveiligde teken reeksen, zoals Key Vault geheimen en beveiligde teken reeksen, zoals verbindings reeksen, sleutels en tokens, para meters.
 
### <a name="sample-parameterization-template"></a>Voor beeld van parameterisering-sjabloon

Hier volgt een voor beeld van hoe een parameterisering-sjabloon eruit kan zien:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Hier volgt een uitleg van de manier waarop de vorige sjabloon is samengesteld, onderverdeeld op resource type.

#### <a name="pipelines"></a>Pijplijnen
    
* Een eigenschap in het pad `activities/typeProperties/waitTimeInSeconds` is para meters. Elke activiteit in een pijp lijn met de naam `waitTimeInSeconds`, bijvoorbeeld de `Wait` activiteit), wordt als een getal met een standaard naam vastgelegd. Maar heeft geen standaard waarde in de Resource Manager-sjabloon. Het is een verplichte invoer tijdens de implementatie van Resource Manager.
* Op dezelfde manier is een eigenschap met de naam `headers` (bijvoorbeeld in een `Web`-activiteit) para meters van het type `object` (JObject). Het heeft een standaard waarde. Dit is dezelfde waarde als die van de bron-Factory.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle eigenschappen onder het pad `typeProperties` zijn para meters met hun respectievelijke standaard waarden. Er zijn bijvoorbeeld twee eigenschappen onder `IntegrationRuntimes` type-eigenschappen: `computeProperties` en `ssisProperties`. Beide eigenschaps typen worden gemaakt met hun respectievelijke standaard waarden en typen (object).

#### <a name="triggers"></a>Triggers

* Onder `typeProperties`zijn twee eigenschappen para meters. De eerste is `maxConcurrency`, die is opgegeven om een standaard waarde te hebben en is van het type`string`. Deze heeft de standaard parameter naam `<entityName>_properties_typeProperties_maxConcurrency`.
* De eigenschap `recurrence` is ook para meters. Hieronder worden alle eigenschappen op dat niveau opgegeven om para meters te worden ingesteld als teken reeksen, met standaard waarden en parameter namen. Een uitzonde ring is de eigenschap `interval`, die para meters is van het type `number`. De parameter naam is een achtervoegsel met `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Op dezelfde manier is de eigenschap `freq` een teken reeks en wordt de para meter als teken reeks. De eigenschap `freq` is echter para meters zonder standaard waarde. De naam is inge kort en achtervoegsel. Bijvoorbeeld `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Gekoppelde services zijn uniek. Omdat gekoppelde services en gegevens sets een breed scala van typen hebben, kunt u type-specifieke aanpassing opgeven. In dit voor beeld wordt voor alle gekoppelde services van het type `AzureDataLakeStore`een specifieke sjabloon toegepast. Voor alle andere (via `*`) wordt een andere sjabloon toegepast.
* De eigenschap `connectionString` wordt para meters als een `securestring` waarde. Er is geen standaard waarde. Er wordt een kortere parameter naam gebruikt met `connectionString`.
* De eigenschap `secretAccessKey` gebeurt in een `AzureKeyVaultSecret` (bijvoorbeeld in een gekoppelde service van Amazon S3). De para meter wordt automatisch ingesteld als een Azure Key Vault geheim en opgehaald uit de geconfigureerde sleutel kluis. U kunt ook de sleutel kluis zelf para meters.

#### <a name="datasets"></a>Gegevenssets

* Hoewel het type-specifieke aanpassing beschikbaar is voor gegevens sets, kunt u een configuratie opgeven zonder expliciet een configuratie op het \*niveau te hebben. In het vorige voor beeld zijn alle eigenschappen van de gegevensset onder `typeProperties` para meters.

### <a name="default-parameterization-template"></a>Standaard parameterisering-sjabloon

Hier volgt de huidige standaard sjabloon parameterisering. Als u slechts een paar para meters wilt toevoegen, is het wellicht een goed idee om deze sjabloon rechtstreeks te bewerken, omdat u de bestaande parameterisering-structuur niet kwijtraakt.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

In het volgende voor beeld ziet u hoe u een enkele waarde kunt toevoegen aan de standaard sjabloon parameterisering. We willen alleen een bestaande Azure Databricks interactieve cluster-ID voor een gekoppelde Databricks-service toevoegen aan het parameter bestand. Dit bestand is hetzelfde als het vorige bestand, met uitzonde ring van de toevoeging van `existingClusterId` onder het veld eigenschappen van `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Gekoppelde Resource Manager-sjablonen

Als u CI/CD voor uw gegevens fabrieken hebt ingesteld, kunt u de limieten van de Azure Resource Manager-sjabloon overschrijden, omdat uw fabriek groter groeit. Een limiet is bijvoorbeeld het maximum aantal resources in een resource manager-sjabloon. Data Factory nu gekoppelde Resource Manager-sjablonen genereert om grote fabrieken te kunnen voorzien van de volledige Resource Manager-sjabloon voor een fabriek. Met deze functie wordt de gehele nettolading van de fabriek onderverdeeld in verschillende bestanden, zodat u niet beperkt bent door de limieten.

Als u Git hebt geconfigureerd, worden de gekoppelde sjablonen gegenereerd en opgeslagen in de volledige Resource Manager-sjablonen in de adf_publish vertakking in een nieuwe map met de naam linkedTemplates:

![Map gekoppelde Resource Manager-sjablonen](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De gekoppelde Resource Manager-sjablonen bestaan gewoonlijk uit een hoofd sjabloon en een reeks onderliggende sjablonen die zijn gekoppeld aan de Master. De bovenliggende sjabloon heet ArmTemplate_master. json en de onderliggende sjablonen hebben de naam van het patroon ArmTemplate_0. json, ArmTemplate_1. json, enzovoort. 

Als u gekoppelde sjablonen wilt gebruiken in plaats van de volledige Resource Manager-sjabloon, moet u uw CI/CD-taak bijwerken zodat deze verwijst naar ArmTemplate_master. json in plaats van ArmTemplateForFactory. json (de volledige Resource Manager-sjabloon). Resource Manager vereist ook dat u de gekoppelde sjablonen uploadt naar een opslag account, zodat Azure deze kan openen tijdens de implementatie. Zie [gekoppelde Resource Manager-sjablonen implementeren met VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)voor meer informatie.

Vergeet niet om de Data Factory scripts in uw CI/CD-pijp lijn vóór en na de implementatie taak toe te voegen.

Als u geen git hebt geconfigureerd, kunt u de gekoppelde sjablonen openen via **arm-sjabloon exporteren** in de lijst met **arm** -sjablonen.

## <a name="hotfix-production-branch"></a>Productie vertakking hotfix

Als u een Factory implementeert voor productie en beseft dat er een fout is die direct moet worden opgelost, maar u niet de huidige collaboration Branch kunt implementeren, moet u mogelijk een hotfix implementeren. Deze aanpak is net zo bekend als snelle herstel techniek of QFE.

1.  In azure DevOps gaat u naar de release die is geïmplementeerd voor productie. Zoek de laatste door voering die is geïmplementeerd.

2.  Haal de ID van de samenwerkings vertakking op uit het bericht door voeren.

3.  Maak een nieuwe hotfix vertakking van die doorvoer.

4.  Ga naar de Azure Data Factory UX en schakel over naar de hotfix-vertakking.

5.  Los de fout op met behulp van de Azure Data Factory UX. Test uw wijzigingen.

6.  Nadat de oplossing is gecontroleerd, selecteert u **arm-sjabloon exporteren** om de hotfix Resource Manager-sjabloon op te halen.

7.  Controleer deze build hand matig in de vertakking adf_publish.

8.  Als u uw release pijplijn hebt geconfigureerd om automatisch te activeren op basis van adf_publish check-ins, wordt een nieuwe release automatisch gestart. U kunt ook hand matig een release in de wachtrij plaatsen.

9.  Implementeer de hotfix-release voor de test-en productie fabrieken. Deze release bevat de vorige nettolading van de productie plus de oplossing die u in stap 5 hebt gemaakt.

10. Voeg de wijzigingen van de hotfix toe aan de ontwikkelings vertakking, zodat latere versies niet dezelfde fout bevatten.

## <a name="best-practices-for-cicd"></a>Aanbevolen procedures voor CI/CD

Als u gebruik wilt maken van Git-integratie met uw data factory en een CI/CD-pijp lijn hebt die uw wijzigingen van de ontwikkeling naar de test verplaatst en vervolgens naar productie, raden we u aan deze aanbevolen procedures te volgen:

-   **Git-integratie**. U hoeft alleen uw ontwikkel data factory te configureren met git-integratie. Wijzigingen in testen en productie worden geïmplementeerd via CI/CD en beschikken niet over git-integratie.

-   **Data Factory CI/cd-script**. Vóór de implementatie stap van resource manager in CI/CD moet u bepaalde taken uitvoeren, zoals Triggers stoppen en opnieuw starten en het opschonen uitvoeren. U wordt aangeraden Power shell-scripts te gebruiken vóór en na de implementatie. Zie [actieve triggers bijwerken](#update-active-triggers)voor meer informatie.

-   **Integration Runtimes en delen**. Integration Runtimes veranderen niet vaak en zijn vergelijkbaar in alle fasen van uw CI/CD. Data Factory verwacht dat u dezelfde naam en hetzelfde type Integration runtime in alle fasen van CI/CD hebt. Als u integratie-Runtimes in alle fasen wilt delen, kunt u overwegen een ternaire fabriek alleen te gebruiken om de gedeelde integratie-runtime te bevatten. U kunt deze gedeelde Factory in al uw omgevingen gebruiken als het type gekoppelde integratie runtime.

-   **Key Vault**. Wanneer u gekoppelde services gebruikt op basis van Azure Key Vault, kunt u ze verder benutten door afzonderlijke sleutel kluizen voor verschillende omgevingen te bewaren. U kunt ook afzonderlijke machtigings niveaus configureren voor elke sleutel kluis. Het is bijvoorbeeld mogelijk dat uw team leden geen machtigingen voor productie geheimen mogen hebben. Als u deze aanpak volgt, wordt u aangeraden dezelfde geheime namen in alle fasen te houden. Als u dezelfde namen behoudt, hoeft u uw Resource Manager-sjablonen niet te wijzigen in de CI-en CD-omgevingen, omdat de naam van de sleutel kluis een van de para meters van de Resource Manager-sjabloon is.

## <a name="unsupported-features"></a>Niet-ondersteunde functies

- Data Factory is standaard niet toegestaan Cherry-orderverzamelen van door voeringen of selectief publiceren van resources. De publicatie bevat alle wijzigingen die zijn aangebracht in de data factory.

    - Data Factory-entiteiten zijn afhankelijk van elkaar. Triggers zijn bijvoorbeeld afhankelijk van pijp lijnen, en pijp lijnen zijn afhankelijk van gegevens sets en andere pijp lijnen. Selectief publiceren van een subset van bronnen kan leiden tot onverwacht gedrag en fouten.
    - In zeldzame gevallen wanneer u selectief publiceren nodig hebt, kunt u overwegen om een hotfix te gebruiken. Zie [hotfix Production Branch](#hotfix-production-branch)(Engelstalig) voor meer informatie.

-   U kunt niet publiceren vanuit privé-filialen.

-   U kunt momenteel geen projecten hosten op bitbucket.

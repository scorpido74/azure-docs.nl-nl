---
title: Continue integratie en levering in Azure Data Factory
description: Leer hoe u continue integratie en levering gebruiken om Data Factory-pijplijnen van de ene omgeving (ontwikkeling, test, productie) naar de andere te verplaatsen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 6aad01808ad155b745b614d8de6009386f0d2914
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687963"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Continue integratie en levering in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het testen van elke wijziging in uw codebase automatisch en zo vroeg mogelijk.Continue levering volgt de tests die plaatsvinden tijdens continue integratie en duwt wijzigingen in een faserings- of productiesysteem.

In Azure Data Factory betekent continue integratie en levering (CI/CD) het verplaatsen van Data Factory-pijplijnen van de ene omgeving (ontwikkeling, test, productie) naar de andere. U de UX-integratie van Data Factory gebruiken met Azure Resource Manager-sjablonen om CI/CD uit te brengen.

In de UX Gegevensfabriek u een resourcemanagersjabloon genereren in het vervolgkeuzemenu **ARM-sjabloon.** Wanneer u **ARM-sjabloon exporteren selecteert,** genereert de portal de sjabloon Resourcebeheer voor de gegevensfabriek en een configuratiebestand met al uw verbindingstekenreeksen en andere parameters. Vervolgens maakt u één configuratiebestand voor elke omgeving (ontwikkeling, test, productie). Het sjabloonbestand voor resourcebeheer blijft hetzelfde voor alle omgevingen.

Voor een negen minuten durende introductie van deze functie en een demonstratie, bekijk deze video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-levenscyclus

Hieronder vindt u een voorbeeldoverzicht van de CI/CD-levenscyclus in een Azure-gegevensfabriek die is geconfigureerd met Azure Repos Git. Zie [Bronbeheer in Azure Data Factory](source-control.md)voor meer informatie over het configureren van een Git-repository.

1.  Er wordt een ontwikkelingsgegevensfabriek gemaakt en geconfigureerd met Azure Repos Git. Alle ontwikkelaars moeten toestemming hebben om gegevensfabriekbronnen zoals pijplijnen en gegevenssets te maken.

1.  Terwijl de ontwikkelaars wijzigingen aanbrengen in hun functiebranches, debuggen ze hun pijplijnuitvoeringen met hun meest recente wijzigingen. Zie [Iteratieve ontwikkeling en foutopsporing met Azure Data Factory](iterative-development-debugging.md)voor meer informatie over het opsporen van een pijplijn.

1.  Nadat de ontwikkelaars tevreden zijn met hun wijzigingen, maken ze een pull-aanvraag van hun functiebranch naar de master- of samenwerkingsbranch om hun wijzigingen te laten beoordelen door collega's.

1.  Nadat een pull-aanvraag is goedgekeurd en wijzigingen zijn samengevoegd in de hoofdbranch, kunnen de wijzigingen worden gepubliceerd in de ontwikkelingsfabriek.

1.  Wanneer het team klaar is om de wijzigingen in de testfabriek en vervolgens naar de productiefabriek te implementeren, exporteert het team de resourcemanagersjabloon vanuit de hoofdbranch.

1.  De sjabloon geëxporteerde Resource Manager wordt geïmplementeerd met verschillende parameterbestanden naar de testfabriek en de productiefabriek.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Een resourcemanagersjabloon maken voor elke omgeving

1. Selecteer in de lijst **ARM-sjabloon** de optie **ARM-sjabloon exporteren** om de sjabloon Resourcemanager voor uw gegevensfabriek in de ontwikkelomgeving te exporteren.

   ![Een resourcebeheersjabloon exporteren](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Selecteer **ARM-sjabloon importeren**in uw test- en productiegegevensfabrieken . Met deze actie gaat u naar de Azure-portal, waar u de geëxporteerde sjabloon importeren. Selecteer **Uw eigen sjabloon maken in de editor** om de sjablooneditor resourcebeheer te openen.

   ![Uw eigen sjabloon maken](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecteer **Bestand laden**en selecteer vervolgens de sjabloon gegenereerde Resourcemanager. Dit is het **bestand arm_template.json** in het zip-bestand dat is geëxporteerd in stap 1.

   ![Sjabloon bewerken](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Voer in de sectie Instellingen de configuratiewaarden in, zoals gekoppelde servicereferenties. Wanneer u klaar bent, selecteert u **Kopen** om de sjabloon Resourcebeheer te implementeren.

   ![Sectie Instellingen](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Verbindingsreeksen

Zie het artikel van de connector voor informatie over het configureren van verbindingstekenreeksen. Zie Bijvoorbeeld voor Azure SQL Database [gegevens kopiëren van of naar Azure SQL Database met Azure Data Factory](connector-azure-sql-database.md). Als u een verbindingstekenreeks wilt verifiëren, u de codeweergave voor de bron openen in de UX gegevensfabriek. In de codeweergave wordt het wachtwoord of het gedeelte van de accountsleutel van de verbindingstekenreeks verwijderd. Als u de codeweergave wilt openen, selecteert u het pictogram dat hier wordt gemarkeerd:

![Codeweergave openen om de verbindingstekenreeks te zien](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Continue integratie automatiseren met Azure Pipelines-releases

Hieronder volgt een handleiding voor het instellen van een Azure Pipelines-release, die de implementatie van een gegevensfabriek naar meerdere omgevingen automatiseert.

![Diagram voor continue integratie met Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Vereisten

-   Een Azure-abonnement dat is gekoppeld aan Visual Studio Team Foundation Server of Azure Repos dat gebruikmaakt van het eindpunt van de [Azure Resource Manager-service](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Een gegevensfabriek die is geconfigureerd met Azure Repos Git-integratie.

-   Een [Azure-sleutelkluis](https://azure.microsoft.com/services/key-vault/) met de geheimen voor elke omgeving.

### <a name="set-up-an-azure-pipelines-release"></a>Een Azure Pipelines-release instellen

1.  Open in [Azure DevOps](https://dev.azure.com/)het project dat is geconfigureerd met uw gegevensfabriek.

1.  Selecteer aan de linkerkant van de pagina **Pijplijnen**en selecteer **Releases**.

    ![Pijplijnen, releases selecteren](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecteer **Nieuwe pijplijn**of selecteer, als u bestaande pijplijnen hebt, **Nieuw** en vervolgens **Nieuwe releasepijplijn**.

1.  Selecteer de **sjabloon Leeg maken.**

    ![Lege taak selecteren](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Voer in het vak **Werkgebied** naam in, voer de naam van uw omgeving in.

1.  Selecteer **Artefact toevoegen**en selecteer vervolgens de opslagplaats die is geconfigureerd met uw gegevensfabriek. Selecteer **adf_publish** voor de **standaardvertakking**. Selecteer Voor de **standaardversie**De optie **Laatste uit de standaardvertakking**.

    ![Een artefact toevoegen](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Voeg een Azure Resource Manager Deployment-taak toe:

    a.  Selecteer in de faseweergave De optie **Werkgebiedtaken weergeven**.

    ![Podiumweergave](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Maak een nieuwe taak. Zoek naar **Azure Resource Group Deployment**en selecteer **Toevoegen**.

    c.  Selecteer in de taak Implementatie het abonnement, de brongroep en de locatie voor de doelgegevensfabriek. Geef indien nodig referenties op.

    d.  Selecteer **resourcegroep** **maken of bijwerken in**de actielijst.

    e.  Selecteer de knop ellips (**... )** naast het vak **Sjabloon.** Blader naar de Azure Resource Manager-sjabloon die u hebt gemaakt met **ARM-sjabloon importeren** in de [sjabloon Een resourcebeheer maken voor elke omgevingsectie](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) van dit artikel. Zoek naar dit <FactoryName> bestand in de map van de adf_publish branch.

    f.  Selecteer **...** naast het vak **Sjabloonparameters** om het parametersbestand te kiezen. Het bestand dat u kiest, is afhankelijk van of u een kopie hebt gemaakt of het standaardbestand ARMTemplateParametersForFactory.json gebruikt.

    g.  Selecteer **...** naast het vak **Sjabloonparameters overschrijven** en voer de gegevens voor de doelgegevensfabriek in. Voer de naam van het geheim in tussen dubbele aanhalingstekens voor referenties die afkomstig zijn van Azure Key Vault. Als de naam van het geheim bijvoorbeeld cred1 is, voert u **'$(cred1)"** in voor deze waarde.

    h. Selecteer **Incrementeel** voor de **implementatiemodus**.

    > [!WARNING]
    > Als u **Voltooid** selecteert voor de **implementatiemodus,** kunnen bestaande resources worden verwijderd, inclusief alle bronnen in de doelgroep die niet zijn gedefinieerd in de sjabloon Resourcebeheer.

    ![Data Factory Prod-implementatie](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Sla de releasepijplijn op.

1. Als u een release wilt activeren, selecteert **u Release maken**.

   ![Release maken selecteren](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> In CI/CD-scenario's moet het type integration runtime (IR) in verschillende omgevingen hetzelfde zijn. Als u bijvoorbeeld een zelfgehoste IR in de ontwikkelomgeving hebt, moet dezelfde IR ook van type zelf worden gehost in andere omgevingen, zoals test en productie. Als u de implementatiekeer van integratie in meerdere fasen deelt, moet u de implementatietijden van de integratie configureren als gekoppeld zelf gehost in alle omgevingen, zoals ontwikkeling, test en productie.

### <a name="get-secrets-from-azure-key-vault"></a>Krijg geheimen van Azure Key Vault

Als u geheimen hebt om door te geven in een Azure Resource Manager-sjabloon, raden we u aan Azure Key Vault te gebruiken met de azure pipelines-release.

Er zijn twee manieren om met geheimen om te gaan:

1.  Voeg de geheimen toe aan het parametersbestand. Zie [Azure Key Vault gebruiken om veilige parameterwaarde door te geven tijdens de implementatie](../azure-resource-manager/templates/key-vault-parameter.md)voor meer informatie.

    Maak een kopie van het parametersbestand dat is geüpload naar de publicatiebranch. Stel de waarden in van de parameters die u uit Key Vault wilt halen met deze indeling:

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

    Wanneer u deze methode gebruikt, wordt het geheim automatisch uit de sleutelkluis gehaald.

    Het parametersbestand moet ook in de publicatiebranch staan.

1. Voeg een [Azure Key Vault-taak](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) toe voordat de Azure Resource Manager-implementatietaak die in de vorige sectie is beschreven:

    1.  Maak op het tabblad **Taken** een nieuwe taak. Zoek naar **Azure Key Vault** en voeg deze toe.

    1.  Selecteer in de taak Key Vault het abonnement waarin u de sleutelkluis hebt gemaakt. Geef indien nodig referenties op en selecteer vervolgens de sleutelkluis.

    ![Een key vault-taak toevoegen](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Machtigingen verlenen aan de Azure Pipelines-agent

De Azure Key Vault-taak kan mislukken met een fout Toegang geweigerd als de juiste machtigingen niet zijn ingesteld. Download de logboeken voor de release en zoek het PS1-bestand met de opdracht om machtigingen te geven aan de Azure Pipelines-agent. U de opdracht rechtstreeks uitvoeren. U de hoofd-id uit het bestand kopiëren en het toegangsbeleid handmatig toevoegen aan de Azure-portal. `Get`en `List` zijn de vereiste minimummachtigingen.

### <a name="update-active-triggers"></a>Actieve triggers bijwerken

Implementatie kan mislukken als u actieve triggers probeert bij te werken. Als u actieve triggers wilt bijwerken, moet u deze handmatig stoppen en deze opnieuw starten na de implementatie. U dit doen door een Azure PowerShell-taak te gebruiken:

1.  Voeg op het tabblad **Taken** van de release een **Azure PowerShell-taak** toe. Kies taakversie 4.*. 

1.  Selecteer het abonnement waarin uw fabriek zich bevindt.

1.  Selecteer **Scriptbestandspad** als scripttype. Hiervoor moet u uw PowerShell-script opslaan in uw repository. Het volgende PowerShell-script kan worden gebruikt om triggers te stoppen:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

U vergelijkbare stappen `Start-AzDataFactoryV2Trigger` uitvoeren (met de functie) om de triggers opnieuw te starten na implementatie.

### <a name="sample-pre--and-post-deployment-script"></a>Voorbeeld van pre- en post-deployment script

Het volgende voorbeeldscript kan worden gebruikt om triggers te stoppen voordat ze worden geïmplementeerd en deze daarna opnieuw te starten. Het script bevat ook code om verwijderde bronnen te verwijderen. Sla het script op in een Azure DevOps git-repository en verwijs ernaar via een Azure PowerShell-taak met versie 4.*.

Wanneer u een pre-implementatiescript uitvoert, moet u een variatie van de volgende parameters opgeven in het veld **Scriptargumenten.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Wanneer u een script na de implementatie uitvoert, moet u een variatie van de volgende parameters opgeven in het veld **Scriptargumenten.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-taak](media/continuous-integration-deployment/continuous-integration-image11.png)

Hier is het script dat kan worden gebruikt voor pre- en post-deployment. Het is goed voor verwijderde bronnen en resourcereferenties.

```powershell
param
(
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
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
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
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
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
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
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
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Aangepaste parameters gebruiken met Resource Manager-sjabloon

Als u de GIT-modus gebruikt, u de standaardeigenschappen in de sjabloon Resourcemanager overschrijven om eigenschappen in te stellen die zijn geparameteriseerd in de sjabloon en eigenschappen die hard gecodeerd zijn. Mogelijk wilt u de standaardparameterisatiesjabloon in deze scenario's overschrijven:

* U gebruikt geautomatiseerde CI/CD en u wilt sommige eigenschappen wijzigen tijdens de implementatie van Resource Manager, maar de eigenschappen worden niet standaard geparameteriseerd.
* Uw fabriek is zo groot dat de standaard resourcemanagersjabloon ongeldig is omdat deze meer dan de maximaal toegestane parameters heeft (256).

Als u onder deze voorwaarden de standaardparameterisatiesjabloon wilt overschrijven, maakt u een bestand met de naam **arm-template-parameters-definition.json** in de map die is opgegeven als de hoofdmap voor de git-integratie in de gegevensfabriek. Je moet die exacte bestandsnaam gebruiken. Data Factory leest dit bestand van welke branch u zich momenteel bevindt in de Azure Data Factory-portal, niet alleen vanuit de samenwerkingsbranche. U het bestand maken of bewerken vanuit een privé-filiaal, waar u uw wijzigingen testen door **ARM-sjabloon exporteren** in de gebruikersinterface te selecteren. U het bestand vervolgens samenvoegen in de samenwerkingsbranch. Als er geen bestand wordt gevonden, wordt de standaardsjabloon gebruikt.

> [!NOTE]
> Een aangepaste parameteriseringssjabloon wijzigt de parameterlimiet ARM-sjabloon van 256 niet. Hiermee u het aantal parametereigenschappen kiezen en verkleinen.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxis van een aangepast parametersbestand

Hieronder volgen enkele richtlijnen die u moet volgen wanneer u het aangepaste parametersbestand maakt. Het bestand bestaat uit een sectie voor elk entiteitstype: trigger, pipeline, linked service, dataset, integratieruntime, enzovoort.
* Voer het eigenschappenpad in onder het relevante entiteitstype.
* Als u een `*` eigenschapsnaam instelt op geeft u aan dat u alle eigenschappen eronder wilt parameteriseren (alleen tot op het eerste niveau, niet recursief). U ook uitzonderingen op deze configuratie verstrekken.
* Als u de waarde van een eigenschap als tekenreeks instelt, geeft u aan dat u de eigenschap wilt parameteriseren. Gebruik de `<action>:<name>:<stype>`indeling .
   *  `<action>` kan een van deze tekens zijn:
      * `=` betekent de huidige waarde behouden als de standaardwaarde voor de parameter.
      * `-` betekent dat u de standaardwaarde voor de parameter niet behoudt.
      * `|` is een speciaal geval voor geheimen van Azure Key Vault voor verbindingstekenreeksen of sleutels.
   * `<name>` is de naam van de parameter. Als het leeg is, neemt het de naam van het bezit. Als de waarde `-` begint met een teken, wordt de naam ingekort. Bijvoorbeeld, `AzureStorage1_properties_typeProperties_connectionString` zou worden ingekort `AzureStorage1_connectionString`tot .
   * `<stype>` is het type parameter. Als `<stype>` dit leeg is, `string`is het standaardtype . Ondersteunde waarden: `string` `bool`, `number` `object`, `securestring`, en .
* Als u een array in het definitiebestand opgeeft, geeft u aan dat de overeenkomende eigenschap in de sjabloon een array is. Data Factory doorloopt alle objecten in de array met behulp van de definitie die is opgegeven in het doel van de in-integratieruntime van de array. Het tweede object, een tekenreeks, wordt de naam van de eigenschap, die wordt gebruikt als de naam voor de parameter voor elke iteratie.
* Een definitie kan niet specifiek zijn voor een resourceinstantie. Elke definitie is van toepassing op alle resources van dat type.
* Standaard worden alle beveiligde tekenreeksen, zoals Key Vault-geheimen en beveiligde tekenreeksen, zoals verbindingstekenreeksen, sleutels en tokens, geparameteriseerd.
 
### <a name="sample-parameterization-template"></a>Sjabloon voor parameterisering van het voorbeeld

Hier volgt een voorbeeld van hoe een parameteriseringssjabloon eruit zou kunnen zien:

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
Hier volgt een uitleg over hoe de voorgaande sjabloon is opgebouwd, uitgesplitst naar resourcetype.

#### <a name="pipelines"></a>Pijplijnen
    
* Elke eigenschap in `activities/typeProperties/waitTimeInSeconds` het pad wordt geparameteriseerd. Elke activiteit in een pijplijn met een `waitTimeInSeconds` eigenschap op `Wait` codeniveau met de naam (bijvoorbeeld de activiteit) wordt geparameteriseerd als een getal, met een standaardnaam. Er is echter geen standaardwaarde in de sjabloon Resourcemanager. Het is een verplichte invoer tijdens de implementatie van Resource Manager.
* Op dezelfde manier `headers` wordt een eigenschap `Web` die wordt aangeroepen `object` (bijvoorbeeld in een activiteit) geparameteriseerd met tekst (JObject). Het heeft een standaardwaarde, die dezelfde waarde heeft als die van de bronfabriek.

#### <a name="integrationruntimes"></a>IntegratieRuntimes

* Alle eigenschappen onder `typeProperties` het pad worden geparameteriseerd met hun respectievelijke standaardwaarden. Er zijn bijvoorbeeld twee `IntegrationRuntimes` eigenschappen onder `computeProperties` `ssisProperties`typeeigenschappen: en . Beide eigenschapstypen worden gemaakt met hun respectievelijke standaardwaarden en typen (Object).

#### <a name="triggers"></a>Triggers

* Onder `typeProperties`, twee eigenschappen zijn parameterized. De eerste `maxConcurrency`is , die is opgegeven om een`string`standaardwaarde te hebben en is van het type . Het heeft de `<entityName>_properties_typeProperties_maxConcurrency`standaard parameternaam .
* De `recurrence` eigenschap is ook parameterized. Onder het, alle eigenschappen op dat niveau zijn opgegeven te worden parameterized als tekenreeksen, met standaardwaarden en parameternamen. Een uitzondering `interval` is de eigenschap, die `number`is geparameteriseerd als type . De parameternaam is `<entityName>_properties_typeProperties_recurrence_triggerSuffix`achtergezet met . Op dezelfde `freq` manier is de eigenschap een tekenreeks en wordt deze geparameteriseerd als een tekenreeks. De `freq` eigenschap is echter parameterized zonder standaardwaarde. De naam wordt ingekort en achtergeschreven. Bijvoorbeeld `<entityName>_freq`.

#### <a name="linkedservices"></a>Gekoppelde Services

* Gekoppelde diensten zijn uniek. Omdat gekoppelde services en gegevenssets een breed scala aan typen hebben, u typespecifieke aanpassingen bieden. In dit voorbeeld wordt voor `AzureDataLakeStore`alle gekoppelde services van het type een specifieke sjabloon toegepast. Voor alle andere `*`(via), zal een andere sjabloon worden toegepast.
* De `connectionString` eigenschap wordt geparameteriseerd als waarde. `securestring` Het heeft geen standaardwaarde. Het zal een verkorte parameternaam hebben `connectionString`die is achtergezet met .
* De `secretAccessKey` eigenschap is `AzureKeyVaultSecret` toevallig een (bijvoorbeeld in een Amazon S3 gekoppelde service). Het wordt automatisch geparameteriseerd als een Azure Key Vault-geheim en opgehaald uit de geconfigureerde sleutelkluis. U ook de sleutelkluis zelf parameteriseren.

#### <a name="datasets"></a>Gegevenssets

* Hoewel typespecifieke aanpassingen beschikbaar zijn voor gegevenssets, u \*configuratie bieden zonder expliciet een configuratie op niveau te hebben. In het voorgaande voorbeeld worden `typeProperties` alle gegevensseteigenschappen onder geparameteriseerd.

### <a name="default-parameterization-template"></a>Sjabloon standaardparameterisatie

Hieronder volgt de huidige standaardparameterisatiesjabloon. Als u slechts een paar parameters moet toevoegen, is het bewerken van deze sjabloon mogelijk een goed idee omdat u de bestaande parameterisatiestructuur niet verliest.

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

In het volgende voorbeeld ziet u hoe u één waarde toevoegt aan de standaardparameterisatiesjabloon. We willen alleen een bestaande interactieve cluster-id van Azure Databricks toevoegen voor een Databricks-gekoppelde service aan het parametersbestand. Houd er rekening mee dat dit bestand hetzelfde `existingClusterId` is als `Microsoft.DataFactory/factories/linkedServices`het vorige bestand, behalve voor de toevoeging van onder het eigenschappenveld van .

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

Als u CI/CD hebt ingesteld voor uw gegevensfabrieken, u de sjabloonlimieten azure resource manager overschrijden naarmate uw fabriek groter wordt. Eén limiet is bijvoorbeeld het maximum aantal resources in een resourcemanagersjabloon. Om grote fabrieken te huisvesten en tegelijkertijd de volledige Resource Manager-sjabloon voor een fabriek te genereren, genereert Data Factory nu gekoppelde Resource Manager-sjablonen. Met deze functie wordt het volledige laadvermogen in de fabriek opgesplitst in verschillende bestanden, zodat u niet wordt beperkt door de limieten.

Als u Git hebt geconfigureerd, worden de gekoppelde sjablonen gegenereerd en opgeslagen naast de volledige Resource Manager-sjablonen in de adf_publish-branch in een nieuwe map genaamd linkedTemplates:

![Map Met gekoppelde resourcebeheersjablonen](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De gekoppelde Resource Manager-sjablonen bestaan meestal uit een hoofdsjabloon en een set onderliggende sjablonen die aan het model zijn gekoppeld. De bovenliggende sjabloon wordt ArmTemplate_master.json genoemd en onderliggende sjablonen worden benoemd met het patroon ArmTemplate_0.json, ArmTemplate_1.json, enzovoort. 

Als u gekoppelde sjablonen wilt gebruiken in plaats van de volledige resourcemanagersjabloon, werkt u uw CI/CD-taak bij om ArmTemplate_master.json aan te wijzen in plaats van ArmTemplateForFactory.json (de volledige sjabloon Resourcebeheer). Resourcebeheer vereist ook dat u de gekoppelde sjablonen uploadt naar een opslagaccount, zodat Azure er tijdens de implementatie toegang toe heeft. Zie [Gekoppelde Resource Manager-sjablonen implementeren met VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)voor meer informatie.

Vergeet niet om de Scripts van Gegevensfabriek voor en na de implementatietaak toe te voegen aan uw CI/CD-pijplijn.

Als Git niet is geconfigureerd, hebt u toegang tot de gekoppelde sjablonen via **ARM-sjabloon exporteren** in de lijst **ARM-sjabloon.**

## <a name="hotfix-production-branch"></a>Productietak Hotfix

Als u een fabriek in productie neemt en zich realiseert dat er een bug is die meteen moet worden opgelost, maar u de huidige samenwerkingsbranch niet implementeren, moet u mogelijk een hotfix implementeren. Deze aanpak is bekend als quick-fix engineering of QFE.

1.    Ga in Azure DevOps naar de release die is geïmplementeerd voor productie. Zoek de laatste commit die is geïmplementeerd.

2.    Haal vanuit het commit-bericht de commit-ID van de samenwerkingsbranche.

3.    Maak een nieuwe hotfix-branch van die commit.

4.    Ga naar de UX van Azure Data Factory en schakel over naar de hotfix-branch.

5.    Met de AZURE Data Factory UX verwerk je de bug. Test je wijzigingen.

6.    Nadat de oplossing is geverifieerd, selecteert u **ARM-sjabloon exporteren** om de sjabloon hotfix Resource Manager te krijgen.

7.    Controleer handmatig deze inbouw in de adf_publish tak.

8.    Als u de releasepijplijn hebt geconfigureerd om automatisch te activeren op basis van adf_publish check-ins, wordt een nieuwe release automatisch gestart. Anders bedien je handmatig een release in de wachtrij.

9.    Implementeer de hotfix-release in de test- en productiefabrieken. Deze release bevat de vorige productie payload plus de fix die je hebt gemaakt in stap 5.

10.    Voeg de wijzigingen van de hotfix toe aan de ontwikkelbranch, zodat latere releases niet dezelfde bug bevatten.

## <a name="best-practices-for-cicd"></a>Aanbevolen procedures voor CI/CD

Als u Git-integratie met uw gegevensfabriek gebruikt en een CI/CD-pijplijn hebt die uw wijzigingen verplaatst van ontwikkeling naar test en vervolgens naar productie, raden we de volgende aanbevolen procedures aan:

-   **Git integratie**. U hoeft alleen uw ontwikkelingsgegevensfabriek te configureren met Git-integratie. Wijzigingen in de test en productie worden geïmplementeerd via CI/CD en hebben geen Git-integratie nodig.

-   **Gegevensfabriek CI/CD-script**. Voordat u de implementatiestap Resourcemanager in CI/CD uitvoert, moet u bepaalde taken voltooien, zoals het stoppen en opnieuw starten van triggers en het uitvoeren van opschoning. We raden u aan PowerShell-scripts voor en na de implementatie te gebruiken. Zie [Actieve triggers bijwerken](#update-active-triggers)voor meer informatie .

-   **Inburgeringstijden en delen**. Integratie-looptijden veranderen niet vaak en zijn vergelijkbaar in alle fasen van uw CI/CD. Data Factory verwacht dus dat u dezelfde naam en type integratieruntime hebt in alle fasen van CI/CD. Als u de runtimes voor integratie in alle fasen wilt delen, u overwegen een ternaire fabriek te gebruiken om de runtimes voor gedeelde integratie te bevatten. U deze gedeelde fabriek in al uw omgevingen gebruiken als een gekoppeld runtime-type voor integratie.

-   **Sleutelkluis.** Wanneer u gekoppelde services op basis van Azure Key Vault gebruikt, u deze verder benutten door afzonderlijke sleutelkluizen voor verschillende omgevingen te bewaren. U ook afzonderlijke machtigingsniveaus configureren voor elke sleutelkluis. U wilt bijvoorbeeld niet dat uw teamleden machtigingen hebben voor het produceren van geheimen. Als u deze aanpak volgt, raden we u aan om dezelfde geheime namen in alle fasen te bewaren. Als u dezelfde namen behoudt, hoeft u uw Resource Manager-sjablonen niet te wijzigen in CI/CD-omgevingen, omdat het enige dat verandert, de naam van de sleutelkluis is, een van de sjabloonparameters resourcebeheer.

## <a name="unsupported-features"></a>Niet-ondersteunde functies

- Door het ontwerp, Data Factory staat niet toe cherry-picking van commits of selectieve publicatie van middelen. Publiceert zal alle wijzigingen in de gegevensfabriek bevatten.

    - Gegevensfabrieksentiteiten zijn van elkaar afhankelijk. Triggers zijn bijvoorbeeld afhankelijk van pijplijnen en pijplijnen zijn afhankelijk van gegevenssets en andere pijplijnen. Selectieve publicatie van een subset van resources kan leiden tot onverwacht gedrag en fouten.
    - In zeldzame gevallen wanneer u selectieve publicatie nodig hebt, u overwegen een hotfix te gebruiken. Zie [Hotfix-productietak](#hotfix-production-branch)voor meer informatie .

-   Je niet publiceren vanuit privé-takken.

-   U momenteel geen projecten hosten op Bitbucket.

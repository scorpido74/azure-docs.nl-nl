---
title: Continue integratie en levering in Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: ff1d34852890a8d5005153ebdfa2fa0f9749d129
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030615"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Continue integratie en levering (CI/CD) in Azure Data Factory

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het testen van elke wijziging die in uw code base wordt uitgevoerd automatisch en zo snel mogelijk. Continue levering volgt de tests die optreden tijdens continue integratie en het pushen van wijzigingen in een staging-of productie systeem.

In Azure Data Factory wordt doorlopende integratie & levering het verplaatsen van Data Factory pijp lijnen van de ene omgeving (ontwikkeling, test, productie) naar een andere. Als u doorlopende integratie & levering wilt uitvoeren, kunt u Data Factory UX-integratie met Azure Resource Manager-sjablonen gebruiken. De Data Factory UX kan een resource manager-sjabloon genereren op basis van de vervolg keuzelijst **arm-sjabloon** . Wanneer u **arm-sjabloon exporteren**selecteert, genereert de Portal de Resource Manager-sjabloon voor de Data Factory en een configuratie bestand dat alle verbindings reeksen en andere para meters bevat. Vervolgens maakt u een configuratie bestand voor elke omgeving (ontwikkelen, testen, productie). Het sjabloon bestand van de hoofd Resource Manager blijft hetzelfde voor alle omgevingen.

Bekijk de volgende video voor een inleiding en demonstratie van negen minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Continue integratie levenscyclus

Hieronder vindt u een voor beeld van een overzicht van de levens cyclus voor continue integratie en levering in een Azure-gegevensfactory die is geconfigureerd met Azure opslag plaatsen git. Zie [broncode beheer in azure Data Factory](source-control.md)voor meer informatie over het configureren van een Git-opslag plaats.

1.  Een ontwikkelings data factory wordt gemaakt en geconfigureerd met Azure opslag plaatsen Git, waar alle ontwikkel aars toestemming hebben om Data Factory resources zoals pijp lijnen en gegevens sets te schrijven.

1.  Wanneer de ontwikkel aars wijzigingen aanbrengen in hun functie vertakking, kunnen ze fouten opsporen in hun pijplijn uitvoeringen met hun meest recente wijzigingen. Zie [iteratieve ontwikkeling en fout opsporing met Azure Data Factory](iterative-development-debugging.md)voor meer informatie over het opsporen van fouten in een pijplijn uitvoering.

1.  Zodra de ontwikkel aars tevreden zijn met hun wijzigingen, maken ze een pull-aanvraag van hun functie vertakking in de vertakking Master of collaboration om hun wijzigingen te verkrijgen die door peers worden gecontroleerd.

1.  Nadat de pull-aanvraag is goedgekeurd en wijzigingen zijn samengevoegd in de hoofd vertakking, kunnen ze publiceren naar de ontwikkelings fabriek.

1.  Wanneer het team gereed is voor het implementeren van de wijzigingen in de test-Factory en vervolgens naar de productie-Factory, exporteren ze de Resource Manager-sjabloon uit de hoofd vertakking.

1.  De geëxporteerde Resource Manager-sjabloon wordt geïmplementeerd met verschillende parameter bestanden naar de test-Factory en de productie-Factory.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Een resource manager-sjabloon maken voor elke omgeving

Selecteer in de vervolg keuzelijst **arm-sjabloon** de optie **arm-sjabloon exporteren** om de Resource Manager-sjabloon voor uw Data Factory te exporteren in de ontwikkel omgeving.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Selecteer in uw test-en productie gegevens fabrieken de optie **arm-sjabloon importeren**. Met deze actie gaat u naar de Azure Portal, waar u de geëxporteerde sjabloon kunt importeren. Selecteer **uw eigen sjabloon bouwen in de editor** om de Resource Manager-sjabloon editor te openen.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Klik op **bestand laden** en selecteer de gegenereerde Resource Manager-sjabloon.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Voer in het deel venster instellingen de configuratie waarden in, zoals de referenties van de gekoppelde service. Zodra u klaar bent, klikt u op **aanschaffen** om de Resource Manager-sjabloon te implementeren.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Verbindingsreeksen

Informatie over het configureren van verbindings reeksen vindt u in het artikel van elke connector. Zie bijvoorbeeld voor Azure SQL Database [gegevens kopiëren naar of van Azure SQL database met behulp van Azure Data Factory](connector-azure-sql-database.md). Als u een connection string wilt controleren, kunt u de code weergave voor de resource openen in de Data Factory UX. In de code weergave wordt het wacht woord of account sleutel gedeelte van de connection string verwijderd. Als u de code weergave wilt openen, selecteert u het pictogram dat is gemarkeerd in de volgende scherm afbeelding.

![Open de code weergave om connection string te bekijken](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Continue integratie automatiseren met Azure pipelines-releases

Hieronder vindt u een hand leiding voor het instellen van een Azure pipelines-release, waarmee de implementatie van een data factory naar meerdere omgevingen wordt geautomatiseerd.

![Diagram van doorlopende integratie met Azure-pijp lijnen](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Vereisten

-   Een Azure-abonnement dat is gekoppeld aan Team Foundation Server of Azure opslag plaatsen met behulp van het [eind punt Azure Resource Manager service](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Een Data Factory dat is geconfigureerd met Azure opslag plaatsen Git-integratie.

-   Een [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) met de geheimen voor elke omgeving.

### <a name="set-up-an-azure-pipelines-release"></a>Een Azure pipelines-release instellen

1.  Open in de [Azure DevOps-gebruikers ervaring](https://dev.azure.com/)het project dat is geconfigureerd met uw Data Factory.

1.  Klik aan de linkerkant van de pagina op **pijp lijnen** en selecteer vervolgens **releases**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecteer **nieuwe pijp lijn** of als u bestaande pijp lijnen, **Nieuw**en vervolgens **nieuwe release pijplijn**hebt.

1.  Selecteer de **lege taak** sjabloon.

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Voer in het veld **naam fase** de naam van uw omgeving in.

1.  Selecteer **een artefact toevoegen**en selecteer dezelfde opslag plaats die is geconfigureerd met uw Data Factory. Kies `adf_publish` als standaard vertakking met de nieuwste standaard versie.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Een Azure Resource Manager implementatie taak toevoegen:

    a.  Klik in de weer gave fase op de koppeling **taak fase taken weer geven** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Een nieuwe taak maken. Zoek naar de implementatie van een **Azure-resource groep**en klik op **toevoegen**.

    c.  Kies in de implementatie taak het abonnement, de resource groep en de locatie voor het doel Data Factory en geef indien nodig referenties op.

    d.  Selecteer in de vervolg keuzelijst actie de optie **resource groep maken of bijwerken**.

    e.  Selecteren **...** in het veld **sjabloon** . Blader naar het Azure Resource Manager sjabloon maken via de stap **arm-sjabloon importeren** in [een resource manager-sjabloon maken voor elke omgeving](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Zoek naar dit bestand in de map `<FactoryName>` van de vertakking `adf_publish`.

    f.  Selecteren **...** in het **veld sjabloon parameters.** om het parameter bestand te kiezen. Kies het juiste bestand, afhankelijk van of u een kopie hebt gemaakt of gebruikmaakt van het standaard bestand *ARMTemplateParametersForFactory. json*.

    g.  Selecteren **...** Naast het veld **para meters van sjabloon negeren** voert u de gegevens voor de doel Data Factory in. Voer de geheime naam tussen dubbele aanhalings tekens in voor referenties die afkomstig zijn van sleutel kluis. Als de naam van het geheim bijvoorbeeld `cred1` is, voert u `"$(cred1)"`for de waarde in.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Selecteer de **incrementele** implementatie modus.

    > [!WARNING]
    > Als u **volledige** implementatie modus selecteert, kunnen bestaande resources worden verwijderd, inclusief alle resources in de doel resource groep die niet zijn gedefinieerd in de Resource Manager-sjabloon.

1.  Sla de release pijplijn op.

1. Als u een release wilt activeren, klikt u op **release maken**

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Geheimen van Azure Key Vault ophalen

Als u geheimen hebt om in een Azure Resource Manager-sjabloon door te geven, raden we u aan Azure Key Vault te gebruiken met de versie van Azure pipeline.

Er zijn twee manieren om geheimen af te handelen:

1.  Voeg de geheimen toe aan het parameter bestand. Zie [Azure Key Vault gebruiken om de waarde van een beveiligde para meter door te geven tijdens de implementatie](../azure-resource-manager/resource-manager-keyvault-parameter.md)voor meer informatie.

    -   Maak een kopie van het parameter bestand dat is geüpload naar de Publish-vertakking en stel de waarden in van de para meters die u wilt ophalen uit de sleutel kluis met de volgende indeling:

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

    -   Wanneer u deze methode gebruikt, wordt het geheim automatisch opgehaald uit de sleutel kluis.

    -   Het parameter bestand moet ook in de publicatie vertakking staan.

1.  Voeg een [Azure Key Vault-taak](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) toe vóór de Azure Resource Manager-implementatie die wordt beschreven in de vorige sectie:

    -   Selecteer het tabblad **taken** , maak een nieuwe taak, zoek naar **Azure Key Vault** en voeg deze toe.

    -   Kies in de Key Vault taak het abonnement waarin u de sleutel kluis hebt gemaakt, Geef indien nodig referenties op en kies vervolgens de sleutel kluis.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Machtigingen verlenen aan de Azure pipelines-agent

De Azure Key Vault taak kan mislukken met een fout bericht over geweigerde toegang als de juiste machtigingen niet aanwezig zijn. Down load de logboeken voor de release en zoek het bestand `.ps1` met de opdracht om machtigingen te verlenen aan de Azure pipelines-agent. U kunt de opdracht rechtstreeks uitvoeren, of u kunt de principal-ID uit het bestand kopiëren en het toegangs beleid hand matig toevoegen in de Azure Portal. **Get** en **List** zijn de mini maal vereiste machtigingen.

### <a name="update-active-triggers"></a>Actieve triggers bijwerken

De implementatie kan mislukken als u probeert actieve triggers bij te werken. Als u actieve triggers wilt bijwerken, moet u deze hand matig stoppen en na de implementatie starten. U kunt dit doen via een Azure Power shell-taak.

1.  Voeg op het tabblad taken van de release een **Azure Power shell** -taak toe.

1.  Kies **Azure Resource Manager** als het verbindings type en selecteer uw abonnement.

1.  Kies in- **line script** als het script type en geef vervolgens uw code op. In het volgende voor beeld worden de triggers gestopt:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

U kunt soort gelijke stappen volgen (met de functie `Start-AzDataFactoryV2Trigger`) om de triggers na de implementatie opnieuw te starten.

> [!IMPORTANT]
> In doorlopende integratie-en implementatie scenario's moet het Integration Runtime type in verschillende omgevingen hetzelfde zijn. Als u bijvoorbeeld een *zelf-hostend* Integration runtime (IR) in de ontwikkel omgeving hebt, moet dezelfde IR van het type zelf worden *gehost* in andere omgevingen, zoals test en productie. En als u integratie-runtime in meerdere fasen deelt, moet u de Integration Runtimes als *gekoppelde zelf-hostende* in alle omgevingen configureren, zoals ontwikkeling, testen en productie.

#### <a name="sample-prepostdeployment-script"></a>Voor beeld van pre/na implementatie-script

Hieronder vindt u een voorbeeld script voor het stoppen van triggers vóór de implementatie en het opnieuw starten van triggers. Het script bevat ook code voor het verwijderen van resources die zijn verwijderd. Zie [install Azure PowerShell in Windows with PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps)(Engelstalig) om de nieuwste versie van Azure PowerShell te installeren.

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

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Aangepaste para meters gebruiken met de Resource Manager-sjabloon

Als u zich in de GIT-modus bevindt, kunt u de standaard eigenschappen in uw Resource Manager-sjabloon vervangen om eigenschappen in te stellen die zijn vastgelegd in de sjabloon en eigenschappen die in een code zijn vastgelegd. In deze scenario's wilt u mogelijk de standaard sjabloon parameterisering overschrijven:

* U gebruikt automatische CI/CD en u wilt enkele eigenschappen wijzigen tijdens de implementatie van Resource Manager, maar de eigenschappen zijn niet standaard ingesteld op para meters.
* Uw fabriek is zo groot dat de standaard Resource Manager-sjabloon ongeldig is omdat deze meer dan de Maxi maal toegestane para meters (256) heeft.

Onder deze omstandigheden kunt u de standaard parameterisering-sjabloon overschrijven door een bestand te maken met de naam *arm-sjabloon-para meters, definitie. json* in de hoofdmap van de opslag plaats. De bestands naam moet exact overeenkomen. Data Factory probeert dit bestand te lezen van de vertakking waar u zich momenteel bevindt in de Azure Data Factory Portal, niet alleen vanuit de vertakking voor samen werking. U kunt het bestand maken of bewerken vanuit een persoonlijke vertakking, waar u uw wijzigingen kunt testen met behulp van de sjabloon voor het **exporteren van arm** in de gebruikers interface. Vervolgens kunt u het bestand samen voegen in de vertakking voor samen werking. Als er geen bestand wordt gevonden, wordt de standaard sjabloon gebruikt.


### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxis van een bestand met aangepaste para meters

Hier volgen enkele richt lijnen die u kunt gebruiken wanneer u het bestand met aangepaste para meters ontwerpt. Het bestand bestaat uit een sectie voor elk entiteits type: trigger, pijp lijn, gekoppelde service, gegevensset, Integration runtime, enzovoort.
* Voer het pad naar de eigenschap in onder het relevante entiteits type.
* Wanneer u een eigenschaps naam instelt op ' \* ' ', geeft u aan dat u alle eigenschappen daaronder wilt para meters (alleen naar het eerste niveau, niet recursief). U kunt hier ook eventuele uitzonde ringen opgeven.
* Wanneer u de waarde van een eigenschap instelt als een teken reeks, geeft u aan dat u de eigenschap wilt para meters. Gebruik de notatie @ no__t-0.
   *  `<action>` @ no__t-1can een van de volgende tekens zijn:
      * `=` @ no__t-1means behoud de huidige waarde als de standaard waarde voor de para meter.
      * `-` @ no__t-1means niet de standaard waarde voor de para meter.
      * `|` @ no__t-1is een speciaal geval voor geheimen van Azure Key Vault voor verbindings reeksen of sleutels.
   * `<name>` @ no__t-1is de naam van de para meter. Als deze leeg is, wordt de naam van de eigenschap gebruikt. Als de waarde begint met een `-`-teken, wordt de naam Inge kort. @No__t-0 wordt bijvoorbeeld Inge kort tot `AzureStorage1_connectionString`.
   * `<stype>` @ no__t-1is het type para meter. Als @ no__t-0 @ no__t-1is leeg is, is het standaard type `string`. Ondersteunde waarden: `string`, `bool`, `number`, `object` en `securestring`.
* Wanneer u een matrix opgeeft in het definitie bestand, geeft u aan dat de overeenkomende eigenschap in de sjabloon een matrix is. Data Factory doorloopt alle objecten in de matrix met behulp van de definitie die is opgegeven in het Integration Runtime-object van de matrix. Het tweede object, een teken reeks, wordt de naam van de eigenschap, die wordt gebruikt als de naam voor de para meter voor elke iteratie.
* Het is niet mogelijk een definitie te hebben die specifiek is voor een resource-exemplaar. Elke wille keurige definitie is van toepassing op alle resources van dat type.
* Standaard zijn alle beveiligde teken reeksen, zoals Key Vault geheimen en beveiligde teken reeksen, zoals verbindings reeksen, sleutels en tokens, para meters.
 
### <a name="sample-parameterization-template"></a>Voor beeld van parameterisering-sjabloon

Hieronder ziet u een voor beeld van hoe een parameterisering-sjabloon eruit kan zien:

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
Hieronder volgt een uitleg van de manier waarop de bovenstaande sjabloon is samengesteld, onderverdeeld op resource type.

#### <a name="pipelines"></a>Pijplijnen
    
* Elke eigenschap in het pad activities/typeProperties/waitTimeInSeconds is para meters. Alle activiteiten in een pijp lijn die een eigenschap op code niveau met de naam `waitTimeInSeconds` hebben (bijvoorbeeld de activiteit `Wait`), worden als een getal met een standaard naam ingesteld. Maar heeft geen standaard waarde in de Resource Manager-sjabloon. Het is een verplichte invoer tijdens de implementatie van Resource Manager.
* Op dezelfde manier is een eigenschap met de naam `headers` (bijvoorbeeld in een `Web`-activiteit) para meters van het type `object` (JObject). Het heeft een standaard waarde. Dit is dezelfde waarde als in de bron-Factory.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle eigenschappen onder het pad `typeProperties` zijn para meters met hun respectievelijke standaard waarden. Er zijn bijvoorbeeld twee eigenschappen onder **IntegrationRuntimes** type-eigenschappen: `computeProperties` en `ssisProperties`. Beide eigenschaps typen worden gemaakt met hun respectievelijke standaard waarden en typen (object).

#### <a name="triggers"></a>Triggers

* Onder `typeProperties` zijn twee eigenschappen para meters. De eerste is `maxConcurrency`, die is opgegeven om een standaard waarde te hebben en is van het type @ no__t-1. Deze heeft de standaard parameter naam `<entityName>_properties_typeProperties_maxConcurrency`.
* De eigenschap `recurrence` is ook para meters. Hieronder worden alle eigenschappen op dat niveau opgegeven om para meters te worden ingesteld als teken reeksen, met standaard waarden en parameter namen. Een uitzonde ring is de eigenschap `interval`, die als para meters is aangestuurd als het nummer type en met de parameter naam met `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Op dezelfde manier is de eigenschap `freq` een teken reeks en wordt de para meter in de vorm van een teken reeks. De eigenschap `freq` is echter para meters zonder standaard waarde. De naam is inge kort en achtervoegsel. Bijvoorbeeld `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Gekoppelde services zijn uniek. Omdat gekoppelde services en gegevens sets een breed scala van typen hebben, kunt u type-specifieke aanpassing opgeven. In dit voor beeld worden alle gekoppelde services van het type `AzureDataLakeStore`, een specifieke sjabloon toegepast en voor alle andere sjablonen (via \*) wordt een andere sjabloon toegepast.
* De eigenschap `connectionString` wordt ingesteld als een `securestring`-waarde, heeft geen standaard waarde en heeft een kortere parameter naam die wordt gebruikt in plaats van `connectionString`.
* De eigenschap `secretAccessKey` heeft een `AzureKeyVaultSecret` (bijvoorbeeld in een gekoppelde service van @no__t 2). De para meter wordt automatisch ingesteld als een Azure Key Vault geheim en opgehaald uit de geconfigureerde sleutel kluis. U kunt ook de sleutel kluis zelf para meters.

#### <a name="datasets"></a>Gegevenssets

* Hoewel het type-specifieke aanpassing beschikbaar is voor gegevens sets, kan de configuratie worden opgegeven zonder expliciet een @no__t configuratie op -0-niveau. In het bovenstaande voor beeld zijn alle eigenschappen van de gegevensset onder `typeProperties` para meters.

### <a name="default-parameterization-template"></a>Standaard parameterisering-sjabloon

Hieronder ziet u de huidige standaard sjabloon parameterisering. Als u slechts één of enkele para meters hoeft toe te voegen, is het mogelijk dat u de bestaande parameterisering-structuur niet kwijtraakt.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

Hieronder ziet u een voor beeld van hoe u een enkele waarde kunt toevoegen aan de standaard sjabloon parameterisering. We willen alleen een bestaande Databricks interactief cluster-ID voor een gekoppelde Databricks-service toevoegen aan het parameter bestand. Opmerking: het onderstaande bestand is hetzelfde als het bovenstaande bestand, met uitzonde ring van `existingClusterId` onder het veld eigenschappen van `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

Als u doorlopende integratie en implementatie (CI/CD) voor uw gegevens fabrieken hebt ingesteld, kunt u de limieten voor de Azure Resource Manager-sjablonen uitvoeren naarmate uw fabriek groter groeit. Een voor beeld van een limiet is het maximum aantal resources in een resource manager-sjabloon. Data Factory nu gekoppelde Resource Manager-sjablonen genereert om grote fabrieken te kunnen samen met het genereren van de volledige Resource Manager-sjabloon voor een fabriek. Met deze functie wordt de gehele nettolading van de fabriek onderverdeeld in verschillende bestanden, zodat u de limieten niet kunt uitvoeren.

Als u Git hebt geconfigureerd, worden de gekoppelde sjablonen gegenereerd en opgeslagen in de volledige Resource Manager-sjablonen in de vertakking `adf_publish` onder een nieuwe map met de naam `linkedTemplates`.

![Map gekoppelde Resource Manager-sjablonen](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De gekoppelde Resource Manager-sjablonen bevatten meestal een hoofd sjabloon en een set onderliggende sjablonen die aan het hoofd zijn gekoppeld. De bovenliggende sjabloon heet `ArmTemplate_master.json`, en de onderliggende sjablonen worden benoemd met het patroon `ArmTemplate_0.json`, `ArmTemplate_1.json`, enzovoort. Als u gekoppelde sjablonen wilt gebruiken in plaats van de volledige Resource Manager-sjabloon, moet u uw CI/CD-taak bijwerken zodat deze verwijst naar `ArmTemplate_master.json` in plaats van `ArmTemplateForFactory.json` (de volledige Resource Manager-sjabloon). Resource Manager vereist ook dat u de gekoppelde sjablonen uploadt naar een opslag account, zodat deze door Azure kunnen worden gebruikt tijdens de implementatie. Zie voor meer informatie [gekoppelde arm-sjablonen implementeren met VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Vergeet niet om de Data Factory scripts in uw CI/CD-pijp lijn vóór en na de implementatie taak toe te voegen.

Als u geen git hebt geconfigureerd, kunt u de gekoppelde sjablonen openen via de beweging voor het exporteren van de **arm-sjabloon** .

## <a name="hot-fix-production-branch"></a>Productie vertakking warm herstellen

Als u een Factory implementeert voor productie en beseft dat er een fout is die direct moet worden opgelost, maar u niet de huidige collaboration Branch kunt implementeren, moet u mogelijk een hot-fix implementeren. Deze aanpak is net zo bekend als snelle herstel techniek of QFE. 

1.  Ga in azure DevOps naar de release die is geïmplementeerd voor productie en zoek de laatste door voering die is geïmplementeerd.

2.  Haal in het commit-bericht de commit-ID van de samenwerkings vertakking op.

3.  Maak een nieuwe Hot-Fix vertakking van die doorvoer.

4.  Ga naar de Azure Data Factory UX en schakel over naar deze vertakking.

5.  Los de fout op met behulp van de Azure Data Factory UX. Test uw wijzigingen.

6.  Zodra de oplossing is geverifieerd, klikt u op **arm-sjabloon exporteren** om de sjabloon Hot-Fix Resource Manager te downloaden.

7.  Deze build hand matig in de adf_publish-vertakking controleren.

8.  Als u uw release pijplijn hebt geconfigureerd om automatisch te activeren op basis van adf_publish check-ins, wordt een nieuwe release automatisch gestart. U kunt ook hand matig een release in de wachtrij plaatsen.

9.  Implementeer de hot-fix release voor de test-en productie fabrieken. Deze release bevat de vorige nettolading van de productie plus de oplossing die u in stap 5 hebt gemaakt.

10. Voeg de wijzigingen van de Hot-Fix toe aan de ontwikkelings vertakking zodat latere releases niet in dezelfde bug worden uitgevoerd.

## <a name="best-practices-for-cicd"></a>Aanbevolen procedures voor CI/CD

Als u gebruik wilt maken van Git-integratie met uw data factory en u een CI/CD-pijp lijn hebt die uw wijzigingen van de ontwikkeling naar de test verplaatst en vervolgens naar productie, raden we u aan de volgende aanbevolen procedures te volgen:

-   **Git-integratie**. U hoeft uw ontwikkel data factory alleen te configureren met git-integratie. Wijzigingen in testen en productie worden geïmplementeerd via CI/CD en beschikken niet over git-integratie.

-   **Data Factory CI/cd-script**. Voor de implementatie stap van resource manager in CI/CD zijn bepaalde taken vereist, zoals het stoppen/starten van triggers en opschonen. We raden u aan om Power shell-scripts te gebruiken vóór en na de implementatie. Zie [actieve triggers bijwerken](#update-active-triggers)voor meer informatie. 

-   **Integration Runtimes en delen**. Integration Runtimes veranderen niet vaak en zijn vergelijkbaar in alle fasen van uw CI/CD. Als gevolg hiervan Data Factory verwacht dat u dezelfde naam en hetzelfde type Integration Runtimes in alle fasen van CI/CD hebt. Als u integratie-Runtimes in alle fasen wilt delen, kunt u overwegen een ternaire fabriek alleen te gebruiken voor de gedeelde integratie runtime. U kunt deze gedeelde Factory in al uw omgevingen gebruiken als het type gekoppelde integratie runtime.

-   **Key Vault**. Wanneer u Azure Key Vault op basis van gekoppelde services gebruikt, kunt u de voor delen hiervan verder benutten door afzonderlijke sleutel kluizen voor verschillende omgevingen te bewaren. U kunt ook afzonderlijke machtigings niveaus configureren voor elk van deze. Het is bijvoorbeeld mogelijk dat uw team leden geen machtigingen voor productie geheimen mogen hebben. Als u deze aanpak volgt, wordt u aangeraden dezelfde geheime namen in alle fasen te houden. Als u dezelfde namen wilt gebruiken, hoeft u uw Resource Manager-sjablonen niet te wijzigen in CI/CD-omgevingen, omdat de naam van de sleutel kluis een van de para meters voor de Resource Manager-sjabloon is.

## <a name="unsupported-features"></a>Niet-ondersteunde functies

- Op basis van het ontwerp kan ADF _geen_ Cherry-orderverzamelen of selectief publiceren van resources toestaan. De publicatie bevat **alle** wijzigingen die zijn aangebracht in de Data Factory

    - Data Factory-entiteiten zijn afhankelijk van elkaar, bijvoorbeeld triggers zijn afhankelijk van pijp lijnen, pijp lijnen zijn afhankelijk van gegevens sets en andere pijp lijnen, enzovoort. Selectief publiceren van een subset van resources _kan_ leiden tot onverwacht gedrag en fouten
    - In zeldzame gevallen waarin selectief publiceren is vereist, kunt u een warme correctie overwegen. Zie voor meer informatie [Hot-Fix Production Branch](#hot-fix-production-branch)

-   U kunt niet publiceren vanuit privé-filialen

-   Vanaf nu kunt u geen projecten hosten op bitbucket

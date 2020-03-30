---
title: DevOps voor een pijplijn voor gegevensopname
titleSuffix: Azure Machine Learning
description: Meer informatie over het toepassen van DevOps-praktijken op een implementatie van gegevensinnamepijplijnen die worden gebruikt om gegevens voor te bereiden op een modeltraining.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247177"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps voor een pijplijn voor gegevensopname

In de meeste scenario's is een oplossing voor het innemen van gegevens een samenstelling van scripts, service-aanroepen en een pijplijn die alle activiteiten orkestreert. In dit artikel leert u hoe u DevOps-praktijken toepast op de ontwikkelingslevenscyclus van een veelgebruikte pijplijn voor gegevensopname. De pijplijn bereidt de gegevens voor op de machine learning-modeltraining.

## <a name="the-solution"></a>De oplossing

Houd rekening met de volgende workflow voor het innemen van gegevens:

![data-ingestion-pipeline](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

In deze benadering worden de trainingsgegevens opgeslagen in een Azure blob-opslag. Een Azure Data Factory-pijplijn haalt de gegevens op uit een invoerblobcontainer, transformeert deze en slaat de gegevens op in de uitvoerblobcontainer. Deze container fungeert als [gegevensopslag](concept-data.md) voor de Azure Machine Learning-service. Na de gegevens voorbereid, de Data Factory pijplijn beroept op een opleiding Machine Learning pijplijn om een model te trainen. In dit specifieke voorbeeld wordt de gegevenstransformatie uitgevoerd door een Python-notitieblok dat wordt uitgevoerd op een Azure Databricks-cluster. 

## <a name="what-we-are-building"></a>Wat we aan het bouwen zijn

Zoals met elke software-oplossing, is er een team (bijvoorbeeld Data Engineers) bezig. 

![cicd-data-opname](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Ze werken samen en delen dezelfde Azure-bronnen zoals Azure Data Factory, Azure Databricks, Azure Storage-account en dergelijke. Het verzamelen van deze middelen is een ontwikkelingsomgeving. De data engineers dragen bij aan dezelfde broncodebasis. Het continuous integration-proces assembleert de code, controleert deze met de codekwaliteitstests, test en produceert artefacten zoals geteste code en Azure Resource Manager-sjablonen. Het continuous delivery-proces implementeert de artefacten in de downstream-omgevingen. In dit artikel wordt uitgelegd hoe u de CI- en CD-processen automatiseren met [Azure Pipelines.](https://azure.microsoft.com/services/devops/pipelines/)

## <a name="source-control-management"></a>Bronbeheer

De teamleden werken op iets andere manieren samen aan de broncode van het Python-notitieblok en de broncode van Azure Data Factory. In beide gevallen wordt de code echter opgeslagen in een bronbeheeropslagplaats (bijvoorbeeld Azure DevOps, GitHub, GitLab) en is de samenwerking normaal gesproken gebaseerd op een vertakkingsmodel (bijvoorbeeld [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Broncode van Python-notitieblok

De gegevenstechnici werken met de broncode van het Python-notitieblok, hetzij lokaal in een IDE -code (bijvoorbeeld [Visual Studio Code)](https://code.visualstudio.com)of rechtstreeks in de werkruimte Databricks. De laatste geeft de mogelijkheid om de code op de ontwikkelomgeving te debuggen. In ieder geval wordt de code samengevoegd naar de repository volgens een vertakkingsbeleid. Het is sterk aanbevolen om de `.py` code op `.ipynb` te slaan in bestanden in plaats van in Jupyter notebook formaat. Het verbetert de leesbaarheid van de code en maakt automatische codekwaliteitscontroles in het CI-proces mogelijk.

### <a name="azure-data-factory-source-code"></a>Broncode van Azure Data Factory

De broncode van Azure Data Factory-pijplijnen is een verzameling json-bestanden die door een werkruimte zijn gegenereerd. Normaal gesproken werken de gegevenstechnici rechtstreeks met een visual designer in de Azure Data Factory-werkruimte in plaats van met de broncodebestanden. Configureer de werkruimte met een bronbeheeropslagplaats zoals deze wordt beschreven in de [documentatie van Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration) Met deze configuratie kunnen de gegevenstechnici samenwerken aan de broncode na een voorkeursvertakkingsworkflow.    

## <a name="continuous-integration-ci"></a>Continue integratie (CI)

Het uiteindelijke doel van het Continuous Integration-proces is om het gezamenlijke teamwerk uit de broncode te verzamelen en voor te bereiden op de implementatie naar de downstream-omgevingen. Net als bij het broncodebeheer is dit proces anders voor de Python-notitieblokken en Azure Data Factory-pijplijnen. 

### <a name="python-notebook-ci"></a>Python-notitieblok CI

Het CI-proces voor de Python-notitieblokken krijgt de code van de samenwerkingsbranch (bijvoorbeeld ***master*** of ***ontwikkel)*** en voert de volgende activiteiten uit:
* Code linting
* Moduletests uitvoeren
* De code opslaan als artefact

In het volgende codefragment wordt de implementatie van deze stappen in een Azure DevOps ***yaml-pijplijn*** getoond:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

De pijplijn maakt gebruik ***van flake8*** om de Python-code linting uit te doen. De eenheidstests die in de broncode zijn gedefinieerd, worden uitgevoerd en publiceert de linting- en testresultaten, zodat ze beschikbaar zijn in het uitvoeringsscherm van azure pipeline:

![linting-unit-tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Als de linting- en eenheidstests zijn geslaagd, kopieert de pijplijn de broncode naar de artefact-opslagplaats die door de volgende implementatiestappen moet worden gebruikt.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

CI-proces voor een Azure Data Factory-pijplijn is een knelpunt in het hele CI/CD-verhaal voor een pijplijn voor gegevensopname. Er is geen ***continue*** integratie. Een implementeerbaar artefact voor Azure Data Factory is een verzameling Azure Resource Manager-sjablonen. De enige manier om deze sjablonen te produceren, is door op de ***publicatieknop*** in de azure data factory-werkruimte te klikken. Er is hier geen automatisering.
De data engineers voegen de broncode van hun functietakken samen in de samenwerkingsbranch, bijvoorbeeld ***master*** of ***ontwikkel***. Vervolgens klikt iemand met de verleende machtigingen op de ***knop Publiceren*** om Azure Resource Manager-sjablonen te genereren vanuit de broncode in de samenwerkingsbranch. Wanneer op de knop wordt geklikt, valideert de werkruimte de pijplijnen (zie het als linting en het testen van eenheden), genereert Azure Resource Manager-sjablonen (zie het als gebouw) en slaat de gegenereerde sjablonen op in een technische branch ***adf_publish*** in dezelfde coderepository (zie het als artefact-publicatie). Deze vertakking wordt automatisch gemaakt door de azure data factory-werkruimte. Dit proces wordt beschreven in details in de [Azure Data Factory-documentatie.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)

Het is belangrijk om ervoor te zorgen dat de gegenereerde Azure Resource Manager-sjablonen no-agnostisch zijn. Dit betekent dat alle waarden die kunnen verschillen van tussen omgevingen worden geparametriseerd. De Azure Data Factory is slim genoeg om de meeste waarden als parameters bloot te leggen. In de volgende sjabloon worden bijvoorbeeld de verbindingseigenschappen van een Azure Machine Learning-werkruimte weergegeven als parameters:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Het is echter mogelijk dat u uw aangepaste eigenschappen wilt blootleggen die niet standaard door de azure data factory-werkruimte worden verwerkt. In het scenario van dit artikel roept een Azure Data Factory-pijplijn een Python-notitieblok op dat de gegevens verwerkt. Het notitieblok accepteert een parameter met de naam van een invoergegevensbestand.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Deze naam is anders voor ***Dev,*** ***QA,*** ***UAT***en ***PROD*** omgevingen. In een complexe pijplijn met meerdere activiteiten kunnen er verschillende aangepaste eigenschappen zijn. Het is een goede gewoonte om al die waarden op één plaats te verzamelen en ze te definiëren als ***pijplijnvariabelen:***

![adf-variabelen](media/how-to-cicd-data-ingestion/adf-variables.png)

De pijplijnactiviteiten kunnen betrekking hebben op de pijplijnvariabelen terwijl ze deze daadwerkelijk worden gebruikt:

![adf-notebook-parameters](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

De werkruimte Azure Data Factory ***stelt standaard*** geen pijplijnvariabelen bloot als Azure Resource Manager-sjablonenparameters. De werkruimte gebruikt de [standaardparameterisatiesjabloon](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) die dictert welke pijplijneigenschappen moeten worden weergegeven als sjabloonparameters voor Azure Resource Manager. Als u pijplijnvariabelen aan de lijst wilt toevoegen, werkt u de sectie 'Microsoft.DataFactory/factories/pipelines' van de [standaardparameterisatiesjabloon](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) bij met het volgende fragment en plaatst u het resultaatjsonbestand in de hoofdmap van de bronmap:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Als u dit doet, dwingt u de werkruimte Azure Data Factory om de variabelen toe te voegen aan de parameterslijst wanneer op de ***publicatieknop*** wordt geklikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

De waarden in het json-bestand zijn standaardwaarden die zijn geconfigureerd in de pijplijndefinitie. Er wordt verwacht dat ze worden overschreven met de doelomgevingswaarden wanneer de azure resource manager-sjabloon wordt geïmplementeerd.

## <a name="continuous-delivery-cd"></a>Continue levering (CD)

Het proces continuous delivery brengt de artefacten en implementeert ze naar de eerste doelomgeving. Het zorgt ervoor dat de oplossing werkt door het uitvoeren van tests. Als het lukt, gaat het door naar de volgende omgeving. De CD Azure Pipeline bestaat uit meerdere fasen die de omgevingen vertegenwoordigen. Elke fase bevat [implementaties](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) en [taken](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) die de volgende stappen uitvoeren:
* Een Python-notitieblok implementeren in Azure Databricks-werkruimte
* Een Azure Data Factory-pijplijn implementeren 
* De pijplijn uitvoeren
* Controleer het resultaat van de gegevensopname

De pijplijnfasen kunnen worden geconfigureerd met [goedkeuringen](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) en [poorten](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) die extra controle bieden over hoe het implementatieproces zich ontwikkelt door de keten van omgevingen.

### <a name="deploy-a-python-notebook"></a>Een Python-notitieblok implementeren

In het volgende codefragment wordt een Azure [Pipeline-implementatie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) gedefinieerd die een Python-notitieblok kopieert naar een Cluster Gegevensstenen:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

De artefacten die door de CI worden geproduceerd, worden automatisch gekopieerd naar de implementatieagent en zijn beschikbaar in de map ***$(Pipeline.Workspace).*** In dit geval verwijst de implementatietaak naar het ***artefact di-notebooks*** dat het Python-notitieblok bevat. Deze [implementatie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) maakt gebruik van de [extensie Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) om de notitieblokbestanden naar de werkruimte Databricks te kopiëren.
De ***Deploy_to_QA*** fase bevat een verwijzing naar ***devops-ds-qa-vg*** variabele groep gedefinieerd in het Azure DevOps project. De stappen in deze fase verwijzen naar de variabelen van deze variabele groep (bijvoorbeeld $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). Het idee is dat de volgende fase (bijvoorbeeld ***Deploy_to_UAT)*** zal werken met dezelfde variabele namen gedefinieerd in zijn eigen UAT-scoped variabele groep.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Een Azure Data Factory-pijplijn implementeren

Een implementeerbaar artefact voor Azure Data Factory is een Azure Resource Manager-sjabloon. Daarom wordt het geïmplementeerd met de ***azure resourcegroepimplementatietaak*** zoals deze wordt gedemonstreerd in het volgende fragment:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
De waarde van de parameter gegevensbestandsnaam is afkomstig van de variabele $(DATA_FILE_NAME) gedefinieerd in een variabele groep in een QA-fase. Op dezelfde manier kunnen alle parameters die zijn gedefinieerd in ***ARMTemplateForFactory.json*** worden overschreven. Als dit niet het is, worden de standaardwaarden gebruikt.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>De pijplijn uitvoeren en het resultaat van de gegevensopname controleren

De volgende stap is ervoor te zorgen dat de geïmplementeerde oplossing werkt. Met de volgende taakdefinitie wordt een Azure Data Factory-pijplijn uitgevoerd met een [PowerShell-script](https://github.com/microsoft/DataOps/tree/master/adf/utils) en wordt een Python-notitieblok uitgevoerd op een Azure Databricks-cluster. Het notitieblok controleert of de gegevens correct zijn ingenomen en valideert het gegevensbestand met een naam van $bin_FILE_NAME.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

De laatste taak in de taak controleert het resultaat van de uitvoering van het notitieblok. Als er een fout wordt geretourneerd, wordt de status van de uitvoering van de pijplijn ingesteld als mislukt.

## <a name="putting-pieces-together"></a>Stukken in elkaar zetten

Het resultaat van dit artikel is een CI/CD Azure Pipeline die bestaat uit de volgende fasen:
* CI
* Implementeren op QA
    * Implementeren in Databricks + Implementeren in ADF
    * Integratietest

Het bevat een aantal ***Deploy-fasen die*** gelijk zijn aan het aantal doelomgevingen dat u hebt. Elke ***implementatiefase*** bevat twee [implementaties](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) die parallel worden uitgevoerd en een [taak](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) die wordt uitgevoerd na implementaties om de oplossing op de omgeving te testen.

Een voorbeeldimplementatie van de pijplijn wordt geassembleerd in het volgende ***yaml-fragment:***

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Volgende stappen

* [Broncodebeheer in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Continue integratie en levering in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps voor Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)

---
title: DevOps voor een pijp lijn voor gegevens opname
titleSuffix: Azure Machine Learning
description: Meer informatie over het Toep assen van DevOps-procedures voor de implementatie van een pijp lijn voor gegevens opname die wordt gebruikt om gegevens voor te bereiden voor een model training.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247177"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps voor een pijp lijn voor gegevens opname

In de meeste gevallen is een oplossing voor gegevens opname een samen stelling van scripts, aanroepen van de service en een pijp lijn die alle activiteiten organiseert. In dit artikel leert u hoe u DevOps-procedures toepast op de ontwikkelings levenscyclus van een gemeen schappelijke gegevens opname pijplijn. Met de pijp lijn worden de gegevens voor de training van het Machine Learning model voor bereid.

## <a name="the-solution"></a>De oplossing

Bekijk de volgende werk stroom voor gegevens opname:

![gegevens opname-pijp lijn](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

In deze benadering worden de trainings gegevens opgeslagen in een Azure Blob-opslag. Een Azure Data Factory pijp lijn haalt de gegevens op uit een invoer-BLOB-container, transformeert deze en slaat de gegevens op in de uitvoer BLOB-container. Deze container fungeert als [gegevens opslag](concept-data.md) voor de Azure machine learning-service. Als u de gegevens hebt voor bereid, roept de Data Factory-pijp lijn een training Machine Learning pijp lijn aan om een model te trainen. In dit specifieke voor beeld wordt de gegevens transformatie uitgevoerd door een python-notebook, dat wordt uitgevoerd op een Azure Databricks cluster. 

## <a name="what-we-are-building"></a>Wat we bouwen

Net als bij elke software oplossing is er een team (bijvoorbeeld gegevens technici) aan het werk. 

![cicd-gegevens opname](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Ze werken samen en delen dezelfde Azure-resources, zoals Azure Data Factory, Azure Databricks, Azure Storage account en dergelijke. Het verzamelen van deze resources is een ontwikkel omgeving. De gegevens technici dragen bij aan dezelfde bron code basis. Het proces voor continue integratie assembleert de code, controleert deze met de kwaliteits tests, eenheids tests en produceert artefacten zoals geteste code en Azure Resource Manager sjablonen. Met het doorlopende leverings proces worden de artefacten geïmplementeerd naar de downstream-omgevingen. In dit artikel wordt beschreven hoe u de CI-en CD-processen kunt automatiseren met [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Broncode beheer

De team leden werken op iets verschillende manieren om samen te werken aan de bron code van de python-notebook en de Azure Data Factory bron code. In beide gevallen wordt de code echter opgeslagen in een opslag plaats voor bron beheer (bijvoorbeeld Azure DevOps, GitHub, GitLab) en de samen werking is normaal gebaseerd op een bepaald vertakkings model (bijvoorbeeld [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Bron code van python-notebook

De gegevens technici werken met de bron code van python-notebook lokaal in een IDE (bijvoorbeeld [Visual Studio code](https://code.visualstudio.com)) of rechtstreeks in de Databricks-werk ruimte. De laatste biedt de mogelijkheid om fouten op te sporen in de code in de ontwikkel omgeving. In elk geval wordt de code samengevoegd met de opslag plaats na een vertakkings beleid. Het is raadzaam om de code op te slaan `.py` in bestanden in plaats `.ipynb` van in de Jupyter-notebook-indeling. Het verbetert de Lees baarheid van de code en maakt automatische controle van de code kwaliteit mogelijk in het CI-proces.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory bron code

De bron code van Azure Data Factory pijp lijnen is een verzameling json-bestanden die door een werk ruimte worden gegenereerd. Normaal gesp roken werken de gegevens technici met een visuele ontwerper in de werk ruimte Azure Data Factory, in plaats van de bron code bestanden rechtstreeks. Configureer de werk ruimte met een broncode beheer bibliotheek zoals beschreven in de [Azure Data Factory documentatie](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). Als deze configuratie is geïmplementeerd, kunnen de gegevens technici samen werken aan de bron code na een voorkeurs werk stroom voor vertakking.    

## <a name="continuous-integration-ci"></a>Doorlopende integratie (CI)

Het ultieme doel van het proces voor continue integratie is het verzamelen van het gezamenlijke team werk van de bron code en het voorbereiden van de implementatie op de downstream-omgevingen. Net als bij broncode beheer is dit proces verschillend voor de python-notebooks en Azure Data Factory pijp lijnen. 

### <a name="python-notebook-ci"></a>Python-notebook-CI

Het CI-proces voor de python-notebooks haalt de code op uit de vertakking voor samen werking (bijvoorbeeld ***Master*** of ***develope***) en voert de volgende activiteiten uit:
* Code linting
* Moduletests uitvoeren
* De code opslaan als een artefact

In het volgende code fragment ziet u de implementatie van deze stappen in een Azure DevOps ***yaml*** -pijp lijn:

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

De pijp lijn gebruikt ***flake8*** om de python-code te gebruiken. De eenheids tests die in de bron code zijn gedefinieerd, worden uitgevoerd en de resultaten van de linting en test worden gepubliceerd, zodat ze beschikbaar zijn in het uitvoerings scherm van Azure pipeline:

![pluisief-eenheids tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Als de samenvoegings-en eenheids test is geslaagd, kopieert de pijp lijn de bron code naar de artefact opslagplaats die moet worden gebruikt door de volgende implementaties tappen.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

CI-proces voor een Azure Data Factory pijp lijn is een knel punt in het hele CI/CD-verhaal voor een gegevens opname pijplijn. Er is geen ***continue*** integratie. Een implementeerbaar artefact voor Azure Data Factory is een verzameling Azure Resource Manager sjablonen. De enige manier om deze sjablonen te maken, is door op de knop ***publiceren*** te klikken in de werk ruimte Azure Data Factory. Er is hier geen automatisering.
De gegevens technici voegen de bron code van hun functie vertakkingen samen in de vertakking voor samen werking, bijvoorbeeld ***hoofd*** of ***ontwikkeling***. Vervolgens klikt iemand met de verleende machtigingen op de knop ***publiceren*** om Azure Resource Manager sjablonen te genereren op basis van de bron code in de vertakking voor samen werking. Wanneer op de knop wordt geklikt, worden de pijp lijnen door de werk ruimte gevalideerd (deze zijn van toepassing op het maken van linten en de eenheids tests), worden Azure Resource Manager sjablonen gegenereerd (deze zijn van toepassing op basis van het gebouw) en worden de gegenereerde sjablonen opgeslagen in een technische vertakking ***adf_publish*** in dezelfde code opslagplaats (denk eraan dat het publicatie artefacten is). Deze vertakking wordt automatisch gemaakt door de Azure Data Factory-werk ruimte. Dit proces wordt beschreven in de Azure Data Factory- [documentatie](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

Het is belang rijk om ervoor te zorgen dat de gegenereerde Azure Resource Manager sjablonen omgeving neutraal zijn. Dit betekent dat alle waarden die tussen omgevingen kunnen verschillen, parametrized zijn. Het Azure Data Factory is slim genoeg om het meren deel van dergelijke waarden als para meters weer te geven. Zo worden in de volgende sjabloon de verbindings eigenschappen voor een Azure Machine Learning werk ruimte als para meters weer gegeven:

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

Het is echter mogelijk dat u uw aangepaste eigenschappen wilt weer geven die niet standaard worden verwerkt door de Azure Data Factory-werk ruimte. In het scenario van dit artikel roept een Azure Data Factory pijp lijn een python-notebook aan om de gegevens te verwerken. Het notitie blok accepteert een para meter met de naam van een invoer gegevensbestand.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Deze naam wijkt af van ***ontwikkel***-, ***QA***-, ***bedoeld***-en ***productie*** omgevingen. In een complexe pijp lijn met meerdere activiteiten kunnen er verschillende aangepaste eigenschappen zijn. Het is raadzaam om al deze waarden op één plek te verzamelen en ze als pijplijn ***variabelen***te definiëren:

![ADF-variabelen](media/how-to-cicd-data-ingestion/adf-variables.png)

De pijplijn activiteiten kunnen verwijzen naar de pijplijn variabelen en ze in feite gebruiken:

![ADF-notebook-para meters](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

In de Azure Data Factory-werk ruimte worden pijplijn variabelen ***niet*** standaard weer gegeven als Azure Resource Manager sjablonen-para meters. In de werk ruimte wordt gebruikgemaakt van de [standaard sjabloon voor parameterisering](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) die bepaalt welke pijplijn eigenschappen moeten worden weer gegeven als Azure Resource Manager sjabloon parameters. Als u pijplijn variabelen wilt toevoegen aan de lijst, werkt u het gedeelte ' micro soft. DataFactory/factorions/pijp lijnen ' van de [standaard sjabloon parameterisering](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) bij met het volgende code fragment en plaatst u het JSON-bestand in de hoofdmap van de bronmap:

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

Als u dit doet, wordt de Azure Data Factory-werk ruimte de variabelen aan de lijst met para meters toegevoegd wanneer u op de knop ***publiceren*** klikt:

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

De waarden in het JSON-bestand zijn standaard waarden die zijn geconfigureerd in de pijplijn definitie. Ze worden naar verwachting overschreven door de doel omgevings waarden wanneer de Azure Resource Manager sjabloon wordt geïmplementeerd.

## <a name="continuous-delivery-cd"></a>Continue levering (CD)

Het doorlopende leverings proces neemt de artefacten en implementeert deze in de eerste doel omgeving. Er wordt gecontroleerd of de oplossing werkt door tests uit te voeren. Als dat lukt, gaat het verder met de volgende omgeving. De CD Azure-pijp lijn bestaat uit meerdere fasen die de omgevingen vertegenwoordigen. Elke fase bevat [implementaties](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) en [taken](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) die de volgende stappen uitvoeren:
* Een python-notebook implementeren op Azure Databricks werk ruimte
* Een Azure Data Factory-pijp lijn implementeren 
* De pijplijn uitvoeren
* Het resultaat van de gegevens opname controleren

De pijplijn fasen kunnen worden geconfigureerd met [goed keuringen](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) en [poorten](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) die extra controle bieden over hoe het implementatie proces zich ontwikkelt door de keten van omgevingen.

### <a name="deploy-a-python-notebook"></a>Een python-notebook implementeren

Het volgende code fragment definieert een Azure-pijplijn [implementatie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) waarmee een python-notebook wordt gekopieerd naar een Databricks-cluster:

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

De artefacten die door de CI worden geproduceerd, worden automatisch naar de implementatie agent gekopieerd en zijn beschikbaar in de map ***$ (pipeline. workspace)*** . In dit geval verwijst de implementatie taak naar het artefact van de ***di-notebook*** met de python-notebook. Deze [implementatie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) maakt gebruik van de [Databricks Azure DevOps-extensie](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) om de notitieblok bestanden te kopiëren naar de Databricks-werk ruimte.
De ***Deploy_to_QA*** fase bevat een verwijzing naar de variabelen groep ***devops-DS-QA-VG*** die is gedefinieerd in het Azure devops-project. De stappen in deze fase verwijzen naar de variabelen van deze variabelen groep (bijvoorbeeld $ (DATABRICKS_URL), $ (DATABRICKS_TOKEN)). Het idee is dat de volgende fase (bijvoorbeeld ***Deploy_to_UAT***) wordt gebruikt met dezelfde namen van variabelen die zijn gedefinieerd in de eigen bedoeld.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Een Azure Data Factory-pijp lijn implementeren

Een implementeerbaar artefact voor Azure Data Factory is een Azure Resource Manager sjabloon. Daarom zal het worden geïmplementeerd met de implementatie taak van de ***Azure-resource groep*** , zoals wordt gedemonstreerd in het volgende code fragment:

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
De waarde van de para meter data filename is afkomstig van de variabele $ (DATA_FILE_NAME) die in een variabele van een QA-fase is gedefinieerd. Op dezelfde manier kunnen alle para meters die zijn gedefinieerd in ***ARMTemplateForFactory. json*** worden overschreven. Als dat niet het geval is, worden de standaard waarden gebruikt.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>De pijp lijn uitvoeren en het resultaat van de gegevens opname controleren

De volgende stap is om ervoor te zorgen dat de geïmplementeerde oplossing werkt. De volgende taak definitie voert een Azure Data Factory pijp lijn met een [Power shell-script](https://github.com/microsoft/DataOps/tree/master/adf/utils) uit en voert een python-notebook uit op een Azure Databricks cluster. Het notitie blok controleert of de gegevens correct zijn opgenomen en valideert het resultaat gegevensbestand met $ (bin_FILE_NAME)-naam.

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

De laatste taak in de taak controleert het resultaat van de uitvoering van de notitie blok. Als er een fout wordt geretourneerd, wordt de status van de pijplijn uitvoering ingesteld op mislukt.

## <a name="putting-pieces-together"></a>Delen samen brengen

Het resultaat van dit artikel is een CI/CD Azure-pijp lijn die bestaat uit de volgende fasen:
* CI
* Implementeren naar QA
    * Implementeren naar Databricks + implementeren naar ADF
    * Integratie test

Het bevat een aantal ***implementatie*** fasen die gelijk zijn aan het aantal doel omgevingen dat u hebt. Elke ***implementatie*** fase bevat twee [implementaties](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) die parallel worden uitgevoerd en een [taak](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) die wordt uitgevoerd na implementaties om de oplossing in de omgeving te testen.

Een voorbeeld implementatie van de pijp lijn wordt geassembleerd in het volgende ***yaml*** -fragment:

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

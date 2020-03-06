---
title: CLI-extensie
titleSuffix: Azure Machine Learning
description: Meer informatie over de Azure Machine Learning CLI-extensie voor de Azure CLI. De Azure-CLI is een opdrachtregelprogramma voor meerdere platformen die u kunt werken met resources in de Azure-cloud. Met de Machine Learning extensie kunt u werken met Azure Machine Learning. Met de MILLILITER CLI worden resources zoals uw werk ruimte, gegevens opslag, gegevens sets, pijp lijnen, modellen en implementaties gemaakt en beheerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402488"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>De CLI-uitbrei ding voor Azure Machine Learning gebruiken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

De Azure Machine Learning CLI is een uitbrei ding van [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), een platformoverschrijdende opdracht regel interface voor het Azure-platform. Deze uitbrei ding bevat opdrachten voor het werken met Azure Machine Learning. Zo kunt u uw machine learning activiteiten automatiseren. De volgende lijst bevat enkele voor beelden van acties die u kunt uitvoeren met de CLI-extensie:

+ Uitvoeren van experimenten voor het maken van machine learning-modellen

+ Machine learning-modellen voor het gebruik van de klant registreren

+ Verpakken, implementeren en bijhouden van de levenscyclus van machine learning-modellen

De CLI is geen vervanging voor de SDK van Azure Machine Learning. Het is een aanvullend hulp programma dat is geoptimaliseerd voor het verwerken van zeer geparametriseerde taken die goed zijn afgestemd op automatisering.

## <a name="prerequisites"></a>Vereisten

* Voor het gebruik van de CLI, moet u een Azure-abonnement hebben. Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* De [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Volledige referentie documenten

Zoek de [volledige referentie documenten voor de extensie Azure-cli-ml van Azure cli](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>De extensie installeren

Gebruik de volgende opdracht voor het installeren van de Machine Learning CLI-extensie:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Voorbeeld bestanden die u kunt gebruiken met de onderstaande opdrachten, vindt u [hier](https://aka.ms/azml-deploy-cloud).

Wanneer u hierom wordt gevraagd, selecteert u `y` om de uitbrei ding te installeren.

Om te controleren of de extensie is geïnstalleerd, gebruikt u de volgende opdracht uit om een lijst met ML-specifieke subopdrachten weer te geven:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>De extensie bijwerken

Als u de Machine Learning CLI-extensie wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>De extensie verwijderen

Als u wilt verwijderen van de CLI-extensie, gebruik de volgende opdracht:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resourcebeheer

De volgende opdrachten laten zien hoe u de CLI gebruiken voor het beheren van resources die worden gebruikt door Azure Machine Learning.

+ Als u er nog geen hebt, maakt u een resource groep:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Een Azure Machine Learning-werk ruimte maken:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Met deze opdracht maakt u een werk ruimte Basic Edition. Als u een Enter prise-werk ruimte wilt maken, gebruikt u de `--sku enterprise` switch met de `az ml workspace create` opdracht. Zie [Wat is Azure machine learning](overview-what-is-azure-ml.md#sku)voor meer informatie over Azure machine learning-edities.

    Zie [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)(Engelstalig) voor meer informatie.

+ Voeg een werkruimte configuratie toe aan een map om CLI-contextuele bewustzijn in te scha kelen.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze opdracht maakt u een `.azureml` submap die voor beelden van runconfig-en Conda-omgevings bestanden bevat. Het bevat ook een `config.json`-bestand dat wordt gebruikt om te communiceren met uw Azure Machine Learning-werk ruimte.

    Zie voor meer informatie [AZ ml map attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Een Azure Blob-container als gegevens opslag koppelen.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Zie voor meer informatie [AZ ml Data Store attach-BLOB](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Bestanden uploaden naar een gegevens opslag.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Zie voor meer informatie [AZ ml Data Store upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Koppel een AKS-cluster als een compute-doel.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Zie [AZ ml computetarget attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) voor meer informatie.

+ Maak een nieuw AMLcompute-doel.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Zie [AZ ml computetarget Create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)voor meer informatie.

## <a id="experiments"></a>Experimenten uitvoeren

* Start een uitvoering van uw experiment. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst vóór \*. runconfig als u uw bestands systeem bekijkt) op basis van de para meter-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Met de `az ml folder attach` opdracht maakt u een `.azureml`-submap, die twee voor beelden van runconfig-bestanden bevat. 
    >
    > Als u een python-script hebt waarmee een configuratie object voor een uitvoering programmatisch wordt gemaakt, kunt u [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een RunConfig-bestand.
    >
    > Het volledige runconfig-schema vindt u in dit [JSON-bestand](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Het schema wordt door de `description` sleutel van elk object zelf gedocumenteerd. Daarnaast zijn er opsommingen voor mogelijke waarden en een sjabloon fragment aan het einde.

    Zie [AZ ml run-script uitvoeren](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

* Een lijst met experimenten weer geven:

    ```azurecli-interactive
    az ml experiment list
    ```

    Zie voor meer informatie [AZ ml experimenten List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Beheer van gegevensset

De volgende opdrachten laten zien hoe u met gegevens sets in Azure Machine Learning kunt werken:

+ Een gegevensset registreren:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Gebruik `az ml dataset register --show-template`voor informatie over de indeling van het JSON-bestand dat wordt gebruikt voor het definiëren van de gegevensset.

    Zie voor meer informatie [AZ ml dataset REGI ster](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Een actieve of afgeschafte gegevensset archiveren:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Zie voor meer informatie [AZ ml dataset Archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Een gegevensset afschaffen:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Zie [AZ ml dataset afschaffen](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)voor meer informatie.

+ Alle gegevens sets in een werk ruimte weer geven:

    ```azurecli-interactive
    az ml dataset list
    ```

    Zie de [lijst AZ ml dataset](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)voor meer informatie.

+ Details van een gegevensset ophalen:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Zie [AZ ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)(Engelstalig) voor meer informatie.

+ Een gearchiveerde of afgeschafte gegevensset opnieuw activeren:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Zie voor meer informatie [AZ ml dataset reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Registratie van een gegevensset opheffen:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Zie [AZ ml dataset unregisterd](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)(Engelstalig) voor meer informatie.

## <a name="environment-management"></a>Omgevings beheer

De volgende opdrachten laten zien hoe u Azure Machine Learning [omgevingen](how-to-configure-environment.md) kunt maken, registreren en weer geven voor uw werk ruimte:

+ Maak een steiger bestand voor een omgeving:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Zie [AZ ml Environment steigers](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)voor meer informatie.

+ Een omgeving registreren:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Zie [AZ ml Environment REGI ster](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)(Engelstalig) voor meer informatie.

+ Geregistreerde omgevingen weer geven:

    ```azurecli-interactive
    az ml environment list
    ```

    Zie de [lijst AZ ml Environment](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)(Engelstalig) voor meer informatie.

+ Een geregistreerde omgeving downloaden:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Zie [AZ ml Environment down load](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)(Engelstalig) voor meer informatie.

### <a name="environment-configuration-schema"></a>Configuratie schema omgeving

Als u de opdracht `az ml environment scaffold` hebt gebruikt, wordt er een sjabloon `azureml_environment.json` bestand gegenereerd dat kan worden gewijzigd en gebruikt om aangepaste omgevings configuraties te maken met de CLI. Het object op het hoogste niveau is in de python-SDK los gekoppeld aan de klasse [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) . 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

De volgende tabel bevat informatie over elk veld op het hoogste niveau in het JSON-bestand, het type en een beschrijving. Als een object type is gekoppeld aan een klasse uit de python-SDK, is er een losse 1:1 overeenkomst tussen elk JSON-veld en de naam van de open bare variabele in de python-klasse. In sommige gevallen kan het veld worden toegewezen aan een constructor-argument in plaats van een klassen variabele. Het `environmentVariables` veld wordt bijvoorbeeld toegewezen aan de `environment_variables` variabele in de [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) -klasse.

| JSON-veld | Type | Beschrijving |
|---|---|---|
| `name` | `string` | De naam van de omgeving. Start de naam niet met **micro soft** of **AzureML**. |
| `version` | `string` | De versie van de omgeving. |
| `environmentVariables` | `{string: string}` | Een hash-toewijzing van namen en waarden van omgevings variabelen. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Object waarmee de python-omgeving en-interpreter worden gedefinieerd die moeten worden gebruikt voor de doel Compute-resource. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definieert instellingen voor het aanpassen van de docker-installatie kopie die is gebaseerd op de specificaties van de omgeving. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | In het gedeelte worden Spark-instellingen geconfigureerd. Het wordt alleen gebruikt wanneer Framework is ingesteld op PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Hiermee configureert u Databricks-bibliotheek afhankelijkheden. |
| `inferencingStackVersion` | `string` | Hiermee geeft u de versie van de stack voor het in de wachtrij plaatsen toevoegen aan de installatie kopie. Als u wilt voor komen dat een inleidende stack wordt toegevoegd, moet u dit veld `null`laten staan. Geldige waarde: ' meest recent '. |

## <a name="ml-pipeline-management"></a>ML pijplijn beheer

De volgende opdrachten laten zien hoe u met machine learning pijp lijnen kunt werken:

+ Een machine learning-pijp lijn maken:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Zie voor meer informatie [AZ ml pijp lijn Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Zie [machine learning-pijp lijnen definiëren in YAML](reference-pipeline-yaml.md)voor meer informatie over het yaml-bestand van de pijp lijn.

+ Een pijp lijn uitvoeren:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Zie voor meer informatie [AZ ml run-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Zie [machine learning-pijp lijnen definiëren in YAML](reference-pipeline-yaml.md)voor meer informatie over het yaml-bestand van de pijp lijn.

+ Een pijp lijn plannen:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Zie voor meer informatie [AZ ml pijp lijn Create-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Zie [machine learning-pijp lijnen definiëren in YAML](reference-pipeline-yaml.md#schedules)voor meer informatie over het yaml-bestand van de pipeline-planning.

## <a name="model-registration-profiling-deployment"></a>Model registratie, profile ring, implementatie

De volgende opdrachten laten zien hoe u een getraind model registreert en implementeert u deze vervolgens als een productieservice:

+ Registreer een model met Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Zie voor meer informatie [AZ ml model REGI ster](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Optioneel** Profiel uw model om optimale CPU-en geheugen waarden voor implementatie te verkrijgen.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Zie [AZ ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)(Engelstalig) voor meer informatie.

+ Implementeer uw model op AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Zie voor meer informatie over het schema voor het dezicht van de configuratie voor het dezicht configuratie [schema](#inferenceconfig)voor het inlichten.
    
    Zie [implementatie configuratie schema](#deploymentconfig)voor meer informatie over het schema van het configuratie bestand voor implementatie.

    Zie [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)(Engelstalig) voor meer informatie.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Configuratie schema voor afleiding

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Configuratie schema implementatie

### <a name="local-deployment-configuration-schema"></a>Configuratie schema voor lokale implementatie

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Configuratie schema voor implementatie van Azure container instance 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Configuratie schema voor implementatie van Azure Kubernetes service

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Volgende stappen

* [Opdracht verwijzing voor de machine learning cli-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [machine learning modellen trainen en implementeren met behulp van Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning)

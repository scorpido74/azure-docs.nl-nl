---
title: CLI-extensie
titleSuffix: Azure Machine Learning
description: Meer informatie over de AZURE Machine Learning CLI-extensie voor de Azure CLI. De Azure CLI is een cross-platform command-line hulpprogramma waarmee u werken met resources in de Azure-cloud. Met de machine learning-extensie u werken met Azure Machine Learning. De ML CLI maakt en beheert resources zoals uw werkruimte, gegevenswinkels, gegevenssets, pijplijnen, pijplijnen, modellen en implementaties.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402488"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>De CLI-extensie voor Azure Machine Learning gebruiken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

De Azure Machine Learning CLI is een uitbreiding van de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), een cross-platform command-line interface voor het Azure-platform. Deze extensie biedt opdrachten voor het werken met Azure Machine Learning. Hiermee u uw machine learning-activiteiten automatiseren. In de volgende lijst vindt u enkele voorbeeldacties die u uitvoeren met de CLI-extensie:

+ Experimenten uitvoeren om machine learning-modellen te maken

+ Machine learning-modellen registreren voor klantgebruik

+ De levenscyclus van uw machine learning-modellen verpakken, implementeren en bijhouden

De CLI is geen vervanging voor de Azure Machine Learning SDK. Het is een complementair hulpmiddel dat is geoptimaliseerd om sterk geparameteriseerde taken te verwerken die goed passen bij automatisering.

## <a name="prerequisites"></a>Vereisten

* Als u de CLI wilt gebruiken, moet u een Azure-abonnement hebben. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* De [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Volledige referentiedocumenten

Zoek de [volledige referentiedocumenten voor de azure-cli-ml-extensie van Azure CLI.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)

## <a name="install-the-extension"></a>De extensie installeren

Als u de CLI-extensie Machine Learning wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Voorbeeldbestanden die u gebruiken met de onderstaande opdrachten zijn [hier](https://aka.ms/azml-deploy-cloud)te vinden.

Selecteer desgevraagd `y` de extensie te installeren.

Als u wilt controleren of de extensie is geïnstalleerd, gebruikt u de volgende opdracht om een lijst met ML-specifieke subopdrachten weer te geven:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>De extensie bijwerken

Als u de CLI-extensie Machine Learning wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>De extensie verwijderen

Als u de CLI-extensie wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resourcebeheer

De volgende opdrachten laten zien hoe u de CLI gebruiken om resources te beheren die worden gebruikt door Azure Machine Learning.

+ Als u er nog geen hebt, maakt u een resourcegroep:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Maak een Azure Machine Learning-werkruimte:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Met deze opdracht wordt een werkruimte voor de basiseditie gemaakt. Als u een werkruimte `--sku enterprise` voor een `az ml workspace create` onderneming wilt maken, gebruikt u de schakelaar met de opdracht. Zie Wat is Azure Machine Learning voor meer informatie over Azure Machine [Learning-edities.](overview-what-is-azure-ml.md#sku)

    Zie [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)voor meer informatie.

+ Voeg een werkruimteconfiguratie toe aan een map om cli-contextuele bekendheid in te schakelen.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze `.azureml` opdracht wordt een submap gemaakt die voorbeeldrunconfig- en conda-omgevingbestanden bevat. Het bevat `config.json` ook een bestand dat wordt gebruikt om te communiceren met uw Azure Machine Learning-werkruimte.

    Zie az [ml map attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Voeg een Azure blob-container toe als een Gegevensarchief.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Zie [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)voor meer informatie.

+ Bestanden uploaden naar een Datastore.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Zie voor meer informatie [az ml datastore uploaden.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)

+ Voeg een AKS-cluster toe als een compute target.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Zie voor meer informatie [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Maak een nieuw AMLcompute-doel.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Zie az ml computetarget voor meer informatie [amlcompute maken.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)

## <a name="run-experiments"></a><a id="experiments"></a>Experimenten uitvoeren

* Start een run van uw experiment. Wanneer u deze opdracht gebruikt, geeft u de naam \*van het runconfig-bestand (de tekst vóór .runconfig als u naar uw bestandssysteem kijkt) op tegen de parameter -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > De `az ml folder attach` opdracht `.azureml` maakt een submap, die twee voorbeeldrunconfig-bestanden bevat. 
    >
    > Als u een Python-script hebt dat een programmatisch uitgevoerd configuratieobject maakt, u [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als runconfig-bestand.
    >
    > Het volledige runconfig schema is te vinden in dit [JSON-bestand.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) Het schema documenteert zichzelf `description` via de sleutel van elk object. Daarnaast zijn er enums voor mogelijke waarden, en een sjabloon fragment aan het einde.

    Zie [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

* Bekijk een lijst met experimenten:

    ```azurecli-interactive
    az ml experiment list
    ```

    Zie voor meer informatie [de az ml-experimentlijst](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gegevenssetbeheer

In de volgende opdrachten wordt uitgelegd hoe u met gegevenssets werken in Azure Machine Learning:

+ Een gegevensset registreren:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Voor informatie over de indeling van het JSON-bestand dat wordt gebruikt om de gegevensset te definiëren, gebruikt u `az ml dataset register --show-template`.

    Zie voor meer informatie [het az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archiveer een actieve of afgeschafte gegevensset:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Zie voor meer informatie [het az ml dataset archief](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Een gegevensset afkeuren:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Zie voor meer informatie [de gegevensset az ml deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Alle gegevenssets in een werkruimte weergeven:

    ```azurecli-interactive
    az ml dataset list
    ```

    Zie de [lijst met az ml-gegevenssets](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)voor meer informatie.

+ Meer informatie over een gegevensset:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Zie voor meer informatie [de gegevensset AZ ML tonen.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)

+ Een gearchiveerde of afgeschafte gegevensset opnieuw activeren:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Zie de [az ml-gegevensset reactiveren](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)voor meer informatie.

+ Het registreren van een gegevensset:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Zie voor meer informatie [de gegevensset az ml uitschrijven.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)

## <a name="environment-management"></a>Milieubeheer

In de volgende opdrachten wordt uitgelegd hoe u Azure Machine [Learning-omgevingen](how-to-configure-environment.md) voor uw werkruimte maken, registreren en aanbieden:

+ Steigerbestanden maken voor een omgeving:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Zie voor meer informatie [het schavot az ml-omgevingsschavot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)

+ Registreer een omgeving:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Zie voor meer informatie [het az ml-milieuregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Lijst geregistreerde omgevingen:

    ```azurecli-interactive
    az ml environment list
    ```

    Zie voor meer informatie [de az ml-milieulijst](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Download een geregistreerde omgeving:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Zie voor meer informatie [de az ml-omgeving downloaden.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)

### <a name="environment-configuration-schema"></a>Configuratieschema voor omgeving

Als u `az ml environment scaffold` de opdracht hebt `azureml_environment.json` gebruikt, genereert deze een sjabloonbestand dat kan worden gewijzigd en gebruikt om aangepaste omgevingsconfiguraties te maken met de CLI. Het object op het hoogste [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) niveau wordt losjes toegewezen aan de klasse in de Python SDK. 

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

In de volgende tabel wordt elk veld op het hoogste niveau in het JSON-bestand, het type en een beschrijving beschreven. Als een objecttype is gekoppeld aan een klasse van de Python SDK, is er een losse 1:1-overeenkomst tussen elk JSON-veld en de openbare variabele naam in de klasse Python. In sommige gevallen kan het veld toewijzen aan een constructeurargument in plaats van een klassevariabele. Het `environmentVariables` veld wordt bijvoorbeeld `environment_variables` toegewezen aan [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) de variabele in de klasse.

| Json-veld | Type | Beschrijving |
|---|---|---|
| `name` | `string` | Naam van de omgeving. Begin de naam niet met **Microsoft** of **AzureML**. |
| `version` | `string` | Versie van de omgeving. |
| `environmentVariables` | `{string: string}` | Een hash-map met omgevingsvariabele namen en waarden. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Object dat de Python-omgeving en de tolk definieert om te gebruiken op doelcompute resource. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Hiermee definieert u instellingen om de Docker-afbeelding aan te passen die is gebouwd volgens de specificaties van de omgeving. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | De sectie configureert Spark-instellingen. Het wordt alleen gebruikt wanneer het framework is ingesteld op PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Hiermee configureert u afhankelijkheden van de Databricks-bibliotheek. |
| `inferencingStackVersion` | `string` | Hiermee geeft u de binnenkomende stapelversie op die aan de afbeelding is toegevoegd. Om te voorkomen dat een inferencing stack wordt toegevoegd, verlaat u dit veld `null`. Geldige waarde: "laatste". |

## <a name="ml-pipeline-management"></a>ML-pijplijnbeheer

De volgende opdrachten laten zien hoe u met machine learning-pijplijnen werken:

+ Maak een machine learning-pijplijn:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Zie [az ml pipeline create voor](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)meer informatie.

    Zie [Machine learning-pijplijnen definiëren in YAML](reference-pipeline-yaml.md)voor meer informatie over het YAML-bestand voor pijplijnen.

+ Voer een pijplijn uit:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Zie [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)voor meer informatie.

    Zie [Machine learning-pijplijnen definiëren in YAML](reference-pipeline-yaml.md)voor meer informatie over het YAML-bestand voor pijplijnen.

+ Een pijplijn plannen:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Zie az [ml pipeline create-schedule voor](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)meer informatie.

    Zie [Machine learning-pijplijnen definiëren in YAML voor](reference-pipeline-yaml.md#schedules)meer informatie over het YAML-bestand voor pijplijnplanning.

## <a name="model-registration-profiling-deployment"></a>Modelregistratie, profilering, implementatie

De volgende opdrachten laten zien hoe u een getraind model registreert en implementeert het vervolgens als productieservice:

+ Een model registreren met Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Zie voor meer informatie [het az ml-modelregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **OPTIONEEL** Profileer uw model om optimale CPU- en geheugenwaarden te krijgen voor implementatie.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Zie voor meer informatie [het az ml-modelprofiel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Uw model implementeren op AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Zie [Inference configuratieschema](#inferenceconfig)voor meer informatie over het configuratieschema van de conclusie .
    
    Zie [Configuratieschema voor](#deploymentconfig)implementatie voor meer informatie over het bestandsschema voor implementatieconfiguratie .

    Zie az [ml-model implementeren](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)voor meer informatie.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Inferenceconfiguratieschema

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Configuratieschema voor implementatie

### <a name="local-deployment-configuration-schema"></a>Configuratieschema voor lokale implementatie

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Configuratieschema voor azure-containerinstantie-implementatie 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Configuratieschema voor Azure Kubernetes Service-implementatie

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Volgende stappen

* [Opdrachtverwijzing voor de MACHINE Learning CLI-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Machine learning-modellen trainen en implementeren met Azure-pijplijnen](/azure/devops/pipelines/targets/azure-machine-learning)

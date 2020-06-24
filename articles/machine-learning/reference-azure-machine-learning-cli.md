---
title: Azure Machine Learning CLI installeren & gebruiken
description: Meer informatie over het installeren en gebruiken van de Azure Machine Learning CLI-extensie voor het maken en beheren van resources zoals uw werk ruimte, gegevens opslag, gegevens sets, pijp lijnen, modellen en implementaties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18
ms.openlocfilehash: cd636ed1ff4369360fb5faae02e8095983cad043
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214516"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Installeer & gebruik de CLI-extensie voor Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

De Azure Machine Learning CLI is een uitbrei ding van [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), een platformoverschrijdende opdracht regel interface voor het Azure-platform. Deze uitbrei ding bevat opdrachten voor het werken met Azure Machine Learning. Zo kunt u uw machine learning activiteiten automatiseren. De volgende lijst bevat enkele voor beelden van acties die u kunt uitvoeren met de CLI-extensie:

+ Experimenten uitvoeren om machine learning modellen te maken

+ machine learning modellen registreren voor klant gebruik

+ De levens cyclus van uw machine learning modellen inpakken, implementeren en bijhouden

De CLI is geen vervanging voor de Azure Machine Learning SDK. Het is een aanvullend hulp programma dat is geoptimaliseerd voor het verwerken van zeer geparametriseerde taken die goed zijn afgestemd op automatisering.

## <a name="prerequisites"></a>Vereisten

* Als u de CLI wilt gebruiken, moet u een Azure-abonnement hebben. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Als u de CLI-opdrachten in dit document vanuit uw **lokale omgeving**wilt gebruiken, hebt u de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

    Als u de [Azure Cloud shell](https://azure.microsoft.com//features/cloud-shell/)gebruikt, wordt de CLI geopend via de browser en in de Cloud.

## <a name="full-reference-docs"></a>Volledige referentie documenten

Zoek de [volledige referentie documenten voor de extensie Azure-cli-ml van Azure cli](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI koppelen aan uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, kunt u deze sectie overs Laan. De Cloud shell verifieert u automatisch met het account dat u hebt aangemeld bij uw Azure-abonnement.

Er zijn verschillende manieren waarop u kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De meeste basis is om interactief te verifiëren met behulp van een browser. Als u interactief wilt verifiëren, opent u een opdracht regel of Terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdracht regel volgen. De instructies moeten bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en een autorisatie code invoeren.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Zie [Aanmelden met Azure cli](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)voor andere verificatie methoden.

## <a name="install-the-extension"></a>De extensie installeren

Als u de Machine Learning CLI-extensie wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Voorbeeld bestanden die u kunt gebruiken met de onderstaande opdrachten, vindt u [hier](https://aka.ms/azml-deploy-cloud).

Wanneer u hierom wordt gevraagd, selecteert `y` u om de uitbrei ding te installeren.

Als u wilt controleren of de extensie is geïnstalleerd, gebruikt u de volgende opdracht om een lijst met ML-specifieke subopdrachten weer te geven:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>De extensie bijwerken

Als u de Machine Learning CLI-extensie wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>De extensie verwijderen

Als u de CLI-extensie wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Resourcebeheer

De volgende opdrachten laten zien hoe u de CLI gebruikt om resources te beheren die worden gebruikt door Azure Machine Learning.

+ Als u er nog geen hebt, maakt u een resource groep:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Een Azure Machine Learning-werk ruimte maken:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Met deze opdracht maakt u een werk ruimte Basic Edition. Als u een Enter prise-werk ruimte wilt maken, gebruikt u de `--sku enterprise` Schakel optie met de `az ml workspace create` opdracht. Zie [Wat is Azure machine learning](overview-what-is-azure-ml.md#sku)voor meer informatie over Azure machine learning-edities.

    Zie [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)(Engelstalig) voor meer informatie.

+ Voeg een werkruimte configuratie toe aan een map om CLI-contextuele bewustzijn in te scha kelen.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze opdracht maakt u een `.azureml` submap die voor beelden van runconfig-en Conda-omgevings bestanden bevat. Het bevat ook een `config.json` bestand dat wordt gebruikt om te communiceren met uw Azure machine learning-werk ruimte.

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

## <a name="run-experiments"></a><a id="experiments"></a>Experimenten uitvoeren

* Start een uitvoering van uw experiment. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand (de tekst voor \* . runconfig als u uw bestands systeem bekijkt) op basis van de para meter-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach`Met de opdracht wordt een `.azureml` submap gemaakt die twee voor beelden van runconfig-bestanden bevat. 
    >
    > Als u een python-script hebt waarmee een configuratie object voor een uitvoering programmatisch wordt gemaakt, kunt u [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een RunConfig-bestand.
    >
    > Het volledige runconfig-schema vindt u in dit [JSON-bestand](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Het schema wordt zelf gedocumenteerd via de `description` sleutel van elk object. Daarnaast zijn er opsommingen voor mogelijke waarden en een sjabloon fragment aan het einde.

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

    Voor informatie over de indeling van het JSON-bestand dat wordt gebruikt voor het definiëren van de gegevensset, gebruikt u `az ml dataset register --show-template` .

    Zie voor meer informatie [AZ ml dataset REGI ster](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-register).

+ Alle gegevens sets in een werk ruimte weer geven:

    ```azurecli-interactive
    az ml dataset list
    ```

    Zie de [lijst AZ ml dataset](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-list)voor meer informatie.

+ Details van een gegevensset ophalen:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Zie [AZ ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-show)(Engelstalig) voor meer informatie.

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

Als u de opdracht hebt gebruikt, wordt er `az ml environment scaffold` een sjabloon `azureml_environment.json` bestand gegenereerd dat kan worden gewijzigd en gebruikt om aangepaste omgevings configuraties te maken met de cli. Het object op het hoogste niveau is in de python-SDK los toegewezen aan de [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) klasse. 

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

De volgende tabel bevat informatie over elk veld op het hoogste niveau in het JSON-bestand, het type en een beschrijving. Als een object type is gekoppeld aan een klasse uit de python-SDK, is er een losse 1:1 overeenkomst tussen elk JSON-veld en de naam van de open bare variabele in de python-klasse. In sommige gevallen kan het veld worden toegewezen aan een constructor-argument in plaats van een klassen variabele. Het veld wordt bijvoorbeeld `environmentVariables` toegewezen aan de `environment_variables` variabele in de- [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) klasse.

| JSON-veld | Type | Beschrijving |
|---|---|---|
| `name` | `string` | De naam van de omgeving. Start de naam niet met **micro soft** of **AzureML**. |
| `version` | `string` | De versie van de omgeving. |
| `environmentVariables` | `{string: string}` | Een hash-toewijzing van namen en waarden van omgevings variabelen. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Object waarmee de python-omgeving en-interpreter worden gedefinieerd die moeten worden gebruikt voor de doel Compute-resource. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definieert instellingen voor het aanpassen van de docker-installatie kopie die is gebaseerd op de specificaties van de omgeving. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | In het gedeelte worden Spark-instellingen geconfigureerd. Het wordt alleen gebruikt wanneer Framework is ingesteld op PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Hiermee configureert u Databricks-bibliotheek afhankelijkheden. |
| `inferencingStackVersion` | `string` | Hiermee geeft u de versie van de stack voor het in de wachtrij plaatsen toevoegen aan de installatie kopie. Als u wilt voor komen dat een inleidende stack wordt toegevoegd, verlaat u dit veld `null` . Geldige waarde: ' meest recent '. |

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

De volgende opdrachten laten zien hoe u een getraind model kunt registreren en vervolgens als een productie service kunt implementeren:

+ Een model registreren bij Azure Machine Learning:

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

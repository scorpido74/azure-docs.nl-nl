---
title: Machine learning CLI-extensie
titleSuffix: Azure Machine Learning service
description: Meer informatie over de Azure Machine Learning CLI-extensie voor de Azure CLI. De Azure-CLI is een opdrachtregelprogramma voor meerdere platformen die u kunt werken met resources in de Azure-cloud. De Machine Learning-extensie kunt u werken met de Azure Machine Learning-Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 6c5068512f8ba26f7710bca7c0fccb98e0a5be33
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996752"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Gebruik de CLI-extensie voor Azure Machine Learning-service

De CLI van Azure Machine Learning is een uitbreiding van de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), een platformoverschrijdende opdrachtregelinterface voor het Azure-platform. Deze uitbrei ding bevat opdrachten voor het werken met de Azure Machine Learning-service. Zo kunt u uw machine learning activiteiten automatiseren. De volgende lijst bevat enkele voor beelden van acties die u kunt uitvoeren met de CLI-extensie:

+ Uitvoeren van experimenten voor het maken van machine learning-modellen

+ Machine learning-modellen voor het gebruik van de klant registreren

+ Verpakken, implementeren en bijhouden van de levenscyclus van machine learning-modellen

De CLI is geen vervanging voor de SDK van Azure Machine Learning. Het is een aanvullend hulp programma dat is geoptimaliseerd voor het verwerken van zeer geparametriseerde taken die goed zijn afgestemd op automatisering.

## <a name="prerequisites"></a>Vereisten

* Voor het gebruik van de CLI, moet u een Azure-abonnement hebben. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer nog vandaag de [gratis of betaalde versie van de Azure Machine Learning Service](https://aka.ms/AMLFree).

* De [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Volledige referentie documenten

Zoek de [volledige referentie documenten voor de extensie Azure-cli-ml van Azure cli](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>De extensie installeren

Gebruik de volgende opdracht voor het installeren van de Machine Learning CLI-extensie:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Voorbeeld bestanden die u kunt gebruiken met de onderstaande opdrachten, vindt u [hier](https://aka.ms/azml-deploy-cloud).

Wanneer u hierom wordt gevraagd, selecteert u `y` voor het installeren van de extensie.

Om te controleren of de extensie is geïnstalleerd, gebruikt u de volgende opdracht uit om een lijst met ML-specifieke subopdrachten weer te geven:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>De extensie bijwerken

Als u de Machine Learning CLI-extensie wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Verwijder de extensie

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

+ Maak een Azure Machine Learning-service-werkruimte:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Zie [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)(Engelstalig) voor meer informatie.

+ Voeg een werkruimte configuratie toe aan een map om CLI-contextuele bewustzijn in te scha kelen.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze opdracht maakt `.azureml` u een submap die voor beelden van runconfig-en Conda-omgevings bestanden bevat. Het bevat ook een `config.json` bestand dat wordt gebruikt om te communiceren met uw Azure machine learning-werk ruimte.

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

* Start een uitvoering van uw experiment. Wanneer u deze opdracht gebruikt, geeft u de naam op van het runconfig-bestand \*(de tekst voor. runconfig als u uw bestands systeem bekijkt) op basis van de para meter-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Met `az ml folder attach` de opdracht wordt `.azureml` een submap gemaakt die twee voor beelden van runconfig-bestanden bevat. 
    >
    > Als u een python-script hebt waarmee een configuratie object voor een uitvoering programmatisch wordt gemaakt, kunt u [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als een RunConfig-bestand.
    >
    > Zie [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)voor meer voor beelden van runconfig-bestanden.

    Zie [AZ ml run-script uitvoeren](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

* Een lijst met experimenten weer geven:

    ```azurecli-interactive
    az ml experiment list
    ```

    Zie voor meer informatie [AZ ml experimenten List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="environment-management"></a>Omgevings beheer

De volgende opdrachten laten zien hoe u Azure Machine Learning-service [omgevingen](how-to-configure-environment.md) maakt, registreert en vermeldt voor uw werk ruimte:

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

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Configuratie schema implementatie

### <a name="local-deployment-configuration-schema"></a>Configuratie schema voor lokale implementatie

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Configuratie schema voor implementatie van Azure container instance 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Configuratie schema voor implementatie van Azure Kubernetes service

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Volgende stappen

* [Opdracht verwijzing voor de machine learning cli-extensie](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [machine learning modellen trainen en implementeren met behulp van Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning)

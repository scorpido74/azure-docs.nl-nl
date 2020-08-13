---
title: Hoe en waar modellen moeten worden geïmplementeerd
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe en waar u uw Azure Machine Learning-modellen kunt implementeren, waaronder Azure Container Instances, de Azure Kubernetes-service, Azure IoT Edge en Programmeer bare poort matrices voor velden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 67a66ae94508a8d7f54d6112de95fa65a8fd5f09
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185414"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellen implementeren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van uw machine learning model als een webservice in de Azure-Cloud of het Azure IoT Edge van apparaten.

De werkstroom is vrijwel altijd hetzelfde, ongeacht waar u het model implementeert:

1. Registreer het model.
1. Een configuratie voor een afleiding voorbereiden
1. Een invoer script voorbereiden (tenzij u [geen code-implementatie](how-to-deploy-no-code-deployment.md)gebruikt)
1. Implementeer het model op het rekendoel.
1. Test het geïmplementeerde model, ook wel webservice genoemd.

Zie [modellen beheren, implementeren en bewaken met Azure machine learning](concept-model-management-and-deployment.md)voor meer informatie over de concepten die zijn betrokken bij de implementatie werk stroom.

## <a name="prerequisites"></a>Vereisten

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](https://aka.ms/azml-deploy-cloud)gebruiken.
- De [Azure-extensie (opdracht regel Interface) voor de machine learning-service](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](https://aka.ms/azml-deploy-cloud)gebruiken.
- De [Azure machine learning-Software Development Kit (SDK) voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Volg de instructies in de Azure CLI-documentatie voor [het instellen van uw abonnements context](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Ga vervolgens als volgt te werk:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

Als u de werk ruimten wilt zien waartoe u toegang hebt.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Zie de documentatie van [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) voor meer informatie over het gebruik van de SDK om verbinding te maken met een werk ruimte.


---


## <a name="register-your-model"></a><a id="registermodel"></a>Uw model registreren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werk ruimte. Nadat u de bestanden hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op naar een Cloud locatie (van een trainings uitvoering) of van een lokale map. Dit pad is alleen bedoeld voor het vinden van de bestanden die worden geüpload als onderdeel van het registratie proces. Het hoeft niet overeen te komen met het pad dat wordt gebruikt in het vermeldings script. Zie voor meer informatie [model bestanden zoeken in uw invoer script](how-to-deploy-advanced-entry-script.md#load-registered-models).

Machine learning-modellen worden geregistreerd in uw Azure Machine Learning-werk ruimte. Het model kan afkomstig zijn van Azure Machine Learning of van een andere locatie. Bij het registreren van een model kunt u optioneel meta gegevens over het model opgeven. De `tags` en `properties` woorden lijsten die u toepast op een model registratie kunnen vervolgens worden gebruikt voor het filteren van modellen.

De volgende voor beelden laten zien hoe u een model kunt registreren.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainings uitvoering

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

De `--asset-path` para meter verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `--asset-path` u het pad in naar een map die de bestanden bevat.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `-p` u het pad in naar een map die de bestanden bevat.

`az ml model register`Raadpleeg de [referentie documentatie](/cli/azure/ext/azure-cli-ml/ml/model)voor meer informatie.

# <a name="python"></a>[Python](#tab/python)


Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werk ruimte. Nadat u de bestanden hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op naar een Cloud locatie (van een trainings uitvoering) of van een lokale map. Dit pad is alleen bedoeld voor het vinden van de bestanden die worden geüpload als onderdeel van het registratie proces. Het hoeft niet overeen te komen met het pad dat wordt gebruikt in het vermeldings script. Zie voor meer informatie [model bestanden zoeken in uw invoer script](how-to-deploy-advanced-entry-script.md#load-registered-models).

Machine learning-modellen worden geregistreerd in uw Azure Machine Learning-werk ruimte. Het model kan afkomstig zijn van Azure Machine Learning of van een andere locatie. Bij het registreren van een model kunt u optioneel meta gegevens over het model opgeven. De `tags` en `properties` woorden lijsten die u toepast op een model registratie kunnen vervolgens worden gebruikt voor het filteren van modellen.

De volgende voor beelden laten zien hoe u een model kunt registreren.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainings uitvoering

  Wanneer u de SDK gebruikt voor het trainen van een model, kunt u een [uitvoerings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) object of een [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) -object ontvangen, afhankelijk van hoe u het model hebt getraind. Elk object kan worden gebruikt voor het registreren van een model dat is gemaakt door een experiment.

  + Een model van een `azureml.core.Run` object registreren:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    De `model_path` para meter verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `model_path` u het pad in naar een map die de bestanden bevat. Zie de documentatie [Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) voor meer informatie.

  + Een model van een `azureml.train.automl.run.AutoMLRun` object registreren:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In dit voor beeld `metric` worden de `iteration` para meters en niet opgegeven, zodat de iteratie met de beste primaire metriek wordt geregistreerd. De `model_id` waarde die wordt geretourneerd door de uitvoering, wordt gebruikt in plaats van een model naam.

    Zie de documentatie van [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) voor meer informatie.


### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

U kunt een model registreren door het lokale pad van het model op te geven. U kunt het pad naar een map of één bestand opgeven. U kunt deze methode gebruiken om modellen te registreren die zijn getraind met Azure Machine Learning en vervolgens te downloaden. U kunt deze methode ook gebruiken voor het registreren van modellen die buiten Azure Machine Learning worden getraind.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **De SDK en ONNX gebruiken**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `model_path` u het pad in naar een map die de bestanden bevat.

Zie de documentatie voor de [model klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)voor meer informatie.

Zie [een bestaand model implementeren](how-to-deploy-existing-model.md)voor meer informatie over het werken met modellen die buiten Azure machine learning zijn getraind.

---

## <a name="define-an-entry-script"></a>Een invoer script definiëren

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Een configuratie voor het afstellen van een interferentie definiëren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

De volgende opdracht laat zien hoe u een model implementeert met behulp van de CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In dit voor beeld geeft de configuratie de volgende instellingen aan:

* Dat het model python vereist
* Het [invoer script](#define-an-entry-script), dat wordt gebruikt voor het afhandelen van webaanvragen die worden verzonden naar de geïmplementeerde service
* Het Conda-bestand met een beschrijving van de Python-pakketten die nodig zijn voor de

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met een afnemende configuratie.

# <a name="python"></a>[Python](#tab/python)

Een configuratie voor het afwijzen van een interferentie beschrijft het instellen van de webservice die uw model bevat. Het wordt later gebruikt wanneer u het model implementeert.

In de configuratie voor demijnen wordt Azure Machine Learning omgevingen gebruikt voor het definiëren van de software afhankelijkheden die nodig zijn voor uw implementatie. Met omgevingen kunt u de software-afhankelijkheden maken, beheren en opnieuw gebruiken die zijn vereist voor training en implementatie. U kunt een omgeving maken op basis van aangepaste afhankelijkheids bestanden of een van de met Azure Machine Learning omgevingen met een van de curator gebruiken. De volgende YAML is een voor beeld van een Conda-afhankelijkheids bestand voor ingrijpen. Houd er rekening mee dat u de standaard waarden van azureml-defaults met versie >= 1.0.45 als een PIP-afhankelijkheid moet aangeven, omdat deze de functionaliteit bevat die nodig is om het model als een webservice te hosten. Als u het automatisch genereren van schema's wilt gebruiken, moet uw invoer script ook de `inference-schema` pakketten importeren.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Als uw afhankelijkheid beschikbaar is via zowel Conda als PIP (van PyPi), raadt micro soft u aan om de Conda-versie te gebruiken, aangezien Conda-pakketten doorgaans worden geleverd met vooraf gemaakte binaire bestanden die de installatie betrouwbaarder maken.
>
> Zie [Wat is Conda en PIP](https://www.anaconda.com/understanding-conda-and-pip/)? voor meer informatie.
>
> Als u wilt controleren of uw afhankelijkheid beschikbaar is via Conda, gebruikt u de `conda search <package-name>` opdracht of gebruikt u de pakket indexen op [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) en [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

U kunt het afhankelijkheden bestand gebruiken om een omgevings object te maken en dit op te slaan in uw werk ruimte voor toekomstig gebruik:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Zie voor een gedetailleerde bespreking van het gebruik en aanpassen van python-omgevingen met Azure Machine Learning [& software omgevingen maken in azure machine learning](how-to-use-environments.md).

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met een afnemende configuratie.


In het volgende voor beeld ziet u hoe u een omgeving laadt vanuit uw werk ruimte en deze vervolgens gebruikt met de configuratie voor inschakeling:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over omgevingen.

Zie de documentatie van [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) voor meer informatie over de configuratie van de afleiding.

---

## <a name="choose-a-compute-target"></a>Een reken doel kiezen

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Een implementatie configuratie definiëren

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

De beschik bare opties voor een implementatie configuratie zijn afhankelijk van het reken doel dat u kiest.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Zie voor meer informatie de documentatie van [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="python"></a>[Python](#tab/python)

Voordat u uw model implementeert, moet u de implementatie configuratie definiëren. *De implementatie configuratie is specifiek voor het reken doel dat als host fungeert voor de webservice.* Wanneer u bijvoorbeeld een model lokaal implementeert, moet u de poort opgeven waar de service aanvragen accepteert. De implementatie configuratie maakt geen deel uit van het invoer script. Het wordt gebruikt voor het definiëren van de kenmerken van het Compute-doel die als host dienen voor het model en het script.

Mogelijk moet u ook de reken resource maken, als u bijvoorbeeld nog geen AKS-instantie (Azure Kubernetes service) hebt die aan uw werk ruimte is gekoppeld.

De volgende tabel bevat een voor beeld van het maken van een implementatie configuratie voor elk reken doel:

| Rekendoel | Implementatie configuratie-voor beeld |
| ----- | ----- |
| Lokaal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

De klassen voor lokale, Azure Container Instances-en AKS-webservices kunnen worden geïmporteerd uit `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---



## <a name="deploy-your-model"></a>Uw model implementeren

U bent nu klaar om uw model te implementeren. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Een geregistreerd model gebruiken

Als u het model in uw Azure Machine Learning-werk ruimte hebt geregistreerd, vervangt u ' mymodel: 1 ' door de naam van uw model en het versie nummer.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Een lokaal model gebruiken

Als u uw model liever niet wilt registreren, kunt u de para meter ' Source Directory ' in uw inferenceconfig.jsdoor geven aan om een lokale map op te geven van waaruit uw model moet worden gebruikt.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

In het onderstaande voor beeld wordt een lokale implementatie gedemonstreerd. De syntaxis is afhankelijk van het berekenings doel dat u in de vorige stap hebt gekozen.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de documentatie voor [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)en [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)voor meer informatie.

---

### <a name="understanding-service-state"></a>Informatie over de service status

Tijdens de implementatie van het model ziet u mogelijk de wijziging van de service status terwijl deze volledig wordt geïmplementeerd.

In de volgende tabel worden de verschillende service statussen beschreven:

| Status van webservice | Beschrijving | Eind status?
| ----- | ----- | ----- |
| Overstappen | De service is in het implementatie proces. | Nee |
| Niet in orde | De service is geïmplementeerd, maar is momenteel niet bereikbaar.  | Nee |
| Unschedulable | De service kan op dit moment niet worden geïmplementeerd vanwege een gebrek aan resources. | Nee |
| Mislukt | De implementatie van de service is mislukt vanwege een fout of een crash. | Ja |
| In orde | De service is in orde en het eind punt is beschikbaar. | Ja |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Batch-deinterferentie
Azure Machine Learning Compute-doelen worden gemaakt en beheerd door Azure Machine Learning. Ze kunnen worden gebruikt voor batch voorspelling van Azure Machine Learning pijp lijnen.

Zie [batch voorspellingen uitvoeren](tutorial-pipeline-batch-scoring-classification.md)voor een overzicht van batch deinterferentie met Azure machine learning compute.

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge afleiding
Ondersteuning voor het implementeren naar de rand is in preview. Zie [Deploy Azure machine learning als een IOT Edge-module](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)voor meer informatie.

## <a name="delete-resources"></a>Resources verwijderen

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `az ml service <name of webservice>` .

Als u een geregistreerd model uit uw werk ruimte wilt verwijderen, gebruikt u`az ml model delete <model id>`

Meer informatie over [het verwijderen van een webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) en [het verwijderen van een model](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `service.delete()` .
Als u een geregistreerd model wilt verwijderen, gebruikt u `model.delete()` .

Zie de documentatie voor [webservice. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) en [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)voor meer informatie.

---


## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)


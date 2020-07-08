---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e6a7ee8ed497c87c08f13e09a0022b0835c952cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82975053"
---
Als u een webservice wilt bijwerken, gebruikt u de- `update` methode. U kunt de webservice bijwerken voor het gebruik van een nieuw model, een nieuw invoer script of nieuwe afhankelijkheden die kunnen worden opgegeven in een Afleidings configuratie. Zie de documentatie voor [webservice-update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)voor meer informatie.

> [!IMPORTANT]
> Wanneer u een nieuwe versie van een model maakt, moet u elke service die u wilt gebruiken hand matig bijwerken.
>
> U kunt de SDK niet gebruiken om een webservice bij te werken die is gepubliceerd vanuit de Azure Machine Learning Designer.

**De SDK gebruiken**

De volgende code laat zien hoe u met behulp van de SDK het model, de omgeving en het invoer script voor een webservice bijwerkt:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**De CLI gebruiken**

U kunt ook een webservice bijwerken met behulp van de versie van ML CLI. In het volgende voor beeld ziet u hoe u een nieuw model registreert en een webservice bijwerkt om het nieuwe model te gebruiken:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In dit voor beeld wordt een JSON-document gebruikt om de model gegevens van de registratie opdracht door te geven aan de opdracht update.
>
> Als u de service wilt bijwerken om een nieuw invoer script of-omgeving te gebruiken, maakt u een Afleidings [configuratie bestand](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) en geeft u het op met de `ic` para meter.

Zie de documentatie van [AZ ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) voor meer informatie.
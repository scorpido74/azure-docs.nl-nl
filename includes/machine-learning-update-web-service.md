---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: f1eee95cf35b831fc2a213044d700fd5afbdfc96
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997926"
---
Voor het bijwerken van de webservice, gebruikt u de `update` methode. U kunt de webservice bijwerken voor het gebruik van een nieuw model, een invoer script of afhankelijkheden die kunnen worden opgegeven met behulp van een Afleidings configuratie. Zie voor meer informatie de verwijzing voor [webservice. update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Wanneer u een nieuwe versie van een model maakt, moet u elke service die u wilt gebruiken hand matig bijwerken.

**De SDK gebruiken**

De volgende code laat zien hoe u met behulp van de SDK het model, de omgeving en het invoer script voor een webservice bijwerkt:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s)
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**De CLI gebruiken**

U kunt ook een webservice bijwerken met behulp van de versie van ML CLI. In het volgende voor beeld ziet u hoe u een nieuw model registreert en vervolgens de webservice bijwerkt om het nieuwe model te gebruiken:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In dit voor beeld wordt een JSON-document gebruikt om de model gegevens van de registratie opdracht door te geven aan de opdracht update.
>
> Als u de service wilt bijwerken om een nieuw invoer script of-omgeving te gebruiken, maakt u een Afleidings [configuratie bestand](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) en geeft u het op met de `ic` para meter.

Zie voor meer informatie de referentie [AZ ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .
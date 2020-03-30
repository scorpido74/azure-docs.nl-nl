---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477252"
---
Als u een webservice `update` wilt bijwerken, gebruikt u de methode. U de webservice bijwerken om een nieuw model, een nieuw invoerscript of nieuwe afhankelijkheden te gebruiken die kunnen worden opgegeven in een gevolgtrekkingsconfiguratie. Zie de documentatie voor [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)voor meer informatie.

> [!IMPORTANT]
> Wanneer u een nieuwe versie van een model maakt, moet u elke service die u wilt gebruiken handmatig bijwerken.
>
> U de SDK niet gebruiken om een webservice bij te werken die is gepubliceerd vanuit de Azure Machine Learning-ontwerper.

**De SDK gebruiken**

In de volgende code ziet u hoe u de SDK gebruikt om het model, de omgeving en het invoerscript voor een webservice bij te werken:

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

**Met behulp van de CLI**

U een webservice ook bijwerken met de ML CLI. In het volgende voorbeeld wordt het registreren van een nieuw model en het bijwerken van een webservice om het nieuwe model te gebruiken, aangetoond:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> In dit voorbeeld wordt een JSON-document gebruikt om de modelgegevens van de registratieopdracht door te geven aan de opdracht Bijwerken.
>
> Als u de service wilt bijwerken om een nieuw invoerscript of -omgeving `ic` te gebruiken, maakt u een [inferenceconfiguratiebestand](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) en geeft u het op met de parameter.

Zie voor meer informatie de documentatie van de [AZ ML-serviceupdate.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)
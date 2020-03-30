---
title: Modellen implementeren in Azure Container Instances
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen als webservice met Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: cb5603c12f06e4f5d5675ca27e171321ecc8827a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536442"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Een model implementeren in Azure Container Instances
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van Azure Machine Learning om een model te implementeren als webservice op Azure Container Instances (ACI). Azure Container Instances gebruiken als een van de volgende voorwaarden waar is:

- U moet uw model snel implementeren en valideren. U hoeft geen ACI-containers van tevoren te maken. Ze worden gemaakt als onderdeel van het implementatieproces.
- U test een model dat in ontwikkeling is. 

Zie [Quota en regiobeschikbaarheid voor](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) het artikel Azure Container Instances voor informatie over quota en regiobeschikbaarheid voor ACI.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een machine learning-model dat in uw werkruimte is geregistreerd. Als u geen geregistreerd model hebt, raadpleegt u [Hoe en waar u modellen implementeren.](how-to-deploy-and-where.md)

- De [Azure CLI-extensie voor Machine Learning-service](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of de Azure Machine Learning Visual Studio [Code-extensie](tutorial-setup-vscode-extension.md).

- De __Python-codefragmenten__ in dit artikel gaan ervan uit dat de volgende variabelen zijn ingesteld:

    * `ws`- Ingesteld op uw werkruimte.
    * `model`- Stel in op uw geregistreerde model.
    * `inference_config`- Stel in op de conclusieconfiguratie voor het model.

    Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

- De __CLI-fragmenten__ in dit artikel gaan `inferenceconfig.json` ervan uit dat u een document hebt gemaakt. Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van dit document.

## <a name="deploy-to-aci"></a>Implementeren naar ACI

Als u een model wilt implementeren in Azure Container Instances, maakt u een __implementatieconfiguratie__ waarin de benodigde rekenbronnen worden beschreven. Bijvoorbeeld het aantal cores en geheugen. U hebt ook een __gevolgtrekkingsconfiguratie__nodig, waarin de omgeving wordt beschreven die nodig is om het model en de webservice te hosten. Zie Hoe en waar u modellen [kunt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van de conclusieconfiguratie.

### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Met behulp van de CLI

Als u wilt implementeren met de CLI, gebruikt u de volgende opdracht. Vervang `mymodel:1` door de naam en versie van het geregistreerde model. Vervang `myservice` door de naam om deze service te geven:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Zie voor meer informatie de referentie voor het [implementeren van het AZ ML-model.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 

## <a name="using-vs-code"></a>VS Code gebruiken

Zie [uw modellen implementeren met VS-code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> U hoeft geen ACI-container te maken om vooraf te testen. ACI containers worden gemaakt als dat nodig is.

## <a name="update-the-web-service"></a>De webservice bijwerken

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning-webservices beveiligen met SSL](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning-modellen controleren met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)

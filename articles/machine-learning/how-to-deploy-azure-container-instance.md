---
title: Modellen implementeren in Azure Container Instances
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen als een webservice met behulp van Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: d31d6553e4b18cbd99960af582175ab89236fa5b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315266"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Een model implementeren op Azure Container Instances


Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model als een webservice op Azure Container Instances (ACI). Gebruik Azure Container Instances als aan een van de volgende voor waarden wordt voldaan:

- U moet uw model snel implementeren en valideren. U hoeft geen ACI-containers vooraf te maken. Ze worden gemaakt als onderdeel van het implementatie proces.
- U test een model dat wordt ontwikkeld. 

Zie [quota's en regionale Beschik baarheid voor Azure container instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artikel voor meer informatie over de beschik baarheid van quota en REGIO'S voor ACI.

> [!IMPORTANT]
> Het wordt ten zeerste aanbevolen om lokaal fouten op te sporen voordat u de webservice implementeert, voor meer informatie. Raadpleeg [lokaal fouten opsporen](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> U kunt ook verwijzen naar Azure Machine Learning: [Deploy to Local Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local) (Implementeren naar lokale notebook)

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een machine learning model dat in uw werk ruimte is geregistreerd. Als u geen geregistreerd model hebt, raadpleegt u [hoe en hoe u modellen implementeert](how-to-deploy-and-where.md).

- De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

- In de code fragmenten van __python__ in dit artikel wordt ervan uitgegaan dat de volgende variabelen zijn ingesteld:

    * `ws` -Ingesteld op uw werk ruimte.
    * `model` -Ingesteld op uw geregistreerde model.
    * `inference_config` -Stel in op de configuratie voor het afstellen van het model.

    Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

- In het __cli__ -fragment in dit artikel wordt ervan uitgegaan dat u een document hebt gemaakt `inferenceconfig.json` . Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van dit document.

## <a name="deploy-to-aci"></a>Implementeren naar ACI

Als u een model wilt implementeren in Azure Container Instances, maakt u een __implementatie configuratie__ waarin de benodigde reken resources worden beschreven. Bijvoorbeeld het aantal kernen en het geheugen. U hebt ook een Afleidings __configuratie__nodig, waarmee de omgeving wordt beschreven die nodig is voor het hosten van het model en de webservice. Zie [hoe en wanneer u modellen wilt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van de configuratie voor demijnen.

> [!NOTE]
> * ACI is alleen geschikt voor kleine modellen met een grootte van 1 GB. 
> * We raden u aan om één knoop punt AKS te gebruiken voor ontwikkel-en test grotere modellen.
> * Het aantal modellen dat moet worden geïmplementeerd, is beperkt tot 1.000 modellen per implementatie (per container). 

### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model. implementeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice-wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truewait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>De CLI gebruiken

Als u wilt implementeren met behulp van de CLI, gebruikt u de volgende opdracht. Vervang door `mymodel:1` de naam en versie van het geregistreerde model. Vervang door `myservice` de naam om deze service te verlenen:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Zie voor meer informatie de referentie [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>VS Code gebruiken

Zie [uw modellen implementeren met VS code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> U hoeft geen ACI-container te maken om vooraf te testen. ACI-containers worden zo nodig gemaakt.


## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [De webservice bijwerken](how-to-deploy-update-web-service.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)

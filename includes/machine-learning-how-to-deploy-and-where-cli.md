---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542770"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](../articles/machine-learning/how-to-manage-workspace.md)voor meer informatie.
- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](https://aka.ms/azml-deploy-cloud)gebruiken.
- De [Azure-extensie (opdracht regel Interface) voor de machine learning-service](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

Volg de instructies in de Azure CLI-documentatie voor [het instellen van uw abonnements context](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Ga vervolgens als volgt te werk:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

Als u de werk ruimten wilt zien waartoe u toegang hebt.

## <a name="register-your-model"></a><a id="registermodel"></a>Uw model registreren

Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werk ruimte. Nadat u de bestanden hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op naar een Cloud locatie (van een trainings uitvoering) of van een lokale map. Dit pad is alleen bedoeld voor het vinden van de bestanden die worden geüpload als onderdeel van het registratie proces. Het hoeft niet overeen te komen met het pad dat wordt gebruikt in het vermeldings script. Zie voor meer informatie [model bestanden zoeken in uw invoer script](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Machine learning-modellen worden geregistreerd in uw Azure Machine Learning-werk ruimte. Het model kan afkomstig zijn van Azure Machine Learning of van een andere locatie. Bij het registreren van een model kunt u optioneel meta gegevens over het model opgeven. De `tags` en `properties` woorden lijsten die u toepast op een model registratie kunnen vervolgens worden gebruikt voor het filteren van modellen.

De volgende voor beelden laten zien hoe u een model kunt registreren.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Een model registreren vanuit een Azure ML-trainings uitvoering

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

De `--asset-path` para meter verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `--asset-path` u het pad in naar een map die de bestanden bevat.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt `-p` u het pad in naar een map die de bestanden bevat.

`az ml model register`Raadpleeg de [referentie documentatie](/cli/azure/ext/azure-cli-ml/ml/model)voor meer informatie.

## <a name="define-an-entry-script"></a>Een invoer script definiëren

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Een configuratie voor het afstellen van een interferentie definiëren

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

De volgende opdracht laat zien hoe u een model implementeert met behulp van de CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In dit voor beeld geeft de configuratie de volgende instellingen aan:

* Dat het model python vereist
* Het [invoer script](#define-an-entry-script), dat wordt gebruikt voor het afhandelen van webaanvragen die worden verzonden naar de geïmplementeerde service
* Het Conda-bestand met een beschrijving van de Python-pakketten die nodig zijn voor de

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](../articles/machine-learning/how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met een afnemende configuratie.

## <a name="choose-a-compute-target"></a>Een reken doel kiezen

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Een implementatie configuratie definiëren

De beschik bare opties voor een implementatie configuratie zijn afhankelijk van het reken doel dat u kiest.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Zie voor meer informatie de documentatie van [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Uw model implementeren

U bent nu klaar om uw model te implementeren.

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
## <a name="delete-resources"></a>Resources verwijderen

Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `az ml service <name of webservice>` .

Als u een geregistreerd model uit uw werk ruimte wilt verwijderen, gebruikt u`az ml model delete <model id>`

Meer informatie over [het verwijderen van een webservice](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) en [het verwijderen van een model](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)
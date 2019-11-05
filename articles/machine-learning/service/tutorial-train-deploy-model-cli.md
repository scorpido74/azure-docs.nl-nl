---
title: Modellen trainen en implementeren vanuit de CLI
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de machine learning extensie voor Azure CLI om een model te trainen, te registreren en te implementeren vanaf de opdracht regel.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 3f619caf7e2713e1c9251550b06c8bdefba5936f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493397"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Zelf studie: een model trainen en implementeren vanuit de CLI
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelf studie gebruikt u de extensie machine learning voor Azure CLI voor het trainen, registreren en implementeren van een model.

De python-trainings scripts in deze zelf studie gebruiken [scikit-leren](https://scikit-learn.org/) om een basis model te trainen. De focus van deze zelf studie bevindt zich niet in de scripts of het model, maar het proces van het gebruik van de CLI om met Azure Machine Learning te werken.

U leert hoe u de volgende acties uitvoert:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werkruimte maken
> * De reken resource maken die wordt gebruikt om het model te trainen
> * Een trainings uitvoering starten
> * Een model registreren en downloaden
> * Het model als een webservice implementeren
> * Score gegevens met behulp van de webservice

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Als u de CLI-opdrachten in dit document vanuit uw **lokale omgeving**wilt gebruiken, hebt u de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

    Als u de [Azure Cloud shell](https://azure.microsoft.com//features/cloud-shell/)gebruikt, wordt de CLI geopend via de browser en in de Cloud.

## <a name="download-the-example-project"></a>Down load het voorbeeld project

Voor deze zelf studie downloadt u het [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) -project. De bestanden in de mappen `model-training` en `model-deployment` worden gebruikt door de stappen in deze zelf studie.

Als u een lokale kopie van de bestanden wilt ophalen, kunt u [een zip-archief downloaden](https://github.com/microsoft/MLOps/archive/master.zip)of de volgende Git-opdracht gebruiken om de opslag plaats te klonen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Trainings bestanden

De `model-training` Directory bevat de volgende bestanden die worden gebruikt bij het trainen van een model:

* `.azureml\sklearn.runconfig`: een __configuratie__ bestand voor de uitvoering. Dit bestand definieert de runtime-omgeving die nodig is om het model te trainen.
* `train-sklearn.py`: het trainings script. Dit bestand wordt het model treinen.
* `mylib.py`: een helper-module die wordt gebruikt door `train-sklearn.py`.
* `training-env.yml`: definieert de software-afhankelijkheden die nodig zijn om het trainings script uit te voeren.

Het trainings script maakt gebruik van de diabetes-gegevensset die is meegeleverd met scikit-Leer hoe een model kan worden getraind.

### <a name="deployment-files"></a>Implementatie bestanden

De `model-deployment` Directory bevat de volgende bestanden, die worden gebruikt voor het implementeren van het getrainde model als een webservice:

* `aciDeploymentConfig.yml`: een __configuratie__ bestand voor de implementatie. Dit bestand definieert de hosting omgeving die nodig is voor het model.
* `inferenceConfig.yml`: een afleiding configuration__-bestand. Dit bestand definieert de software omgeving die door de service wordt gebruikt om gegevens met het model te scoren.
* `score.py`: een python-script dat binnenkomende gegevens accepteert, het het model verlaagt en vervolgens een antwoord retourneert.
* `scoring-env.yml`: de Conda-afhankelijkheden die nodig zijn om het model en `score.py` script uit te voeren.

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

Er zijn verschillende manieren waarop u kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De meeste basis is om interactief te verifiëren met behulp van een browser. Als u interactief wilt verifiëren, opent u een opdracht regel of Terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdracht regel volgen. De instructies moeten bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en een autorisatie code invoeren.

## <a name="install-the-machine-learning-extension"></a>De machine learning-extensie installeren

Als u de extensie machine learning wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Als er een bericht wordt weer gegeven dat de extensie al is geïnstalleerd, gebruikt u de volgende opdracht om naar de nieuwste versie bij te werken:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resource groep is een basis container van resources op het Azure-platform. Wanneer u met de Azure Machine Learning werkt, bevat de resource groep uw Azure Machine Learning-werk ruimte. Het bevat ook andere Azure-Services die worden gebruikt door de werk ruimte. Als u bijvoorbeeld uw model traint met behulp van een Cloud Compute-resource, wordt die resource in de resource groep gemaakt.

Gebruik de volgende opdracht om __een nieuwe resource groep te maken__. Vervang `<resource-group-name>` door de naam die u voor deze resource groep wilt gebruiken. Vervang `<location>` door de Azure-regio die u voor deze resource groep wilt gebruiken:

> [!TIP]
> U moet een regio selecteren waar de Azure Machine Learning beschikbaar is. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)voor meer informatie.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Het antwoord van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Zie [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)(Engelstalig) voor meer informatie over het werken met resource groepen.

## <a name="create-a-workspace"></a>Een werkruimte maken

Gebruik de volgende opdracht om een nieuwe werk ruimte te maken. Vervang `<workspace-name>` door de naam die u voor deze werk ruimte wilt gebruiken. Vervang `<resource-group-name>` door de naam van de resource groep:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Lokaal project verbinden met de werk ruimte

Gebruik in een Terminal-of opdracht prompt de volgende opdrachten om mappen te wijzigen in de map `mlops` en vervolgens verbinding te maken met uw werk ruimte:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Met deze opdracht maakt u een `.azureml/config.json` bestand, dat informatie bevat die nodig is om verbinding te maken met uw werk ruimte. De rest van de `az ml`-opdrachten die in deze zelf studie worden gebruikt, gebruiken dit bestand, dus u hoeft de werk ruimte en resource groep niet aan alle opdrachten toe te voegen.

## <a name="create-the-compute-target-for-training"></a>Het reken doel voor de training maken

In dit voor beeld wordt een Azure Machine Learning Compute-cluster gebruikt om het model te trainen. Als u een nieuw reken cluster wilt maken, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Met deze opdracht wordt een nieuw reken doel gemaakt met de naam `cpu`, met een maximum van vier knoop punten. De geselecteerde VM-grootte biedt een virtuele machine met een GPU-resource. Zie [VM-typen en-groottes] voor meer informatie over de grootte van de virtuele machine.

> [!IMPORTANT]
> De naam van het berekenings doel (`cpu` in dit geval) is belang rijk. ernaar wordt verwezen door het `.azureml/sklearn.runconfig`-bestand dat in de volgende sectie wordt gebruikt.

## <a name="submit-the-training-run"></a>De trainings uitvoering verzenden

Als u een training wilt starten op het `cpu` Compute-doel, wijzigt u de directory's in de `model-training` Directory en gebruikt u vervolgens de volgende opdracht:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Met deze opdracht geeft u een naam op voor het experiment (`myexperiment`). In het experiment wordt informatie over deze uitvoering opgeslagen in de werk ruimte.

Met de para meter `-c sklearn` wordt het `.azureml/sklearn.runconfig` bestand opgegeven. Zoals eerder vermeld, bevat dit bestand informatie die wordt gebruikt voor het configureren van de omgeving die wordt gebruikt voor de uitvoering van de training. Als u dit bestand inspecteert, ziet u dat het verwijst naar het `cpu` Compute-doel dat u eerder hebt gemaakt. U ziet ook het aantal knoop punten dat moet worden gebruikt bij het trainen (`"nodeCount": "4"`) en bevat een `"condaDependenciees"` sectie met een lijst met de Python-pakketten die nodig zijn om het trainings script uit te voeren.

Zie [Compute-doelen voor model training instellen en gebruiken](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)voor meer informatie over het uitvoeren van configuratie bestanden. u kunt ook verwijzen naar dit [JSON-bestand](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) om het volledige schema voor een runconfig te bekijken.

De para meter `-t` slaat een verwijzing op naar deze run in een JSON-bestand en wordt gebruikt in de volgende stappen om het model te registreren en te downloaden.

Als proces voor het uitvoeren van training worden gegevens uit de trainings sessie naar de externe Compute-resource gestreamd. Een deel van de informatie is vergelijkbaar met de volgende tekst:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Deze tekst wordt vastgelegd in het trainings script (`train-sklearn.py`) en er worden twee van de prestatie gegevens voor dit model weer gegeven. In dit geval willen we het model met de hoogste alfabetische waarde. De metrische gegevens voor prestaties zijn specifiek voor het model dat u wilt trainen. Andere modellen hebben verschillende prestatie gegevens.

Als u de `train-sklearn.py`inspecteert, ziet u dat ook de waarde alpha wordt gebruikt wanneer de getrainde modellen worden opgeslagen in een bestand. In dit geval wordt een aantal modellen treinen. Het eerste met de hoogste Alfa moet het beste zijn. Op basis van de uitvoer hierboven en de code wordt het model met een alpha van 0,95 opgeslagen als `./outputs/ridge_0.95.pkl`

Het model is opgeslagen in de `./outputs` Directory op het reken doel waar het is getraind. In dit geval wordt het Azure Machine Learning Compute-exemplaar in de Azure-Cloud. Het trainings proces uploadt de inhoud van de `./outputs` Directory automatisch van het berekenings doel waar trainingen plaatsvindt naar uw Azure Machine Learning-werk ruimte. Het wordt opgeslagen als onderdeel van het experiment (`myexperiment` in dit voor beeld).

## <a name="register-the-model"></a>Het model registreren

Als u het model rechtstreeks vanuit de opgeslagen versie in uw experiment wilt registreren, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Met deze opdracht wordt het `outputs/ridge_0.95.pkl` bestand dat door de training wordt gemaakt, geregistreerd als een nieuwe model registratie met de naam `mymodel`. De `--assets-path` verwijst naar een pad in een experiment. In dit geval worden het experiment en de uitvoerings informatie geladen vanuit het `runoutput.json` bestand dat is gemaakt met de opdracht training. De `-t registeredmodel.json` maakt een JSON-bestand dat verwijst naar het nieuwe geregistreerde model dat is gemaakt met deze opdracht, en wordt gebruikt door andere CLI-opdrachten die met geregistreerde modellen werken.

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Model versie beheer

Noteer het versie nummer dat voor het model is geretourneerd. De versie wordt telkens verhoogd wanneer u een nieuw model met deze naam registreert. U kunt het model bijvoorbeeld downloaden en registreren vanuit een lokaal bestand met behulp van de volgende opdrachten:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

Met de eerste opdracht wordt het geregistreerde model gedownload naar de huidige map. De bestands naam is `ridge_0.95.pkl`. Dit is het bestand waarnaar wordt verwezen wanneer u het model registreert. Met de tweede opdracht wordt het lokale model (`-p "ridge_0.95.pkl"`) geregistreerd met dezelfde naam als de vorige registratie (`mymodel`). Deze keer wordt de versie weer gegeven met de JSON-gegevens die worden geretourneerd.

## <a name="deploy-the-model"></a>Het model implementeren

Als u een model wilt implementeren, wijzigt u de mappen in de `model-deployment` Directory en gebruikt u vervolgens de volgende opdracht:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Het bericht ' kan docker-client maken ' kan worden weer gegeven. U kunt dit bericht negeren. De CLI kan een webservice implementeren naar een lokale docker-container en controleren op docker. In dit geval gebruiken we geen lokale implementatie.

Met deze opdracht wordt een nieuwe service met de naam `myservice`geïmplementeerd met versie 1 van het model dat u eerder hebt geregistreerd.

Het `inferenceConfig.yml` bestand bevat informatie over het uitvoeren van een afleiding, zoals het vermeldings script (`score.py`) en de software-afhankelijkheden. Voor meer informatie over de structuur van dit bestand raadpleegt u het schema voor het afzien van de [configuratie](reference-azure-machine-learning-cli.md#inference-configuration-schema). Zie [modellen implementeren met de Azure machine learning](how-to-deploy-and-where.md#prepare-to-deploy)voor meer informatie over invoer scripts.

De `aciDeploymentConfig.yml` beschrijft de implementatie omgeving die wordt gebruikt voor het hosten van de service. De implementatie configuratie is specifiek voor het reken type dat u gebruikt voor de implementatie. In dit geval wordt een exemplaar van Azure container gebruikt. Zie het [implementatie configuratie schema](reference-azure-machine-learning-cli.md#deployment-configuration-schema)voor meer informatie.

Het duurt enkele minuten voordat het implementatie proces is voltooid.

> [!TIP]
> In dit voor beeld wordt Azure Container Instances gebruikt. Implementaties naar ACI maken automatisch de benodigde ACI-resource. Als u in plaats daarvan naar de Azure Kubernetes-service wilt implementeren, moet u een AKS-cluster van tevoren maken en dit opgeven als onderdeel van de `az ml model deploy` opdracht. Voor een voor beeld van de implementatie van naar AKS raadpleegt u [een model implementeren in een Azure Kubernetes service-cluster](how-to-deploy-azure-kubernetes-service.md).

Na enkele minuten wordt informatie die lijkt op de volgende JSON geretourneerd:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>De Score ring-URI

Het `scoringUri` dat door de implementatie wordt geretourneerd, is het REST-eind punt voor een model dat is geïmplementeerd als webservice. U kunt deze URI ook ophalen met behulp van de volgende opdracht:

```azurecli-interactive
az ml service show -n myservice
```

Met deze opdracht wordt hetzelfde JSON-document geretourneerd, met inbegrip van de `scoringUri`.

Het REST-eind punt kan worden gebruikt om gegevens naar de service te verzenden. Zie [een Azure machine learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md) voor meer informatie over het maken van een client toepassing die gegevens naar de service verzendt

### <a name="send-data-to-the-service"></a>Gegevens verzenden naar de service

Hoewel u een client toepassing kunt maken om het eind punt aan te roepen, biedt de machine learning CLI een hulp programma dat kan fungeren als een test-client. Gebruik de volgende opdracht om test gegevens te verzenden naar de service:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

Het antwoord van de opdracht is vergelijkbaar met `[4684.920839774082]`.

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> De resources die u hebt gemaakt kunnen worden gebruikt als de vereisten voor andere Azure Machine Learning-zelfstudies en artikelen met procedures.

### <a name="delete-deployed-service"></a>Geïmplementeerde service verwijderen

Als u van plan bent de Azure Machine Learning-werk ruimte te blijven gebruiken, maar de geïmplementeerde service wilt verwijderen om de kosten te verlagen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml service delete -n myservice
```

Met deze opdracht wordt een JSON-document geretourneerd dat de naam van de verwijderde service bevat. Het kan enkele minuten duren voordat de service is verwijderd.

### <a name="delete-the-training-compute"></a>De trainings Compute verwijderen

Gebruik de volgende opdracht als u van plan bent om de Azure Machine Learning-werk ruimte te blijven gebruiken, maar u het `cpu` Compute-doel dat voor training is gemaakt, wilt verwijderen:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Met deze opdracht wordt een JSON-document geretourneerd dat de ID van het verwijderde Compute-doel bevat. Het kan enkele minuten duren voordat het Compute-doel is verwijderd.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om de resources te gebruiken die u hebt gemaakt, verwijdert u deze zodat u geen extra kosten in rekening brengt.

Gebruik de volgende opdracht om de resource groep en alle Azure-resources die in dit document zijn gemaakt, te verwijderen. Vervang `<resource-group-name>` door de naam van de resource groep die u eerder hebt gemaakt:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Volgende stappen

In deze Azure Machine Learning zelf studie hebt u de machine learning CLI gebruikt voor de volgende taken:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werkruimte maken
> * De reken resource maken die wordt gebruikt om het model te trainen
> * Een trainings uitvoering starten
> * Een model registreren en downloaden
> * Het model als een webservice implementeren
> * Score gegevens met behulp van de webservice

Zie [de CLI-extensie voor Azure machine learning gebruiken](reference-azure-machine-learning-cli.md)voor meer informatie over het gebruik van de cli.

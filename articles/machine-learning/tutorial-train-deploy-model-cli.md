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
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617736"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Zelf studie: een model trainen en implementeren vanuit de CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelf studie gebruikt u de extensie machine learning voor Azure CLI voor het trainen, registreren en implementeren van een model.

De python-trainings scripts in deze zelf studie gebruiken [scikit-leren](https://scikit-learn.org/) om een basis model te trainen. De focus van deze zelf studie bevindt zich niet in de scripts of het model, maar het proces van het gebruik van de CLI om met Azure Machine Learning te werken.

U leert hoe u de volgende acties uitvoert:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werk ruimte maken
> * De reken resource maken die wordt gebruikt om het model te trainen
> * De gegevensset definiëren en registreren die wordt gebruikt om het model te trainen
> * Een trainings uitvoering starten
> * Een model registreren en downloaden
> * Het model als een webservice implementeren
> * Score gegevens met behulp van de webservice

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Als u de CLI-opdrachten in dit document vanuit uw **lokale omgeving**wilt gebruiken, hebt u de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

    Als u de [Azure Cloud shell](https://azure.microsoft.com//features/cloud-shell/)gebruikt, wordt de CLI geopend via de browser en in de Cloud.

## <a name="download-the-example-project"></a>Down load het voorbeeld project

Voor deze zelf studie downloadt u [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) het project. De bestanden in de `examples/cli-train-deploy` map worden gebruikt door de stappen in deze zelf studie.

Als u een lokale kopie van de bestanden wilt ophalen, kunt u [een zip-archief downloaden](https://github.com/microsoft/MLOps/archive/master.zip)of de volgende Git-opdracht gebruiken om de opslag plaats te klonen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Trainings bestanden

De `examples/cli-train-deploy` map van het project bevat de volgende bestanden die worden gebruikt bij het trainen van een model:

* `.azureml\mnist.runconfig`: Een __configuratie__ bestand voor de uitvoering. Dit bestand definieert de runtime-omgeving die nodig is om het model te trainen. In dit voor beeld koppelt het ook de gegevens die worden gebruikt om het model te trainen in de trainings omgeving.
* `scripts\train.py`: Het trainings script. Dit bestand wordt het model treinen.
* `scripts\utils.py`: Een Help-bestand dat wordt gebruikt door het trainings script.
* `.azureml\conda_dependencies.yml`: Hiermee worden de software-afhankelijkheden gedefinieerd die nodig zijn om het trainings script uit te voeren.
* `dataset.json`: De definitie van de gegevensset. Wordt gebruikt om de MNIST-gegevensset te registreren in de Azure Machine Learning-werk ruimte.

### <a name="deployment-files"></a>Implementatie bestanden

De opslag plaats bevat de volgende bestanden, die worden gebruikt voor het implementeren van het getrainde model als een webservice:

* `aciDeploymentConfig.yml`: Een __configuratie__ bestand voor de implementatie. Dit bestand definieert de hosting omgeving die nodig is voor het model.
* `inferenceConfig.json`: Een Afleidings __configuratie__ bestand. Dit bestand definieert de software omgeving die door de service wordt gebruikt om gegevens met het model te scoren.
* `score.py`: Een python-script waarmee inkomende gegevens worden geaccepteerd, wordt gescoord met het model en retourneert een antwoord.
* `scoring-env.yml`: De Conda-afhankelijkheden die nodig zijn om `score.py` het model en script uit te voeren.
* `testdata.json`: Een gegevens bestand dat kan worden gebruikt voor het testen van de geïmplementeerde webservice.

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

Er zijn verschillende manieren waarop u kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De meeste basis is om interactief te verifiëren met behulp van een browser. Als u interactief wilt verifiëren, opent u een opdracht regel of Terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdracht regel volgen. De instructies moeten bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en een autorisatie code invoeren.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

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

Gebruik de volgende opdracht om __een nieuwe resource groep te maken__. Vervang `<resource-group-name>` door de naam die u voor deze resource groep wilt gebruiken. Vervang `<location>` door de Azure-regio die u wilt gebruiken voor deze resource groep:

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

Gebruik bij een Terminal-of opdracht prompt de volgende opdrachten om mappen te wijzigen `cli-train-deploy` in de map en vervolgens verbinding te maken met uw werk ruimte:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Met deze opdracht maakt `.azureml/config.json` u een bestand, dat informatie bevat die nodig is om verbinding te maken met uw werk ruimte. De rest van de `az ml` opdrachten die in deze zelf studie worden gebruikt, gebruiken dit bestand, dus u hoeft de werk ruimte en resource groep niet aan alle opdrachten toe te voegen.

## <a name="create-the-compute-target-for-training"></a>Het reken doel voor de training maken

In dit voor beeld wordt een Azure Machine Learning Compute-cluster gebruikt om het model te trainen. Als u een nieuw reken cluster wilt maken, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Met deze opdracht maakt u een nieuw reken `cpu-cluster`doel met de naam, met een maximum van vier knoop punten. De geselecteerde VM-grootte biedt een virtuele machine met een GPU-resource. Zie [VM-typen en-groottes] voor meer informatie over de grootte van de virtuele machine.

> [!IMPORTANT]
> De naam van het COMPUTE-`cpu-cluster` doel (in dit geval) is belang rijk. ernaar wordt verwezen door het `.azureml/mnist.runconfig` bestand dat in de volgende sectie wordt gebruikt.

## <a name="define-the-dataset"></a>De gegevensset definiëren

Voor het trainen van een model kunt u de trainings gegevens opgeven met behulp van een gegevensset. Als u een gegevensset wilt maken op basis van de CLI, moet u een definitie bestand voor de gegevensset opgeven. Het `dataset.json` bestand dat in de opslag plaats wordt gegeven, maakt een nieuwe gegevensset met behulp van de MNIST-gegevens. De gegevensset die wordt gemaakt, `mnist-dataset`heeft de naam.

Als u de gegevensset wilt registreren `dataset.json` met behulp van het bestand, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Kopieer de waarde van de `id` invoer, zoals deze wordt gebruikt in de volgende sectie.

Als u een uitgebreidere sjabloon voor een gegevensset wilt weer geven, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Verwijzen naar de gegevensset

Als u de gegevensset beschikbaar wilt maken in de trainings omgeving, moet u ernaar verwijzen vanuit het runconfig-bestand. Het `.azureml/mnist.runconfig` bestand bevat de volgende YAML-vermeldingen:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Wijzig de waarde van het `id` item zodat deze overeenkomt met de waarde die is geretourneerd tijdens het registreren van de gegevensset. Deze waarde wordt gebruikt voor het laden van de gegevens in het reken doel tijdens de training.

Deze YAML resulteert in de volgende acties tijdens de training:

* Koppelt de gegevensset (op basis van de ID van de gegevensset) in de trainings omgeving en slaat het pad op naar het koppel punt in `mnist` de omgevings variabele.
* Hiermee wordt de locatie van de gegevens (koppel punt) in de trainings omgeving door gegeven aan het `--data-folder` script met behulp van het argument.

Het runconfig-bestand bevat ook informatie over het configureren van de omgeving die wordt gebruikt voor de uitvoering van de training. Als u dit bestand inspecteert, ziet u dat het verwijst naar `cpu-compute` het berekenings doel dat u eerder hebt gemaakt. Het bevat ook een lijst met het aantal knoop punten dat moet`"nodeCount": "4"`worden gebruikt bij het trainen `"condaDependencies"` () en een sectie met een lijst met de Python-pakketten die nodig zijn om het trainings script uit te voeren.

> [!TIP]
> Hoewel het mogelijk is om hand matig een runconfig-bestand te maken, is de oplossing in dit voor `generate-runconfig.py` beeld gemaakt met behulp van het bestand dat is opgenomen in de opslag plaats. Met dit bestand wordt een verwijzing naar de geregistreerde gegevensset opgehaald, wordt een uitvoeren van de configuratie programmatisch gemaakt en vervolgens opgeslagen in een bestand.

Zie [Compute-doelen voor model training instellen en gebruiken](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)voor meer informatie over het uitvoeren van configuratie bestanden. Zie [runconfigschema. json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)voor een volledige JSON-verwijzing.

## <a name="submit-the-training-run"></a>De trainings uitvoering verzenden

Gebruik de volgende opdracht om een training `cpu-cluster` te starten die wordt uitgevoerd op het reken doel:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Met deze opdracht geeft u een naam op voor`myexperiment`het experiment (). In het experiment wordt informatie over deze uitvoering opgeslagen in de werk ruimte.

Met `-c mnist` de para meter `.azureml/mnist.runconfig` wordt het bestand opgegeven.

Met `-t` de para meter wordt een verwijzing naar deze uitvoering opgeslagen in een JSON-bestand en wordt gebruikt in de volgende stappen om het model te registreren en te downloaden.

Als proces voor het uitvoeren van training worden gegevens uit de trainings sessie naar de externe Compute-resource gestreamd. Een deel van de informatie is vergelijkbaar met de volgende tekst:

```output
Predict the test set
Accuracy is 0.9185
```

Deze tekst wordt vastgelegd in het trainings script en toont de nauw keurigheid van het model. Andere modellen hebben verschillende prestatie gegevens.

Als u het trainings script inspecteert, ziet u dat het ook de alpha-waarde gebruikt wanneer het getrainde model `outputs/sklearn_mnist_model.pkl`wordt opgeslagen in.

Het model is opgeslagen in de `./outputs` map op het reken doel waar het is getraind. In dit geval wordt het Azure Machine Learning Compute-exemplaar in de Azure-Cloud. Het trainings proces uploadt automatisch de inhoud van de `./outputs` map van het berekenings doel waar training plaatsvindt naar uw Azure machine learning-werk ruimte. Het wordt opgeslagen als onderdeel van het experiment (`myexperiment` in dit voor beeld).

## <a name="register-the-model"></a>Het model registreren

Als u het model rechtstreeks vanuit de opgeslagen versie in uw experiment wilt registreren, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Met deze opdracht wordt `outputs/sklearn_mnist_model.pkl` het bestand dat door de training wordt gemaakt, geregistreerd als een `mymodel`nieuwe model registratie met de naam. De `--assets-path` verwijst naar een pad in een experiment. In dit geval worden het experiment en de uitvoerings informatie geladen vanuit het `runoutput.json` bestand dat wordt gemaakt door de opdracht training. Het `-t registeredmodel.json` maakt een JSON-bestand dat verwijst naar het nieuwe geregistreerde model dat is gemaakt met deze opdracht, en wordt gebruikt door andere CLI-opdrachten die met geregistreerde modellen werken.

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
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Met de eerste opdracht wordt het geregistreerde model gedownload naar de huidige map. De naam van het `sklearn_mnist_model.pkl`bestand is, waarnaar wordt verwezen wanneer u het model hebt geregistreerd. Met de tweede opdracht wordt het lokale model`-p "sklearn_mnist_model.pkl"`() geregistreerd met dezelfde naam als de vorige registratie`mymodel`(). Deze keer wordt de versie weer gegeven met de JSON-gegevens die worden geretourneerd.

## <a name="deploy-the-model"></a>Het model implementeren

Als u een model wilt implementeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Er wordt mogelijk een waarschuwing weer gegeven over het controleren van LocalWebservice of het maken van een docker-client is mislukt. U kunt dit veilig negeren, omdat u geen lokale webservice implementeert.

Met deze opdracht wordt een nieuwe service met `myservice`de naam met versie 1 van het model dat u eerder hebt geregistreerd, geïmplementeerd.

Het `inferenceConfig.yml` bestand bevat informatie over het gebruik van het model voor demijnen. Er wordt bijvoorbeeld verwezen naar het vermelding script (`score.py`) en de software-afhankelijkheden.

Voor meer informatie over de structuur van dit bestand raadpleegt u het schema voor het afzien van de [configuratie](reference-azure-machine-learning-cli.md#inference-configuration-schema). Zie [modellen implementeren met de Azure machine learning](how-to-deploy-and-where.md#prepare-to-deploy)voor meer informatie over invoer scripts.

De `aciDeploymentConfig.yml` beschrijving van de implementatie omgeving die wordt gebruikt voor het hosten van de service. De implementatie configuratie is specifiek voor het reken type dat u gebruikt voor de implementatie. In dit geval wordt een exemplaar van Azure container gebruikt. Zie het [implementatie configuratie schema](reference-azure-machine-learning-cli.md#deployment-configuration-schema)voor meer informatie.

Het duurt enkele minuten voordat het implementatie proces is voltooid.

> [!TIP]
> In dit voor beeld wordt Azure Container Instances gebruikt. Implementaties naar ACI maken automatisch de benodigde ACI-resource. Als u in plaats daarvan naar de Azure Kubernetes-service wilt implementeren, moet u een AKS-cluster van tevoren maken en dit opgeven als `az ml model deploy` onderdeel van de opdracht. Voor een voor beeld van de implementatie van naar AKS raadpleegt u [een model implementeren in een Azure Kubernetes service-cluster](how-to-deploy-azure-kubernetes-service.md).

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

Het `scoringUri` resultaat van de implementatie is het rest-eind punt voor een model dat is geïmplementeerd als webservice. U kunt deze URI ook ophalen met behulp van de volgende opdracht:

```azurecli-interactive
az ml service show -n myservice
```

Met deze opdracht wordt hetzelfde JSON-document geretourneerd, `scoringUri`met inbegrip van de.

Het REST-eind punt kan worden gebruikt om gegevens naar de service te verzenden. Zie [een Azure machine learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md) voor meer informatie over het maken van een client toepassing die gegevens naar de service verzendt

### <a name="send-data-to-the-service"></a>Gegevens verzenden naar de service

Hoewel u een client toepassing kunt maken om het eind punt aan te roepen, biedt de machine learning CLI een hulp programma dat kan fungeren als een test-client. Gebruik de volgende opdracht om gegevens in het `testdata.json` bestand naar de service te verzenden:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Als u Power shell gebruikt, gebruikt u in plaats daarvan de volgende opdracht:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Het antwoord van de opdracht is vergelijkbaar met `[ 3 ]`.

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

Als u van plan bent om de Azure Machine Learning-werk ruimte te blijven gebruiken, maar u het `cpu-cluster` berekenings doel voor de training wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
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
> * Een Azure Machine Learning-werk ruimte maken
> * De reken resource maken die wordt gebruikt om het model te trainen
> * De gegevensset definiëren en registreren die wordt gebruikt om het model te trainen
> * Een trainings uitvoering starten
> * Een model registreren en downloaden
> * Het model als een webservice implementeren
> * Score gegevens met behulp van de webservice

Zie [de CLI-extensie voor Azure machine learning gebruiken](reference-azure-machine-learning-cli.md)voor meer informatie over het gebruik van de cli.

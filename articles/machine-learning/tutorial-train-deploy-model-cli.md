---
title: Modellen uit de CLI trainen en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de machine learning-extensie voor Azure CLI om een model te trainen, registreren en implementeren vanaf de opdrachtregel.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617736"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Zelfstudie: Een model trainen en implementeren vanuit de CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie gebruikt u de machine learning-extensie voor de Azure CLI om een model te trainen, registreren en implementeren.

De Python-trainingsscripts in deze zelfstudie gebruiken [scikit-learn](https://scikit-learn.org/) om een basismodel te trainen. De focus van deze zelfstudie ligt niet op de scripts of het model, maar op het proces van het gebruik van de CLI om te werken met Azure Machine Learning.

U leert hoe u de volgende acties uitvoert:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werkruimte maken
> * De rekenbron maken die wordt gebruikt om het model te trainen
> * De gegevensset definiëren en registreren die wordt gebruikt om het model te trainen
> * Een trainingsrun starten
> * Een model registreren en downloaden
> * Het model implementeren als webservice
> * Gegevens scoren met de webservice

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Als u de CLI-opdrachten in dit document vanuit uw **lokale omgeving**wilt gebruiken, hebt u de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

    Als u de [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)gebruikt, wordt de CLI geopend via de browser en leeft deze in de cloud.

## <a name="download-the-example-project"></a>Het voorbeeldproject downloaden

Download het [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) project voor deze zelfstudie. De bestanden `examples/cli-train-deploy` in de map worden gebruikt door de stappen in deze zelfstudie.

Download een [.zip-archief](https://github.com/microsoft/MLOps/archive/master.zip)of gebruik de volgende Opdracht Git om de opslagplaats te klonen om een lokale kopie van de bestanden te krijgen:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Trainingsbestanden

De `examples/cli-train-deploy` map van het project bevat de volgende bestanden, die worden gebruikt bij het trainen van een model:

* `.azureml\mnist.runconfig`: Een __uitgevoerd configuratiebestand.__ In dit bestand wordt de runtime-omgeving gedefinieerd die nodig is om het model te trainen. In dit voorbeeld worden ook de gegevens gemonteerd die worden gebruikt om het model in de trainingsomgeving te trainen.
* `scripts\train.py`: Het trainingsscript. Dit dossier traint het model.
* `scripts\utils.py`: Een helperbestand dat wordt gebruikt door het trainingsscript.
* `.azureml\conda_dependencies.yml`: Definieert de softwareafhankelijkheden die nodig zijn om het trainingsscript uit te voeren.
* `dataset.json`: De definitie van de gegevensset. Wordt gebruikt om de MNIST-gegevensset te registreren in de Azure Machine Learning-werkruimte.

### <a name="deployment-files"></a>Implementatiebestanden

De repository bevat de volgende bestanden, die worden gebruikt om het getrainde model als webservice te implementeren:

* `aciDeploymentConfig.yml`: Een __configuratiebestand voor implementatie.__ Dit bestand definieert de hostingomgeving die nodig is voor het model.
* `inferenceConfig.json`: Een __inferentetrekkingconfiguratiebestand.__ Dit bestand definieert de softwareomgeving die door de service wordt gebruikt om gegevens met het model te scoren.
* `score.py`: Een python-script dat binnenkomende gegevens accepteert, het scoort met het model en vervolgens een antwoord retourneert.
* `scoring-env.yml`: De afhankelijkheden van conda `score.py` die nodig zijn om het model en het script uit te voeren.
* `testdata.json`: Een gegevensbestand dat kan worden gebruikt om de geïmplementeerde webservice te testen.

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

Er zijn verschillende manieren waarop u vanaf de CLI verifiëren voor uw Azure-abonnement. De meest elementaire is om interactief te verifiëren met behulp van een browser. Als u interactief wilt verifiëren, opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdrachtregel volgen. De instructies omvatten [https://aka.ms/devicelogin](https://aka.ms/devicelogin) browsen naar en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>De machine learning-extensie installeren

Als u de machine learning-extensie wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Als u een bericht ontvangt dat de extensie al is geïnstalleerd, gebruikt u de volgende opdracht om bij te werken naar de nieuwste versie:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een basiscontainer met resources op het Azure-platform. Wanneer u met de Azure Machine Learning werkt, bevat de resourcegroep uw Azure Machine Learning-werkruimte. Het bevat ook andere Azure-services die door de werkruimte worden gebruikt. Als u uw model bijvoorbeeld traint met behulp van een op de cloud gebaseerde compute resource, wordt die bron gemaakt in de resourcegroep.

Als __u een nieuwe resourcegroep wilt maken,__ gebruikt u de volgende opdracht. Vervang `<resource-group-name>` de naam die u voor deze resourcegroep wilt gebruiken. Vervang `<location>` het Azure-gebied dat u voor deze brongroep wilt gebruiken:

> [!TIP]
> U moet een gebied selecteren waar de Azure Machine Learning beschikbaar is. Zie Producten [die per regio beschikbaar zijn voor](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)informatie .

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

Zie [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)voor meer informatie over het werken met resourcegroepen.

## <a name="create-a-workspace"></a>Een werkruimte maken

Als u een nieuwe werkruimte wilt maken, gebruikt u de volgende opdracht. Vervang `<workspace-name>` de naam die u voor deze werkruimte wilt gebruiken. Vervang `<resource-group-name>` de naam van de resourcegroep:

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

## <a name="connect-local-project-to-workspace"></a>Lokaal project verbinden met werkruimte

Gebruik vanuit een terminal- of opdrachtprompt de volgende `cli-train-deploy` opdrachten om mappen in de map te wijzigen en vervolgens verbinding te maken met uw werkruimte:

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

Met deze `.azureml/config.json` opdracht wordt een bestand gemaakt dat informatie bevat die nodig is om verbinding te maken met uw werkruimte. De rest `az ml` van de opdrachten die in deze zelfstudie worden gebruikt, gebruiken dit bestand, zodat u de werkruimte- en resourcegroep niet aan alle opdrachten hoeft toe te voegen.

## <a name="create-the-compute-target-for-training"></a>Het rekendoel voor training maken

In dit voorbeeld wordt een Azure Machine Learning Compute-cluster gebruikt om het model te trainen. Als u een nieuw rekencluster wilt maken, gebruikt u de volgende opdracht:

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

Met deze opdracht wordt `cpu-cluster`een nieuw berekend doel gemaakt met de naam , met een maximum van vier knooppunten. De geselecteerde VM-grootte biedt een VM met een GPU-bron. Zie [VM-typen en -formaten].

> [!IMPORTANT]
> De naam van het`cpu-cluster` rekendoel ( in dit geval), is belangrijk; het wordt verwezen `.azureml/mnist.runconfig` door het bestand dat in de volgende sectie wordt gebruikt.

## <a name="define-the-dataset"></a>De gegevensset definiëren

Als u een model wilt trainen, u de trainingsgegevens verstrekken met behulp van een gegevensset. Als u een gegevensset wilt maken vanuit de CLI, moet u een gegevenssetdefinitiebestand opgeven. Het `dataset.json` bestand in de repo maakt een nieuwe gegevensset met behulp van de MNIST-gegevens. De gegevensset die `mnist-dataset`wordt maakt, heeft de naam .

Als u de `dataset.json` gegevensset wilt registreren met het bestand, gebruikt u de volgende opdracht:

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
> Kopieer de waarde `id` van de vermelding, zoals deze wordt gebruikt in de volgende sectie.

Als u een uitgebreidere sjabloon voor een gegevensset wilt bekijken, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>De gegevensset verwijzen

Als u de gegevensset beschikbaar wilt maken in de trainingsomgeving, moet u ernaar verwijzen in het runconfig-bestand. Het `.azureml/mnist.runconfig` bestand bevat de volgende YAML-vermeldingen:

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

Wijzig de waarde `id` van de vermelding om de waarde te matchen die is geretourneerd toen u de gegevensset registreerde. Deze waarde wordt gebruikt om de gegevens tijdens de training in het rekendoel te laden.

Deze YAML resulteert in de volgende acties tijdens de training:

* Hiermee wordt de gegevensset (op basis van de id van de gegevensset) `mnist` in de trainingsomgeving gemonteerd en wordt het pad naar het bevestigingspunt in de omgevingsvariabele opgeslagen.
* Geeft de locatie van de gegevens (bevestigingspunt) in `--data-folder` de trainingsomgeving door aan het script met behulp van het argument.

Het runconfig-bestand bevat ook informatie die wordt gebruikt om de omgeving te configureren die wordt gebruikt door de trainingsrun. Als u dit bestand inspecteert, ziet u `cpu-compute` dat het verwijst naar het rekendoel dat u eerder hebt gemaakt. Het bevat ook het aantal knooppunten`"nodeCount": "4"`dat moet `"condaDependencies"` worden gebruikt bij het trainen ( ) en bevat een sectie met de Python-pakketten die nodig zijn om het trainingsscript uit te voeren.

> [!TIP]
> Hoewel het mogelijk is om handmatig een runconfig-bestand te maken, is het bestand in dit voorbeeld gemaakt met behulp van het `generate-runconfig.py` bestand dat in de repository is opgenomen. Dit bestand krijgt een verwijzing naar de geregistreerde gegevensset, maakt een run config programatically, en dan blijft het bestand.

Zie [Rekendoelen instellen en gebruiken voor modeltraining voor](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)meer informatie over het uitvoeren van configuratiebestanden. Zie de [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)voor een volledige JSON-referentie.

## <a name="submit-the-training-run"></a>Stuur de trainingsrun in

Als u een trainingsrun op het `cpu-cluster` rekendoel wilt starten, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Met deze opdracht geeft u`myexperiment`een naam op voor het experiment ( ). Het experiment slaat informatie over deze run op in de werkruimte.

De `-c mnist` parameter `.azureml/mnist.runconfig` geeft het bestand op.

De `-t` parameter slaat een verwijzing naar deze run op in een JSON-bestand en wordt in de volgende stappen gebruikt om het model te registreren en te downloaden.

Terwijl de training processen uitvoert, worden informatie uit de trainingssessie op de externe compute resource verwerkt. Een deel van de informatie is vergelijkbaar met de volgende tekst:

```output
Predict the test set
Accuracy is 0.9185
```

Deze tekst wordt vastgelegd in het trainingsscript en geeft de nauwkeurigheid van het model weer. Andere modellen hebben verschillende prestatiestatistieken.

Als u het trainingsscript inspecteert, zult u merken dat het ook `outputs/sklearn_mnist_model.pkl`de alfawaarde gebruikt wanneer het getrainde model wordt opgeslagen in .

Het model is `./outputs` opgeslagen in de map op het compute-doel waar het is getraind. In dit geval de instantie Azure Machine Learning Compute in de Azure-cloud. Het trainingsproces uploadt automatisch de `./outputs` inhoud van de map vanuit het rekendoel waar training plaatsvindt naar uw Azure Machine Learning-werkruimte. Het wordt opgeslagen als onderdeel`myexperiment` van het experiment (in dit voorbeeld).

## <a name="register-the-model"></a>Het model registreren

Als u het model rechtstreeks wilt registreren vanuit de opgeslagen versie in uw experiment, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Met deze `outputs/sklearn_mnist_model.pkl` opdracht wordt het bestand dat door `mymodel`de trainingsrun is gemaakt, geregistreerd als een nieuw modelregistratie met de naam . De `--assets-path` verwijzingen naar een pad in een experiment. In dit geval worden de experiment- en `runoutput.json` rungegevens geladen vanuit het bestand dat is gemaakt door de trainingsopdracht. Hiermee `-t registeredmodel.json` wordt een JSON-bestand gemaakt dat verwijst naar het nieuwe geregistreerde model dat door deze opdracht is gemaakt en wordt gebruikt door andere CLI-opdrachten die werken met geregistreerde modellen.

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

### <a name="model-versioning"></a>Modelversiebeheer

Let op het versienummer dat is geretourneerd voor het model. De versie wordt verhoogd telkens wanneer u een nieuw model met deze naam registreert. U het model bijvoorbeeld downloaden en registreren uit een lokaal bestand met behulp van de volgende opdrachten:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

De eerste opdracht downloadt het geregistreerde model naar de huidige map. De bestandsnaam `sklearn_mnist_model.pkl`is , dat is het bestand waarnaar wordt verwezen toen u het model registreerde. De tweede opdracht registreert`-p "sklearn_mnist_model.pkl"`het lokale model ( )`mymodel`met dezelfde naam als de vorige registratie ( ). Deze keer, de JSON gegevens geretourneerd geeft de versie als 2.

## <a name="deploy-the-model"></a>Het model implementeren

Als u een model wilt implementeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Mogelijk ontvangt u een waarschuwing over 'Kan het bestaan van LocalWebservice niet worden gecontroleerd' of 'Kan u dockerclient niet maken'. U dit veilig negeren, omdat u geen lokale webservice implementeert.

Met deze opdracht wordt `myservice`een nieuwe service met de naam , met behulp van versie 1 van het model dat u eerder hebt geregistreerd, geïmplementeerd.

Het `inferenceConfig.yml` bestand bevat informatie over het gebruik van het model voor gevolgtrekking. Het verwijst bijvoorbeeld naar het`score.py`invoerscript ( ) en softwareafhankelijkheden.

Zie het [configuratieschema Inference](reference-azure-machine-learning-cli.md#inference-configuration-schema)voor meer informatie over de structuur van dit bestand. Zie Modellen implementeren met [de Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy)voor meer informatie over invoerscripts.

De `aciDeploymentConfig.yml` beschrijving van de implementatieomgeving die wordt gebruikt om de service te hosten. De implementatieconfiguratie is specifiek voor het rekentype dat u voor de implementatie gebruikt. In dit geval wordt een Azure Container Instance gebruikt. Zie het [configuratieschema implementatie](reference-azure-machine-learning-cli.md#deployment-configuration-schema)voor meer informatie .

Het duurt enkele minuten voordat het implementatieproces is voltooid.

> [!TIP]
> In dit voorbeeld wordt Azure Container Instances gebruikt. Implementaties naar ACI maken automatisch de benodigde ACI-bron. Als u in plaats daarvan zou implementeren in Azure Kubernetes Service, moet u van `az ml model deploy` tevoren een AKS-cluster maken en opgeven als onderdeel van de opdracht. Zie [Een model implementeren naar een Azure Kubernetes Service-cluster](how-to-deploy-azure-kubernetes-service.md)voor een voorbeeld van implementeren naar AKS.

Na enkele minuten wordt informatie die vergelijkbaar is met de volgende JSON geretourneerd:

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

### <a name="the-scoring-uri"></a>De scorende URI

De `scoringUri` geretourneerde van de implementatie is het REST-eindpunt voor een model dat als webservice is geïmplementeerd. U deze URI ook krijgen met behulp van de volgende opdracht:

```azurecli-interactive
az ml service show -n myservice
```

Met deze opdracht wordt hetzelfde `scoringUri`JSON-document geretourneerd, inclusief het .

Het REST-eindpunt kan worden gebruikt om gegevens naar de service te verzenden. Zie [Een Azure Machine Learning-model dat is geïmplementeerd als webservice gebruiken voor](how-to-consume-web-service.md) informatie over het maken van een clienttoepassing die gegevens naar de service verzendt.

### <a name="send-data-to-the-service"></a>Gegevens naar de service verzenden

Hoewel u een clienttoepassing maken om het eindpunt aan te roepen, biedt de machine learning CLI een hulpprogramma dat als testclient kan fungeren. Gebruik de volgende opdracht om `testdata.json` gegevens in het bestand naar de service te sturen:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Als u PowerShell gebruikt, gebruikt u in plaats daarvan de volgende opdracht:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Het antwoord van de `[ 3 ]`opdracht is vergelijkbaar met .

## <a name="clean-up-resources"></a>Resources opschonen

> [!IMPORTANT]
> De resources die u hebt gemaakt kunnen worden gebruikt als de vereisten voor andere Azure Machine Learning-zelfstudies en artikelen met procedures.

### <a name="delete-deployed-service"></a>Geïmplementeerde service verwijderen

Als u van plan bent de Azure Machine Learning-werkruimte te blijven gebruiken, maar de geïmplementeerde service wilt verwijderen om de kosten te verlagen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml service delete -n myservice
```

Met deze opdracht wordt een JSON-document geretourneerd dat de naam van de verwijderde service bevat. Het kan enkele minuten duren voordat de service wordt verwijderd.

### <a name="delete-the-training-compute"></a>De trainingsgegevensberekenen verwijderen

Als u van plan bent de Azure Machine Learning-werkruimte `cpu-cluster` te blijven gebruiken, maar het rekendoel dat is gemaakt voor training wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Met deze opdracht wordt een JSON-document geretourneerd dat de id van het verwijderde rekendoel bevat. Het kan enkele minuten duren voordat het rekendoel is verwijderd.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent de resources die u hebt gemaakt te gebruiken, verwijdert u deze zodat u geen extra kosten hoeft te maken.

Als u de brongroep en alle Azure-resources die in dit document zijn gemaakt, wilt verwijderen, gebruikt u de volgende opdracht. Vervang `<resource-group-name>` de naam van de resourcegroep die u eerder hebt gemaakt:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie azure machine learning hebt u de CLI voor machine learning gebruikt voor de volgende taken:

> [!div class="checklist"]
> * De machine learning-extensie installeren
> * Een Azure Machine Learning-werkruimte maken
> * De rekenbron maken die wordt gebruikt om het model te trainen
> * De gegevensset definiëren en registreren die wordt gebruikt om het model te trainen
> * Een trainingsrun starten
> * Een model registreren en downloaden
> * Het model implementeren als webservice
> * Gegevens scoren met de webservice

Zie [De CLI-extensie gebruiken voor Azure Machine Learning voor](reference-azure-machine-learning-cli.md)meer informatie over het gebruik van de CLI.

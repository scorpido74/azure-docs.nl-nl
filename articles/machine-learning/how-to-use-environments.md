---
title: Herbruikbare ML-omgevingen maken
titleSuffix: Azure Machine Learning
description: Omgevingen maken en beheren voor model training en-implementatie. Python-pakketten en andere instellingen voor de omgeving beheren.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: de1e01e56079753a1a9ee8debc2f2172e1dc8375
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548302"
---
# <a name="reuse-environments-for-training--deployment-with-azure-machine-learning"></a>Omgevingen hergebruiken voor training & implementatie met Azure Machine Learning.
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over het maken en beheren van Azure Machine Learning [omgevingen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) , zodat u de software afhankelijkheden van uw projecten kunt bijhouden en reproduceren tijdens het ontwikkelen.

Beheer van de software-afhankelijkheid is een algemene taak voor ontwikkel aars. U wilt er zeker van zijn dat de builds worden gereproduceerd zonder veel hand matige software configuratie. Met oplossingen voor lokale ontwikkeling, zoals PIP en Conda, biedt de klasse Azure Machine Learning omgevingen een oplossing voor zowel lokale als gedistribueerde Cloud ontwikkeling.

In de voor beelden in dit artikel wordt uitgelegd hoe u:

* Een omgeving maken en pakket afhankelijkheden opgeven
* Omgevingen ophalen en bijwerken
* Omgeving gebruiken voor training
* Omgeving gebruiken voor de implementatie van webservices

Zie het [conceptuele artikel](concept-environments.md) voor een overzicht van de manier waarop omgevingen werken in azure machine learning.

## <a name="prerequisites"></a>Vereisten

* De Azure Machine Learning SDK voor python is [geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Een omgeving maken

Er zijn meerdere manieren om een omgeving te maken voor uw experimenten.

### <a name="use-curated-environment"></a>Gecuratore omgeving gebruiken

U kunt een van de gestarte omgevingen selecteren om te beginnen met. 

* De omgeving met __AzureML-minimale__ bevat een minimale set pakketten voor het inschakelen van tracking en het uploaden van activa. U kunt deze gebruiken als uitgangs punt voor uw eigen omgeving.

* De omgeving voor de __AzureML-zelf studie__ bevat algemene data Science-pakketten, zoals Scikit-learn, Pandas en matplotlib, en een grotere set van AzureML-SDK-pakketten.

In de cache geplaatste docker-installatie kopieën worden er back-ups van gemaakt.

Gebruik de methode __environment. Get__ om een van de met de curator gevoerde omgevingen te selecteren:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

U kunt de gegroepeerde omgevingen en hun pakketten weer geven met behulp van de volgende code:
```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Start niet uw eigen omgevings naam met het voor voegsel voor _AzureML_ . Het is gereserveerd voor gecuratore omgevingen.

### <a name="instantiate-an-environment-object"></a>Een omgevings object instantiëren

Als u hand matig een omgeving wilt maken, importeert u de omgevings klasse vanuit de SDK en maakt u een instantie van een omgevings object met de volgende code.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="conda-and-pip-specification-files"></a>Conda-en PIP-specificatie bestanden

U kunt ook een omgeving maken op basis van een Conda-specificatie of een PIP-vereisten bestand.
Gebruik de [from_conda_specification ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) of de methode [from_pip_requirements ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) en voeg de naam van uw omgeving en het bestandspad van het gewenste bestand in het argument methode toe.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

#From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="existing-conda-environment"></a>Bestaande Conda-omgeving

Als u een bestaande Conda-omgeving op uw lokale computer hebt, biedt de service een oplossing voor het maken van een omgevings object. Op deze manier kunt u uw lokale interactieve omgeving opnieuw gebruiken op externe uitvoeringen.

Met de volgende code wordt een omgevings object gemaakt van de bestaande Conda-omgeving `mycondaenv` met de methode [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="automatically-create-environments"></a>Automatisch omgevingen maken

Automatisch een omgeving maken door het verzenden van een training met de methode Submit (). Wanneer u een trainings uitvoering verzendt, kan het maken van een nieuwe omgeving enkele minuten duren, afhankelijk van de grootte van de vereiste afhankelijkheden. 

Als u geen omgeving opgeeft in uw uitvoerings configuratie voordat u de uitvoering verzendt, wordt er een standaard omgeving voor u gemaakt.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attaches training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Shows each step of run 
run.wait_for_completion(show_output=True)
```

Op dezelfde manier kunt u, als u een [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) -object gebruikt voor training, het Estimator-exemplaar rechtstreeks als een uitvoering verzenden zonder dat u een omgeving hoeft op te geven. Het `Estimator`-object heeft de omgeving en het reken doel al ingekapseld.


## <a name="add-packages-to-an-environment"></a>Pakketten toevoegen aan een omgeving

Voeg pakketten toe aan een omgeving met Conda-, PIP-of privé-wiel bestanden. Geef elke pakket afhankelijkheid op met behulp van de [CondaDependency-klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)en voeg deze toe aan de PythonSection van de omgeving.

### <a name="conda-and-pip-packages"></a>Conda-en PIP-pakketten

Als een pakket beschikbaar is in een Conda-pakket opslagplaats, is het raadzaam om de installatie van Conda over PIP te gebruiken. De reden hiervoor is dat Conda-pakketten doorgaans worden geleverd met vooraf gemaakte binaire bestanden die de installatie betrouwbaarder maken.

In het volgende voor beeld worden `scikit-learn`, met name versie 0.21.3, en `pillow` pakket toegevoegd aan de omgeving, respectievelijk `myenv` met de methoden [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) en [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) .

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Privé-wiel bestanden

U kunt privé PIP-wiel bestanden gebruiken door deze eerst naar uw werkruimte opslag te uploaden met behulp van de methode static [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) , vervolgens de opslag-URL vast te leggen en de URL door te geven aan de `add_pip_package()`-methode

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Omgevingen beheren

Omgevingen beheren zodat u ze kunt bijwerken, volgen en opnieuw gebruiken in reken doelen en met andere gebruikers van de werk ruimte.

### <a name="register-environments"></a>Omgevingen registreren

De omgeving wordt automatisch bij uw werk ruimte geregistreerd wanneer u een uitvoeren of een webservice implementeert. U kunt de omgeving ook hand matig registreren met de methode [REGI ster ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) . Met deze bewerking wordt de omgeving omgezet in een entiteit die wordt bijgehouden en geversiond in de Cloud en kan worden gedeeld tussen werk ruimte gebruikers.

Met de volgende code wordt de omgeving, `myenv`, geregistreerd in de werk ruimte `ws`.

```python
myenv.register(workspace=ws)
```

Wanneer de omgeving voor de eerste keer wordt gebruikt, wordt deze in de praktijk of implementatie geregistreerd bij de werk ruimte, gebouwd en geïmplementeerd op het berekenings doel. De omgevingen worden in de cache opgeslagen door de service. Het opnieuw gebruiken van een omgeving in de cache neemt veel minder tijd in beslag dan het gebruik van een nieuwe service of een update die is bijgewerkt.

### <a name="get-existing-environments"></a>Bestaande omgevingen ophalen

De omgevings klasse biedt methoden waarmee u bestaande omgevingen in uw werk ruimte kunt ophalen op naam, als een lijst of een specifieke training voor het oplossen van problemen of controle doeleinden, evenals reproduceer baarheid.

#### <a name="view-list-of-environments"></a>Lijst met omgevingen weer geven

Bekijk de omgevingen in uw werk ruimte met [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)en selecteer vervolgens een omgeving om opnieuw te gebruiken.

#### <a name="get-environment-by-name"></a>Omgeving ophalen op naam

U kunt ook een specifieke omgeving verkrijgen op naam en versie.
De volgende code maakt gebruik van de methode [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) om de versie `1` van de omgeving op te halen, `myenv` in de `ws`-werk ruimte.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Specifieke omgeving voor het uitvoeren van training

Als u de omgeving wilt ophalen die wordt gebruikt voor een specifieke uitvoering nadat de training is voltooid, gebruikt u de methode [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) in de klasse run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Een bestaande omgeving bijwerken

Als u wijzigingen aanbrengt in een bestaande omgeving, zoals het toevoegen van een python-pakket, wordt een nieuwe versie van een omgeving gemaakt wanneer u uitvoeren, model implementeren of de omgeving hand matig registreert. Met versie beheer kunt u in de loop van de tijd wijzigingen in de omgeving weer geven.

Als u een python-pakket versie van een bestaande omgeving wilt bijwerken, geeft u het exacte versie nummer voor dat pakket op. Anders wordt de bestaande omgeving met pakket versies van de Azure Machine Learning opnieuw gebruikt wanneer de omgeving is gemaakt.

### <a name="debug-the-image-build"></a>Fouten opsporen in de installatie kopie maken

In dit voor beeld wordt de methode [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) gebruikt om een omgeving hand matig te maken als docker-installatie kopie, en worden de uitvoer logboeken gecontroleerd op basis van de installatie kopie met behulp van [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). De ingebouwde installatie kopie wordt vervolgens weer gegeven onder de werk ruimte Azure Container Registry, wat nuttig is voor het opsporen van fouten.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker en omgevingen

 Met de [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) van de klasse Azure machine learning `Environments` kunt u het gast besturingssysteem waarmee uw training wordt uitgevoerd, gedetailleerd aanpassen en beheren.

Wanneer u docker `enable`, bouwt de service een docker-installatie kopie en maakt een python-omgeving met uw specificaties binnen die docker-container. Dit biedt extra isolatie en reproduceer baarheid voor uw trainings uitvoeringen.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Als de docker-installatie kopie eenmaal is gemaakt, wordt deze standaard weer gegeven in de Azure Container Registry die aan de werk ruimte is gekoppeld.  De naam van de opslag plaats heeft de indeling *azureml_\<uuid\>* . Het onderdeel voor de unieke id (*uuid*) komt overeen met een hash die is berekend op basis van de configuratie van de omgeving. Hiermee kan de service bepalen of een installatie kopie die overeenkomt met de opgegeven omgeving al bestaat voor hergebruik.

Daarnaast gebruikt de service automatisch een van de op Ubuntu Linux gebaseerde [basis installatie kopieën](https://github.com/Azure/AzureML-Containers)en installeert de opgegeven Python-pakketten. De basis installatie kopie heeft CPU-en GPU-versies. Azure Machine Learning detecteert automatisch welke versie moet worden gebruikt.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
# Alternatively, you can specify the contents of dockerfile of your base image
with open("docker_file_of_your_base_image", "r") as f:
    dockerfile_contents_of_your_base_image=f.read()
myenv.docker.base_dockerfile=dockerfile_contents_of_your_base_image 
```

> [!NOTE]
> Als u `environment.python.user_managed_dependencies=False` opgeeft wanneer u een aangepaste docker-installatie kopie gebruikt, zal de service een Conda-omgeving in de installatie kopie bouwen en de uitvoering in die omgeving uitvoeren, in plaats van python-bibliotheken te gebruiken die u mogelijk hebt geïnstalleerd op de basis installatie kopie. Stel de para meter in op `True` om uw eigen geïnstalleerde pakketten te gebruiken.

## <a name="using-environments-for-training"></a>Omgevingen gebruiken voor training

Als u een trainings uitvoering wilt verzenden, moet u uw omgeving, het [berekenings doel](concept-compute-target.md) en het python-trainings script in een uitvoerings configuratie combi neren. een wrapper-object dat wordt gebruikt voor het verzenden van uitvoeringen.

Wanneer u een trainings uitvoering verzendt, kan het maken van een nieuwe omgeving enkele minuten duren, afhankelijk van de grootte van de vereiste afhankelijkheden. De omgevingen worden in de cache opgeslagen door de service, dus als de omgevings definitie ongewijzigd blijft, wordt de volledige instel tijd slechts eenmaal in rekening gebracht.

In het volgende voor beeld van een lokaal script wordt aangegeven waar u [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) als uw wrapper-object zou gebruiken.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Als u de uitvoerings geschiedenis wilt uitschakelen of moment opnamen wilt uitvoeren, gebruikt u de instelling onder `ScriptRunConfig.run_config.history`.

Als u de omgeving niet opgeeft in de configuratie van de uitvoering, wordt door de service een standaard omgeving voor u gemaakt wanneer u de uitvoering verzendt.

### <a name="train-with-an-estimator"></a>Trainen met een estimator

Als u een [Estimator](how-to-train-ml-models.md) voor training gebruikt, kunt u het Estimator-exemplaar gewoon rechtstreeks indienen, omdat het de omgeving en het reken doel al heeft ingekapseld.

De volgende code maakt gebruik van een Estimator voor een training met één knoop punt dat wordt uitgevoerd op een externe Compute voor een scikit-leer model en gaat ervan uit dat een eerder gemaakt Compute-doel object, `compute_target` en Data Store-object `ds`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="using-environments-for-web-service-deployment"></a>Omgevingen gebruiken voor de implementatie van web service

U kunt omgevingen gebruiken bij het implementeren van uw model als een webservice. Hierdoor kan een reproduceer bare, verbonden werk stroom worden gebruikt voor het trainen, testen en implementeren van uw model met exact dezelfde bibliotheken in uw training en voor het afleiden van berekeningen.

Als u een webservice wilt implementeren, moet u de omgeving combi neren, reken kracht, Score script en geregistreerd model in uw implementatie object [implementeren ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-). Meer informatie over het [implementeren van webservices](how-to-deploy-and-where.md).

In dit voor beeld wordt ervan uitgegaan dat u een trainings uitvoering hebt voltooid en dat model wilt implementeren in een Azure container instance (ACI). Bij het bouwen van de webservice worden het model en de Score bestanden gekoppeld aan de installatie kopie en wordt de Azure Machine Learning ring stack toegevoegd aan de installatie kopie.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Voorbeeld-laptops

In dit [voor beeld wordt het notitie blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) uitgebreid op concepten en methoden die in dit artikel worden getoond.

[Een model implementeren met behulp van een aangepaste docker-basis installatie kopie](how-to-deploy-custom-docker-image.md) laat zien hoe u een model implementeert met behulp van een aangepaste docker-basis installatie kopie.

Dit [voor beeld](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) laat zien hoe u een Spark-model als een webservice implementeert.

## <a name="create-and-manage-environments-with-the-cli"></a>Omgevingen maken en beheren met de CLI

De [Azure machine learning cli](reference-azure-machine-learning-cli.md) komt overeen met de meerderheid van de functionaliteit van de PYTHON-SDK en kan worden gebruikt voor het maken en beheren van omgevingen. Met de volgende opdrachten wordt de basis functionaliteit gedemonstreerd.

Met de volgende opdracht worden de bestanden gesteigerd voor een standaard omgevings definitie in de opgegeven map. Deze bestanden zijn JSON-bestanden die vergelijkbaar zijn met de functie in de SDK en kunnen worden gebruikt om nieuwe omgevingen met aangepaste instellingen te maken. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Voer de volgende opdracht uit om een omgeving te registreren vanuit een opgegeven map.

```azurecli-interactive
az ml environment register -d myenvdir
```

Als u de volgende opdracht uitvoert, worden alle geregistreerde omgevingen weer geven.

```azurecli-interactive
az ml environment list
```

Down load een geregistreerde omgeving met de volgende opdracht.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de naslag informatie voor de SDK van de [omgevings klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .

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
ms.date: 09/16/2019
ms.openlocfilehash: b46ca59bc93477c338001009ff7eeeddc7248684
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147331"
---
# <a name="create-and-manage-reusable-environments-for-training-and-deployment-with-azure-machine-learning"></a>Maak en beheer herbruikbare omgevingen voor training en implementatie met Azure Machine Learning.

In dit artikel vindt u informatie over het maken en beheren van Azure Machine Learning [omgevingen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) , zodat u de software afhankelijkheden van uw projecten kunt bijhouden en reproduceren tijdens het ontwikkelen.

Beheer van de software-afhankelijkheid is een algemene taak voor ontwikkel aars. U wilt er zeker van zijn dat de builds worden gereproduceerd zonder veel hand matige software configuratie. Met oplossingen voor lokale ontwikkeling, zoals PIP en Conda, biedt de klasse Azure Machine Learning omgevingen een oplossing voor zowel lokale als gedistribueerde Cloud ontwikkeling.

In de voor beelden in dit artikel wordt uitgelegd hoe u:

* Een omgeving maken en pakket afhankelijkheden opgeven
* Omgevingen ophalen en bijwerken
* Omgeving gebruiken voor training
* Omgeving gebruiken voor de implementatie van webservices

## <a name="what-are-environments"></a>Wat zijn omgevingen?

In omgevingen worden de Python-pakketten, omgevings variabelen en software-instellingen voor uw trainings-en Score scripts en uitvoerings tijden (python, Spark of docker) opgegeven. Ze zijn beheerde en geversiede entiteiten in uw Azure Machine Learning-werk ruimte waarmee reproduceer bare, audit bare en Portable machine learning werk stromen kunnen worden toegepast op verschillende Compute-doelen.

U kunt een omgevings object gebruiken op uw lokale Compute voor het ontwikkelen van uw trainings script, het hergebruiken van dezelfde omgeving op Azure Machine Learning Compute for model-training op schaal en het implementeren van uw model zelfs met diezelfde omgeving.

In het volgende voor beeld ziet u dat hetzelfde omgevings object kan worden gebruikt in zowel de configuratie voor training als tijdens uw detraining-en implementatie configuratie voor web service-implementaties.

![Diagram van de omgeving in machine learning werk stroom](./media/how-to-use-environments/ml-environment.png)

### <a name="types-of-environments"></a>Typen omgevingen

Omgevingen kunnen breed worden onderverdeeld in twee categorieën: door de **gebruiker beheerde** en door het **systeem beheerde**.

Voor een door een gebruiker beheerde omgeving bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat nodig is voor uw trainings script voor het Compute-doel. Conda controleert uw omgeving niet en installeert niets voor u. 

Door systeem beheerde omgevingen worden gebruikt wanneer u wilt dat [Conda](https://conda.io/docs/) de python-omgeving en de script afhankelijkheden voor u beheert. De service gaat ervan uit dat type omgeving standaard, vanwege de bruikbaarheid op externe Compute-doelen die niet hand matig kunnen worden geconfigureerd.

## <a name="prerequisites"></a>Vereisten

* De Azure Machine Learning SDK voor python is [geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

## <a name="create-an-environment"></a>Een omgeving maken

Er zijn meerdere manieren om een omgeving te maken voor uw experimenten.

### <a name="instantiate-an-environment-object"></a>Een omgevings object instantiëren

Als u hand matig een omgeving wilt maken, importeert u de omgevings klasse uit de SDK en maakt u een instantie van een omgevings object met de volgende code.

```python
from azureml.core import Environment
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

Hieronder wordt een omgevings object van de bestaande Conda-omgeving `mycondaenv` gemaakt met de methode [from_existing_conda_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

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

Op dezelfde manier kunt u, [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) als u een object gebruikt voor de training, het Estimator-exemplaar rechtstreeks als een uitvoering verzenden zonder dat u een omgeving hoeft `Estimator` op te geven. Dit komt doordat het object de omgeving en het reken doel al heeft ingekapseld.


## <a name="add-packages-to-an-environment"></a>Pakketten toevoegen aan een omgeving

Voeg pakketten toe aan een omgeving met Conda-, PIP-of privé-wiel bestanden. Geef elke pakket afhankelijkheid op met behulp van de [CondaDependency-klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)en voeg deze toe aan de PythonSection van de omgeving.

### <a name="conda-and-pip-packages"></a>Conda-en PIP-pakketten

Als een pakket beschikbaar is in een Conda-pakket opslagplaats, is het raadzaam om de installatie van Conda over PIP te gebruiken. De reden hiervoor is dat Conda-pakketten doorgaans worden geleverd met vooraf gemaakte binaire bestanden die de installatie betrouwbaarder maken.

`myenv` In het volgende voor `scikit-learn`beeld worden de [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) -en [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) - `pillow` methoden, met name 0.21.3 en pakket toegevoegd aan de omgeving.

```python
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs scikit-learn version 0.21.3 conda package
conda_dep.add_conda_package("scikit-learn==0.21.3")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

### <a name="private-wheel-files"></a>Privé-wiel bestanden

U kunt privé PIP-wiel bestanden gebruiken door deze eerst naar uw werkruimte opslag te uploaden met [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) behulp van statische methode, vervolgens de opslag-URL vast te leggen en `add_pip_package()` de URL door te geven aan de methode

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

De volgende registreren de omgeving, `myenv`, in de `ws`werk ruimte.

```python
myenv.register(workspace=ws)
```

Wanneer de omgeving voor de eerste keer wordt gebruikt, wordt deze in de praktijk of implementatie geregistreerd bij de werk ruimte, gebouwd en geïmplementeerd op het berekenings doel. De omgevingen worden in de cache opgeslagen door de service. Het opnieuw gebruiken van een omgeving in de cache neemt veel minder tijd in beslag dan het gebruik van een nieuwe service of een update die is bijgewerkt.

### <a name="get-existing-environments"></a>Bestaande omgevingen ophalen

De omgevings klasse biedt methoden waarmee u bestaande omgevingen in uw werk ruimte kunt ophalen op naam, als een lijst of een specifieke training-uitvoering. Voor het oplossen van problemen of controle doeleinden, reproduceer baarheid

#### <a name="view-list-of-environments"></a>Lijst met omgevingen weer geven

Bekijk de omgevingen in uw werk ruimte met [lijst ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-)en selecteer vervolgens een omgeving om opnieuw te gebruiken.

```python
from azureml.core import Environment
list("workspace_name")
```

#### <a name="get-environment-by-name"></a>Omgeving ophalen op naam

U kunt ook een specifieke omgeving verkrijgen op naam en versie.
De volgende code gebruikt de methode [Get ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) om de versie `1` `myenv` van de omgeving op te halen `ws` in de werk ruimte.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="training-run-specific-environment"></a>Specifieke omgeving voor het uitvoeren van training

Als u de omgeving wilt ophalen die wordt gebruikt voor een specifieke uitvoering nadat de training is voltooid, gebruikt u de methode [get_environment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--)in de klasse run.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Een bestaande omgeving bijwerken

Als u wijzigingen aanbrengt in een bestaande omgeving, zoals het toevoegen van een python-pakket, wordt een nieuwe versie van een omgeving gemaakt wanneer u uitvoeren uitvoert, model implementeren of de omgeving hand matig registreert. Met versie beheer kunt u in de loop van de tijd wijzigingen in de omgeving weer geven.

Als u een python-pakket versie van een bestaande omgeving wilt bijwerken, geeft u het exacte versie nummer voor dat pakket op. Anders wordt de bestaande omgeving met pakket versies van de Azure Machine Learning opnieuw gebruikt wanneer de omgeving is gemaakt.

### <a name="debug-the-image-build"></a>Fouten opsporen in de installatie kopie maken

In dit voor beeld wordt de methode [Build ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace-) gebruikt om een omgeving hand matig te maken als docker-installatie kopie, en worden de uitvoer logboeken gecontroleerd vanuit de installatie kopie met behulp van [wait_for_completion ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-). De ingebouwde installatie kopie wordt vervolgens weer gegeven onder de werk ruimte Azure Container Registry, wat nuttig is voor het opsporen van fouten.

```python
from azureml.core import Image
build = env.build()
build.wait_for_completion(show_output=True)
```

## <a name="docker-and-environments"></a>Docker en omgevingen

 Met de [DockerSection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) van de `Environments` klasse Azure machine learning kunt u het gast besturingssysteem waarmee uw training wordt uitgevoerd, gedetailleerd aanpassen en beheren.

Wanneer u `enable` docker hebt, bouwt de service een docker-installatie kopie en maakt een python-omgeving met uw specificaties binnen die docker-container. Dit biedt extra isolatie en reproduceer baarheid voor uw trainings uitvoeringen.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Als de docker-installatie kopie eenmaal is gemaakt, wordt deze standaard weer gegeven in de Azure Container Registry die aan de werk ruimte is gekoppeld.  De naam van de opslag plaats heeft de indeling *azureml\>/azureml_\<uuid*. Het onderdeel voor de unieke id (*uuuid*) komt overeen met een hash die is berekend op basis van de configuratie van de omgeving. Hiermee kan de service bepalen of een installatie kopie die overeenkomt met de opgegeven omgeving al bestaat voor hergebruik.

Daarnaast gebruikt de service automatisch een van de op Ubuntu Linux gebaseerde [basis installatie kopieën](https://github.com/Azure/AzureML-Containers)en installeert de opgegeven Python-pakketten. De basis installatie kopie heeft CPU-en GPU-versies en u kunt de GPU-installatie `gpu_support=True`kopie opgeven door in te stellen.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"

# Specify GPU image
myenv.docker.gpu_support=True
```

> [!NOTE]
> Als u opgeeft `environment.python.user_managed_dependencies=False` wanneer u een aangepaste docker-installatie kopie gebruikt, wordt er een Conda-omgeving in de installatie kopie gemaakt en wordt de uitvoering in die omgeving uitgevoerd, in plaats van python-bibliotheken te gebruiken die u mogelijk hebt geïnstalleerd op de basis installatie kopie. Stel de para meter `True` in op om uw eigen geïnstalleerde pakketten te gebruiken.

## <a name="using-environments-for-training"></a>Omgevingen gebruiken voor training

Als u een trainings uitvoering wilt verzenden, moet u uw omgeving, het [berekenings doel](concept-compute-target.md) en het python-trainings script in een uitvoerings configuratie combi neren. een wrapper-object dat wordt gebruikt voor het verzenden van uitvoeringen.

Wanneer u een trainings uitvoering verzendt, kan het maken van een nieuwe omgeving enkele minuten duren, afhankelijk van de grootte van de vereiste afhankelijkheden. De omgevingen worden in de cache opgeslagen door de service, dus als de omgevings definitie ongewijzigd blijft, wordt de volledige instel tijd slechts eenmaal in rekening gebracht.

Hier volgt een voor beeld van een lokaal script dat u [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) als uw wrapper-object zou gebruiken.

```python
from azureml.core import Environment, ScriptRunConfig, Experiment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.compute_target = "local"

# Attach environment to run config
runconfig.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Als u de uitvoerings geschiedenis wilt uitschakelen of moment opnamen wilt uitvoeren, `ScriptRunConfig.run_config.history`gebruikt u de instelling onder.

Als u de omgeving niet opgeeft in de configuratie van de uitvoering, wordt door de service een standaard omgeving voor u gemaakt wanneer u de uitvoering verzendt.

### <a name="train-with-an-estimator"></a>Trainen met een estimator

Als u een [Estimator](how-to-train-ml-models.md) voor training gebruikt, kunt u het Estimator-exemplaar gewoon rechtstreeks indienen, omdat het de omgeving en het reken doel al heeft ingekapseld.

Hieronder wordt een Estimator gebruikt voor een training met één knoop punt dat wordt uitgevoerd op een externe Compute voor een scikit-leer model, en wordt ervan uitgegaan dat `compute_target` er een eerder gemaakt `ds`Compute-doel object en een Data Store-object is.

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

U kunt omgevingen gebruiken bij het implementeren van uw model als een webservice. Hierdoor kan een reproduceer bare, verbonden werk stroom worden gebruikt voor het trainen, testen en implementeren van uw model met exact dezelfde bibliotheken in uw training en voor het afnemen van interferenties.

Als u een webservice wilt implementeren, moet u de omgeving combi neren, reken kracht, Score script en geregistreerd model in uw implementatie object [implementeren ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-). Meer informatie over het [implementeren van webservices](how-to-deploy-and-where.md).

In dit voor beeld wordt ervan uitgegaan dat u een trainings uitvoering hebt voltooid die dat model wilt implementeren in een Azure container instance (ACI). Bij het bouwen van de webservice worden het model en de Score bestanden gekoppeld aan de installatie kopie en wordt de Azure Machine Learning ring stack toegevoegd aan de installatie kopie.

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

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Voor het trainen](tutorial-train-models-with-aml.md) van een model wordt gebruikgemaakt van een beheerd reken doel om een model te trainen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de naslag informatie voor de SDK van de [omgevings klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) .

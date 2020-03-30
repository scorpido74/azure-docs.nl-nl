---
title: Herbruikbare ML-omgevingen maken
titleSuffix: Azure Machine Learning
description: Omgevingen maken en beheren voor modeltraining en -implementatie. Python-pakketten en andere instellingen voor de omgeving beheren.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 3b1fc5dc427a8a9a1987b0ef916b99edb25e292a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063969"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Omgevingen opnieuw gebruiken voor training en implementatie met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leest u hoe u Azure Machine [Learning-omgevingen maakt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)en beheert. Gebruik de omgevingen om de softwareafhankelijkheden van uw projecten te volgen en te reproduceren terwijl ze evolueren.

Softwareafhankelijkheidsbeheer is een veelvoorkomende taak voor ontwikkelaars. U wilt ervoor zorgen dat builds reproduceerbaar zijn zonder uitgebreide handmatige softwareconfiguratie. De klasse `Environment` Azure Machine Learning is verantwoordelijk voor lokale ontwikkelingsoplossingen zoals pip en Conda en biedt een oplossing voor zowel lokale als gedistribueerde cloudontwikkeling.

De voorbeelden in dit artikel laten zien hoe:

* Maak een omgeving en geef pakketafhankelijkheden op.
* Omgevingen ophalen en bijwerken.
* Gebruik een omgeving voor training.
* Gebruik een omgeving voor de implementatie van webservices.

Zie [Wat zijn ML-omgevingen voor](concept-environments.md)een overzicht op hoog niveau van hoe omgevingen werken in Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

* De [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Een omgeving maken

In de volgende secties worden de verschillende manieren verkend waarop u een omgeving voor uw experimenten maken.

### <a name="use-a-curated-environment"></a>Een samengestelde omgeving gebruiken

U een van de samengestelde omgevingen selecteren om mee te beginnen: 

* De _AzureML-Minimal-omgeving_ bevat een minimale set pakketten om het bijhouden en uploaden van activa mogelijk te maken. U het gebruiken als uitgangspunt voor uw eigen omgeving.

* De _azureml-zelf-zelf-omgeving_ bevat algemene data science-pakketten. Deze pakketten omvatten Scikit-Learn, Panda's, Matplotlib en een grotere set azureml-sdk-pakketten.

Samengestelde omgevingen worden ondersteund door Docker-afbeeldingen in de cache. Deze backing vermindert de kosten voor de voorbereiding van de run.

Gebruik `Environment.get` de methode om een van de samengestelde omgevingen te selecteren:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

U de samengestelde omgevingen en hun pakketten aanbieden met behulp van de volgende code:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Start uw eigen omgevingsnaam niet met het _AzureML-voorvoegsel._ Dit voorvoegsel is gereserveerd voor samengestelde omgevingen.

### <a name="instantiate-an-environment-object"></a>Een omgevingsobject instantiëren

Als u handmatig een omgeving `Environment` wilt maken, importeert u de klasse uit de SDK. Gebruik vervolgens de volgende code om een omgevingsobject te instantiëren.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Conda- en pip-specificatiebestanden gebruiken

U ook een omgeving maken op basis van een Conda-specificatie of een pip-vereistenbestand. Gebruik [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) de methode [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) of de methode. Vermeld in het argument methode uw omgevingsnaam en het bestandspad van het gewenste bestand.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Bestaande Conda-omgevingen gebruiken

Als u een bestaande Conda-omgeving op uw lokale computer hebt, u de service gebruiken om een omgevingsobject te maken. Door deze strategie te gebruiken, u uw lokale interactieve omgeving opnieuw gebruiken op externe runs.

Met de volgende code wordt een omgevingsobject `mycondaenv`gemaakt uit de bestaande Conda-omgeving . Het maakt [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) gebruik van de methode.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Omgevingen automatisch maken

Maak automatisch een omgeving door een trainingsrun in te dienen. Verzend de run `submit()` met behulp van de methode. Wanneer u een trainingsrun indient, kan de bouw van de nieuwe omgeving enkele minuten duren. De bouwduur is afhankelijk van de grootte van de vereiste afhankelijkheden. 

Als u geen omgeving opgeeft in uw runconfiguratie voordat u de run verzendt, wordt er een standaardomgeving voor u gemaakt.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Als u een [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) object gebruikt voor training, u de schatterinstantie ook rechtstreeks als een uitvoering indienen zonder een omgeving op te geven. Het `Estimator` object bevat al de omgeving en het rekendoel.

## <a name="add-packages-to-an-environment"></a>Pakketten toevoegen aan een omgeving

Voeg pakketten toe aan een omgeving met behulp van Conda-, pip- of privéwielbestanden. Geef elke pakketafhankelijkheid op [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) met behulp van de klasse. Voeg het toe aan `PythonSection`de omgeving.

### <a name="conda-and-pip-packages"></a>Conda en pip pakketten

Als een pakket beschikbaar is in een Conda-pakketopslagplaats, raden we u aan de Conda-installatie te gebruiken in plaats van de pip-installatie. Conda-pakketten worden meestal geleverd met vooraf gebouwde binaire bestanden die de installatie betrouwbaarder maken.

Het volgende voorbeeld wordt toegevoegd aan de omgeving. Het voegt versie 0.21.3 van `scikit-learn`. Het voegt `pillow` ook `myenv`het pakket, . Het voorbeeld [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) maakt gebruik [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) van de methode en de methode, respectievelijk.

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

>[!IMPORTANT]
> Als u dezelfde omgevingsdefinitie voor een andere run gebruikt, gebruikt de Azure Machine Learning-service de afbeelding in de cache van uw omgeving opnieuw. Als u bijvoorbeeld ```numpy```een omgeving maakt met een niet-vastgemaakte pakketafhankelijkheid, blijft die omgeving de pakketversie gebruiken die is geïnstalleerd op het moment van het maken van de _omgeving._ Ook elke toekomstige omgeving met overeenkomende definitie zal blijven gebruiken van de oude versie. Zie [Milieugebouw, caching en hergebruik](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)voor meer informatie.

### <a name="private-wheel-files"></a>Privéwielbestanden

U privépipwielbestanden gebruiken door ze eerst te uploaden naar uw werkruimteopslag. Je uploadt ze [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) met behulp van een statische methode. Vervolgens leg je de opslag-URL `add_pip_package()` vast en geef je de URL door aan de methode.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Omgevingen beheren

Beheer omgevingen zodat u ze bijwerken, bijhouden en opnieuw gebruiken voor rekendoelen en met andere gebruikers van de werkruimte.

### <a name="register-environments"></a>Omgevingen registreren

De omgeving wordt automatisch geregistreerd bij uw werkruimte wanneer u een run verzendt of een webservice implementeert. U de omgeving ook handmatig [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) registreren met behulp van de methode. Deze bewerking maakt de omgeving tot een entiteit die wordt bijgehouden en in de cloud is geversioned. De entiteit kan worden gedeeld tussen gebruikers van de werkruimte.

De volgende code `myenv` registreert `ws` de omgeving in de werkruimte.

```python
myenv.register(workspace=ws)
```

Wanneer u de omgeving voor het eerst gebruikt in training of implementatie, wordt deze geregistreerd bij de werkruimte. Vervolgens wordt het gebouwd en geïmplementeerd op het rekendoel. De service caches de omgevingen. Het opnieuw gebruiken van een cacheomgeving kost veel minder tijd dan het gebruik van een nieuwe service of een service die is bijgewerkt.

### <a name="get-existing-environments"></a>Bestaande omgevingen weergeven

De `Environment` klasse biedt methoden waarmee u bestaande omgevingen in uw werkruimte ophalen. U omgevingen ophalen op naam, als lijst of op basis van een specifieke trainingsrun. Deze informatie is handig voor het oplossen van problemen, controle en reproduceerbaarheid.

#### <a name="view-a-list-of-environments"></a>Een lijst met omgevingen weergeven

Bekijk de omgevingen in [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) uw werkruimte met behulp van de klasse. Selecteer vervolgens een omgeving die u wilt hergebruiken.

#### <a name="get-an-environment-by-name"></a>Een omgeving bij naam krijgen

U ook een specifieke omgeving op naam en versie krijgen. De volgende code [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) gebruikt de `1` methode `myenv` om `ws` de versie van de omgeving op de werkruimte op te halen.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Een run-specifieke omgeving trainen

Gebruik de [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) methode in de `Run` klas om de omgeving te krijgen die voor een specifieke run is gebruikt nadat de training is voltooid.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Een bestaande omgeving bijwerken

Stel dat u een bestaande omgeving wijzigt, bijvoorbeeld door een Python-pakket toe te voegen. Er wordt vervolgens een nieuwe versie van de omgeving gemaakt wanneer u een run verzendt, een model implementeert of de omgeving handmatig registreert. Met de versiebeheer u de wijzigingen van de omgeving in de loop van de tijd bekijken.

Als u een Python-pakketversie wilt bijwerken in een bestaande omgeving, geeft u het versienummer voor dat pakket op. Als u het exacte versienummer niet gebruikt, gebruikt Azure Machine Learning de bestaande omgeving opnieuw met de oorspronkelijke pakketversies.

### <a name="debug-the-image-build"></a>Foutopsporing van de afbeeldingsopbouw

In het volgende [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) voorbeeld wordt de methode gebruikt om handmatig een omgeving te maken als dockerafbeelding. Het controleert de uitvoerlogboeken van [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)de afbeelding te bouwen met behulp van . De ingebouwde afbeelding wordt vervolgens weergegeven in de instantie Azure Container Registry van de werkruimte. Deze informatie is handig voor het debuggen.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Docker inschakelen

 Met [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) de klasse `Environment` Azure Machine Learning u het gastbesturingssysteem waarop u uw training uitvoert, nauwkeurig aanpassen en beheren.

Wanneer u Docker inschakelt, bouwt de service een Docker-afbeelding. Het creëert ook een Python-omgeving die uw specificaties gebruikt in die Docker-container. Deze functionaliteit biedt extra isolatie en reproduceerbaarheid voor uw trainingsruns.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Standaard wordt de nieuw gebouwde Docker-afbeelding weergegeven in het containerregister dat is gekoppeld aan de werkruimte.  De naam van de opslagplaats heeft het formulier *azureml/azureml_\<\>uuid*. Het unieke*id-gedeelte*van de naam komt overeen met een hash die wordt berekend vanuit de omgevingsconfiguratie. Deze correspondentie stelt de service in staat om te bepalen of een afbeelding voor de gegeven omgeving al bestaat voor hergebruik.

Bovendien maakt de service automatisch gebruik van een van de Op Ubuntu Linux gebaseerde [basisafbeeldingen.](https://github.com/Azure/AzureML-Containers) Het installeert de opgegeven Python-pakketten. Het basisbeeld heeft CPU-versies en GPU-versies. Azure Machine Learning detecteert automatisch welke versie moet worden gebruikt.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

U ook een aangepast Dockerfile opgeven. Het is het eenvoudigst om te beginnen met ```FROM``` een van Azure Machine Learning basisafbeeldingen met de opdracht Docker en vervolgens uw eigen aangepaste stappen toe te voegen. Gebruik deze aanpak als u niet-Python-pakketten als afhankelijkheden moet installeren.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Door de gebruiker beheerde afhankelijkheden gebruiken

In sommige situaties bevat uw aangepaste basisafbeelding mogelijk al een Python-omgeving met pakketten die u wilt gebruiken.

De Azure Machine Learning-service bouwt standaard een Conda-omgeving met door u opgegeven afhankelijkheden en voert de uitvoering uit in die omgeving in plaats van python-bibliotheken te gebruiken die u op de basisafbeelding hebt geïnstalleerd. 

Als u uw eigen geïnstalleerde `Environment.python.user_managed_dependencies = True`pakketten wilt gebruiken, stelt u de parameter in. Zorg ervoor dat de basisafbeelding een Python-tolk bevat en de pakketten bevat die uw trainingsscript nodig heeft.

Als u bijvoorbeeld wilt uitvoeren in een basisMiniconda-omgeving waarop het NumPy-pakket is geïnstalleerd, geeft u eerst een Dockerfile op met een stap om het pakket te installeren. Stel vervolgens de door de `True`gebruiker beheerde afhankelijkheden in op . 

U ook een pad naar een specifieke Python-tolk in de afbeelding opgeven door de `Environment.python.interpreter_path` variabele in te stellen.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Omgevingen gebruiken voor training

Als u een trainingsrun wilt indienen, moet u uw omgeving, [rekendoel](concept-compute-target.md)en uw Python-script trainen combineren tot een run-configuratie. Deze configuratie is een wrapperobject dat wordt gebruikt voor het verzenden van uitvoeringen.

Wanneer u een trainingsrun indient, kan het bouwen van een nieuwe omgeving enkele minuten duren. De duur is afhankelijk van de grootte van de vereiste afhankelijkheden. De omgevingen worden in de cache opgeslagen door de service. Dus zolang de omgevingsdefinitie ongewijzigd blijft, hoeft u slechts één keer de volledige insteltijd te maken.

In het volgende voorbeeld van lokale [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) scriptrun ziet u waar u het wrapperobject gebruikt.

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
> Als u de runsgeschiedenis wilt uitschakelen `ScriptRunConfig.run_config.history`of momentopnamen wilt uitvoeren, gebruikt u de instelling onder .

Als u de omgeving in uw run-configuratie niet opgeeft, maakt de service een standaardomgeving wanneer u uw uitvoering verzendt.

### <a name="use-an-estimator-for-training"></a>Gebruik een schatter voor training

Als u een [schatter](how-to-train-ml-models.md) gebruikt voor training, u de schatterinstantie rechtstreeks indienen. Het bevat al de omgeving en het rekendoel.

De volgende code maakt gebruik van een schatter voor een training met één knooppunt. Het draait op een `scikit-learn` externe compute voor een model. Er wordt van uitgegaan dat u `compute_target`eerder een compute target `ds`object hebt gemaakt, en een datastore object, .

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

## <a name="use-environments-for-web-service-deployment"></a>Omgevingen gebruiken voor implementatie van webservices

U omgevingen gebruiken wanneer u uw model implementeert als een webservice. Deze mogelijkheid maakt een reproduceerbare, verbonden workflow mogelijk. In deze werkstroom u uw model trainen, testen en implementeren met dezelfde bibliotheken in zowel uw trainingsgegevensberekening als uw gevolgtrekking.

Als u een webservice wilt implementeren, combineert u de omgeving, de [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)conclusieberekening, het scorescript en het geregistreerde model in uw implementatieobject. Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

Ga er in dit voorbeeld van uit dat u een trainingsrun hebt voltooid. Nu wilt u dat model implementeren in Azure Container Instances. Wanneer u de webservice bouwt, worden het model en de scorebestanden op de afbeelding gemonteerd en wordt de azure machine learning-inferencestack aan de afbeelding toegevoegd.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Voorbeeldnotitieblokken

In [dit voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) wordt uitgebreid met concepten en methoden die in dit artikel worden gedemonstreerd.

[Implementeer een model met behulp van een aangepaste Docker-basisafbeelding](how-to-deploy-custom-docker-image.md) laat zien hoe u een model implementeert met behulp van een aangepaste Docker-basisafbeelding.

In [dit voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) wordt uitgelegd hoe u een Spark-model als webservice implementeert.

## <a name="create-and-manage-environments-with-the-cli"></a>Omgevingen maken en beheren met de CLI

De [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) weerspiegelt het grootste deel van de functionaliteit van de Python SDK. U het gebruiken om omgevingen te maken en te beheren. De opdrachten die we bespreken in deze sectie tonen basisfunctionaliteit.

Met de volgende opdracht worden de bestanden voor een standaardomgevingsdefinitie in de opgegeven map weergegeven. Deze bestanden zijn JSON-bestanden. Ze werken als de overeenkomstige klasse in de SDK. U de bestanden gebruiken om nieuwe omgevingen te maken met aangepaste instellingen. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Voer de volgende opdracht uit om een omgeving te registreren vanuit een opgegeven map.

```azurecli-interactive
az ml environment register -d myenvdir
```

Voer de volgende opdracht uit om alle geregistreerde omgevingen weer te geven.

```azurecli-interactive
az ml environment list
```

Download een geregistreerde omgeving met behulp van de volgende opdracht.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: Een model trainen](tutorial-train-models-with-aml.md)als u een beheerd rekendoel wilt gebruiken om een model te trainen.
* Nadat u een getraind model hebt, leert u [hoe en waar u modellen implementeren.](how-to-deploy-and-where.md)
* Bekijk de [ `Environment` klasse SDK-referentie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zie het [voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments)voor meer informatie over de concepten en methoden die in dit artikel worden beschreven.

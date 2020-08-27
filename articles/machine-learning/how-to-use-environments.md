---
title: Software omgevingen gebruiken
titleSuffix: Azure Machine Learning
description: Omgevingen maken en beheren voor model training en-implementatie. Python-pakketten en andere instellingen voor de omgeving beheren.
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3368a42248e084476eb27318abbcd1ca9fbfdacf
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927541"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Software omgevingen maken & gebruiken in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning [omgevingen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)kunt maken en beheren. Gebruik de omgevingen om de software afhankelijkheden van uw projecten bij te houden en te reproduceren tijdens het ontwikkelen.

Beheer van de software-afhankelijkheid is een algemene taak voor ontwikkel aars. U wilt er zeker van zijn dat de builds worden verreproduceerd zonder uitgebreide hand matige software configuratie. De Azure Machine Learning- `Environment` klassen accounts voor lokale ontwikkel oplossingen, zoals PIP en Conda en gedistribueerde Cloud ontwikkeling via docker-mogelijkheden.

In de voor beelden in dit artikel wordt uitgelegd hoe u:

* Een omgeving maken en pakket afhankelijkheden opgeven.
* Omgevingen ophalen en bijwerken.
* Een omgeving gebruiken voor training.
* Gebruik een-omgeving voor de implementatie van webservices.

Zie [Wat zijn omgevingen van ml?](concept-environments.md) voor een overzicht van hoe omgevingen werken in azure machine learning. Zie [hier](how-to-configure-environment.md)voor meer informatie over het configureren van ontwikkel omgevingen.

## <a name="prerequisites"></a>Vereisten

* De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Een omgeving maken

In de volgende secties worden de verschillende manieren besproken waarop u een omgeving kunt maken voor uw experimenten.

### <a name="use-a-curated-environment"></a>Een gecuratore omgeving gebruiken

Een gehoste omgeving bevat verzamelingen van Python-pakketten en is standaard beschikbaar in uw werk ruimte. Deze omgevingen worden ondersteund door docker-installatie kopieën in de cache, waarmee de kosten voor het uitvoeren van de voor bereiding worden verminderd. U kunt een van deze populaire gestarte omgevingen selecteren om te beginnen met: 

* De omgeving met _AzureML-minimale_ bevat een minimale set pakketten voor het inschakelen van tracking en het uploaden van activa. U kunt deze gebruiken als uitgangs punt voor uw eigen omgeving.

* De omgeving voor de _AzureML-zelf studie_ bevat algemene data Science-pakketten. Deze pakketten bevatten Scikit-learn, Pandas, matplotlib en een grotere set van azureml-SDK-pakketten.

Zie het artikel over de gedocumenteerde [omgevingen](resource-curated-environments.md)voor een lijst met de gecuratore omgevingen.

Gebruik de- `Environment.get` methode om een van de met de curator gevoerde omgevingen te selecteren:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

U kunt de gegroepeerde omgevingen en de bijbehorende pakketten weer geven met behulp van de volgende code:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Start uw eigen omgevings naam niet met het voor voegsel voor _AzureML_ . Dit voor voegsel is gereserveerd voor gecuratore omgevingen.


### <a name="instantiate-an-environment-object"></a>Een omgevings object instantiëren

Importeer de `Environment` klasse vanuit de SDK om hand matig een omgeving te maken. Gebruik vervolgens de volgende code om een omgevings object te instantiëren.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

Als u uw eigen omgeving definieert, moet u een lijst maken `azureml-defaults` met versie >= 1.0.45 als een PIP-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

### <a name="use-conda-and-pip-specification-files"></a>Conda-en PIP-specificatie bestanden gebruiken

U kunt een omgeving maken op basis van een Conda-specificatie of een PIP-vereisten bestand. Gebruik de [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) methode of de- [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) methode. Neem in het argument methode de naam van uw omgeving en het bestandspad van het gewenste bestand op. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="use-existing-environments"></a>Bestaande omgevingen gebruiken

Als u een bestaande Conda-omgeving hebt op uw lokale computer, kunt u de service gebruiken om een omgevings object te maken. Door deze strategie te gebruiken, kunt u uw lokale interactieve omgeving opnieuw gebruiken op externe uitvoeringen.

Met de volgende code wordt een omgevings object gemaakt op basis van de bestaande Conda-omgeving `mycondaenv` . De methode wordt gebruikt [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) .

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

Een omgevings definitie kan worden opgeslagen in een map in een gemakkelijk te bewerken indeling met de- [`save_to_directory()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#save-to-directory-path--overwrite-false-) methode. Na wijziging kan een nieuwe omgeving worden geïnstantieerd door bestanden uit de map te laden.

```python
myenv = Environment.save_to_directory(path = "path-to-destination-directory", overwrite = False)
# modify the environment definition
newenv = Environment.load_from_directory(path = "path-to-source-directory")
```

### <a name="create-environments-automatically"></a>Automatisch omgevingen maken

Automatisch een omgeving maken door een trainings uitvoering in te dienen. Verzend de uitvoering met behulp van de- `submit()` methode. Wanneer u een training uitvoert, kan het maken van de nieuwe omgeving enkele minuten duren. De duur van de build is afhankelijk van de grootte van de vereiste afhankelijkheden. 

Als u geen omgeving opgeeft in uw uitvoerings configuratie voordat u de uitvoering verzendt, wordt er een standaard omgeving voor u gemaakt.

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

Op dezelfde manier kunt u, als u een [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) object gebruikt voor training, het Estimator-exemplaar rechtstreeks als een uitvoering verzenden zonder een omgeving op te geven. Het `Estimator` object heeft de omgeving en het reken doel al ingekapseld.

## <a name="add-packages-to-an-environment"></a>Pakketten toevoegen aan een omgeving

Voeg pakketten toe aan een omgeving met behulp van Conda-, PIP-of privé-wiel bestanden. Geef elke pakket afhankelijkheid op met behulp van de- [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) klasse. Voeg deze toe aan de omgeving `PythonSection` .

### <a name="conda-and-pip-packages"></a>Conda-en PIP-pakketten

Als een pakket beschikbaar is in een Conda-pakket opslagplaats, is het raadzaam om de installatie van Conda te gebruiken in plaats van de PIP-installatie. Conda-pakketten worden meestal geleverd met vooraf gemaakte binaire bestanden die de installatie betrouwbaarder maken.

In het volgende voor beeld wordt toegevoegd aan de omgeving `myenv` . Er wordt versie 1.17.0 van toegevoegd `numpy` . Het pakket wordt ook toegevoegd `pillow` . In het voor beeld worden [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) respectievelijk de methode en de [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) methode gebruikt.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

U kunt ook omgevings variabelen toevoegen aan uw omgeving. Deze worden vervolgens beschikbaar via OS. Environ. Ga naar het trainings script.

```python
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

>[!IMPORTANT]
> Als u dezelfde omgevings definitie gebruikt voor een andere uitvoering, gebruikt de Azure Machine Learning-service de in de cache opgeslagen afbeelding van uw omgeving opnieuw. Als u een omgeving met een niet-vastgemaakte pakket afhankelijkheid maakt, blijft ```numpy``` die omgeving de pakket versie gebruiken die is geïnstalleerd _op het moment dat de omgeving wordt gemaakt_. Daarnaast blijft de oude versie van alle toekomstige omgevingen met de overeenkomende definitie bewaard. Zie [omgeving bouwen, in cache plaatsen en opnieuw gebruiken](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)voor meer informatie.

### <a name="private-python-packages"></a>Privé Python-pakketten

Als u Python-pakketten privé en veilig wilt gebruiken zonder ze aan het open bare Internet bloot te stellen, raadpleegt u het artikel [How to use private python packages](how-to-use-private-python-packages.md).

## <a name="manage-environments"></a>Omgevingen beheren

Omgevingen beheren, zodat u ze kunt bijwerken, volgen en opnieuw gebruiken in reken doelen en met andere gebruikers van de werk ruimte.

### <a name="register-environments"></a>Omgevingen registreren

De omgeving wordt automatisch bij uw werk ruimte geregistreerd wanneer u een uitvoeren of een webservice implementeert. U kunt de omgeving ook hand matig registreren met behulp van de- [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) methode. Met deze bewerking wordt de omgeving omgezet in een entiteit die wordt bijgehouden en geversiond in de Cloud. De entiteit kan worden gedeeld tussen werk ruimte-gebruikers.

Met de volgende code wordt de `myenv` omgeving geregistreerd bij de `ws` werk ruimte.

```python
myenv.register(workspace=ws)
```

Wanneer u de omgeving voor de eerste keer gebruikt in training of implementatie, wordt deze geregistreerd bij de werk ruimte. Vervolgens wordt het gebouwd en geïmplementeerd op het berekenings doel. De service slaat de omgevingen in de cache op. Het opnieuw gebruiken van een omgeving in de cache neemt veel minder tijd in beslag dan het gebruik van een nieuwe service of een update die is bijgewerkt.

### <a name="get-existing-environments"></a>Bestaande omgevingen ophalen

De `Environment` klasse biedt methoden waarmee u bestaande omgevingen in uw werk ruimte kunt ophalen. U kunt omgevingen ophalen op naam, als een lijst of door een specifieke training uit te voeren. Deze informatie is nuttig voor het oplossen van problemen, controles en reproduceer baarheid.

#### <a name="view-a-list-of-environments"></a>Een lijst met omgevingen weer geven

Bekijk de omgevingen in uw werk ruimte met behulp van de- [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) klasse. Selecteer vervolgens een omgeving die u opnieuw wilt gebruiken.

#### <a name="get-an-environment-by-name"></a>Een omgeving op naam ophalen

U kunt ook een specifieke omgeving verkrijgen op naam en versie. De volgende code gebruikt de [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) methode om de versie `1` van de `myenv` omgeving op te halen in de `ws` werk ruimte.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Een run-specifieke omgeving trainen

Gebruik de methode in de-klasse om de omgeving op te halen die voor een specifieke uitvoering is gebruikt nadat de training is voltooid [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) `Run` .

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Een bestaande omgeving bijwerken

Stel dat u een bestaande omgeving wijzigt, bijvoorbeeld door het toevoegen van een python-pakket. Dit zal tijd duren als er een nieuwe versie van de omgeving wordt gemaakt wanneer u een uitvoering verzendt, een model implementeert of de omgeving hand matig registreert. Met versie beheer kunt u de wijzigingen van de omgeving gedurende een periode bekijken. 

Als u een python-pakket versie in een bestaande omgeving wilt bijwerken, geeft u het versie nummer voor dat pakket op. Als u niet het exacte versie nummer gebruikt, wordt de bestaande omgeving door Azure Machine Learning hergebruikt met de oorspronkelijke pakket versies.

### <a name="debug-the-image-build"></a>Fouten opsporen in de installatie kopie maken

In het volgende voor beeld wordt de [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) methode gebruikt om hand matig een omgeving te maken als docker-installatie kopie. Hiermee worden de uitvoer logboeken van de installatie kopie gecontroleerd met behulp van [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) . De ingebouwde installatie kopie wordt vervolgens weer gegeven in de Azure Container Registry-instantie van de werk ruimte. Deze informatie is nuttig voor het opsporen van fouten.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

Het is handig eerst installatie kopieën lokaal te bouwen met behulp van de- [`build_local()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-local-workspace--platform-none----kwargs-) methode. Stel de optionele para meter in om een docker-installatie kopie te maken `useDocker=True` . Als u de resulterende installatie kopie naar het container register van de AzureML-werk ruimte wilt pushen, stelt u in `pushImageToWorkspaceAcr=True` .

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  Het wijzigen van de volg orde van afhankelijkheden of kanalen in een omgeving resulteert in een nieuwe omgeving en moet een nieuwe installatie kopie maken. Als u de `build()` methode voor een bestaande installatie kopie aanroept, worden ook de afhankelijkheden bijgewerkt als er nieuwe versies zijn. 

## <a name="enable-docker"></a>Docker inschakelen

Docker-container biedt een efficiënte manier om de afhankelijkheden te integreren. Wanneer u docker inschakelt, bouwt Azure ML een docker-installatie kopie en maakt een python-omgeving in die container, op basis van uw specificaties. De docker-installatie kopieën worden in de cache opgeslagen en opnieuw gebruikt: de eerste uitvoering in een nieuwe omgeving duurt doorgaans langer naarmate de installatie kopie wordt gemaakt.

[`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py)Met de van de Azure machine learning- `Environment` klasse kunt u het gast besturingssysteem waarmee u uw training uitvoert, nauw keurig aanpassen en beheren. De `arguments` variabele kan worden gebruikt om extra argumenten op te geven die moeten worden door gegeven aan de opdracht docker run.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Standaard wordt de zojuist gemaakte docker-installatie kopie weer gegeven in het container register dat is gekoppeld aan de werk ruimte.  De naam van de opslag plaats heeft de notatie *azureml/azureml_ \<uuid\> *. Het*uuid*-gedeelte van de naam komt overeen met een hash die wordt berekend op basis van de configuratie van de omgeving. Met deze correspondentie kan de service bepalen of een installatie kopie voor de opgegeven omgeving al bestaat voor hergebruik.

### <a name="use-a-custom-docker-image-or-dockerfile"></a>Een aangepaste docker-installatie kopie of Dockerfile gebruiken 
De service gebruikt automatisch een van de op Ubuntu Linux gebaseerde [basis installatie kopieën](https://github.com/Azure/AzureML-Containers). De opgegeven Python-pakketten worden geïnstalleerd. De basis installatie kopie heeft CPU-versies en GPU-versies. Azure Machine Learning detecteert automatisch welke versie moet worden gebruikt. Het is ook mogelijk om een [aangepaste docker-basis installatie kopie](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image#create-a-custom-base-image)te gebruiken.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning ondersteunt alleen docker-installatie kopieën die de volgende software bieden:
> * Ubuntu 16,04 of hoger.
> * Conda 4.5. # of hoger.
> * Python 3.5. #, 3.6. # of 3.7. #.

U kunt ook een aangepaste Dockerfile opgeven. Het is eenvoudig om te beginnen met een van Azure Machine Learning basis installatie kopieën met behulp van docker ```FROM``` -opdracht en vervolgens uw eigen aangepaste stappen toe te voegen. Gebruik deze methode als u niet-python-pakketten als afhankelijkheden moet installeren. Vergeet niet om de basis installatie kopie in te stellen op geen.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

### <a name="specify-your-own-python-interpreter"></a>Uw eigen Python-interpreter opgeven

In sommige gevallen bevat uw aangepaste basis installatie kopie mogelijk al een python-omgeving met pakketten die u wilt gebruiken.

Stel de para meter in om uw eigen geïnstalleerde pakketten te gebruiken en Conda uit te scha kelen `Environment.python.user_managed_dependencies = True` . Zorg ervoor dat de basis installatie kopie een Python-interpreter bevat en dat de pakketten uw trainings script nodig hebben.

Als u bijvoorbeeld in een basis Miniconda-omgeving wilt uitvoeren waarop NumPy-pakket is geïnstalleerd, moet u eerst een Dockerfile met een stap opgeven om het pakket te installeren. Stel vervolgens de door de gebruiker beheerde afhankelijkheden in op `True` . 

U kunt ook een pad naar een specifieke Python-interpreter in de installatie kopie opgeven door de variabele in te stellen `Environment.python.interpreter_path` .

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

> [!WARNING]
> Als u een aantal python-afhankelijkheden in uw docker-installatie kopie installeert en vergeet om user_managed_dependencies = True in te stellen, worden deze pakketten niet in de uitvoerings omgeving opgenomen, waardoor er runtime fouten optreden. Azure ML bouwt standaard een Conda-omgeving met afhankelijkheden die u hebt opgegeven en voert de uitvoering in die omgeving uit in plaats van python-bibliotheken te gebruiken die u hebt geïnstalleerd op de basis installatie kopie.

### <a name="retrieve-image-details"></a>Details van installatie kopie ophalen

Voor een geregistreerde omgeving kunt u details van de installatie kopie ophalen met behulp van de volgende code, waarbij `details` een instantie van [DockerImageDetails](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockerimagedetails?view=azure-ml-py) is (AzureML python SDK >= 1,11) en alle informatie bevat over de omgevings afbeelding, zoals de dockerfile, het REGI ster en de naam van de installatie kopie.

```python
details = environment.get_image_details(workspace=ws)
```

## <a name="use-environments-for-training"></a>Omgevingen gebruiken voor training

Als u een trainings uitvoering wilt verzenden, moet u uw omgeving, het [reken doel](concept-compute-target.md)en het python-script van uw training combi neren in een uitvoerings configuratie. Deze configuratie is een wrapper-object dat wordt gebruikt voor het verzenden van uitvoeringen.

Wanneer u een trainings uitvoering verzendt, kan het maken van een nieuwe omgeving enkele minuten duren. De duur is afhankelijk van de grootte van de vereiste afhankelijkheden. De omgevingen worden in de cache opgeslagen door de service. Als de omgevings definitie ongewijzigd blijft, maakt u de volledige instel tijd slechts één keer.

In het volgende voor beeld van een lokaal script wordt aangegeven waar u kunt gebruiken [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) als uw wrapper-object.

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
> Als u de uitvoerings geschiedenis wilt uitschakelen of moment opnamen wilt uitvoeren, gebruikt u de instelling onder `ScriptRunConfig.run_config.history` .

Als u de omgeving niet opgeeft in de configuratie van de uitvoering, wordt door de service een standaard omgeving gemaakt wanneer u de uitvoering verzendt.

### <a name="use-an-estimator-for-training"></a>Een Estimator gebruiken voor training

Als u een [Estimator](how-to-train-ml-models.md) voor training gebruikt, kunt u het Estimator-exemplaar rechtstreeks verzenden. De omgeving en het doel van de berekening worden al ingekapseld.

De volgende code maakt gebruik van een Estimator voor het uitvoeren van een training met één knoop punt. Het wordt uitgevoerd op een externe Compute voor een `scikit-learn` model. Hierbij wordt ervan uitgegaan dat u eerder een compute-doel object, `compute_target` en een Data Store-object hebt gemaakt `ds` .

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

## <a name="use-environments-for-web-service-deployment"></a>Omgevingen gebruiken voor de implementatie van webservices

U kunt omgevingen gebruiken wanneer u uw model als een webservice implementeert. Met deze mogelijkheid kan een reproduceer bare, verbonden werk stroom worden gemaakt. In deze werk stroom kunt u uw model trainen, testen en implementeren met behulp van dezelfde bibliotheken in zowel uw trainings Compute als uw berekenings berekening.

Als u een webservice wilt implementeren, combineert u de omgeving, verlaagt u de reken kracht, het Score script en het geregistreerde model in uw implementatie object [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) . Zie [hoe en waar modellen worden geïmplementeerd](how-to-deploy-and-where.md)voor meer informatie.

In dit voor beeld wordt ervan uitgegaan dat u een trainings uitvoering hebt voltooid. Nu wilt u dat model implementeren naar Azure Container Instances. Wanneer u de webservice bouwt, worden het model en de Score bestanden gekoppeld aan de installatie kopie en wordt de Azure Machine Learning ring stack toegevoegd aan de installatie kopie.

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

## <a name="notebooks"></a>Notebooks

Dit [artikel](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks#add-new-kernels) bevat informatie over het installeren van een Conda-omgeving als een kernel in een notebook.

[Een model implementeren met behulp van een aangepaste docker-basis installatie kopie](how-to-deploy-custom-docker-image.md) laat zien hoe u een model implementeert met behulp van een aangepaste docker-basis installatie kopie.

Dit [voor beeld](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) laat zien hoe u een Spark-model als een webservice implementeert.

## <a name="create-and-manage-environments-with-the-cli"></a>Omgevingen maken en beheren met de CLI

De [Azure machine learning cli](reference-azure-machine-learning-cli.md) komt grotendeels overeen met de functionaliteit van de PYTHON-SDK. U kunt deze gebruiken om omgevingen te maken en te beheren. De opdrachten die in deze sectie worden besproken, illustreren de basis functionaliteit.

Met de volgende opdracht worden de bestanden gesteigerd voor een standaard omgevings definitie in de opgegeven map. Deze bestanden zijn JSON-bestanden. Ze werken zoals de bijbehorende klasse in de SDK. U kunt de bestanden gebruiken om nieuwe omgevingen met aangepaste instellingen te maken. 

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

Down load een geregistreerde omgeving met behulp van de volgende opdracht.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een model trainen](tutorial-train-models-with-aml.md)als u een beheerd Compute-doel wilt gebruiken voor het trainen van een model.
* Nadat u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de [ `Environment` Naslag informatie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)voor de klasse-SDK.

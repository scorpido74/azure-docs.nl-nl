---
title: Handleiding voor het oplossen van problemen met de implementatie
titleSuffix: Azure Machine Learning
description: Meer informatie over het omzeilen, oplossen en oplossen van problemen met veelvoorkomende docker-implementatie fouten met de Azure Kubernetes-service en Azure Container Instances het gebruik van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: bf86826d77c690b60c7b091d6250a85fffd21fc0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896337"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Problemen met Azure Machine Learning Azure Kubernetes-service en Azure Container Instances-implementatie oplossen

Meer informatie over het oplossen van veelvoorkomende docker-implementatie fouten met Azure Container Instances (ACI) en Azure Kubernetes service (AKS) met behulp van Azure Machine Learning.

Bij het implementeren van een model in Azure Machine Learning, voert het systeem een aantal taken uit.

De aanbevolen en meest recente benadering voor model implementatie is via de API [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) met behulp van een [omgevings](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) object als invoer parameter. In dit geval maakt onze service een basis-docker-installatie kopie voor u tijdens de implementatie fase en koppelt u de vereiste modellen in één aanroep. De basis taken voor implementatie zijn:

1. Registreer het model in de werkruimte model-register.

2. Configuratie voor in-of afleiding definiëren:
    1. Maak een [omgevings](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) object op basis van de afhankelijkheden die u opgeeft in het yaml-bestand van de omgeving of gebruik een van onze aangeschafte omgevingen.
    2. Maak een Afleidings configuratie (InferenceConfig-object) op basis van de omgeving en het Score script.

3. Implementeer het model op de Azure container instance-service (ACI) of naar de Azure Kubernetes-service (AKS).

Meer informatie over dit proces in de [Modelbeheer](concept-model-management-and-deployment.md) inleiding.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* De [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Om lokaal fouten op te sporen, moet u een werkende docker-installatie op uw lokale systeem hebben.

    Als u de docker-installatie wilt controleren, gebruikt u de opdracht `docker run hello-world` vanaf een Terminal of opdracht prompt. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het installeren van docker of het oplossen van problemen met docker-fouten.

## <a name="before-you-begin"></a>Voordat u begint

Als u een probleem ondervindt, het eerste wat te doen is de Implementatietaak opdelen (vorige beschreven) in afzonderlijke stappen het probleem te isoleren.

Als u de nieuwe/Aanbevolen implementatie methode via [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) -API met een [omgevings](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) object als een invoer parameter gebruikt, kan uw code worden onderverdeeld in drie belang rijke stappen:

1. Registreer het model. Hier volgt een voor beeld van code:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Configuratie voor het afstellen van een afleiding definiëren voor implementatie:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Implementeer het model met behulp van de in de vorige stap gemaakte configuratie voor inactiviteit:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Nadat u het implementatieproces in afzonderlijke taken hebt gesplitst, kunnen we kijken naar enkele van de meest voorkomende fouten.

## <a name="debug-locally"></a>Lokaal fouten opsporen

Als u problemen ondervindt bij het implementeren van een model naar ACI of AKS, kunt u het implementeren als een lokale webservice. Het gebruik van een lokale webservice maakt het gemakkelijker om problemen op te lossen. De docker-installatie kopie met het model wordt gedownload en gestart op het lokale systeem.

> [!WARNING]
> Lokale web service-implementaties worden niet ondersteund voor productie scenario's.

Als u lokaal wilt implementeren, wijzigt u de code in `LocalWebservice.deploy_configuration()` om een implementatie configuratie te maken. Gebruik vervolgens `Model.deploy()` om de service te implementeren. In het volgende voor beeld wordt een model (opgenomen in de variabele model) geïmplementeerd als lokale webservice:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Houd er rekening mee dat als u uw eigen Conda-specificatie YAML definieert, u de waarden van azureml-standaard gebruikt met versie > = 1.0.45 als een PIP-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

Op dit moment kunt u de service als normaal gebruiken. De volgende code toont bijvoorbeeld de verzen ding van gegevens naar de service:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over het aanpassen van uw python-omgeving. 

### <a name="update-the-service"></a>De service bijwerken

Tijdens lokale tests moet u mogelijk het `score.py` bestand bijwerken om logboek registratie toe te voegen of om problemen op te lossen die u hebt gedetecteerd. Als u wijzigingen in het `score.py` bestand opnieuw wilt laden, gebruikt u `reload()`. Met de volgende code wordt bijvoorbeeld het script voor de service opnieuw geladen en vervolgens worden er gegevens naar verzonden. De gegevens worden gescoord met behulp van het bijgewerkte `score.py`-bestand:

> [!IMPORTANT]
> De `reload` methode is alleen beschikbaar voor lokale implementaties. Zie de sectie Update van [modellen implementeren](how-to-deploy-and-where.md#update)voor meer informatie over het bijwerken van een implementatie naar een ander reken doel.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Het script wordt opnieuw geladen vanaf de locatie die is opgegeven door het `InferenceConfig`-object dat door de service wordt gebruikt.

Als u het model, Conda afhankelijkheden of implementatie configuratie wilt wijzigen, gebruikt u [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). In het volgende voor beeld wordt het model bijgewerkt dat door de service wordt gebruikt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>De service verwijderen

Als u de service wilt verwijderen, gebruikt u [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a>Het docker-logboek controleren

U kunt gedetailleerde berichten voor Docker-engine van het serviceobject afdrukken. U kunt het logboek weer geven voor ACI-, AKS-en lokale implementaties. In het volgende voor beeld ziet u hoe u de logboeken afdrukt.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Service starten mislukt

Nadat de installatie kopie is gemaakt, probeert het systeem een container te starten met behulp van de implementatie configuratie. Als onderdeel van de container starten van proces, de `init()` functie in uw scoring-script wordt aangeroepen door het systeem. Als er niet-onderschepte uitzonderingen in de `init()` functioneren, ziet u mogelijk **CrashLoopBackOff** fout in het foutbericht.

Gebruik de informatie in de sectie [het docker-logbestand controleren](#dockerlog) om de logboeken te controleren.

## <a name="function-fails-get_model_path"></a>Functie mislukt: get_model_path()

Vaak wordt de functie [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) in de functie `init()` aangeroepen om een model bestand of een map met model bestanden in de container te vinden. Als het model bestand of de map niet kan worden gevonden, mislukt de functie. De eenvoudigste manier om op te sporen van deze fout is om uit te voeren de volgende Python-code in de Container-shell:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

In dit voor beeld wordt het lokale pad (ten opzichte van `/var/azureml-app`) afgedrukt in de container waarin uw score script het model bestand of de map verwacht te vinden. Vervolgens kunt u controleren of het bestand of map inderdaad waarop is naar verwachting.

Als het logboek registratie niveau wordt ingesteld op DEBUG, kan er extra informatie worden vastgelegd, wat nuttig kan zijn bij het identificeren van de fout.

## <a name="function-fails-runinput_data"></a>Functie mislukt: run(input_data)

Als de service is geïmplementeerd, maar deze loopt vast bij het plaatsen van gegevens naar het scoring-eindpunt, kunt u fout-instructie in afvangen toevoegen uw `run(input_data)` functie zodat het gedetailleerde foutbericht wordt weergegeven in plaats daarvan retourneert. Bijvoorbeeld:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Houd er rekening mee**: retourneren foutmeldingen vanuit de `run(input_data)` aanroep moet worden gedaan voor het opsporen van fouten in doel alleen. Uit veiligheids overwegingen moet u geen fout berichten op deze manier retour neren in een productie omgeving.

## <a name="http-status-code-503"></a>HTTP-status code 503

Azure Kubernetes service-implementaties ondersteunen automatisch schalen, zodat er replica's kunnen worden toegevoegd ter ondersteuning van extra belasting. De functie voor automatisch schalen is echter ontworpen voor het afhandelen van **geleidelijke** wijzigingen in de belasting. Als u grote pieken in aanvragen per seconde ontvangt, ontvangen clients mogelijk een HTTP-status code van 503.

Er zijn twee dingen die u kunnen helpen bij het voor komen van 503-status codes:

* Wijzig het gebruiks niveau waarop automatisch schalen nieuwe replica's maakt.
    
    Het doel gebruik voor automatisch schalen is standaard ingesteld op 70%, wat betekent dat de service pieken kan verwerken in aanvragen per seconde (RPS) van Maxi maal 30%. U kunt het gebruiks doel aanpassen door de `autoscale_target_utilization` in te stellen op een lagere waarde.

    > [!IMPORTANT]
    > Met deze wijziging worden er geen replica's *meer gemaakt.* In plaats daarvan worden ze gemaakt met een lagere drempel waarde voor het gebruik. In plaats van te wachten totdat de service 70% wordt gebruikt, kan het wijzigen van de waarde in 30% ertoe leiden dat er replica's worden gemaakt wanneer er sprake is van 30%.
    
    Als de webservice de huidige maximum replica's al gebruikt en u nog steeds 503-status codes ziet, verhoogt u de `autoscale_max_replicas` waarde om het maximale aantal replica's te verhogen.

* Wijzig het minimale aantal replica's. Het verg Roten van de minimale replica's biedt een grotere pool voor het afhandelen van de binnenkomende pieken.

    Als u het minimum aantal replica's wilt verhogen, stelt u `autoscale_min_replicas` in op een hogere waarde. U kunt de vereiste replica's berekenen met behulp van de volgende code en waarden vervangen door waarden die specifiek zijn voor uw project:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Als er pieken van aanvragen worden ontvangen die groter zijn dan de nieuwe minimum replica's kunnen worden verwerkt, kunt u 503s opnieuw ontvangen. Als het verkeer naar uw service toeneemt, is het mogelijk dat u de minimum replica's moet verg Roten.

Voor meer informatie over het instellen van `autoscale_target_utilization`, `autoscale_max_replicas`en `autoscale_min_replicas` voor, raadpleegt u de [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) -module verwijzing.

## <a name="advanced-debugging"></a>Geavanceerde fout opsporing

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die in uw model implementatie is opgenomen. Als het script voor de vermelding bijvoorbeeld mislukt en de reden niet kan worden bepaald door aanvullende logboek registratie. Met Visual Studio code en de Python Tools for Visual Studio (PTVSD) kunt u koppelen aan de code die wordt uitgevoerd in de docker-container.

> [!IMPORTANT]
> Deze methode van fout opsporing werkt niet wanneer u `Model.deploy()` en `LocalWebservice.deploy_configuration` gebruikt voor het lokaal implementeren van een model. In plaats daarvan moet u een installatie kopie maken met behulp van de methode [model. package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) .

Voor lokale web service-implementaties is een werkende docker-installatie op uw lokale systeem vereist. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het gebruik van docker.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om de Python Tools for Visual Studio (PTVSD) te installeren op uw lokale en ontwikkelings omgeving voor code:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Zie [fout opsporing op afstand](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)voor meer informatie over het gebruik van PTVSD met VS-code.

1. Als u VS code wilt configureren om met de docker-installatie kopie te communiceren, maakt u een nieuwe configuratie voor fout opsporing:

    1. Selecteer in VS code het menu __fout opsporing__ en selecteer vervolgens __Open configuraties__. Er wordt een bestand met de naam __Launch. json__ geopend.

    1. Zoek in het bestand __Launch. json__ de regel met `"configurations": [`en voeg de volgende tekst toe:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Als er al andere vermeldingen in de sectie configuraties staan, voegt u een komma (,) toe na de code die u hebt ingevoegd.

        Deze sectie wordt gekoppeld aan de docker-container via poort 5678.

    1. Sla het bestand __Launch. json__ op.

### <a name="create-an-image-that-includes-ptvsd"></a>Een installatie kopie maken die PTVSD bevat

1. Wijzig de Conda-omgeving voor uw implementatie, zodat deze PTVSD bevat. In het volgende voor beeld ziet u hoe u het toevoegt met behulp van de para meter `pip_packages`:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Als u PTVSD wilt starten en wilt wachten op een verbinding wanneer de service wordt gestart, voegt u het volgende toe aan de bovenkant van het `score.py`-bestand:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Maak een installatie kopie op basis van de omgevings definitie en haal de installatie kopie op in het lokale REGI ster. Tijdens het opsporen van fouten wilt u mogelijk wijzigingen aanbrengen in de bestanden in de installatie kopie zonder deze opnieuw te hoeven maken. Als u een tekst editor (vim) in de docker-installatie kopie wilt installeren, gebruikt u de eigenschappen `Environment.docker.base_image` en `Environment.docker.base_dockerfile`:

    > [!NOTE]
    > In dit voor beeld wordt ervan uitgegaan dat `ws` verwijst naar uw Azure Machine Learning-werk ruimte en dat `model` het model is dat wordt geïmplementeerd. Het `myenv.yml` bestand bevat de Conda-afhankelijkheden die u in stap 1 hebt gemaakt.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = NONE
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Zodra de installatie kopie is gemaakt en gedownload, wordt het pad naar de afbeelding (inclusief opslag plaats, naam en label, in dit geval ook de samen vatting) weer gegeven in een bericht dat er ongeveer als volgt uitziet:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Gebruik de volgende opdracht om een tag toe te voegen om het gemakkelijker te maken om met de afbeelding te werken. Vervang `myimagepath` door de locatie waarde uit de vorige stap.

    ```bash
    docker tag myimagepath debug:1
    ```

    Voor de rest van de stappen kunt u naar de lokale installatie kopie verwijzen als `debug:1` in plaats van de waarde van het volledige pad naar de installatie kopie.

### <a name="debug-the-service"></a>Fouten opsporen in de service

> [!TIP]
> Als u een time-out hebt ingesteld voor de PTVSD-verbinding in het `score.py` bestand, moet u tussen de code verbinding maken met de foutopsporingssessie voordat de time-out is verstreken. Start VS code, open het lokale exemplaar van `score.py`, stel een onderbrekings punt in en laat het gereed om te gaan voordat u de stappen in deze sectie kunt gebruiken.
>
> Zie [fout opsporing](https://code.visualstudio.com/Docs/editor/debugging)voor meer informatie over het opsporen van fouten en het instellen van onderbrekings punten.

1. Gebruik de volgende opdracht om een docker-container met behulp van de installatie kopie te starten:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Als u VS code aan PTVSD in de container wilt koppelen, opent u VS code en gebruikt u de toets F5 of selecteert u __fout opsporing__. Wanneer u hierom wordt gevraagd, selecteert u de __Azure machine learning: configuratie van docker-fout opsporing__ . U kunt ook het pictogram voor fout opsporing selecteren in de zijbalk, de __Azure machine learning: docker debug__ -vermelding in het vervolg keuzemenu voor fout opsporing en vervolgens de groene pijl gebruiken om het fout opsporingsprogramma te koppelen.

    ![Het pictogram fout opsporing, de knop fout opsporing starten en de configuratie kiezer](./media/how-to-troubleshoot-deployment/start-debugging.png)

Op dit punt verbindt de VS code met PTVSD in de docker-container en stopt dit met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

Zie [fouten opsporen in uw Python-code](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)voor meer informatie over het gebruik van VS code voor het opsporen van problemen met python.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>De container bestanden wijzigen

Als u wijzigingen wilt aanbrengen in bestanden in de installatie kopie, kunt u koppelen aan de container die wordt uitgevoerd en voert u een bash-shell uit. Van daaruit kunt u vim gebruiken om bestanden te bewerken:

1. Gebruik de volgende opdracht om verbinding te maken met de actieve container en een bash-shell in de container te starten:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Als u de bestanden wilt vinden die door de service worden gebruikt, gebruikt u de volgende opdracht uit de bash-shell in de container als de standaard directory afwijkt van `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    Hier kunt u vim gebruiken om het `score.py` bestand te bewerken. Zie [using the vim editor](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)(Engelstalig) voor meer informatie over het gebruik van vim.

1. Wijzigingen in een container worden normaal gesp roken niet persistent gemaakt. Als u de wijzigingen die u hebt aangebracht, wilt opslaan, gebruikt u de volgende opdracht voordat u de shell afsluit die in de bovenstaande stap is gestart (dat wil zeggen, in een andere shell):

    ```bash
    docker commit debug debug:2
    ```

    Met deze opdracht maakt u een nieuwe installatie kopie met de naam `debug:2` die uw bewerkingen bevat.

    > [!TIP]
    > U moet de huidige container stoppen en de nieuwe versie gaan gebruiken voordat de wijzigingen van kracht worden.

1. Zorg ervoor dat u de wijzigingen die u aanbrengt in bestanden in de container gesynchroniseerd blijven met de lokale bestanden die VS code gebruiken. Anders werkt de debugger niet zoals verwacht.

### <a name="stop-the-container"></a>De container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht:

```bash
docker stop debug
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over implementatie:

* [Over het implementeren en waar](how-to-deploy-and-where.md)
* [Zelfstudie: Trainen en implementeren van modellen](tutorial-train-models-with-aml.md)

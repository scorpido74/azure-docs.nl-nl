---
title: Handleiding voor het oplossen van problemen met de implementatie
titleSuffix: Azure Machine Learning
description: Meer informatie over het oplossen, oplossen en oplossen van de veelvoorkomende fouten in de implementatie van Docker met Azure Kubernetes Service en Azure Container Instances met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399680"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure Machine Learning Azure Kubernetes Service en Azure Container Instances-implementatie oplossen

Meer informatie over het oplossen van algemene docker-implementatiefouten met Azure Container Instances (ACI) en Azure Kubernetes Service (AKS) met Azure Machine Learning.

Bij het implementeren van een model in Azure Machine Learning voert het systeem een aantal taken uit.

De aanbevolen en meest up-to-date benadering voor modelimplementatie is via de [API Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) met behulp van een [omgevingsobject](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) als invoerparameter. In dit geval zal onze service een basisdockerafbeelding voor u maken tijdens de implementatiefase en de vereiste modellen in één gesprek monteren. De basisimplementatietaken zijn:

1. Registreer het model in het register van werkruimtemodellen.

2. Inferenceconfiguratie definiëren:
    1. Maak een [omgevingsobject](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) op basis van de afhankelijkheden die u opgeeft in het yaml-bestand van de omgeving of gebruik een van onze aangeschafte omgevingen.
    2. Maak een gevolgtrekkingsconfiguratie (Object InferenceConfig) op basis van de omgeving en het scorescript.

3. Implementeer het model naar azure container instance (ACI) service of naar Azure Kubernetes Service (AKS).

Meer informatie over dit proces vindt u in de inleiding [van modelbeheer.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er geen hebt, probeert u de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)
* De [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* De [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* De [CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Als u lokaal wilt debuggen, moet u een werkende Docker-installatie op uw lokale systeem hebben.

    Als u de installatie van `docker run hello-world` uw Docker wilt verifiëren, gebruikt u de opdracht van een terminal of opdrachtprompt. Zie de [dockerdocumentatie](https://docs.docker.com/)voor informatie over het installeren van Docker of het oplossen van dockerfouten.

## <a name="before-you-begin"></a>Voordat u begint

Als u een probleem tegenkomt, moet u eerst de implementatietaak (vorige beschreven) in afzonderlijke stappen opsplitsen om het probleem te isoleren.

Ervan uitgaande dat u de nieuwe/aanbevolen implementatiemethode gebruikt via [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API met een [omgevingsobject](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) als invoerparameter, kan uw code worden opgesplitst in drie belangrijke stappen:

1. Registreer het model. Hier is een voorbeeld code:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Definieer de conclusieconfiguratie voor implementatie:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Implementeer het model met behulp van de gevolgtrekkingsconfiguratie die in de vorige stap is gemaakt:

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

Zodra u het implementatieproces hebt opgesplitst in afzonderlijke taken, kunnen we enkele van de meest voorkomende fouten bekijken.

## <a name="debug-locally"></a>Lokaal opsporen

Als u problemen ondervindt bij het implementeren van een model in ACI of AKS, probeert u het te implementeren als een lokale webservice. Het gebruik van een lokale webservice maakt het eenvoudiger om problemen op te lossen. De Docker-afbeelding met het model wordt gedownload en gestart op uw lokale systeem.

> [!WARNING]
> Lokale webservice-implementaties worden niet ondersteund voor productiescenario's.

Als u lokaal wilt implementeren, `LocalWebservice.deploy_configuration()` wijzigt u de code om te gebruiken om een implementatieconfiguratie te maken. Gebruik `Model.deploy()` vervolgens om de service te implementeren. In het volgende voorbeeld wordt een model (in de modelvariabele) geïmplementeerd als een lokale webservice:

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

Houd er rekening mee dat als u uw eigen conda-specificatie YAML definieert, u azureml-standaardinstellingen moet vermelden met versie >= 1.0,45 als pip-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als webservice te hosten.

Op dit punt u werken met de service als normaal. De volgende code toont bijvoorbeeld het verzenden van gegevens naar de service:

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

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over het aanpassen van uw Python-omgeving. 

### <a name="update-the-service"></a>De service bijwerken

Tijdens lokale tests moet u `score.py` het bestand mogelijk bijwerken om logboekregistratie toe te voegen of proberen eventuele problemen op te lossen die u hebt ontdekt. Als u wijzigingen `score.py` in `reload()`het bestand opnieuw wilt laden, gebruikt u . Met de volgende code wordt bijvoorbeeld het script voor de service opnieuw geladen en worden gegevens naar de service geladen. De gegevens worden gescoord `score.py` met behulp van het bijgewerkte bestand:

> [!IMPORTANT]
> De `reload` methode is alleen beschikbaar voor lokale implementaties. Zie het updategedeelte van [Deploy-modellen](how-to-deploy-and-where.md#update)voor informatie over het bijwerken van een implementatie naar een ander rekendoel.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Het script wordt opnieuw geladen vanaf `InferenceConfig` de locatie die is opgegeven door het object dat door de service wordt gebruikt.

Als u het model, afhankelijkheden van Conda of implementatieconfiguratie wilt wijzigen, gebruikt u [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). In het volgende voorbeeld wordt het model bijgewerkt dat door de service wordt gebruikt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>De service verwijderen

Als u de service wilt verwijderen, gebruikt u [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Het dockerlogboek inspecteren

U gedetailleerde Docker-enginelogboekberichten uit het serviceobject afdrukken. U het logboek voor ACI-, AKS- en Lokale implementaties bekijken. In het volgende voorbeeld wordt uitgelegd hoe u de logboeken afdrukt.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Servicelancering mislukt

Nadat de afbeelding is gebouwd, probeert het systeem een container te starten met behulp van uw implementatieconfiguratie. Als onderdeel van het opstartproces van containers wordt de `init()` functie in uw scorescript door het systeem aangeroepen. Als er geen uitzonderingen `init()` zijn in de functie, ziet u mogelijk de fout **CrashLoopBackOff** in het foutbericht.

Gebruik de informatie in de sectie [De dockerlogboek inspecteren](#dockerlog) om de logboeken te controleren.

## <a name="function-fails-get_model_path"></a>Functie mislukt: get_model_path()

Vaak wordt `init()` in de functie in het scorescript de functie [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) aangeroepen om een modelbestand of een map met modelbestanden in de container te vinden. Als het modelbestand of de map niet kan worden gevonden, mislukt de functie. De eenvoudigste manier om deze fout te debuggen, is door de onderstaande Python-code in de shell Container uit te voeren:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

In dit voorbeeld wordt het `/var/azureml-app`lokale pad (ten opzichte van ) afgedrukt in de container waar uw scorescript het modelbestand of de map verwacht. Vervolgens u controleren of het bestand of de map is inderdaad waar het naar verwachting zijn.

Als u het logboekregistratieniveau instelt op FOUTOPSPORING KAN ertoe leiden dat aanvullende gegevens worden geregistreerd, wat handig kan zijn bij het identificeren van de fout.

## <a name="function-fails-runinput_data"></a>Functie mislukt: uitvoeren(input_data)

Als de service is geïmplementeerd, maar deze crasht wanneer u gegevens naar het scoreeindpunt `run(input_data)` plaatst, u een foutmelding toevoegen aan uw functie, zodat er in plaats daarvan een gedetailleerd foutbericht wordt geretourneerd. Bijvoorbeeld:

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

**Opmerking:** Het retourneren `run(input_data)` van foutmeldingen van de aanroep moet alleen worden gedaan voor het foutopsporing van doeleinden. Om veiligheidsredenen moet u foutmeldingen niet op deze manier retourneren in een productieomgeving.

## <a name="http-status-code-502"></a>HTTP-statuscode 502

Een statuscode van 502 geeft aan dat de `run()` service een uitzondering heeft gegenereerd of is gecrasht in de methode van het score.py-bestand. Gebruik de informatie in dit artikel om het bestand te debuggen.

## <a name="http-status-code-503"></a>HTTP-statuscode 503

Azure Kubernetes Service-implementaties ondersteunen automatisch schalen, waarmee replica's kunnen worden toegevoegd om extra belasting te ondersteunen. De autoscaler is echter ontworpen om **geleidelijke** wijzigingen in de belasting aan te kunnen. Als u grote pieken in aanvragen per seconde ontvangt, ontvangen clients mogelijk een HTTP-statuscode 503.

Er zijn twee dingen die kunnen helpen voorkomen dat 503 statuscodes:

* Wijzig het gebruiksniveau waarop automatisch schalen nieuwe replica's maakt.
    
    Standaard is het doelgebruik automatisch schalen ingesteld op 70%, wat betekent dat de service pieken in aanvragen per seconde (RPS) tot 30% kan verwerken. U het gebruiksdoel `autoscale_target_utilization` aanpassen door de waarde in te stellen op een lagere waarde.

    > [!IMPORTANT]
    > Deze wijziging zorgt er niet voor dat replica's *sneller*worden gemaakt. In plaats daarvan worden ze gemaakt op een lagere gebruiksdrempel. In plaats van te wachten tot de service 70% wordt gebruikt, zorgt het wijzigen van de waarde voor 30% ervoor dat replica's worden gemaakt wanneer 30% gebruik optreedt.
    
    Als de webservice al de huidige max replica's gebruikt en u `autoscale_max_replicas` nog steeds 503 statuscodes ziet, verhoogt u de waarde om het maximum aantal replica's te verhogen.

* Wijzig het minimumaantal replica's. Het verhogen van de minimale replica's biedt een grotere pool om de inkomende pieken te verwerken.

    Als u het minimumaantal replica's wilt verhogen, stelt u in op `autoscale_min_replicas` een hogere waarde. U de vereiste replica's berekenen met behulp van de volgende code en waarden vervangen door waarden die specifiek zijn voor uw project:

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
    > Als u aanvraagpieken ontvangt die groter zijn dan de nieuwe minimale replica's kunnen verwerken, ontvangt u mogelijk opnieuw 503's. Als het verkeer naar uw service bijvoorbeeld toeneemt, moet u mogelijk de minimale replica's verhogen.

Zie de referentie `autoscale_target_utilization` `autoscale_max_replicas`van `autoscale_min_replicas` de [AksWebservice-module](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) voor meer informatie over het instellen en voor.

## <a name="http-status-code-504"></a>HTTP-statuscode 504

Een statuscode van 504 geeft aan dat er een time-out is opgetreden voor de aanvraag. De standaardtime-out is 1 minuut.

U de time-out verhogen of proberen de service te versnellen door de score.py om onnodige oproepen te verwijderen te wijzigen. Als deze acties het probleem niet verhelpen, gebruikt u de informatie in dit artikel om het score.py bestand te debuggen. De code kan in een opgehangen staat of een oneindige lus.

## <a name="advanced-debugging"></a>Geavanceerde foutopsporing

In sommige gevallen moet u mogelijk de Python-code in uw modelimplementatie interactief debuggen. Als het invoerscript bijvoorbeeld mislukt en de reden niet kan worden bepaald door extra logboekregistratie. Door Visual Studio Code en de Python Tools for Visual Studio (PTVSD) te gebruiken, u de code die in de Docker-container wordt uitgevoerd, koppelen.

> [!IMPORTANT]
> Deze methode voor foutopsporing werkt `Model.deploy()` niet `LocalWebservice.deploy_configuration` bij het lokaal gebruiken en implementeren van een model. In plaats daarvan moet u een afbeelding maken met de methode [Model.package().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)

Lokale webservice-implementaties vereisen een werkende Docker-installatie op uw lokale systeem. Zie de [Docker Documentation](https://docs.docker.com/)voor meer informatie over het gebruik van Docker.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Als u de Python Tools for Visual Studio (PTVSD) wilt installeren op uw lokale VS-code-ontwikkelomgeving, gebruikt u de volgende opdracht:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Zie [Afstandsbediening opsporen](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)voor meer informatie over het gebruik van PTVSD met VS-code.

1. Als u VS-code wilt configureren om te communiceren met de Docker-afbeelding, maakt u een nieuwe foutopsporingsconfiguratie:

    1. Selecteer in VS-code het menu __Foutopsporing__ en selecteer __Vervolgens Configuraties openen__. Er wordt een bestand met de naam __launch.json__ geopend.

    1. Zoek in het bestand __launch.json__ `"configurations": [`de regel die de volgende tekst bevat en voeg de volgende tekst erachter in:

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
        > Als er al andere vermeldingen in de sectie configuraties zijn, voegt u een komma (,) toe na de code die u hebt ingevoegd.

        Deze sectie hecht zich aan de Docker container met poort 5678.

    1. Sla het __bestand launch.json__ op.

### <a name="create-an-image-that-includes-ptvsd"></a>Een afbeelding maken met PTVSD

1. Wijzig de conda-omgeving voor uw implementatie, zodat deze PTVSD bevat. In het volgende voorbeeld wordt `pip_packages` het toevoegen ervan aangemaakt met behulp van de parameter:

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

1. Als u PTVSD wilt starten en wilt wachten op een verbinding `score.py` wanneer de service wordt gestart, voegt u het volgende toe aan de bovenkant van het bestand:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Maak een afbeelding op basis van de omgevingsdefinitie en trek de afbeelding naar het lokale register. Tijdens het foutopsporing u wijzigingen aanbrengen in de bestanden in de afbeelding zonder deze opnieuw te hoeven maken. Als u een teksteditor (vim) wilt installeren `Environment.docker.base_image` `Environment.docker.base_dockerfile` in de dockerafbeelding, gebruikt u de eigenschappen en de eigenschappen:

    > [!NOTE]
    > In dit voorbeeld `ws` wordt ervan uitgegaan dat dit `model` verwijst naar uw Azure Machine Learning-werkruimte en dat is het model dat wordt geïmplementeerd. Het `myenv.yml` bestand bevat de conda-afhankelijkheden die in stap 1 zijn gemaakt.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Zodra de afbeelding is gemaakt en gedownload, wordt het afbeeldingspad (inclusief opslagplaats, naam en tag, die in dit geval ook de samenvatting is) weergegeven in een bericht dat vergelijkbaar is met het volgende:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Gebruik de volgende opdracht om een tag toe te voegen om het werken met de afbeelding te vergemakkelijken. Vervang `myimagepath` de locatiewaarde van de vorige stap.

    ```bash
    docker tag myimagepath debug:1
    ```

    Voor de rest van de stappen u `debug:1` verwijzen naar de lokale afbeelding als in plaats van de volledige afbeeldingspadwaarde.

### <a name="debug-the-service"></a>De service debuggen

> [!TIP]
> Als u een time-out instelt voor `score.py` de PTVSD-verbinding in het bestand, moet u VS-code verbinden met de foutopsporingssessie voordat de time-out verloopt. Start VS Code, open `score.py`de lokale kopie van , stel een breekpunt in en laat het klaar staan voordat u de stappen in deze sectie gebruikt.
>
> Zie [Foutopsporing](https://code.visualstudio.com/Docs/editor/debugging)voor meer informatie over foutopsporing en het instellen van breekpunten.

1. Als u een Docker-container wilt starten met de afbeelding, gebruikt u de volgende opdracht:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Als u VS-code in de container aan PTVSD wilt koppelen, opent u VS-code en gebruikt u de F5-toets of selecteert u __Foutopsporing__. Selecteer de configuratie __Azure Machine Learning: Docker Debug__ wanneer u daarom wordt gevraagd. U ook het pictogram foutopsporing selecteren op de zijbalk, de __Azure Machine Learning: Docker Debug-vermelding__ in het vervolgkeuzelijst Foutopsporing debug-vervolgkeuzelijst en vervolgens de groene pijl gebruiken om de foutopsporing toe te voegen.

    ![Het foutopsporingspictogram, de foutopsporingsknop starten en configuratiekiezer](./media/how-to-troubleshoot-deployment/start-debugging.png)

Op dit moment maakt VS Code verbinding met PTVSD in de Docker-container en stopt bij het breekpunt dat u eerder hebt ingesteld. U nu stap door de code als het draait, variabelen bekijken, enz.

Zie [Python-code debuggen](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)voor meer informatie over het gebruik van VS-code om Python te debuggen.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>De containerbestanden wijzigen

Als u wijzigingen wilt aanbrengen in bestanden in de afbeelding, u de lopende container koppelen en een bashshell uitvoeren. Van daaruit u vim gebruiken om bestanden te bewerken:

1. Als u verbinding wilt maken met de lopende container en een bashshell in de container wilt starten, gebruikt u de volgende opdracht:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Als u de bestanden wilt vinden die door de service worden gebruikt, gebruikt `/var/azureml-app`u de volgende opdracht van de bashshell in de container als de standaardmap anders is dan:

    ```bash
    cd /var/azureml-app
    ```

    Vanaf hier u vim gebruiken `score.py` om het bestand te bewerken. Zie [De Vim-editor gebruiken](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)voor meer informatie over het gebruik van vim.

1. Wijzigingen in een container blijven normaal gesproken niet bestaan. Als u wijzigingen wilt opslaan die u aanbrengt, gebruikt u de volgende opdracht voordat u de shell verlaat die in de bovenstaande stap is gestart (dat wil zeggen in een andere shell):

    ```bash
    docker commit debug debug:2
    ```

    Met deze opdracht wordt `debug:2` een nieuwe afbeelding gemaakt met de naam die uw bewerkingen bevat.

    > [!TIP]
    > U moet de huidige container stoppen en de nieuwe versie gaan gebruiken voordat wijzigingen van kracht worden.

1. Zorg ervoor dat de wijzigingen die u in bestanden in de container aanbrengt, synchroon blijven met de lokale bestanden die VS-code gebruikt. Anders werkt de foutopsporingservaring niet zoals verwacht.

### <a name="stop-the-container"></a>De container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht:

```bash
docker stop debug
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over implementatie:

* [Hoe te implementeren en waar](how-to-deploy-and-where.md)
* [Zelfstudie: & modellen implementeren](tutorial-train-models-with-aml.md)

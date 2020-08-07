---
title: Problemen met docker-implementatie oplossen
titleSuffix: Azure Machine Learning
description: Meer informatie over het omzeilen, oplossen en oplossen van problemen met veelvoorkomende docker-implementatie fouten met de Azure Kubernetes-service en Azure Container Instances het gebruik van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 3f8a3c705878e212e6a26670e20b5a81a3f2a6ba
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904374"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Problemen met docker-implementatie van modellen met Azure Kubernetes service en Azure Container Instances 

Meer informatie over het oplossen van problemen en het oplossen van algemene docker-implementatie fouten met Azure Container Instances (ACI) en Azure Kubernetes service (AKS) met behulp van Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* De [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* De [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Om lokaal fouten op te sporen, moet u een werkende docker-installatie op uw lokale systeem hebben.

    Als u de docker-installatie wilt controleren, gebruikt u de opdracht `docker run hello-world` vanaf een Terminal of opdracht prompt. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het installeren van docker of het oplossen van problemen met docker-fouten.

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Stappen voor docker-implementatie van machine learning modellen

Bij het implementeren van een model in Azure Machine Learning, voert het systeem een aantal taken uit.

De aanbevolen aanpak voor model implementatie is via de API [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) met behulp van een [omgevings](how-to-use-environments.md) object als invoer parameter. In dit geval maakt de service een basis-docker-installatie kopie tijdens de implementatie fase en koppelt de vereiste modellen in één aanroep. De basis taken voor implementatie zijn:

1. Registreer het model in het werkruimte model register.

2. Configuratie voor in-of afleiding definiëren:
    1. Maak een [omgevings](how-to-use-environments.md) object op basis van de afhankelijkheden die u opgeeft in het yaml-bestand van de omgeving of gebruik een van onze aangeschafte omgevingen.
    2. Maak een Afleidings configuratie (InferenceConfig-object) op basis van de omgeving en het Score script.

3. Implementeer het model op de Azure container instance-service (ACI) of naar de Azure Kubernetes-service (AKS).

Meer informatie over dit proces vindt u in de [ModelBeheer](concept-model-management-and-deployment.md) -inleiding.

## <a name="before-you-begin"></a>Voordat u begint

Als u een probleem ondervindt, moet u eerst de implementatie taak (eerder beschreven) opsplitsen in afzonderlijke stappen om het probleem te isoleren.

Als u de nieuwe/Aanbevolen implementatie methode via [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) -API met een [omgevings](how-to-use-environments.md) object als een invoer parameter gebruikt, kan uw code worden onderverdeeld in drie belang rijke stappen:

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

Als u het implementatie proces hebt onderverdeeld in afzonderlijke taken, kunnen we een aantal van de meest voorkomende fouten bekijken.

## <a name="debug-locally"></a>Lokaal fouten opsporen

Als u problemen ondervindt bij het implementeren van een model naar ACI of AKS, kunt u het implementeren als een lokale webservice. Het gebruik van een lokale webservice maakt het gemakkelijker om problemen op te lossen. De docker-installatie kopie met het model wordt gedownload en gestart op het lokale systeem.

U vindt een voor beeld van een [lokale implementatie notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) in de [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -opslag plaats om een uitvoer bare-voorbeeld te verkennen.

> [!WARNING]
> Lokale web service-implementaties worden niet ondersteund voor productie scenario's.

Als u lokaal wilt implementeren, wijzigt u de code die u wilt gebruiken `LocalWebservice.deploy_configuration()` om een implementatie configuratie te maken. Vervolgens gebruiken `Model.deploy()` om de service te implementeren. In het volgende voor beeld wordt een model (opgenomen in de variabele model) geïmplementeerd als lokale webservice:

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

Als u uw eigen Conda-specificatie YAML definieert, moet u de waarden van azureml-standaard met versie >= 1.0.45 als PIP-afhankelijkheid vermelden. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

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

Tijdens lokale tests moet u het bestand mogelijk bijwerken `score.py` om logboek registratie toe te voegen of om problemen op te lossen die u hebt gedetecteerd. Als u wijzigingen in het `score.py` bestand wilt laden, gebruikt u `reload()` . Met de volgende code wordt bijvoorbeeld het script voor de service opnieuw geladen en vervolgens worden er gegevens naar verzonden. De gegevens worden beoordeeld aan de hand van het bijgewerkte `score.py` bestand:

> [!IMPORTANT]
> De `reload` methode is alleen beschikbaar voor lokale implementaties. Zie [How to update your webservice](how-to-deploy-update-web-service.md)(Engelstalig) voor meer informatie over het bijwerken van een implementatie naar een ander Compute-doel.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Het script wordt opnieuw geladen vanaf de locatie die is opgegeven door het `InferenceConfig` object dat door de service wordt gebruikt.

Als u het model, Conda afhankelijkheden of implementatie configuratie wilt wijzigen, gebruikt u [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). In het volgende voor beeld wordt het model bijgewerkt dat door de service wordt gebruikt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>De service verwijderen

Als u de service wilt verwijderen, gebruikt u [Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Het docker-logboek controleren

U kunt de gedetailleerde docker-engine logboek berichten vanuit het Service object afdrukken. U kunt het logboek weer geven voor ACI-, AKS-en lokale implementaties. In het volgende voor beeld ziet u hoe u de logboeken afdrukt.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Als de regel `Booting worker with pid: <pid>` meerdere keren in de logboeken wordt weer gegeven, betekent dit dat er onvoldoende geheugen is om de werk nemer te starten.
U kunt de fout aanpakken door de waarde van in te verhogen `memory_gb``deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Kan de container niet plannen

Wanneer een service wordt geïmplementeerd in een Azure Kubernetes-service Compute target, probeert Azure Machine Learning de service te plannen met de aangevraagde hoeveelheid resources. Als er na vijf minuten geen knoop punten beschikbaar zijn in het cluster met de juiste hoeveelheid beschik bare resources, mislukt de implementatie met het bericht `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . U kunt deze fout oplossen door meer knoop punten toe te voegen, de SKU van uw knoop punten te wijzigen of de resource vereisten van uw service te wijzigen. 

In het fout bericht wordt doorgaans aangegeven welke resource u meer nodig hebt, bijvoorbeeld als er een fout bericht wordt weer gegeven dat aangeeft dat `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` de service gpu's vereist en er drie knoop punten in het cluster zijn die geen beschik bare gpu's hebben. U kunt dit probleem verhelpen door meer knoop punten toe te voegen als u een GPU-SKU gebruikt, waarbij u overschakelt naar een SKU waarvoor GPU is ingeschakeld als u uw omgeving niet wilt wijzigen en geen Gpu's nodig hebt.  

## <a name="service-launch-fails"></a>Starten van service mislukt

Nadat de installatie kopie is gemaakt, probeert het systeem een container te starten met behulp van de implementatie configuratie. Als onderdeel van het opstart proces van de container `init()` wordt de functie in uw score script aangeroepen door het systeem. Als de functie niet-onderschepte uitzonde ringen bevat `init()` , ziet u mogelijk de fout melding **CrashLoopBackOff** in het fout bericht.

Gebruik de informatie in de sectie [het docker-logbestand controleren](#dockerlog) om de logboeken te controleren.

## <a name="function-fails-get_model_path"></a>De functie is mislukt: get_model_path ()

Vaak wordt de functie `init()` [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) in de functie van het Score script aangeroepen om een model bestand of een map met model bestanden in de container te vinden. Als het model bestand of de map niet kan worden gevonden, mislukt de functie. De eenvoudigste manier om deze fout op te lossen is door de onderstaande python-code uit te voeren in de container shell:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

In dit voor beeld wordt het lokale pad (ten opzichte `/var/azureml-app` van) afgedrukt in de container waarin het Score script het model bestand of de map verwacht te vinden. Vervolgens kunt u controleren of het bestand of de map inderdaad de verwachte locatie is.

Als het logboek registratie niveau wordt ingesteld op DEBUG, kan er extra informatie worden vastgelegd, wat nuttig kan zijn bij het identificeren van de fout.

## <a name="function-fails-runinput_data"></a>De functie is mislukt: uitvoeren (input_data)

Als de service is geïmplementeerd, maar vastloopt wanneer u gegevens naar het Score-eind punt post, kunt u een fout melding toevoegen aan de `run(input_data)` functie zodat er in plaats daarvan een gedetailleerd fout bericht wordt geretourneerd. Bijvoorbeeld:

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

**Opmerking**: het retour neren van fout berichten uit de `run(input_data)` aanroep moet alleen worden uitgevoerd voor het doel van de fout opsporing. Uit veiligheids overwegingen moet u geen fout berichten op deze manier retour neren in een productie omgeving.

## <a name="http-status-code-502"></a>HTTP-status code 502

Een 502-status code geeft aan dat de service een uitzonde ring heeft veroorzaakt of is vastgelopen in de `run()` methode van het score.py-bestand. Gebruik de informatie in dit artikel om fouten in het bestand op te sporen.

## <a name="http-status-code-503"></a>HTTP-status code 503

Azure Kubernetes service-implementaties ondersteunen automatisch schalen, zodat er replica's kunnen worden toegevoegd ter ondersteuning van extra belasting. De functie voor automatisch schalen is echter ontworpen voor het afhandelen van **geleidelijke** wijzigingen in de belasting. Als u grote pieken in aanvragen per seconde ontvangt, ontvangen clients mogelijk een HTTP-status code van 503.

Er zijn twee dingen die u kunnen helpen bij het voor komen van 503-status codes:

* Wijzig het gebruiks niveau waarop automatisch schalen nieuwe replica's maakt.
    
    Het doel gebruik voor automatisch schalen is standaard ingesteld op 70%, wat betekent dat de service pieken kan verwerken in aanvragen per seconde (RPS) van Maxi maal 30%. U kunt het doel van het gebruik aanpassen door de `autoscale_target_utilization` in te stellen op een lagere waarde.

    > [!IMPORTANT]
    > Met deze wijziging worden er geen replica's *meer gemaakt.* In plaats daarvan worden ze gemaakt met een lagere drempel waarde voor het gebruik. In plaats van te wachten totdat de service 70% wordt gebruikt, kan het wijzigen van de waarde in 30% ertoe leiden dat er replica's worden gemaakt wanneer er sprake is van 30%.
    
    Als de webservice de huidige maximum replica's al gebruikt en u nog steeds 503 status codes ziet, verhoogt u de `autoscale_max_replicas` waarde om het maximale aantal replica's te verhogen.

* Wijzig het minimale aantal replica's. Het verg Roten van de minimale replica's biedt een grotere pool voor het afhandelen van de binnenkomende pieken.

    Als u het minimum aantal replica's wilt verhogen, stelt `autoscale_min_replicas` u een hogere waarde in. U kunt de vereiste replica's berekenen met behulp van de volgende code en waarden vervangen door waarden die specifiek zijn voor uw project:

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

`autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` Zie de naslag gids voor [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) voor meer informatie over het instellen, en voor.

## <a name="http-status-code-504"></a>HTTP-status code 504

Een 504-status code geeft aan dat er een time-out is opgetreden voor de aanvraag. De standaard time-out is 1 minuut.

U kunt de time-out verhogen of proberen de service te versnellen door de score.py te wijzigen zodat overbodige aanroepen worden verwijderd. Als deze acties het probleem niet verhelpen, gebruikt u de informatie in dit artikel om fouten op te sporen in het score.py-bestand. De code kan een niet-reagerende status hebben of een oneindige lus.

## <a name="advanced-debugging"></a>Geavanceerde fout opsporing

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die in uw model implementatie is opgenomen. Als het script voor de vermelding bijvoorbeeld mislukt en de reden niet kan worden bepaald door aanvullende logboek registratie. Door Visual Studio code en de debugpy te gebruiken, kunt u koppelen aan de code die wordt uitgevoerd in de docker-container. Ga voor meer informatie naar de [richt lijnen voor interactieve fout opsporing in VS code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over implementatie:

* [Implementeren en waar](how-to-deploy-and-where.md)
* [Zelf studie: modellen trainen & implementeren](tutorial-train-models-with-aml.md)

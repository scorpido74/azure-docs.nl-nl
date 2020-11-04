---
title: Problemen met de implementatie van web-service oplossen
titleSuffix: Azure Machine Learning
description: Meer informatie over het omzeilen, oplossen en oplossen van problemen met veelvoorkomende docker-implementatie fouten met Azure Kubernetes service en Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/02/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy
ms.openlocfilehash: dfbfea22738e6aeb0df31ad941b2ff10e53795a4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311286"
---
# <a name="troubleshoot-model-deployment"></a>Problemen met model implementatie oplossen

Meer informatie over het oplossen van problemen en het oplossen van algemene docker-implementatie fouten met Azure Container Instances (ACI) en Azure Kubernetes service (AKS) met behulp van Azure Machine Learning.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).
* De [Azure machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* De [Azure cli](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* De [cli-extensie voor Azure machine learning](reference-azure-machine-learning-cli.md).
* Om lokaal fouten op te sporen, moet u een werkende docker-installatie op uw lokale systeem hebben.

    Als u de docker-installatie wilt controleren, gebruikt u de opdracht `docker run hello-world` vanaf een Terminal of opdracht prompt. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het installeren van docker of het oplossen van problemen met docker-fouten.

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Stappen voor docker-implementatie van machine learning modellen

Wanneer u een model implementeert naar een niet-lokale Compute in Azure Machine Learning, gebeurt het volgende:

1. De Dockerfile die u hebt opgegeven in uw omgevings object in uw InferenceConfig wordt naar de Cloud verzonden, samen met de inhoud van de bronmap
1. Als een eerder gemaakte installatie kopie niet beschikbaar is in het container register, wordt een nieuwe docker-installatie kopie in de Cloud gebouwd en opgeslagen in de standaard container register van uw werk ruimte.
1. De docker-installatie kopie van het container register wordt gedownload naar uw reken doel.
1. De standaard-Blob-opslag van uw werk ruimte is gekoppeld aan het berekenings doel, zodat u toegang krijgt tot geregistreerde modellen
1. De webserver wordt geïnitialiseerd door het uitvoeren van de functie van de invoer script `init()`
1. Wanneer het geïmplementeerde model een aanvraag ontvangt, wordt `run()` die aanvraag door uw functie verwerkt

Het belangrijkste verschil bij het gebruik van een lokale implementatie is dat de container installatie kopie is gebaseerd op uw lokale machine. Daarom moet u docker hebben geïnstalleerd voor een lokale implementatie.

Inzicht in deze stappen op hoog niveau helpt u te begrijpen waar fouten zich voordoen.

## <a name="get-deployment-logs"></a>Implementatie logboeken ophalen

De eerste stap in fouten bij fout opsporing is het ophalen van de implementatie Logboeken. Volg eerst de [instructies hier](how-to-deploy-and-where.md#connect-to-your-workspace) om verbinding te maken met uw werk ruimte.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azcli)

Ga als volgt te werk om de logboeken van een geïmplementeerde webservice op te halen:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Ervan uitgaande dat u een object van `azureml.core.Workspace` het type met de naam hebt `ws` , kunt u het volgende doen:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Lokaal fouten opsporen

Als u problemen ondervindt bij het implementeren van een model naar ACI of AKS, implementeert u het als een lokale webservice. Het gebruik van een lokale webservice maakt het gemakkelijker om problemen op te lossen.

U vindt een voor beeld van een [lokale implementatie notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) in de  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -opslag plaats om een uitvoer bare-voorbeeld te verkennen.

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

Als u uw eigen Conda-specificatie YAML definieert, vermeldt u de versie van azureml-defaults >= 1.0.45 als een PIP-afhankelijkheid. Dit pakket is nodig om het model als een webservice te hosten.

Op dit moment kunt u de service als normaal gebruiken. De volgende code toont het verzenden van gegevens naar de service:

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

Als u het model, Conda afhankelijkheden of implementatie configuratie wilt wijzigen, gebruikt u [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-). In het volgende voor beeld wordt het model bijgewerkt dat door de service wordt gebruikt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>De service verwijderen

Als u de service wilt verwijderen, gebruikt u [Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Het docker-logboek controleren

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

Wanneer een service wordt geïmplementeerd in een Azure Kubernetes Service-rekendoel, wordt getracht de service te plannen met de aangevraagde hoeveelheid resources. Als er na vijf minuten geen knoop punten beschikbaar zijn in het cluster met de juiste hoeveelheid resources, mislukt de implementatie. Het fout bericht is `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . U kunt deze fout oplossen door meer knoop punten toe te voegen, de SKU van uw knoop punten te wijzigen of de resource vereisten van uw service te wijzigen. 

In het fout bericht wordt doorgaans aangegeven welke resource u meer nodig hebt, bijvoorbeeld als er een fout bericht wordt weer gegeven dat aangeeft dat `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` de service gpu's vereist en er drie knoop punten in het cluster zijn die geen beschik bare gpu's hebben. U kunt dit probleem verhelpen door meer knoop punten toe te voegen als u een GPU-SKU gebruikt, waarbij u overschakelt naar een SKU waarvoor GPU is ingeschakeld als u uw omgeving niet wilt wijzigen en geen Gpu's nodig hebt.  

## <a name="service-launch-fails"></a>Starten van service mislukt

Nadat de installatie kopie is gemaakt, probeert het systeem een container te starten met behulp van de implementatie configuratie. Als onderdeel van het opstart proces van de container `init()` wordt de functie in uw score script aangeroepen door het systeem. Als de functie niet-onderschepte uitzonde ringen bevat `init()` , ziet u mogelijk de fout melding **CrashLoopBackOff** in het fout bericht.

Gebruik de informatie in de sectie [het docker-logbestand controleren](#dockerlog) om de logboeken te controleren.

## <a name="function-fails-get_model_path"></a>De functie is mislukt: get_model_path ()

Vaak wordt de functie `init()` [model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) van de functie in het Score script aangeroepen om een model bestand of een map met model bestanden in de container te vinden. Als het modelbestand of de map niet wordt gevonden, mislukt de functie. De eenvoudigste manier om deze fout op te lossen is door de onderstaande python-code uit te voeren in de container shell:

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

**Opmerking** : het retour neren van fout berichten uit de `run(input_data)` aanroep moet alleen worden uitgevoerd voor het doel van de fout opsporing. Uit veiligheids overwegingen moet u geen fout berichten op deze manier retour neren in een productie omgeving.

## <a name="http-status-code-502"></a>HTTP-statuscode 502

Een 502-status code geeft aan dat de service een uitzonde ring heeft veroorzaakt of is vastgelopen in de `run()` methode van het score.py-bestand. Gebruik de informatie in dit artikel om fouten in het bestand op te sporen.

## <a name="http-status-code-503"></a>HTTP-statuscode 503

Azure Kubernetes service-implementaties ondersteunen automatisch schalen, zodat er replica's kunnen worden toegevoegd ter ondersteuning van extra belasting. De automatische schaal functie is ontworpen voor het afhandelen van **geleidelijke** wijzigingen in de belasting. Als u grote pieken in aanvragen per seconde ontvangt, ontvangen clients mogelijk een HTTP-status code van 503. Hoewel de automatisch schalen snel reageert, duurt het AKS een aanzienlijke hoeveelheid tijd om extra containers te maken.

Beslissingen voor omhoog/omlaag schalen is gebaseerd op het gebruik van de huidige container replica's. Het huidige gebruik is het aantal replica's dat bezig is met het verwerken van een aanvraag, gedeeld door het totale aantal huidige replica's. Als dit aantal groter is dan `autoscale_target_utilization` , worden er meer replica's gemaakt. Als deze lager is, worden de replica's gereduceerd. Beslissingen voor het toevoegen van replica's zijn nu en snel (rond 1 seconde). Beslissingen voor het verwijderen van replica's zijn conservatief (ongeveer 1 minuut). Het doel gebruik voor automatisch schalen is standaard ingesteld op **70%** , wat betekent dat de service pieken kan verwerken in aanvragen per seconde (RPS) van **Maxi maal 30%**.

Er zijn twee dingen die u kunnen helpen bij het voor komen van 503-status codes:

> [!TIP]
> Deze twee benaderingen kunnen afzonderlijk of in combi natie worden gebruikt.

* Wijzig het gebruiks niveau waarop automatisch schalen nieuwe replica's maakt. U kunt het doel van het gebruik aanpassen door de `autoscale_target_utilization` in te stellen op een lagere waarde.

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

`autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` Zie de naslag gids voor [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) voor meer informatie over het instellen, en voor.

## <a name="http-status-code-504"></a>HTTP-statuscode 504

Een 504-status code geeft aan dat er een time-out is opgetreden voor de aanvraag. De standaard time-out is 1 minuut.

U kunt de time-out verhogen of proberen de service te versnellen door de score.py te wijzigen zodat overbodige aanroepen worden verwijderd. Als deze acties het probleem niet verhelpen, gebruikt u de informatie in dit artikel om fouten op te sporen in het score.py-bestand. De code kan een niet-reagerende status hebben of een oneindige lus.

## <a name="advanced-debugging"></a>Geavanceerde fout opsporing

Mogelijk moet u interactief fouten opsporen in de Python-code die in uw modelimplementatie is opgenomen. Als het script voor de vermelding bijvoorbeeld mislukt en de reden niet kan worden bepaald door aanvullende logboek registratie. Door Visual Studio code en de debugpy te gebruiken, kunt u koppelen aan de code die wordt uitgevoerd in de docker-container.

Ga voor meer informatie naar de [richt lijnen voor interactieve fout opsporing in VS code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Gebruikers forum voor model implementatie](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over implementatie:

* [Implementeren en waar](how-to-deploy-and-where.md)
* [Zelf studie: modellen trainen & implementeren](tutorial-train-models-with-aml.md)
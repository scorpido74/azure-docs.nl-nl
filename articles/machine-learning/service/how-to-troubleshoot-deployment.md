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
ms.openlocfilehash: cb0f373000d09cb387fb73eec344997381fe45d1
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961666"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Problemen met Azure Machine Learning Azure Kubernetes-service en Azure Container Instances-implementatie oplossen

Meer informatie over het oplossen van veelvoorkomende docker-implementatie fouten met Azure Container Instances (ACI) en Azure Kubernetes service (AKS) met behulp van Azure Machine Learning.

Bij het implementeren van een model in Azure Machine Learning, voert het systeem een aantal taken uit. De implementatietaken zijn:

1. Registreer het model in de werkruimte model-register.

2. Bouw een Docker-installatiekopie, met inbegrip van:
    1. Download het geregistreerde model uit het register. 
    2. Maak een docker-bestand, met een Python-omgeving op basis van de afhankelijkheden die u in de omgeving yaml-bestand opgeeft.
    3. Uw modelbestanden en het scoring-script dat u opgeeft in de dockerfile toevoegen.
    4. Bouw een nieuwe Docker-installatiekopie met behulp van het bestand dockerfile.
    5. Registreer de Docker-installatiekopie met de Azure Container Registry die zijn gekoppeld aan de werkruimte.

    > [!IMPORTANT]
    > Afhankelijk van uw code wordt het maken van afbeeldingen automatisch uitgevoerd zonder uw invoer.

3. De Docker-installatiekopie implementeren naar Azure Container exemplaar (ACI)-service of naar Azure Kubernetes Service (AKS).

4. Starten van een nieuwe container (of containers) in ACI of AKS. 

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

Het verbreken van de implementatie in taken is handig als u de API van de [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) -API of [webservice (web. deploy_from_model)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) gebruikt, omdat beide functies de bovengenoemde stappen als één actie uitvoeren. Deze Api's zijn meestal handig, maar het helpt bij het opruimen van de stappen bij het oplossen van problemen door ze te vervangen door de onderstaande API-aanroepen.

1. Registreer het model. Hier volgt een voorbeeld van code:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Bouw de installatiekopie. Hier volgt een voorbeeld van code:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. De installatiekopie implementeert als service. Hier volgt een voorbeeld van code:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Nadat u het implementatieproces in afzonderlijke taken hebt gesplitst, kunnen we kijken naar enkele van de meest voorkomende fouten.

## <a name="image-building-fails"></a>De installatiekopie van het bouwen van mislukt

Als de docker-installatie kopie niet kan worden gebouwd, mislukt de aanroep van de [installatie kopie. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) of [service. wait_for_deployment (](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) ) met enkele fout berichten die een aantal aanwijzingen kunnen bieden. U vindt ook meer informatie over de fouten van het logboek van de build installatiekopie. Hieronder wordt een voorbeeld van code waarin wordt beschreven hoe u voor het detecteren van het logboek-uri van de installatiekopie-build.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

De uri van de installatiekopie-logboek is een SAS-URL die verwijst naar een logboekbestand die zijn opgeslagen in de Azure blob-opslag. Alleen kopiëren en plakken de uri in een browservenster en u kunnen downloaden en weergeven van het logboekbestand.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault toegangs beleid en Azure Resource Manager sjablonen

De build van de installatie kopie kan ook mislukken vanwege een probleem met het toegangs beleid op Azure Key Vault. Deze situatie kan zich voordoen wanneer u een Azure Resource Manager sjabloon gebruikt om de werk ruimte en bijbehorende resources (inclusief Azure Key Vault) meerdere keren te maken. Gebruik bijvoorbeeld de sjabloon meerdere keren met dezelfde para meters als onderdeel van een continue integratie-en implementatie pijplijn.

De meeste bewerkingen voor het maken van resources via sjablonen zijn idempotent, maar Key Vault wist het toegangs beleid telkens wanneer de sjabloon wordt gebruikt. Als u het toegangs beleid wist, wordt de toegang tot de Key Vault verbroken voor een bestaande werk ruimte waarin deze wordt gebruikt. Deze voor waarde resulteert in fouten wanneer u nieuwe installatie kopieën probeert te maken. Hier volgen enkele voor beelden van de fouten die u kunt ontvangen:

__Portal__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Om dit probleem te voor komen, raden we u aan een van de volgende benaderingen te volgen:

* Implementeer de sjabloon niet meer dan één keer voor dezelfde para meters. Of verwijder de bestaande resources voordat u de sjabloon opnieuw maakt.
* Controleer de Key Vault toegangs beleid en gebruik vervolgens dit beleid om de eigenschap `accessPolicies` van de sjabloon in te stellen.
* Controleer of de Key Vault resource al bestaat. Als dit het geval is, moet u het niet opnieuw maken via de sjabloon. Voeg bijvoorbeeld een para meter toe waarmee u het maken van de Key Vault resource kunt uitschakelen als deze al bestaat.

## <a name="debug-locally"></a>Lokaal fouten opsporen

Als u problemen ondervindt bij het implementeren van een model naar ACI of AKS, kunt u het implementeren als een lokaal. Het gebruik van een lokaal maakt het gemakkelijker om problemen op te lossen. De docker-installatie kopie met het model wordt gedownload en gestart op het lokale systeem.

> [!WARNING]
> Lokale implementaties worden niet ondersteund voor productie scenario's.

Als u lokaal wilt implementeren, wijzigt u de code in `LocalWebservice.deploy_configuration()` om een implementatie configuratie te maken. Gebruik vervolgens `Model.deploy()` om de service te implementeren. In het volgende voor beeld wordt een model (opgenomen in de `model` variabele) geïmplementeerd als een lokaal:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the  endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the  is available on
print(service.port)
```

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
    
    Als de huidige maximum replica's al worden gebruikt en u nog steeds 503 status codes ziet, verhoogt u de `autoscale_max_replicas` waarde om het maximale aantal replica's te verhogen.

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
> Deze methode van fout opsporing werkt niet wanneer u `Model.deploy()` en `LocalWebservice.deploy_configuration` gebruikt voor het lokaal implementeren van een model. In plaats daarvan moet u een installatie kopie maken met behulp van de [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) -klasse. 

Lokale implementaties vereisen een werkende docker-installatie op uw lokale systeem. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het gebruik van docker.

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
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
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

1. Tijdens het opsporen van fouten wilt u mogelijk wijzigingen aanbrengen in de bestanden in de installatie kopie zonder deze opnieuw te hoeven maken. Als u een tekst editor (vim) in de docker-installatie kopie wilt installeren, maakt u een nieuw tekst bestand met de naam `Dockerfile.steps` en gebruikt u het volgende als de inhoud van het bestand:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Met een tekst editor kunt u de bestanden in de docker-installatie kopie wijzigen om wijzigingen te testen zonder een nieuwe installatie kopie te maken.

1. Als u een installatie kopie wilt maken die gebruikmaakt van het `Dockerfile.steps`-bestand, gebruikt u de para meter `docker_file` bij het maken van een installatie kopie. In het volgende voor beeld ziet u hoe u dit doet:

    > [!NOTE]
    > In dit voor beeld wordt ervan uitgegaan dat `ws` verwijst naar uw Azure Machine Learning-werk ruimte en dat `model` het model is dat wordt geïmplementeerd. Het `myenv.yml` bestand bevat de Conda-afhankelijkheden die u in stap 1 hebt gemaakt.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

Zodra de installatie kopie is gemaakt, wordt de locatie van de installatie kopie in het REGI ster weer gegeven. De locatie is vergelijkbaar met de volgende tekst:

```text
myregistry.azurecr.io/myimage:1
```

In dit voor beeld is de naam van het REGI ster `myregistry` en heeft de installatie kopie de naam `myimage`. De versie van de installatie kopie is `1`.

### <a name="download-the-image"></a>De installatie kopie downloaden

1. Open een opdracht prompt, Terminal of andere shell en gebruik de volgende [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -opdracht om te verifiëren bij het Azure-abonnement dat uw Azure machine learning-werk ruimte bevat:

    ```azurecli
    az login
    ```

1. Als u de Azure Container Registry (ACR) wilt verifiëren die uw installatie kopie bevat, gebruikt u de volgende opdracht. Vervang `myregistry` door de afbeelding die wordt geretourneerd tijdens het registreren van de installatie kopie:

    ```azurecli
    az acr login --name myregistry
    ```

1. Gebruik de volgende opdracht om de installatie kopie te downloaden naar uw lokale docker. Vervang `myimagepath` door de locatie die wordt geretourneerd bij het registreren van de installatie kopie:

    ```bash
    docker pull myimagepath
    ```

    Het pad naar de afbeelding moet vergelijkbaar zijn met `myregistry.azurecr.io/myimage:1`. Als `myregistry` uw REGI ster is, wordt `myimage` uw installatie kopie en `1` de versie van de installatie kopie.

    > [!TIP]
    > De verificatie van de vorige stap wordt niet voor het laatst gebruikt. Als u lang genoeg wacht tussen de verificatie opdracht en de pull-opdracht, ontvangt u een verificatie fout. Als dit het geval is, moet u zich opnieuw verifiëren.

    De tijd die nodig is om het downloaden te volt ooien, is afhankelijk van de snelheid van uw Internet verbinding. Tijdens het proces wordt een Download status weer gegeven. Zodra het downloaden is voltooid, kunt u de opdracht `docker images` gebruiken om te controleren of het is gedownload.

1. Gebruik de volgende opdracht om een tag toe te voegen om het gemakkelijker te maken om met de afbeelding te werken. Vervang `myimagepath` door de locatie waarde uit stap 2.

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

    ![Het pictogram fout opsporing, de knop fout opsporing starten en de configuratie kiezer](media/how-to-troubleshoot-deployment/start-debugging.png)

Op dit punt verbindt de VS code met PTVSD in de docker-container en stopt dit met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

Zie [fouten opsporen in uw Python-code](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)voor meer informatie over het gebruik van VS code voor het opsporen van problemen met python.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>De container bestanden wijzigen

Als u wijzigingen wilt aanbrengen in bestanden in de installatie kopie, kunt u koppelen aan de container die wordt uitgevoerd en voert u een bash-shell uit. Van daaruit kunt u vim gebruiken om bestanden te bewerken:

1. Gebruik de volgende opdracht om verbinding te maken met de actieve container en een bash-shell in de container te starten:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Als u de bestanden wilt vinden die door de service worden gebruikt, gebruikt u de volgende opdracht uit de bash-shell in de container:

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

---
title: Gegevens van Machine Learning-webservice-eind punten bewaken en verzamelen
titleSuffix: Azure Machine Learning
description: Webservices die zijn geïmplementeerd met Azure Machine Learning bewaken met behulp van Azure-toepassing Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ae66447e128b07ce942b8c2fcc66347a31cfe83f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848856"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Gegevens van ML-webservice-eindpunten bewaken en verzamelen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u gegevens kunt verzamelen van en bewaakt modellen die zijn geïmplementeerd op web service-eind punten in azure Kubernetes service (AKS) of Azure Container Instances (ACI) door de logboeken te doorzoeken en Azure-toepassing inzichten in te scha kelen via 
* [Azure Machine Learning python-SDK](#python)
* [Azure machine learning Studio](#studio) ophttps://ml.azure.com

Naast het verzamelen van de uitvoer gegevens en het antwoord van een eind punt kunt u het volgende controleren:

* Aanvraag tarieven, reactie tijden en fout tarieven
* Afhankelijkheids tarieven, reactie tijden en fout percentages
* Uitzonderingen

[Meer informatie over Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)

* Een Azure Machine Learning-werk ruimte, een lokale map die uw scripts bevat en de Azure Machine Learning SDK voor python is geïnstalleerd. Zie [een ontwikkel omgeving configureren](how-to-configure-environment.md) voor meer informatie over het verkrijgen van deze vereisten.

* Een getraind machine learning model dat moet worden geïmplementeerd in azure Kubernetes service (AKS) of Azure container instance (ACI). Als u er nog geen hebt, raadpleegt u de zelf studie over het [classificatie Model Train image](tutorial-train-models-with-aml.md)

## <a name="query-logs-for-deployed-models"></a>Query logboeken voor geïmplementeerde modellen

Als u logboeken van een eerder geïmplementeerde webservice wilt ophalen, laadt u de service en gebruikt u de `get_logs()` functie. De logboeken bevatten mogelijk gedetailleerde informatie over eventuele fouten die zijn opgetreden tijdens de implementatie.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

## <a name="web-service-metadata-and-response-data"></a>Meta gegevens en antwoord van de webservice

> [!IMPORTANT]
> Met Azure-toepassing Insights worden alleen nettoladingen geregistreerd van Maxi maal 64 kB. Als deze limiet wordt bereikt, worden er mogelijk fouten weer geven, zoals onvoldoende geheugen of wordt er geen informatie vastgelegd.

Als u gegevens wilt vastleggen voor een aanvraag voor de webservice, voegt `print` u instructies toe aan uw score.py-bestand. Elke `print` instructie resulteert in één vermelding in de traceer tabel in Application Insights, onder het bericht `STDOUT` . De inhoud van de `print` instructie wordt opgenomen onder `customDimensions` en vervolgens `Contents` in de tracerings tabel. Als u een JSON-teken reeks afdrukt, produceert deze een hiërarchische gegevens structuur in de tracerings uitvoer onder `Contents` .

U kunt Azure-toepassing inzichten rechtstreeks doorzoeken op toegang tot deze gegevens of een [continue export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) naar een opslag account instellen voor een langere retentie of verdere verwerking. Model gegevens kunnen vervolgens worden gebruikt in de Azure Machine Learning voor het instellen van labels, retraining, uitleg, gegevens analyse of ander gebruik. 


<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Python-SDK gebruiken om te configureren 

### <a name="update-a-deployed-service"></a>Een geïmplementeerde service bijwerken

1. Identificeer de service in uw werk ruimte. De waarde voor `ws` is de naam van uw werk ruimte

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uw service bijwerken en Azure-toepassing Insights inschakelen

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Aangepaste traceringen vastleggen in uw service

Als u aangepaste traceringen wilt registreren, volgt u het standaard implementatie proces voor AKS of ACI in het document [implementeren en where](how-to-deploy-and-where.md) . Gebruik vervolgens de volgende stappen:

1. Als u gegevens wilt verzenden naar Application Insights tijdens de defactorion, werkt u het Score bestand bij door afdruk instructies toe te voegen. Om complexere informatie, zoals de gegevens van de aanvraag en het antwoord, te registreren in de vorm van een JSON-structuur. In het volgende voor beeld score.py-bestand wordt de tijd geregistreerd die het model heeft geïnitialiseerd, de invoer en uitvoer tijdens de afleiding en de tijd waarop fouten optreden:

    > [!IMPORTANT]
    > Met Azure-toepassing Insights worden alleen nettoladingen geregistreerd van Maxi maal 64 kB. Als deze limiet is bereikt, worden er mogelijk fouten weer geven, zoals onvoldoende geheugen of wordt er geen informatie vastgelegd. Als de gegevens die u wilt vastleggen groter zijn dan 64 KB, moet u deze opslaan in Blob Storage met behulp van de gegevens in [gegevens verzamelen voor modellen in de productie omgeving](how-to-enable-data-collection.md).
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. De service configuratie bijwerken
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bouw een installatie kopie en implementeer deze op [AKS of ACI](how-to-deploy-and-where.md).

Zie [logboek registratie inschakelen in azure machine learning](how-to-enable-logging.md) en [gegevens verzamelen van modellen in de productie omgeving](how-to-enable-data-collection.md)voor meer informatie over logboek registratie en het verzamelen van gegevens.

### <a name="disable-tracking-in-python"></a>Tracering in python uitschakelen

Als u Azure-toepassing Insights wilt uitschakelen, gebruikt u de volgende code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Azure Machine Learning Studio gebruiken om te configureren

U kunt ook Azure-toepassing Insights inschakelen vanuit Azure Machine Learning Studio wanneer u klaar bent om uw model te implementeren met behulp van de volgende stappen.

1. Meld u aan bij uw werk ruimte ophttps://ml.azure.com/
1. Ga naar **modellen** en selecteer welk model u wilt implementeren
1. Selecteer **+ implementeren**
1. Het formulier **model implementeren** invullen
1. Het menu **Geavanceerd** uitvouwen

    ![Formulier implementeren](./media/how-to-enable-app-insights/deploy-form.png)
1. Selecteer **Application Insights diagnose en gegevens verzameling inschakelen**

    ![App Insights inschakelen](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Metrische gegevens en logboeken weer geven

De gegevens van uw service worden opgeslagen in uw Azure-toepassing Insights-account, in dezelfde resource groep als Azure Machine Learning.
Om het weer te geven:

1. Ga naar uw Azure Machine Learning-werk ruimte in de [Studio](https://ml.azure.com/).
1. Selecteer **eind punten**.
1. Selecteer de geïmplementeerde service.
1. Schuif omlaag om de **Application Insights URL** te vinden en selecteer de koppeling.

    [![Application Insights URL zoeken](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. In Application Insights selecteert u op het tabblad **overzicht** of de sectie __bewaking__ in de lijst aan de linkerkant __Logboeken__.

    [![Tabblad Overzicht van bewaking](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Als u informatie wilt weer geven die is vastgelegd in het score.py-bestand, bekijkt u de tabel __traces__ . Met de volgende query zoekt u naar Logboeken waarin de __invoer__ waarde is vastgelegd:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![gegevens traceren](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md)voor meer informatie over het gebruik van Azure-toepassing Insights.

## <a name="export-data-for-further-processing-and-longer-retention"></a>Gegevens exporteren voor verdere verwerking en langere retentie

>[!Important]
> Azure-toepassing Insights ondersteunt alleen export naar Blob Storage. Aanvullende limieten van deze export mogelijkheid worden vermeld in [telemetrie exporteren vanuit app Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

U kunt met behulp van Azure-toepassing Insights [doorlopend exporteren](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) berichten verzenden naar een ondersteund opslag account, waarbij een langere retentie kan worden ingesteld. De gegevens worden opgeslagen in JSON-indeling en kunnen gemakkelijk worden geparseerd om model gegevens op te halen. 

Azure Data Factory, Azure ML-pijp lijnen of andere hulpprogram ma's voor gegevens verwerking kunnen worden gebruikt om de gegevens naar behoefte te transformeren. Wanneer u de gegevens hebt getransformeerd, kunt u deze registreren bij de Azure Machine Learning-werk ruimte als een gegevensset. Zie [gegevens sets maken en registreren](how-to-create-register-datasets.md)voor meer informatie.

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Continue export":::


## <a name="example-notebook"></a>Voorbeeld van notebook

De [Enable-app-Insights-in-production-service. ipynb-](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook demonstreert de concepten in dit artikel. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie [een model implementeren in een Azure Kubernetes-service cluster](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) of [een model implementeren in azure container instances](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) om uw modellen te implementeren voor webservice-eind punten, en Azure-toepassing Insights in te scha kelen voor het gebruik van gegevens verzameling en eindpunt bewaking
* Zie [MLOps: modellen beheren, implementeren en bewaken met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) voor meer informatie over het benutten van gegevens die zijn verzameld van modellen in productie. Dergelijke gegevens kunnen helpen uw machine learning proces voortdurend te verbeteren

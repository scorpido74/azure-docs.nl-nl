---
title: Gegevens drift detecteren in AKS-implementaties
titleSuffix: Azure Machine Learning
description: Gegevens drift (preview) detecteren in azure Kubernetes-Service geïmplementeerde modellen in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 0f56ab853983ebf9b3e27f38ae1737c0c2bce4ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430292"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Gegevens drift (preview) detecteren voor modellen die zijn geïmplementeerd in azure Kubernetes service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel leert u hoe u kunt controleren op gegevens overdracht tussen de gegevensset van de training en hoe u gegevens van een geïmplementeerd model dewenst. In de context van machine learning kunnen getrainde machine learning modellen gedegradeerde Voorspellings prestaties ondervinden vanwege drift. Met Azure Machine Learning kunt u gegevens drift bewaken en de service kan een e-mail bericht naar u verzenden wanneer er een drift wordt gedetecteerd.

## <a name="what-is-data-drift"></a>Wat is gegevens drift?

In de context van machine learning is gegevens drift de wijziging in model invoer gegevens die leidt tot het model leren van prestaties. Het is een van de belangrijkste redenen waarbij de nauw keurigheid van het model in de loop van de tijd afneemt, waardoor gegevens drift helpt bij het detecteren van prestatie problemen van modellen. 

## <a name="what-can-i-monitor"></a>Wat kan ik controleren?

Met Azure Machine Learning kunt u de invoer bewaken in een model dat is geïmplementeerd op AKS en deze gegevens vergelijken met de trainings gegevensset voor het model. Op regel matige intervallen worden de gegevens in de vorm van een [moment opname gemaakt en](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)vervolgens berekend op basis van de basislijn gegevensset om een gegevensdrijf analyse te maken die: 

+ Meet de omvang van de gegevens drift, de zogenaamde drift.
+ Meet de gegevens drift-bijdrage per onderdeel, waarmee wordt aangegeven welke functies gegevens drift hebben veroorzaakt.
+ Meet afstands metrieken. Momenteel worden de Wasserstein en de energie-afstand berekend.
+ Meet de distributie van functies. Schatting van huidige kernel-densiteit en histogrammen.
+ Waarschuwingen verzenden naar gegevens via e-mail.

> [!Note]
> Deze service is in (preview) en beperkt in configuratie opties. Raadpleeg onze [API-documentatie](https://docs.microsoft.com/python/api/azureml-datadrift/) en [opmerkingen](azure-machine-learning-release-notes.md) bij de release voor meer informatie en updates. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Hoe gegevens drift wordt bewaakt in Azure Machine Learning

Met behulp van Azure Machine Learning wordt gegevens drift bewaakt door data sets of implementaties. Als u wilt controleren op gegevens drift, een basislijn gegevensset, meestal de trainings gegevensset voor een model, is opgegeven. Een tweede gegevensset-meestal model invoer gegevens die zijn verzameld van een implementatie, wordt getest op basis van de gegevensset van de basislijn planning. Beide gegevens sets worden profileeerd en ingevoerd in de data drift-bewakings service. Een machine learning model wordt getraind om verschillen tussen de twee gegevens sets te detecteren. De prestaties van het model worden geconverteerd naar de snelheids coëfficiënt, die de grootte van de gegevens in de twee data sets meet. Het gebruik van [model interpreteert](how-to-machine-learning-interpretability.md)de functies die bijdragen aan de snelheids coëfficiënt worden berekend. Vanuit het profiel gegevensset wordt statistische informatie over elke functie bijgehouden. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u er nog geen hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

- De Azure Machine Learning SDK voor python is geïnstalleerd. Volg de instructies in [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) om het volgende te doen:

    - Een Miniconda-omgeving maken
    - De Azure Machine Learning SDK voor python installeren

- Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

- Een [configuratie bestand](how-to-configure-environment.md#workspace)voor de werk ruimte.

- Installeer de data drift SDK met behulp van de volgende opdracht:

    ```shell
    pip install azureml-datadrift
    ```

- Een [gegevensset](how-to-create-register-datasets.md) maken op basis van de trainings gegevens van uw model.

- Geef de training-gegevensset op bij het [registreren](concept-model-management-and-deployment.md) van het model. In het volgende voor beeld wordt gedemonstreerd met behulp van de `datasets` para meter voor het opgeven van de trainings gegevensset:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Schakel de verzameling model gegevens](how-to-enable-data-collection.md) in om gegevens te verzamelen van de AKS-implementatie van het model en te bevestigen dat de gegevens worden verzameld in de `modeldata` BLOB-container.

## <a name="configure-data-drift"></a>Gegevens drift configureren
Als u gegevens drift wilt configureren voor uw experiment, moet u afhankelijkheden importeren zoals wordt weer gegeven in het volgende python-voor beeld. 

In dit voor beeld ziet u hoe u het [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) object configureert:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Een DataDriftDetector-uitvoering verzenden

Wanneer het `DataDriftDetector` object is geconfigureerd, kunt u een [Data-drift uitvoeren](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) op een bepaalde datum voor het model. Als onderdeel van de uitvoering moet u DataDriftDetector-waarschuwingen inschakelen door de para meter in te stellen `drift_threshold` . Als de [datadrift_coefficient](#visualize-drift-metrics) hoger is dan de opgegeven `drift_threshold` , wordt er een e-mail bericht verzonden.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Metrische gegevens over drift visualiseren

<a name="metrics"></a>

Nadat u de DataDriftDetector hebt verzonden, kunt u de metrische gegevens over de drift zien die zijn opgeslagen in elke uitvoerings herhaling voor een gegevens-drift:


|Gegevens|Beschrijving|
--|--|
wasserstein_distance|Statistische afstand gedefinieerd voor eendimensionale sprei ding.|
energy_distance|Statistische afstand gedefinieerd voor eendimensionale sprei ding.|
datadrift_coefficient|Berekend op dezelfde manier als de correlatie coëfficiënt van Matthew, maar deze uitvoer is een reëel getal tussen 0 en 1. In de context van drift duidt 0 op geen drift en 1 geeft de maximale waarde voor de drift aan.|
datadrift_contribution|Belang rijk onderdeel van functies die bijdragen aan drift.|

Er zijn meerdere manieren om gegevens over de drift weer te geven:

* Gebruik de `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Gebruik de [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) functie voor elk `datadrift` Run-object.
* Bekijk de metrische gegevens in het gedeelte **modellen** van uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).

In het volgende python-voor beeld ziet u hoe u relevante gegevens waarden kunt tekenen. U kunt de geretourneerde metrische gegevens gebruiken om aangepaste visualisaties te maken:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Door Azure Machine Learning gedetecteerde gegevens drift bekijken](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Scans van gegevens drift plannen 

Wanneer u de functie voor gegevens drift detectie inschakelt, wordt een DataDriftDetector uitgevoerd op de opgegeven geplande frequentie. Als de datadrift_coefficient de opgegeven grootte bereikt `drift_threshold` , wordt een e-mail verzonden bij elke geplande uitvoering. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

De configuratie van de gegevens drift-detectie kan worden weer gegeven onder **modellen** op het tabblad **Details** in uw werk ruimte in de [Azure machine learning Studio](https://ml.azure.com).

[![Data drift Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Resultaten weer geven in uw Azure Machine Learning Studio

Als u de resultaten in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com)wilt weer geven, gaat u naar de model pagina. Op het tabblad Details van het model wordt de data-drift configuratie weer gegeven. Er is nu een tabblad **gegevens drift** beschikbaar voor het visualiseren van de metrische gegevens over de data waarde. 

[![Data drift Azure Machine Learning Studio](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Ontvangst van drijf signalen

Door de drempel waarde voor de waarschuwing voor het instellen van de drift en een e-mail adres op te geven, wordt er automatisch een [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mail waarschuwing verzonden wanneer de plaatsings coëfficiënt hoger is dan de drempel waarde. 

Om aangepaste waarschuwingen en acties in te stellen, worden alle metrische gegevens waarden opgeslagen in de [Application Insights](how-to-enable-app-insights.md) resource die samen met de Azure machine learning-werk ruimte is gemaakt. U kunt de koppeling in de e-mail waarschuwing volgen naar de Application Insights-query.

![Waarschuwing voor gegevens drift per E-mail](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Uw model na drift opnieuw trainen

Wanneer gegevens drift een negatieve invloed heeft op de prestaties van uw geïmplementeerde model, is het tijd om het model opnieuw te trainen. Als u dit wilt doen, gaat u verder met de volgende stappen.

* Onderzoek de verzamelde gegevens en bereid gegevens voor om het nieuwe model te trainen.
* Splits deze in de gegevens voor Train/test.
* Train het model opnieuw met behulp van de nieuwe gegevens.
* Evalueer de prestaties van het zojuist gegenereerde model.
* Implementeer nieuw model als de prestaties beter zijn dan het productie model.

## <a name="next-steps"></a>Volgende stappen

* Zie de [Azure ml data drift-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)voor een volledig voor beeld van het gebruik van gegevens drift. In deze Jupyter Notebook ziet u hoe u met behulp van een [Azure open-gegevensset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) een model traint om het weer te voors pellen, deze te implementeren op AKS en te controleren op gegevens drift. 

* Gegevens drift detecteren met [DataSet-monitors](how-to-monitor-datasets.md).

* We stellen uw vragen, opmerkingen of suggesties enorm op prijs naarmate de gegevens worden verplaatst naar de algemene Beschik baarheid. Gebruik de knop product feedback hieronder. 

---
title: Gegevensdrift detecteren bij AKS-implementaties
titleSuffix: Azure Machine Learning
description: Detecteer gegevensdrift (preview) op Azure Kubernetes Service geïmplementeerde modellen in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537004"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Gegevensdrift (preview) detecteren op modellen die zijn geïmplementeerd in Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel leert u hoe u controleren op gegevensverschuiving tussen de trainingsgegevensset en gevolgtrekkingsgegevens van een geïmplementeerd model. In de context van machine learning kunnen getrainde machine learning-modellen een verminderde voorspellingsprestatie ervaren als gevolg van drift. Met Azure Machine Learning u gegevensdrift controleren en kan de service een e-mailwaarschuwing naar u verzenden wanneer drift wordt gedetecteerd.

## <a name="what-is-data-drift"></a>Wat is datadrift?

In de context van machine learning is datadrift de verandering in modelinvoergegevens die leidt tot prestatiedegradatie van het model. Het is een van de belangrijkste redenen waarom modelnauwkeurigheid na verloop van tijd degradeert, waardoor het monitoren van gegevensdrift helpt bij het detecteren van prestatieproblemen op het gebied van modelprestaties. 

## <a name="what-can-i-monitor"></a>Wat kan ik controleren?

Met Azure Machine Learning u de ingangen naar een model dat op AKS is geïmplementeerd, controleren en deze gegevens vergelijken met de trainingsgegevensset voor het model. Op regelmatige tijdstippen worden de gevolgtrekkingsgegevens [momentopnameen en geprofileerd,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)vervolgens berekend op basis van de basislijngegevensset om een gegevensdriftanalyse te produceren die: 

+ Meet de omvang van de gegevens drift, genaamd de drift coëfficiënt.
+ Meet de bijdrage van gegevensdrift per functie en geeft aan welke functies gegevensdrift hebben veroorzaakt.
+ Meet afstandsstatistieken. Momenteel worden Wasserstein en Energy Distance berekend.
+ Meet de verdeling van functies. Momenteel kernel dichtheid schatting en histogrammen.
+ Stuur waarschuwingen naar gegevensdrift via e-mail.

> [!Note]
> Deze service is in (preview) en beperkt in configuratie-opties. Zie onze [API-documentatie](https://docs.microsoft.com/python/api/azureml-datadrift/) en [releasenotities](azure-machine-learning-release-notes.md) voor meer informatie en updates. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Hoe gegevensdrift wordt gecontroleerd in Azure Machine Learning

Met Azure Machine Learning wordt gegevensdrift gecontroleerd via gegevenssets of implementaties. Om te controleren op gegevensdrift wordt een basislijngegevensset - meestal de trainingsgegevensset voor een model - opgegeven. Een tweede gegevensset - meestal modelinvoergegevens die zijn verzameld vanuit een implementatie - wordt getest op basis van de basislijngegevensset. Beide gegevenssets worden geprofileerd en ingevoerd voor de data drift monitoring service. Een machine learning-model is getraind om verschillen tussen de twee gegevenssets te detecteren. De prestaties van het model worden omgezet in de driftcoëfficiënt, die de omvang van de drift tussen de twee gegevenssets meet. Met behulp van [modelinterpreteerbaarheid](how-to-machine-learning-interpretability.md)worden de functies die bijdragen aan de driftcoëfficiënt berekend. Vanuit het gegevenssetprofiel wordt statistische informatie over elke functie bijgehouden. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als je nog geen account hebt, maak je een gratis account aan voordat je begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

- De Azure Machine Learning SDK voor Python geïnstalleerd. Gebruik de instructies van [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) als volgt:

    - Een Miniconda-omgeving maken
    - De Azure Machine Learning SDK voor Python installeren

- Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md).

- Een [configuratiebestand voor werkruimtes](how-to-configure-environment.md#workspace).

- Installeer de SDK voor gegevensdrift met de volgende opdracht:

    ```shell
    pip install azureml-datadrift
    ```

- Maak een [gegevensset](how-to-create-register-datasets.md) op basis van de trainingsgegevens van uw model.

- Geef de trainingsgegevensset op bij [het registreren van](concept-model-management-and-deployment.md) het model. In het volgende voorbeeld `datasets` wordt aangetoond dat de parameter wordt gebruikt om de trainingsgegevensset op te geven:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Modelgegevensverzameling inschakelen](how-to-enable-data-collection.md) om gegevens te verzamelen van de AKS-implementatie `modeldata` van het model en te bevestigen dat gegevens worden verzameld in de blobcontainer.

## <a name="configure-data-drift"></a>Gegevensdrift configureren
Als u gegevensdrift voor uw experiment wilt configureren, importeert u afhankelijkheden zoals te zien in het volgende Python-voorbeeld. 

In dit voorbeeld wordt [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) het configureren van het object weergegeven:

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

## <a name="submit-a-datadriftdetector-run"></a>Een DataDriftDetector-run verzenden

Als `DataDriftDetector` het object is geconfigureerd, u een [gegevensdriftuitvoeren](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) op een bepaalde datum voor het model indienen. Schakel als onderdeel van de run DataDriftDetector-waarschuwingen in door de `drift_threshold` parameter in te stellen. Als de [datadrift_coefficient](#visualize-drift-metrics) boven `drift_threshold`de gegeven is, wordt een e-mail verzonden.

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

## <a name="visualize-drift-metrics"></a>Driftstatistieken visualiseren

<a name="metrics"></a>

Nadat u uw DataDriftDetector-run hebt verzonden, u de driftstatistieken zien die worden opgeslagen in elke run-iteratie voor een taak voor gegevensdrift:


|Gegevens|Beschrijving|
--|--|
wasserstein_distance|Statistische afstand gedefinieerd voor eendimensionale numerieke verdeling.|
energy_distance|Statistische afstand gedefinieerd voor eendimensionale numerieke verdeling.|
datadrift_coefficient|Berekend op dezelfde manier als Matthew's correlatie coëfficiënt, maar deze output is een reëel aantal variërend van 0 tot 1. In de context van drift geeft 0 geen drift aan en 1 geeft maximale drift aan.|
datadrift_contribution|Functie belang van functies die bijdragen aan drift.|

Er zijn meerdere manieren om driftstatistieken weer te geven:

* Gebruik `RunDetails`de [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Gebruik [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) de functie `datadrift` op elk runobject.
* Bekijk de statistieken uit het gedeelte **Modellen** van uw werkruimte in [Azure Machine Learning-studio](https://ml.azure.com).

In het volgende Python-voorbeeld wordt uitgelegd hoe u relevante gegevensdriftstatistieken plotten. U de geretourneerde statistieken gebruiken om aangepaste visualisaties te maken:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Zie gegevensdrift gedetecteerd door Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Gegevensdriftscans plannen 

Wanneer u detectie van gegevensdrift inschakelt, wordt een DataDriftDetector uitgevoerd op de opgegeven, geplande frequentie. Als de datadrift_coefficient `drift_threshold`de gegeven bereikt, wordt een e-mail verzonden met elke geplande run. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

De configuratie van de datadriftdetector is te zien onder **Modellen** op het tabblad **Details** in uw werkruimte in de [Azure Machine Learning-studio.](https://ml.azure.com)

[![Azure Machine Learning studio Data Drift](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Resultaten weergeven in uw Azure Machine Learning-studio

Als u resultaten wilt weergeven in uw werkruimte in [Azure Machine Learning-studio,](https://ml.azure.com)navigeert u naar de modelpagina. Op het tabblad details van het model wordt de configuratie van gegevensdrift weergegeven. Er is nu een tabblad **Gegevensdrift** beschikbaar om de gegevensdriftstatistieken te visualiseren. 

[![Azure Machine Learning studio Data Drift](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Driftmeldingen ontvangen

Door de driftcoëfficiëntwaarschuwingsdrempel in te stellen en een e-mailadres op te geven, wordt automatisch een e-mailwaarschuwing [van Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) verzonden wanneer de driftcoëfficiënt boven de drempelwaarde ligt. 

Als u aangepaste waarschuwingen en acties wilt instellen, worden alle gegevensdriftstatistieken opgeslagen in de [Application Insights-bron](how-to-enable-app-insights.md) die is gemaakt samen met de Azure Machine Learning-werkruimte. U de koppeling in de e-mailwaarschuwing volgen voor de query Toepassingsstatistieken.

![E-mailwaarschuwing voor gegevensdrift](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Train je model na drift om

Wanneer gegevensdrift een negatieve invloed heeft op de prestaties van uw geïmplementeerde model, is het tijd om het model om te scholen. Ga hiervoor verder met de volgende stappen.

* Onderzoek de verzamelde gegevens en bereid gegevens voor om het nieuwe model te trainen.
* Splits het op in trein-/testgegevens.
* Train het model opnieuw met behulp van de nieuwe gegevens.
* Evalueer de prestaties van het nieuw gegenereerde model.
* Implementeer een nieuw model als de prestaties beter zijn dan het productiemodel.

## <a name="next-steps"></a>Volgende stappen

* Zie het [Azure ML-gegevensdriftnotitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)voor een volledig voorbeeld van het gebruik van gegevensdrift. Deze Jupyter-notitieblok toont aan dat u een [Azure Open Dataset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) gebruikt om een model te trainen om het weer te voorspellen, te implementeren op AKS en te controleren op gegevensdrift. 

* Detecteer gegevensdrift met [gegevenssetmonitors](how-to-monitor-datasets.md).

* We stellen uw vragen, opmerkingen of suggesties zeer op prijs als gegevensdrift zich verplaatst naar algemene beschikbaarheid. Gebruik hieronder de productfeedbackknop! 

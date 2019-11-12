---
title: Logboek registratie inschakelen in Azure Machine Learning
description: Meer informatie over het inschakelen van logboek registratie in Azure Machine Learning met behulp van zowel het standaard python-logboek registratie pakket als het gebruik van specifieke SDK-functies.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 91e00b8e1d13f69ae3ba446bcbc09f06f387c439
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931123"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Logboek registratie inschakelen in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met de Azure Machine Learning python-SDK kunt u logboek registratie inschakelen met zowel het standaard python-logboek registratie pakket als met de SDK-specifieke functionaliteit voor lokale logboek registratie en logboek registratie in uw werk ruimte in de portal. Logboeken bieden ontwikkel aars met realtime informatie over de toepassings status en kunnen helpen bij het vaststellen van fouten of waarschuwingen. In dit artikel leert u verschillende manieren om logboek registratie in te scha kelen op de volgende gebieden:

> [!div class="checklist"]
> * Trainings modellen en reken doelen
> * Installatie kopie maken
> * Geïmplementeerde modellen
> * Instellingen voor python-`logging`

[Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md). Gebruik de [gids](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) voor meer informatie over de SDK.

## <a name="training-models-and-compute-target-logging"></a>Trainings modellen en logboek registratie van Compute-doelen

Er zijn meerdere manieren om logboek registratie in te scha kelen tijdens het model trainings proces en de voor beelden die worden weer gegeven, illustreren veelvoorkomende ontwerp patronen. U kunt met behulp van de functie `start_logging` op de `Experiment`-klasse eenvoudig uitvoerings gegevens vastleggen in uw werk ruimte in de Cloud.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Zie de referentie documentatie voor de klasse [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) voor extra logboek registratie functies.

Als u de lokale logboek registratie van de toepassings status tijdens de trainings voortgang wilt inschakelen, gebruikt u de para meter `show_output`. Als u uitgebreide logboek registratie inschakelt, kunt u de details van het trainings proces en informatie over externe bronnen of COMPUTE-doelen bekijken. Gebruik de volgende code om logboek registratie in te scha kelen bij het verzenden van experimenten.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

U kunt ook dezelfde para meter gebruiken in de functie `wait_for_completion` van de resulterende uitvoering.

```python
run.wait_for_completion(show_output=True)
```

De SDK biedt ook ondersteuning voor het gebruik van het standaard pakket voor python-logboek registratie in bepaalde scenario's voor training. In het volgende voor beeld wordt een logboek registratie niveau van `INFO` in een `AutoMLConfig`-object ingeschakeld.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

U kunt ook de para meter `show_output` gebruiken bij het maken van een persistent Compute-doel. Geef de para meter op in de functie `wait_for_completion` om logboek registratie in te scha kelen tijdens het maken van het reken doel.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Logboek registratie tijdens het maken van de installatie kopie

Wanneer u logboek registratie inschakelt tijdens het maken van een installatie kopie, kunt u eventuele fouten tijdens het bouw proces zien. Stel de para meter `show_output` in voor de functie `wait_for_deployment()`.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                       image=image,
                                       name="example-image",
                                       workspace=ws
                                       )

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Logboek registratie voor geïmplementeerde modellen

Als u logboeken van een eerder geïmplementeerde webservice wilt ophalen, laadt u de service en gebruikt u de functie `get_logs()`. De logboeken bevatten mogelijk gedetailleerde informatie over eventuele fouten die zijn opgetreden tijdens de implementatie.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

U kunt ook aangepaste stack traceringen voor uw webservice registreren door Application Insights in te scha kelen, waarmee u aanvraag-en reactie tijden, fout tarieven en uitzonde ringen kunt bewaken. Roep de `update()`-functie aan op een bestaande webservice om Application Insights in te scha kelen.

```python
service.update(enable_app_insights=True)
```

Zie de [procedure](how-to-enable-app-insights.md) voor meer informatie over het werken met Application Insights in azure machine learning Studio.

## <a name="python-native-logging-settings"></a>Systeem eigen logboek registratie-instellingen van python

Bepaalde Logboeken in de SDK bevatten mogelijk een fout die aangeeft dat u het logboek registratie niveau wilt instellen op fout opsporing. Als u het logboek registratie niveau wilt instellen, voegt u de volgende code toe aan het script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

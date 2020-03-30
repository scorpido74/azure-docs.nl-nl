---
title: Logboekregistratie inschakelen bij Azure Machine Learning
description: Meer informatie over het inschakelen van logboekregistratie in Azure Machine Learning met behulp van zowel het standaard Python-logboekpakket als het gebruik van SDK-specifieke functionaliteit.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78396152"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Logboekregistratie inschakelen bij Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met de Azure Machine Learning Python SDK u logboekregistratie inschakelen met behulp van zowel het standaard Python-logboekregistratiepakket als met behulp van SDK-specifieke functionaliteit, zowel voor lokale logboekregistratie als logboekregistratie voor uw werkruimte in de portal. Logboeken bieden ontwikkelaars realtime informatie over de toepassingsstatus en kunnen helpen bij het diagnosticeren van fouten of waarschuwingen. In dit artikel leert u verschillende manieren om logboekregistratie in de volgende gebieden in te schakelen:

> [!div class="checklist"]
> * Trainingsmodellen en rekendoelen
> * Beeldmaken
> * Geïmplementeerde modellen
> * Python-instellingen `logging`

[Maak een Azure Machine Learning-werkruimte](how-to-manage-workspace.md). Gebruik de [handleiding](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) voor meer informatie over de SDK.

## <a name="training-models-and-compute-target-logging"></a>Trainingsmodellen en compute target logging

Er zijn meerdere manieren om logboekregistratie in te schakelen tijdens het modeltrainingsproces en de getoonde voorbeelden illustreren algemene ontwerppatronen. U run-gerelateerde gegevens eenvoudig registreren op uw `start_logging` werkruimte `Experiment` in de cloud met behulp van de functie in de klasse.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Zie de referentiedocumentatie voor de klasse [Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) voor extra logboekfuncties.

Als u de lokale logboekregistratie van `show_output` de toepassingsstatus tijdens de voortgang van de training wilt inschakelen, gebruikt u de parameter. Met het inschakelen van verbose logging u details uit het trainingsproces zien, evenals informatie over externe bronnen of rekendoelen. Gebruik de volgende code om logboekregistratie in te schakelen bij het indienen van het experiment.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

U dezelfde parameter ook `wait_for_completion` gebruiken in de functie op de resulterende run.

```python
run.wait_for_completion(show_output=True)
```

De SDK ondersteunt ook het gebruik van het standaard python-registratiepakket in bepaalde scenario's voor training. In het volgende voorbeeld `INFO` wordt `AutoMLConfig` een logboekniveau van in een object mogelijk gemaakt.

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

U de `show_output` parameter ook gebruiken bij het maken van een blijvend berekend doel. Geef de parameter `wait_for_completion` in de functie op om logboekregistratie in te schakelen tijdens het maken van compute target.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Logboekregistratie voor geïmplementeerde modellen

Als u logboeken wilt ophalen van een eerder `get_logs()` geïmplementeerde webservice, laadt u de service en gebruikt u de functie. De logboeken kunnen gedetailleerde informatie bevatten over eventuele fouten die tijdens de implementatie zijn opgetreden.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

U ook aangepaste stacksporen voor uw webservice registreren door Application Insights in te schakelen, waarmee u aanvraag-/reactietijden, uitvalpercentages en uitzonderingen controleren. Bel `update()` de functie op een bestaande webservice om Application Insights in te schakelen.

```python
service.update(enable_app_insights=True)
```

Zie [Gegevens van ML-eindpunten van de webservice controleren en verzamelen](how-to-enable-app-insights.md)voor meer informatie.

## <a name="python-native-logging-settings"></a>Instellingen voor native logboekregistratie van Python

Bepaalde logboeken in de SDK kunnen een fout bevatten die u instrueert om het logboekregistratieniveau in te stellen op FOUTOPSPORING. Als u het logboekniveau wilt instellen, voegt u de volgende code toe aan uw script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens van ML-eindpunten van webservice controleren en verzamelen](how-to-enable-app-insights.md)
---
title: Azure Machine Learning-pijplijnen plannen
titleSuffix: Azure Machine Learning
description: Plan Azure Machine Learning-pijplijnen met de Azure Machine Learning SDK voor Python. Met geplande pijplijnen u routinematige, tijdrovende taken zoals gegevensverwerking, training en monitoring automatiseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 8e1e718fa4e6660d72203ac98bb6d427cdba2059
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024554"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Machine learning-pijplijnen plannen met Azure Machine Learning SDK voor Python

In dit artikel leert u hoe u een pijplijn programmatisch plant om op Azure uit te voeren. U ervoor kiezen om een schema te maken op basis van verstreken tijd of op wijzigingen in het bestandssysteem. Tijdgebaseerde schema's kunnen worden gebruikt om routinetaken te verzorgen, zoals monitoring voor gegevensdrift. Op wijzigingen gebaseerde schema's kunnen worden gebruikt om te reageren op onregelmatige of onvoorspelbare wijzigingen, zoals het uploaden van nieuwe gegevens of het bewerken van oude gegevens. Nadat je hebt geleerd hoe je schema's maken, leer je hoe je ze ophalen en deactiveren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree)aan.

* Een Python-omgeving waarin de Azure Machine Learning SDK voor Python is geïnstalleerd. Zie [Herbruikbare omgevingen maken en beheren voor training en implementatie met Azure Machine Learning voor](how-to-use-environments.md) meer informatie.

* Een Machine Learning-werkruimte met een gepubliceerde pijplijn. U de pijplijn gebruiken die is ingebouwd in [Het maken en uitvoeren van machine learning-pijplijnen met Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="initialize-the-workspace--get-data"></a>De werkruimte initialiseren & gegevens optevragen

Als u een pijplijn wilt plannen, hebt u een verwijzing nodig naar uw werkruimte, de id van de gepubliceerde pijplijn en de naam van het experiment waarin u de planning wilt maken. U deze waarden krijgen met de volgende code:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Een planning maken

Als u een pijplijn op terugkerende basis wilt uitvoeren, maakt u een planning. Een `Schedule` associeert een pijplijn, een experiment en een trigger. De trigger kan`ScheduleRecurrence` een trigger zijn die het wachten tussen runs beschrijft of een Datastore-pad dat een map opgeeft om op wijzigingen te letten. In beide gevallen hebt u de pijplijn-id en de naam van het experiment nodig om de planning te maken.

Importeer de klassen `Schedule` en `ScheduleRecurrence` klassen boven aan uw python-bestand:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Een tijdsschema maken

De `ScheduleRecurrence` constructeur heeft `frequency` een vereist argument dat een van de volgende tekenreeksen moet zijn: "Minuut", "Uur", "Dag", "Week" of "Maand". Het vereist ook `interval` een integer argument `frequency` waarin wordt aangegeven hoeveel van de eenheden moeten verstrijken tussen de planning begint. Met optionele argumenten u specifieker zijn over de begintijden, zoals beschreven in de [SDK-documenten schema.Optional](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)arguments allow you to be specific about starting times, as detailed in the ScheduleRecurrence SDK docs.

Maak `Schedule` een die elke 15 minuten een run begint:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Een op wijzigingen gebaseerd schema maken

Pijplijnen die worden geactiveerd door bestandswijzigingen, kunnen efficiënter zijn dan tijdsgebaseerde planningen. U bijvoorbeeld een voorbewerkingsstap uitvoeren wanneer een bestand wordt gewijzigd of wanneer een nieuw bestand wordt toegevoegd aan een gegevensmap. U wijzigingen in een gegevensarchief of wijzigingen in een specifieke map in het gegevensarchief controleren. Als u een specifieke map controleert, leiden wijzigingen binnen submappen van die map _niet_ tot een run.

Als u een bestandreactief `Schedule`wilt `datastore` maken, moet u de parameter in de aanroep instellen op [Planning.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Als u een map `path_on_datastore` wilt controleren, stelt u het argument in.

Met `polling_interval` het argument u in enkele minuten de frequentie opgeven waarop het gegevensarchief wordt gecontroleerd op wijzigingen.

Als de pijplijn is gemaakt met een [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)u die variabele `data_path_parameter_name` instellen op de naam van het gewijzigde bestand door het argument in te stellen.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Optionele argumenten bij het maken van een planning

Naast de eerder besproken argumenten u `status` het `"Disabled"` argument instellen op het maken van een inactieve planning. Ten slotte `continue_on_step_failure` u hiermee een Booleaan passeren die het standaardfoutgedrag van de pijplijn overschrijft.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Azure Logic Apps gebruiken voor complexere werkstromen

Azure Logic Apps ondersteunt complexere werkstromen en is veel breder geïntegreerd dan Azure Machine Learning-pijplijnen. Zie [Een run van een Machine Learning-pijplijn](how-to-trigger-published-pipeline.md) activeren vanuit een Logische app voor meer informatie.

## <a name="view-your-scheduled-pipelines"></a>Uw geplande pijplijnen weergeven

Navigeer in uw webbrowser naar Azure Machine Learning. Kies in het gedeelte **Eindpunten** van het navigatiedeelvenster de optie **Pijplijneindpunten**. Dit brengt u naar een lijst met de pijplijnen die zijn gepubliceerd in de werkruimte.

![Pagina Pijplijnen van AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Op deze pagina ziet u beknopte informatie over alle pijplijnen in de werkruimte: namen, beschrijvingen, status, enzovoort. Inzoomen door in uw pijplijn te klikken. Op de resulterende pagina zijn er meer details over uw pijplijn en u inzoomen op afzonderlijke uitvoeringen.

## <a name="deactivate-the-pipeline"></a>De pijplijn deactiveren

Als u `Pipeline` een die is gepubliceerd, maar niet gepland, u deze uitschakelen met:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Als de pijplijn is gepland, moet u eerst de planning annuleren. Haal de id van de planning op uit de portal of door het uitvoeren van:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Zodra u `schedule_id` de wilt uitschakelen, voert u het:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Als u `Schedule.list(ws)` vervolgens opnieuw loopt, moet u een lege lijst krijgen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de Azure Machine Learning SDK voor Python gebruikt om een pijplijn op twee verschillende manieren te plannen. Een schema keert terug op basis van verstreken kloktijd. De andere planning wordt uitgevoerd als `Datastore` een bestand wordt gewijzigd op een opgegeven of in een map in dat archief. U zag hoe u de portal gebruiken om de pijplijn en individuele uitvoeringen te onderzoeken. Ten slotte hebt u geleerd hoe u een planning uitschakelt, zodat de pijplijn niet meer wordt uitgevoerd.

Zie voor meer informatie:

> [!div class="nextstepaction"]
> [Azure Machine Learning-pijplijnen gebruiken voor batchscores](tutorial-pipeline-batch-scoring-classification.md)

* Meer informatie over [pijplijnen](concept-ml-pipelines.md)
* Meer informatie over [het verkennen van Azure Machine Learning met Jupyter](samples-notebooks.md)


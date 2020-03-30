---
title: Versiebeheer van gegevenssets
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw gegevenssets het beste maken en hoe versiebeheer werkt met machine learning-pijplijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528469"
---
# <a name="version-and-track-datasets-in-experiments"></a>Gegevenssets voor versies en bijhouden in experimenten
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning-gegevenssets gebruiken en bijhouden voor reproduceerbaarheid. Datasetversiewerk is een manier om de status van uw gegevens een bladwijzer te geven, zodat u een specifieke versie van de gegevensset toepassen voor toekomstige experimenten.

Typische versiescenario's:

* Wanneer er nieuwe gegevens beschikbaar zijn voor omscholing
* Wanneer u verschillende benaderingen voor gegevensvoorbereiding of functieengineering toepast

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

- [Azure Machine Learning SDK voor Python geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Deze SDK bevat het [azureml-datasetspakket.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)
    
- Een [Azure Machine Learning-werkruimte](concept-workspace.md). Een bestaande ophalen door de volgende code uit te voeren of [een nieuwe werkruimte te maken.](how-to-manage-workspace.md)

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Een [Azure Machine Learning-gegevensset](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Gegevenssetversies registreren en ophalen

Door een gegevensset te registreren, u deze versie, hergebruik en delen in experimenten en met collega's. U meerdere gegevenssets onder dezelfde naam registreren en een specifieke versie ophalen op naam en versienummer.

### <a name="register-a-dataset-version"></a>Een gegevenssetversie registreren

De volgende code registreert een `titanic_ds` nieuwe versie `create_new_version` van `True`de gegevensset door de parameter in te stellen op . Als er geen `titanic_ds` bestaande gegevensset is geregistreerd bij de werkruimte, `titanic_ds` maakt de code een nieuwe gegevensset met de naam en stelt de versie de versie in op 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
U ook een nieuwe versie van een gegevensset registreren op 

### <a name="retrieve-a-dataset-by-name"></a>Een gegevensset ophalen op naam

Standaard retourneert de methode `Dataset` [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) in de klasse de nieuwste versie van de gegevensset die bij de werkruimte is geregistreerd. 

De volgende code krijgt `titanic_ds` versie 1 van de gegevensset.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Aanbevolen procedures voor versiebeheer

Wanneer u een gegevenssetversie maakt, maakt u *geen* extra kopie van gegevens met de werkruimte. Omdat gegevenssets verwijzingen zijn naar de gegevens in uw opslagservice, hebt u één enkele bron van waarheid, beheerd door uw opslagservice.

>[!IMPORTANT]
> Als de gegevens waarop de gegevens van uw gegevensset verwijzen, worden overschreven of verwijderd, wordt de wijziging *niet* teruggedraaid door een specifieke versie van de gegevensset aan te roepen.

Wanneer u gegevens uit een gegevensset laadt, wordt de huidige gegevensinhoud waarnaar naar de gegevensset verwijst, altijd geladen. Als u ervoor wilt zorgen dat elke gegevenssetversie reproduceerbaar is, raden we u aan de gegevensinhoud waarnaar wordt verwezen door de gegevenssetversie niet te wijzigen. Wanneer er nieuwe gegevens binnenkomen, slaat u nieuwe gegevensbestanden op in een afzonderlijke gegevensmap en maakt u vervolgens een nieuwe gegevenssetversie om gegevens uit die nieuwe map op te nemen.

In de volgende afbeeldings- en voorbeeldcode wordt de aanbevolen manier weergegeven om uw gegevensmappen te structureren en gegevenssetsversies te maken die verwijzen naar die mappen:

![Mapstructuur](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>Een gegevensset voor pijplijnuitvoer versie

U een gegevensset gebruiken als invoer en uitvoer van elke machine learning-pijplijnstap. Wanneer u pijplijnen opnieuw uitvoert, wordt de uitvoer van elke pijplijnstap geregistreerd als een nieuwe gegevenssetversie.

Omdat Machine Learning-pijplijnen de uitvoer van elke stap in een nieuwe map vullen telkens wanneer de pijplijn wordt uitgevoerd, zijn de versiegegevenssets reproduceerbaar. Meer informatie over [gegevenssets in pijplijnen](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Gegevenssets in experimenten bijhouden

Voor elk Machine Learning-experiment u de gegevenssets die `Run` als invoer worden gebruikt, eenvoudig traceren via het experimentobject.

De volgende code [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) gebruikt de methode om bij te houden welke invoergegevenssets zijn gebruikt bij de experimentrun:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

U ook `input_datasets` de van https://ml.azure.com/experimenten met behulp van. 

In de volgende afbeelding ziet u waar u de invoergegevensset van een experiment in Azure Machine Learning-studio vinden. Ga in dit voorbeeld naar het deelvenster **Experimenten** en open het `keras-mnist`tabblad **Eigenschappen** voor een specifieke run van het experiment.

![Invoergegevenssets](./media/how-to-version-track-datasets/input-datasets.png)

Gebruik de volgende code om modellen te registreren met gegevenssets:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Na registratie u de lijst met modellen zien die https://ml.azure.com/bij de gegevensset zijn geregistreerd met behulp van Python of naar.

De volgende weergave is in het deelvenster **Gegevenssets** onder **Activa**. Selecteer de gegevensset en selecteer vervolgens het tabblad **Modellen** voor een lijst met modellen die bij de gegevensset zijn geregistreerd. 

![Modellen voor invoergegevenssets](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Volgende stappen

* [Trainen met gegevenssets](how-to-train-with-datasets.md)
* [Meer voorbeeldgegevenssetnotitieblokken](https://aka.ms/dataset-tutorial)

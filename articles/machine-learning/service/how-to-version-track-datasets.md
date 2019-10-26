---
title: Versie beheer van gegevensset
titleSuffix: Azure Machine Learning service
description: Meer informatie over de best practice voor het versie nummer van uw gegevens sets en hoe versie beheer werkt met machine learning pijp lijnen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902000"
---
# <a name="version-and-track-datasets-in-experiments"></a>Versie gegevens sets in experimenten bijhouden

In deze procedure leert u hoe u versie en Azure Machine Learning gegevens sets voor reproduceerbaarheid kunt bijhouden. Het versie beheer van de gegevensset is een manier om de status van uw gegevens te bookmarkren, zodat u een specifieke versie van de gegevensset kunt Toep assen voor toekomstige experimenten.

Typische scenario's die u moet overwegen voor versie beheer:

* Wanneer nieuwe gegevens beschikbaar zijn voor retraining.
* Wanneer u verschillende benaderingen voor gegevens voorbereiding of functie techniek toepast.

## <a name="prerequisites"></a>Vereisten

Voor deze procedure hebt u het volgende nodig:

- De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket [met de azureml-gegevens sets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) .
    
- Een [Azure machine learning-werk ruimte](concept-workspace.md). Haal een bestaande op met de volgende code of [Maak een nieuwe werk ruimte](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Een [Azure machine learning-gegevensset](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Versie van gegevensset registreren en ophalen

Als u een gegevensset registreert, kunt u deze versie, hergebruiken en delen in experimenten en met collega's. U kunt meerdere gegevens sets onder dezelfde naam registreren en een specifieke versie ophalen op naam en versie nummer.

### <a name="register-a-dataset-version"></a>Een gegevensset-versie registreren

Met de volgende code wordt een nieuwe versie van de gegevensset geregistreerd, `titanic_ds`, door de para meter `create_new_version` in te stellen op `True`. Als er geen bestaande `titanic_ds` zijn geregistreerd bij de werk ruimte, wordt er een nieuwe gegevensset gemaakt met de naam `titanic_ds` en wordt de versie ingesteld op 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Een gegevensset op naam ophalen

De methode [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) voor de klasse `Dataset` retourneert standaard de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte. 

Met de volgende code wordt versie 1 van de `titanic_ds` gegevensset opgehaald.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Versie beheer best practice

Wanneer u een gegevensset-versie maakt, maakt u **geen** extra kopie van gegevens met de werk ruimte. Gegevens sets zijn verwijzingen naar de data in uw Storage-service, zodat u slechts één bron van waarheid hebt die wordt beheerd door uw opslag service. 

>[!IMPORTANT]
> Als de gegevens waarnaar wordt verwezen door uw gegevensset worden overschreven of verwijderd, kan het aanroepen van een specifieke versie van de gegevensset de wijziging niet ongedaan maken. 

Bij het laden van gegevens uit een gegevensset wordt altijd de huidige gegevens inhoud waarnaar wordt verwezen door de gegevensset geladen. Als u de reproduceer baarheid van elke gegevensset-versie wilt waarborgen, wordt u aangeraden geen gegevens inhoud te wijzigen waarnaar wordt verwezen door de gegevensset-versie. Als er nieuwe gegevens binnenkomen in, slaat u nieuwe gegevens bestanden op in een afzonderlijke gegevensmap en maakt u een nieuwe gegevensset-versie om gegevens van die nieuwe gegevensmap op te halen.

De volgende afbeeldings-en voorbeeld code tonen de aanbevolen manier om uw gegevens mappen te structureren en gegevensset-versies te maken die verwijzen naar deze mappen.

![Mapstructuur](media/how-to-version-datasets/folder-image.png)

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

## <a name="version-a-pipeline-output-dataset"></a>Een gegevensset voor een pijplijn uitvoer

U kunt dataset gebruiken als invoer en uitvoer van elke machine learning (ML) pijplijn stap. Wanneer u pijp lijnen opnieuw uitvoert, wordt de uitvoer van elke pijplijn stap geregistreerd als een nieuwe gegevensset-versie. 

Omdat ML-pijp lijnen de uitvoer van elke Step Into een nieuwe map worden gevuld telkens wanneer de pijp lijn opnieuw wordt uitgevoerd, worden de versie van de uitvoer gegevens sets gereproduceerd.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Gegevens sets bijhouden in experimenten

Voor elk machine learning experiment kunt u eenvoudig de gegevens sets traceren die worden gebruikt als invoer via het `Run`-object van het geregistreerde model.

Gebruik de volgende code om modellen met gegevens sets te registreren.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Na de registratie kunt u de lijst met modellen zien die zijn geregistreerd bij de gegevensset met behulp van python of de pagina voor het land van de [werk ruimte](https://ml.azure.com/).

De volgende code gebruikt de [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) methode om bij te houden welke invoer gegevens sets zijn gebruikt bij het uitvoeren van het experiment.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

U kunt ook de `input_datasets` van experimenten vinden met behulp van de [werk ruimte landings pagina (preview)](https://ml.azure.com/). 

In de volgende afbeelding ziet u waar u de invoer gegevensset van een experiment kunt vinden op de landings pagina voor de werk ruimte. Voor dit voor beeld gaat u naar het deel venster **experimenten** en opent u het tabblad **Eigenschappen** voor een specifieke uitvoering van uw experiment, `keras-mnist`. 

![Invoer gegevens sets](media/how-to-version-datasets/input-datasets.png)

U kunt ook de modellen vinden die uw gegevensset hebben gebruikt met de portaal pagina voor de werk ruimte. De volgende weer gave is afkomstig uit de Blade gegevens sets onder assets. Selecteer de gegevensset en navigeer naar het tabblad modellen voor een lijst van de modellen die deze gegevensset gebruiken. 

![Modellen van invoer gegevens sets](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Volgende stappen

* [Train met gegevens sets](how-to-train-with-datasets.md).
* [Meer voorbeeld notitieblok van gegevens sets](https://aka.ms/dataset-tutorial).

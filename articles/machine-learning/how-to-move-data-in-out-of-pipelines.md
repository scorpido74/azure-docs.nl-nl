---
title: Invoer- en uitvoergegevens van ML-pijplijnen
titleSuffix: Azure Machine Learning
description: Gegevens voorbereiden, consumeren en genereren in Azure Machine Learning-pijplijnen
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879763"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Gegevens verplaatsen naar en tussen ML-pijplijnstappen (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Gegevens staan centraal in machine learning-pijplijnen. In dit artikel vindt u code voor het importeren, transformeren en verplaatsen van gegevens tussen stappen in een Azure Machine Learning-pijplijn. Zie [Access-gegevens in Azure-opslagservices](how-to-access-data.md)voor een overzicht van hoe gegevens werken in Azure Machine Learning. Zie Wat zijn Azure Machine Learning-pijplijnen voor de voordelen en structuur van Azure Machine [Learning-pijplijnen.](concept-ml-pipelines.md)

In dit artikel ziet u hoe u:

- Objecten `Dataset` gebruiken voor reeds bestaande gegevens
- Toegang tot gegevens binnen uw stappen
- Gegevens `Dataset` splitsen in subsets, zoals verzamelingen voor training en validatie
- Objecten `PipelineData` maken om gegevens over te zetten naar de volgende pijplijnstap
- Objecten `PipelineData` gebruiken als invoer voor pijplijnstappen
- Nieuwe `Dataset` objecten `PipelineData` maken van u die wilt blijven bestaan

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

- De [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot Azure Machine [Learning-studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  Maak [een Azure Machine Learning-werkruimte](how-to-manage-workspace.md) of gebruik een bestaande werkruimte via de Python SDK. Importeer `Workspace` de `Datastore` klasse en de klasse en `config.json` laad `from_config()`uw abonnementsgegevens uit het bestand met de functie . Met deze functie wordt standaard gezocht naar het JSON-bestand in de huidige map, `from_config(path="your/file/path")`maar u ook een padparameter opgeven die naar het bestand moet wijzen met behulp van.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Sommige reeds bestaande gegevens. In dit artikel wordt kort het gebruik van een [Azure blobcontainer](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)weergegeven.

- Optioneel: een bestaande machine learning-pijplijn, zoals beschreven in [Machine Learning-pijplijnen maken en uitvoeren met Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="use-dataset-objects-for-pre-existing-data"></a>Objecten `Dataset` gebruiken voor reeds bestaande gegevens 

De voorkeursmanier om gegevens in een pijplijn in te nemen, is door een [gegevenssetobject](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) te gebruiken. `Dataset`objecten staan voor permanente gegevens die beschikbaar zijn in een werkruimte.

Er zijn vele manieren `Dataset` om objecten te maken en te registreren. Tabelgegevenssets zijn beschikbaar voor afgebakende gegevens in een of meer bestanden. Bestandsgegevenssets zijn voor binaire gegevens (zoals afbeeldingen) of voor gegevens die u zult ontleden. De eenvoudigste programmatische `Dataset` manieren om objecten te maken, zijn het gebruik van bestaande blobs in werkruimteopslag of openbare URL's:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Zie [Azure Machine Learning-gegevenssets maken](how-to-create-register-datasets.md)voor meer opties voor het maken van gegevenssets met verschillende opties en uit verschillende bronnen. 

### <a name="pass-datasets-to-your-script"></a>Gegevenssets doorgeven aan uw script

Als u het pad van de gegevensset wilt doorgeven aan uw script, gebruikt u de `Dataset` methode van `as_named_input()` het object. U het resulterende `DatasetConsumptionConfig` object als argument doorgeven aan `inputs` uw script of, door het argument `Run.get_context().input_datasets[]`te gebruiken voor uw pijplijnscript, u de gegevensset ophalen met behulp van.

Nadat u een benoemde invoer hebt gemaakt, kunt `as_mount()` `as_download()`u de toegangsmodus kiezen: of . Als uw script alle bestanden in uw gegevensset verwerkt en de schijf op uw compute resource groot genoeg is voor de gegevensset, is de downloadtoegangsmodus de betere keuze. De downloadtoegangsmodus voorkomt dat de gegevens tijdens de runtime worden gestreamd. Als uw script toegang heeft tot een subset van de gegevensset of als deze te groot is voor uw gegevensverwerking, gebruikt u de toegangsmodus voor het monteren. Voor meer informatie, lees [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Ga als lid van uw pijplijnstap:

1. Een `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` object gebruiken of (geen 's' aan het eind) gebruiken om een `DatasetConsumptionConfig` object te maken
1. De `as_mount()` `as_download()` toegangsmodus gebruiken of instellen
1. De gegevenssets doorgeven aan uw `arguments` pijplijnstappen `inputs` met behulp van het argument of het argument

In het volgende fragment ziet u het `PythonScriptStep` algemene patroon van het combineren van deze stappen binnen de constructor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

U ook methoden `random_split()` gebruiken `take_sample()` zoals en om meerdere ingangen te maken of de hoeveelheid gegevens die wordt doorgegeven aan uw pijplijnstap te verminderen:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Toegang tot gegevenssets in uw script

Benoemde ingangen voor uw pijplijnstapscript zijn `Run` beschikbaar als een woordenboek in het object. Haal het `Run` actieve `Run.get_context()` object op met behulp `input_datasets`van het woordenboek met benoemde invoer en haal het woordenboek met de benoemde invoer op met behulp van . Als u `DatasetConsumptionConfig` het object `arguments` hebt doorgegeven met het argument in plaats van het `inputs` argument, krijgt u toegang tot de gegevens met behulp van `ArgParser` code. Beide technieken worden gedemonstreerd in het volgende fragment.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

De doorgegeven waarde is het pad naar het gegevenssetbestand(en).

Het is ook mogelijk om `Dataset` direct toegang te krijgen tot een geregistreerde. Aangezien geregistreerde gegevenssets permanent zijn en worden gedeeld in een werkruimte, u ze rechtstreeks ophalen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Gebruik `PipelineData` voor tussenliggende gegevens

Hoewel `Dataset` objecten permanente gegevens vertegenwoordigen, worden [PipelineData-objecten](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) gebruikt voor tijdelijke gegevens die worden uitgevoerd vanuit pijplijnstappen. Omdat de levensduur `PipelineData` van een object langer is dan één pijplijnstap, definieert u deze in het script voor pijplijndefinitie. Wanneer u `PipelineData` een object maakt, moet u een naam en een gegevensarchief opgeven waarop de gegevens zich bevinden. Geef `PipelineData` uw object(en) `PythonScriptStep` door `arguments` aan `outputs` uw object met zowel de argumenten _als_ de argumenten:

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

U ervoor `PipelineData` kiezen om uw object te maken met behulp van een toegangsmodus die een onmiddellijke upload biedt. Stel in `PipelineData`dat geval het argument `upload_mode` `"upload"` in en `output_path_on_compute` gebruik het argument om het pad op te geven waarop u de gegevens schrijft:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Gebruiken `PipelineData` als uitvoer van een trainingsstap

Binnen uw pijplijn `PythonScriptStep`u de beschikbare uitvoerpaden ophalen met behulp van de argumenten van het programma. Als deze stap de eerste is en de uitvoergegevens initialiseert, moet u de map maken op het opgegeven pad. U dan schrijven welke bestanden u `PipelineData`wilt worden opgenomen in de.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Als u `PipelineData` uw `is_directory` met het `True`argument ingesteld op, zou `os.makedirs()` het genoeg zijn om gewoon het gesprek uit te voeren en dan zou je vrij zijn om te schrijven welke bestanden u wilde het pad. Zie de referentiedocumentatie [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) voor meer informatie.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Lezen `PipelineData` als ingangen voor niet-initiële stappen

Nadat de eerste pijplijnstap bepaalde `PipelineData` gegevens naar het pad heeft geschreven en het een uitvoer van die eerste stap wordt, kan deze worden gebruikt als invoer voor een latere stap:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

De waarde `PipelineData` van een invoer is het pad naar de vorige uitvoer. Als, zoals eerder is aangegeven, de eerste stap één bestand heeft geschreven, kan het als volgt eruit zien: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Objecten `PipelineData` converteren `Dataset`naar s

Als u uw `PipelineData` functie langer dan de duur van een `as_dataset()` run beschikbaar wilt `Dataset`maken, gebruikt u de functie om deze om te zetten naar een . U dan `Dataset`registreren de, waardoor het een eersteklas burger in uw werkruimte. Aangezien `PipelineData` uw object elke keer dat de pijplijn wordt uitgevoerd een ander `create_new_version` `True` pad heeft, is het ten zeerste aan te raden om een `Dataset` gemaakt object `PipelineData` te registreren.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [Machine learning-pijplijnen maken en uitvoeren met Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)

---
title: Invoer-en uitvoer gegevens van ML-pijp lijnen
titleSuffix: Azure Machine Learning
description: Gegevens in Azure Machine Learning pijp lijnen voorbereiden, gebruiken en genereren
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879763"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Gegevens verplaatsen naar en tussen ML-pijplijnstappen (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Gegevens zijn centraal voor machine learning pijp lijnen. Dit artikel bevat code voor het importeren, transformeren en verplaatsen van gegevens tussen stappen in een Azure Machine Learning-pijp lijn. Zie [toegang tot gegevens in azure Storage-services](how-to-access-data.md)voor een overzicht van de werking van gegevens in azure machine learning. Zie [Wat zijn Azure machine learning pijp lijnen?](concept-ml-pipelines.md)voor de voor delen en de structuur van Azure machine learning pijp lijnen.

In dit artikel wordt uitgelegd hoe u:

- Objecten `Dataset` gebruiken voor bestaande gegevens
- Toegang tot gegevens in uw stappen
- Gegevens `Dataset` splitsen in subsets, zoals training en validatie subsets
- Objecten `PipelineData` maken voor het overdragen van gegevens naar de volgende pijplijn stap
- Objecten `PipelineData` gebruiken als invoer voor pijplijn stappen
- Nieuwe `Dataset` objecten maken die `PipelineData` u wilt behouden

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een bestaand item via de PYTHON-SDK. Importeer de `Workspace` - `Datastore` en-klasse en laad uw abonnements gegevens uit het `config.json` bestand met behulp van de functie `from_config()`. Deze functie zoekt standaard naar het JSON-bestand in de huidige map, maar u kunt ook een para meter Path opgeven om naar het bestand te `from_config(path="your/file/path")`verwijzen met.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Een aantal bestaande gegevens. In dit artikel wordt een kort overzicht gegeven van het gebruik van een [Azure Blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Optioneel: een bestaande machine learning pijp lijn, zoals de pijplijn die wordt beschreven in [machine learning pijp lijnen maken en uitvoeren met Azure machine learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Objecten `Dataset` gebruiken voor bestaande gegevens 

De voorkeurs manier om gegevens op te nemen in een pijp lijn is het gebruik van een object [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) . `Dataset`objecten vertegenwoordigen permanente gegevens die beschikbaar zijn in een werk ruimte.

Er zijn veel manieren om objecten te maken `Dataset` en te registreren. Tabellaire gegevens sets zijn voor een scheidings teken dat beschikbaar is in een of meer bestanden. Bestand gegevens sets zijn voor binaire gegevens (zoals installatie kopieën) of voor gegevens die u gaat parseren. De eenvoudigste manier om objecten te `Dataset` maken, is het gebruik van bestaande blobs in werkruimte opslag of open bare url's:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Zie [Azure machine learning gegevens sets maken](how-to-create-register-datasets.md)voor meer opties voor het maken van gegevens sets met verschillende opties en uit verschillende bronnen, het registreren van de gegevens in de gebruikers interface van de Azure machine learning. 

### <a name="pass-datasets-to-your-script"></a>Gegevens sets door geven aan het script

Als u het pad van de gegevensset wilt door geven aan uw `Dataset` script, `as_named_input()` gebruikt u de methode van het object. U kunt het resulterende `DatasetConsumptionConfig` object door geven aan uw script als een argument of, met behulp `inputs` van het argument voor uw pijplijn script, u de gegevensset kunt ophalen `Run.get_context().input_datasets[]`met.

Zodra u een benoemde invoer hebt gemaakt, kunt u de toegangs modus kiezen: `as_mount()` of `as_download()`. Als uw script alle bestanden in uw gegevensset verwerkt en de schijf op de reken resource groot genoeg is voor de gegevensset, is de Download toegangs modus de beste keuze. De Download toegangs modus voor komt dat de overhead van het streamen van gegevens tijdens runtime wordt voor komen. Als uw script toegang krijgt tot een subset van de gegevensset of als deze te groot is voor uw compute, moet u de toegangs modus voor koppelen gebruiken. Lees [koppelen versus downloaden](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download) voor meer informatie.

Een gegevensset door geven aan de pijplijn stap:

1. Gebruik `TabularDataset.as_named_inputs()` of `FileDataset.as_named_input()` (geen ' aan het einde) om een `DatasetConsumptionConfig` object te maken
1. De `as_mount()` toegangs `as_download()` modus gebruiken of instellen
1. Geef de gegevens sets door aan de pijplijn stappen met behulp `arguments` van `inputs` het of het argument

Het volgende code fragment toont het algemene patroon van het combi neren van `PythonScriptStep` deze stappen in de constructor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

U kunt ook methoden als `random_split()` gebruiken `take_sample()` om meerdere invoer te maken of de hoeveelheid gegevens te verminderen die worden door gegeven aan de pijplijn stap:

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

### <a name="access-datasets-within-your-script"></a>Toegang tot gegevens sets in uw script

Benoemde invoer van het script voor de pijplijn stap is beschikbaar als een woorden lijst `Run` binnen het object. Haal het actieve `Run` object op `Run.get_context()` met behulp van en haal vervolgens de woorden `input_datasets`lijst met de naam invoer op met behulp van. `DatasetConsumptionConfig` Als u het object hebt door gegeven `arguments` met behulp van `inputs` het argument in plaats van het `ArgParser` argument, opent u de gegevens met behulp van code. Beide technieken worden in het volgende code fragment getoond.

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

De door gegeven waarde is het pad naar de bestand (en) van de gegevensset.

Het is ook mogelijk om rechtstreeks toegang te `Dataset` krijgen tot een geregistreerd. Omdat geregistreerde gegevens sets permanent zijn en worden gedeeld in een werk ruimte, kunt u ze direct ophalen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Gebruiken `PipelineData` voor tussenliggende gegevens

Hoewel `Dataset` objecten persistente gegevens vertegenwoordigen, worden [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) -objecten gebruikt voor tijdelijke gegevens die worden uitgevoerd vanuit pijplijn stappen. Omdat de levens duur van `PipelineData` een object langer is dan één pijplijn stap, definieert u deze in het definitie script van de pijp lijn. Wanneer u een `PipelineData` -object maakt, moet u een naam en een gegevens opslag opgeven waarop de gegevens worden opgeslagen. Geef uw `PipelineData` object (en) aan uw `PythonScriptStep` objecten _door met behulp van_ de `arguments` `outputs` argumenten en:

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

U kunt ervoor kiezen om uw `PipelineData` object te maken met behulp van een toegangs modus waarmee u een onmiddellijke upload kunt doen. In dat geval moet u, wanneer u `PipelineData`uw maakt, `upload_mode` het `"upload"` `output_path_on_compute` argument instellen en gebruiken om het pad op te geven waarnaar u de gegevens wilt schrijven:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Gebruiken `PipelineData` als uitvoer van een trainings stap

Binnen de pijp lijn `PythonScriptStep`kunt u de beschik bare uitvoer paden ophalen met behulp van de argumenten van het programma. Als deze stap het eerst is en de uitvoer gegevens initialiseert, moet u de Directory maken op het opgegeven pad. Vervolgens kunt u de bestanden die u wilt opnemen in de `PipelineData`opslaan.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

`PipelineData` Als u uw hebt gemaakt met `is_directory` het argument ingesteld `True`op, zou het voldoende zijn om alleen de `os.makedirs()` oproep uit te voeren. vervolgens zou u de gewenste bestanden naar het pad kunnen schrijven. Zie de [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) -referentie documentatie voor meer informatie.

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Lezen `PipelineData` als invoer van niet-eerste stappen

Nadat de eerste pijplijn stap enkele gegevens naar het `PipelineData` pad heeft geschreven en deze een uitvoer van die eerste stap wordt, kan deze worden gebruikt als invoer voor een latere stap:

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

De waarde van een `PipelineData` invoer is het pad naar de vorige uitvoer. Als, zoals eerder weer gegeven, de eerste stap een enkel bestand heeft geschreven, kan dit er als volgt uitzien: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Objecten `PipelineData` naar `Dataset`s converteren

Als u langer dan de duur van `PipelineData` een uitvoering beschikbaar wilt maken, gebruikt u de functie ervan `as_dataset()` om deze te converteren naar een. `Dataset` U kunt vervolgens de `Dataset`registreren, waardoor het een eersteklas burger is in uw werk ruimte. Omdat uw `PipelineData` object elke keer dat de pijp lijn wordt uitgevoerd een ander pad heeft, is het raadzaam dat u instelt `create_new_version` op `True` wanneer u een `Dataset` gemaakt van een `PipelineData` object registreert.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)

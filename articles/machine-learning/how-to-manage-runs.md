---
title: Trainingsruns starten, controleren en annuleren in Python
titleSuffix: Azure Machine Learning
description: Leer hoe u beginnen, de status van uw machine learning-experimenten instellen, taggen en organiseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: 8c261a010a1e8f4d1be9b3883510eb38c37a15ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296884"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Trainingsruns starten, controleren en annuleren in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

De [Azure Machine Learning SDK voor Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)Machine Learning [CLI](reference-azure-machine-learning-cli.md)en Azure [Machine Learning-studio](https://ml.azure.com) bieden verschillende methoden om uw uitvoeringen te monitoren, te organiseren en te beheren voor training en experimenten.

In dit artikel worden voorbeelden van de volgende taken weergegeven:

* Monitor de prestaties van de uitvoering.
* Annuleren of mislukken loopt.
* Onderliggende uitvoeringen maken.
* Tag en vind runs.

## <a name="prerequisites"></a>Vereisten

Je hebt de volgende objecten nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md).

* De Azure Machine Learning SDK voor Python (versie 1.0.21 of hoger). Zie De SDK installeren of bijwerken als u de nieuwste versie van de SDK wilt installeren [of bijwerken.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

    Als u uw versie van de Azure Machine Learning SDK wilt controleren, gebruikt u de volgende code:

    ```python
    print(azureml.core.VERSION)
    ```

* De [Azure CLI-](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) en [CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Een run en het registratieproces starten

### <a name="using-the-sdk"></a>De SDK gebruiken

Stel uw experiment in door de klassen [Werkruimte,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) [Experiment,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) [Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)en [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) te importeren uit het [azureml.core-pakket.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Start een run en het [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) registratieproces met de methode.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Met behulp van de CLI

Voer de volgende stappen uit om een run van het experiment te starten:

1. Gebruik vanuit een shell- of opdrachtprompt de Azure CLI om te verifiëren voor uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Voeg een werkruimteconfiguratie toe aan de map die uw trainingsscript bevat. Vervang `myworkspace` door uw Azure Machine Learning-werkruimte. Vervang `myresourcegroup` de Azure-brongroep die uw werkruimte bevat:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Met deze `.azureml` opdracht wordt een submap gemaakt die voorbeeldrunconfig- en conda-omgevingbestanden bevat. Het bevat `config.json` ook een bestand dat wordt gebruikt om te communiceren met uw Azure Machine Learning-werkruimte.

    Zie az [ml map attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Als u de run wilt starten, gebruikt u de volgende opdracht. Wanneer u deze opdracht gebruikt, geeft u de naam \*van het runconfig-bestand (de tekst vóór .runconfig als u naar uw bestandssysteem kijkt) op tegen de parameter -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > De `az ml folder attach` opdracht `.azureml` heeft een submap gemaakt, die twee voorbeeldrunconfig-bestanden bevat.
    >
    > Als u een Python-script hebt dat een programmatisch uitgevoerd configuratieobject maakt, u [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) gebruiken om het op te slaan als runconfig-bestand.
    >
    > Zie voor meer voorbeeld runconfig-bestanden [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Zie [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)voor meer informatie.

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning-studio gebruiken

Voer de volgende stappen uit om een pijplijn in de ontwerper te verzenden (voorbeeld) om een pijplijn in te dienen:

1. Stel een standaard gegevensdoel in voor uw pijplijn.

1. Selecteer **Uitvoeren** boven aan het pijplijncanvas.

1. Selecteer een experiment om de pijplijn uitvoeringen te groeperen.

## <a name="monitor-the-status-of-a-run"></a>De status van een run controleren

### <a name="using-the-sdk"></a>De SDK gebruiken

Krijg de status van [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) een run met de methode.

```python
print(notebook_run.get_status())
```

Als u de run-ID, uitvoeringstijd en aanvullende [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) details over de uitvoering wilt krijgen, gebruikt u de methode.

```python
print(notebook_run.get_details())
```

Wanneer uw run is voltooid, gebruikt u de methode om deze [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) als voltooid te markeren.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Als u het `with...as` ontwerppatroon van Python gebruikt, wordt zichzelf automatisch als voltooid markeren wanneer de run buiten het bereik valt. U hoeft de run niet handmatig als voltooid te markeren.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Met behulp van de CLI

1. Als u een lijst met uitvoeringen voor uw experiment wilt weergeven, gebruikt u de volgende opdracht. Vervang `experiment` de naam van uw experiment:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Met deze opdracht retourneert een JSON-document met informatie over uitvoeringen voor dit experiment.

    Zie voor meer informatie [de az ml-experimentlijst](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Als u informatie over een specifieke run wilt weergeven, gebruikt u de volgende opdracht. Vervang `runid` door de id van de run:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Met deze opdracht wordt een JSON-document geretourneerd met informatie over de run.

    Zie voor meer informatie [de az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning-studio gebruiken

Om het aantal actieve runs voor uw experiment in de studio te bekijken.

1. Navigeer naar de sectie **Experimenten..** 

1. Selecteer een experiment.

    Op de experimentpagina ziet u het aantal actieve rekendoelen en de duur voor elke run. 

1. Selecteer een specifiek runnummer.

1. Op het tabblad **Logboeken** u diagnostische en foutlogboeken voor uw pijplijnuitvoeren vinden.


## <a name="cancel-or-fail-runs"></a>Runs annuleren of mislukken

Als u een fout opmerkt of als het te lang duurt voordat uw run is voltooid, u de run annuleren.

### <a name="using-the-sdk"></a>De SDK gebruiken

Als u een run met [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) de SDK wilt annuleren, gebruikt u de methode:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Als uw run is voltooid, maar er een fout in bevat (bijvoorbeeld [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) het onjuiste trainingsscript is gebruikt), u de methode gebruiken om deze als mislukt te markeren.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Met behulp van de CLI

Als u een run wilt annuleren met de CLI, gebruikt u de volgende opdracht. Vervangen `runid` door de id van de run

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Zie voor meer informatie [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning-studio gebruiken

Ga als volgt te werk om een run in de studio te annuleren met de volgende stappen:

1. Ga naar de lopende pijplijn in de sectie **Experimenten** of **Pijplijnen.** 

1. Selecteer het pijplijnrunnummer dat u wilt annuleren.

1. Selecteer **Annuleren op** de werkbalk


## <a name="create-child-runs"></a>Onderliggende uitvoeringen maken

Onderliggende runs maken om gerelateerde uitvoeringen samen te groeperen, bijvoorbeeld voor verschillende hyperparameter-tuning-iteraties.

> [!NOTE]
> Onderliggende uitvoeringen kunnen alleen worden gemaakt met behulp van de SDK.

In dit codevoorbeeld wordt het `hello_with_children.py` script gebruikt om een batch van [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) vijf onderliggende uitvoeringen te maken vanuit een ingediende uitvoering met behulp van de methode:

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Als ze buiten het bereik vallen, worden onderliggende uitvoeringen automatisch gemarkeerd als voltooid.

Als u veel onderliggende runs [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) efficiënt wilt maken, gebruikt u de methode. Omdat elke creatie resulteert in een netwerkaanroep, is het maken van een batch met uitvoeringen efficiënter dan ze één voor één maken.

### <a name="submit-child-runs"></a>Onderliggende uitvoeringen verzenden

Onderliggende uitvoeringen kunnen ook worden ingediend vanaf een bovenliggende run. Hiermee u hiërarchieën van bovenliggende en onderliggende uitvoeringen maken, die elk worden uitgevoerd op verschillende rekendoelen, verbonden door algemene bovenliggende run-ID.

Gebruik de methode ['submit_child()'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-config--tags-none----kwargs-) om een onderliggend e-mail uit te voeren vanuit een bovenliggende run. Als u dit wilt doen in het bovenliggende run-script, krijgt u de runcontext en verzendt u het onderliggende uitvoeren met behulp van de ``submit_child`` methode van de contextinstantie.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

Binnen een onderliggende run u de bovenliggende run-ID bekijken:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Queryonderliggendvoert

Als u de onderliggende uitvoeringen [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) van een specifieke ouder wilt opvragen, gebruikt u de methode. Met ``recursive = True`` het argument u een geneste boom van kinderen en kleinkinderen opvragen.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Runs taggen en zoeken

In Azure Machine Learning u eigenschappen en tags gebruiken om uw uitvoeringen te ordenen en op te vragen voor belangrijke informatie.

### <a name="add-properties-and-tags"></a>Eigenschappen en tags toevoegen

#### <a name="using-the-sdk"></a>De SDK gebruiken

Als u doorzoekbare metagegevens [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) wilt toevoegen aan uw uitvoeringen, gebruikt u de methode. Met de volgende code `"author"` wordt bijvoorbeeld de eigenschap aan de run toegevoegd:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Eigenschappen zijn onveranderlijk, dus ze maken een permanente record voor controledoeleinden. Het volgende codevoorbeeld resulteert in een `"azureml-user"` fout, `"author"` omdat we al als eigenschapswaarde in de voorgaande code zijn toegevoegd:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

In tegenstelling tot eigenschappen zijn tags mutable. Als u doorzoekbare en zinvolle informatie wilt [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) toevoegen voor consumenten van uw experiment, gebruikt u de methode.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

U ook eenvoudige tekenreekstags toevoegen. Wanneer deze tags in het tagwoordenboek als sleutels `None`worden weergegeven, hebben ze een waarde van .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Met behulp van de CLI

> [!NOTE]
> Met de CLI u alleen tags toevoegen of bijwerken.

Als u een tag wilt toevoegen of bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Zie [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update)voor meer informatie.

### <a name="query-properties-and-tags"></a>Query-eigenschappen en -tags

U query's uitvoeren in een experiment om een lijst met uitvoeringen terug te sturen die overeenkomen met specifieke eigenschappen en tags.

#### <a name="using-the-sdk"></a>De SDK gebruiken

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Met behulp van de CLI

De Azure CLI ondersteunt [JMESPath-query's,](http://jmespath.org) die kunnen worden gebruikt om uitvoeringen te filteren op basis van eigenschappen en tags. Als u een JMESPath-query wilt gebruiken `--query` met de Azure CLI, geeft u deze op met de parameter. In de volgende voorbeelden worden basisquery's weergegeven met eigenschappen en tags:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Zie [Query Azure CLI-opdrachtuitvoer](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)voor meer informatie over het opvragen van Azure CLI-resultaten.

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning-studio gebruiken

1. Navigeer naar de sectie **Pijplijnen.**

1. Gebruik de zoekbalk om pijplijnen te filteren met tags, beschrijvingen, experimentnamen en de naam van de indiener.

## <a name="example-notebooks"></a>Voorbeeldnotitieblokken

De volgende notitieblokken tonen de concepten in dit artikel:

* Zie het [logboekenAPI-notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)voor meer informatie over de API's voor logboekregistratie .

* Zie het [notitieblok Beheer uitvoeringen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)voor meer informatie over het beheren van uitvoeringen met de Azure Machine Learning SDK.

## <a name="next-steps"></a>Volgende stappen

* Zie [Logboekstatistieken tijdens trainingsruns](how-to-track-experiments.md)voor meer informatie over het registreren van statistieken voor uw experimenten.
* Zie [Azure Machine Learning](monitor-azure-machine-learning.md)controleren voor meer informatie over het controleren van resources en logboeken vanuit Azure Machine Learning.
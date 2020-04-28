---
title: Fout opsporing en problemen oplossen ParallelRunStep
titleSuffix: Azure Machine Learning
description: Fout opsporing en probleem oplossing voor ParallelRunStep in machine learning-pijp lijnen in de Azure Machine Learning SDK voor python. Leer veelvoorkomende Valk uilen voor het ontwikkelen met pijp lijnen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122962"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Fout opsporing en problemen oplossen ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u fouten kunt opsporen en oplossen van de [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) -klasse in de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Scripts lokaal testen

Zie de [sectie scripts lokaal testen](how-to-debug-pipelines.md#testing-scripts-locally) voor machine learning pijp lijnen. Uw ParallelRunStep wordt uitgevoerd als een stap in ML-pijp lijnen, zodat hetzelfde antwoord van toepassing is op beide.

## <a name="debugging-scripts-from-remote-context"></a>Fouten opsporen in scripts in externe context

De overgang van het opsporen van fouten in een score script voor het opsporen van fouten in een score script in een pijp lijn kan lastig zijn. Voor informatie over het vinden van uw logboeken in de portal, het [gedeelte met machine learning pijp lijnen over het opsporen van fouten in scripts van een externe context](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). De informatie in deze sectie is ook van toepassing op het uitvoeren van een parallelle stap.

Het logboek bestand `70_driver_log.txt` bevat bijvoorbeeld informatie van de controller die de parallelle run Step-code start.

Vanwege de gedistribueerde aard van parallelle uitvoerings taken zijn er logboeken van verschillende bronnen. Er worden echter twee geconsolideerde bestanden gemaakt die gegevens op hoog niveau bieden:

- `~/logs/overview.txt`: Dit bestand bevat informatie op hoog niveau over het aantal Mini-batches (ook wel taken genoemd) dat tot nu toe is gemaakt en het aantal verwerkte mini batches tot nu toe. Nu wordt het resultaat van de taak weer gegeven. Als de taak is mislukt, wordt het fout bericht weer gegeven en wordt aangegeven waar de probleem oplossing moet worden gestart.

- `~/logs/sys/master.txt`: Dit bestand bevat het hoofd knooppunt (ook wel bekend als Orchestrator) voor het uitvoeren van de taak. Omvat het maken van taken, voortgangs bewaking, het resultaat van de uitvoering.

Logboeken die zijn gegenereerd op basis van een invoer script met behulp van EntryScript. logger en print-instructies worden gevonden in de volgende bestanden:

- `~/logs/user/<ip_address>/Process-*.txt`: Dit bestand bevat logboeken die zijn geschreven van entry_script met behulp van EntryScript. logger. Het bevat ook een instructie Print (stdout) van entry_script.

Als u wilt weten hoe elk knoop punt het Score script heeft uitgevoerd, bekijkt u de afzonderlijke proces logboeken voor elk knoop punt. De proces logboeken kunnen worden gevonden in `sys/worker` de map, gegroepeerd op worker-knoop punten:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Dit bestand bevat gedetailleerde informatie over elke mini-batch, zoals deze wordt opgehaald of voltooid door een werk nemer. Voor elke mini-batch bestaat dit bestand uit:

    - Het IP-adres en de PID van het werk proces. 
    - Het totale aantal items, het aantal items dat is verwerkt en het aantal mislukte items.
    - De tijd van begin tijd, duur, proces tijd en uitvoerings methode.

U kunt ook informatie vinden over het resource gebruik van de processen voor elke werk nemer. Deze informatie bevindt zich in CSV-indeling `~/logs/sys/perf/<ip_address>/`en bevindt zich op. Voor één knoop punt zijn er taak bestanden beschikbaar onder `~logs/sys/perf`. Bekijk bijvoorbeeld de volgende bestanden bij het controleren van het resource gebruik:

- `Process-*.csv`: Het resource gebruik per werk proces. 
- `sys.csv`: Logboek per knoop punt.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hoe kan ik logboek van mijn gebruikers script vanuit een externe context?
U kunt een logboek registratie ophalen van EntryScript zoals hieronder wordt weer gegeven in de voorbeeld code om de logboeken weer te geven in de map **Logboeken/gebruikers** in de portal.

**Een voor beeld van een invoer script met behulp van het logboek:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hoe kan ik een invoer van een zijde, zoals een bestand of bestand (en) met een opzoek tabel, aan al mijn werk rollen door geven?

Een object [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) maken met de invoer aan de zijkant en registreren bij uw werk ruimte. Nadat u dit hebt geopend in uw Afleidings script (bijvoorbeeld in de methode init ()), als volgt:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de SDK-Naslag informatie voor hulp met het pakket met de [contrib-pipeline-stap](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) en de [documentatie](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) voor de klasse ParallelRunStep.

* Volg de [Geavanceerde zelf studie](tutorial-pipeline-batch-scoring-classification.md) over het gebruik van pijp lijnen met parallelle uitvoerings stap.

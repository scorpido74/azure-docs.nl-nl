---
title: Fout opsporing en problemen oplossen ParallelRunStep
titleSuffix: Azure Machine Learning
description: Fout opsporing en probleem oplossing voor ParallelRunStep in machine learning-pijp lijnen in de Azure Machine Learning SDK voor python. Leer veelvoorkomende Valk uilen voor het ontwikkelen met pijp lijnen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: a6a3e9a7a914711f6b7c923ac2249ebf3285c877
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031011"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Fout opsporing en problemen oplossen ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u fouten kunt opsporen en oplossen van de [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) -klasse in de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Scripts lokaal testen

Zie de [sectie scripts lokaal testen](how-to-debug-pipelines.md#testing-scripts-locally) voor machine learning pijp lijnen. Uw ParallelRunStep wordt uitgevoerd als een stap in ML-pijp lijnen, zodat hetzelfde antwoord van toepassing is op beide.

## <a name="debugging-scripts-from-remote-context"></a>Fouten opsporen in scripts in externe context

De overgang van het opsporen van fouten in een score script voor het opsporen van fouten in een score script in een pijp lijn kan lastig zijn. Voor informatie over het vinden van uw logboeken in de portal, het [gedeelte met machine learning pijp lijnen over het opsporen van fouten in scripts van een externe context](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). De informatie in deze sectie is ook van toepassing op een ParallelRunStep.

Het logboek bestand `70_driver_log.txt` bevat bijvoorbeeld informatie van de controller die de ParallelRunStep-code start.

Vanwege de gedistribueerde aard van ParallelRunStep-taken zijn er logboeken van verschillende bronnen. Er worden echter twee geconsolideerde bestanden gemaakt die gegevens op hoog niveau bieden:

- `~/logs/overview.txt`: Dit bestand bevat informatie op hoog niveau over het aantal Mini-batches (ook wel taken genoemd) dat tot nu toe is gemaakt en het aantal verwerkte mini batches tot nu toe. Nu wordt het resultaat van de taak weer gegeven. Als de taak is mislukt, wordt het fout bericht weer gegeven en wordt aangegeven waar de probleem oplossing moet worden gestart.

- `~/logs/sys/master.txt`: Dit bestand bevat het hoofd knooppunt (ook wel bekend als Orchestrator) voor het uitvoeren van de taak. Omvat het maken van taken, voortgangs bewaking, het resultaat van de uitvoering.

Logboeken die zijn gegenereerd op basis van een invoer script met behulp van EntryScript-helper-en-afdruk instructies, worden in de volgende bestanden

- `~/logs/user/<ip_address>/<node_name>.log.txt`: Deze bestanden zijn de logboeken geschreven van entry_script met behulp van de EntryScript-helper. Bevat ook print-instructie (stdout) van entry_script.

Er is sprake van een beknopt overzicht van fouten in uw script:

- `~/logs/user/error.txt`: In dit bestand wordt geprobeerd de fouten in uw script samen te vatten.

Voor meer informatie over fouten in uw script, geldt het volgende:

- `~/logs/user/error/`: Bevat alle fouten die zijn gegenereerd en volledige stack-traces, geordend op knoop punt.

Als u wilt weten hoe elk knoop punt het Score script heeft uitgevoerd, bekijkt u de afzonderlijke proces logboeken voor elk knoop punt. De proces logboeken kunnen worden gevonden in de `sys/node` map, gegroepeerd op worker-knoop punten:

- `~/logs/sys/node/<node_name>.txt`: Dit bestand bevat gedetailleerde informatie over elke mini-batch tijdens het ophalen of volt ooien van een werk nemer. Voor elke mini-batch bestaat dit bestand uit:

    - Het IP-adres en de PID van het werk proces. 
    - Het totale aantal items, het aantal items dat is verwerkt en het aantal mislukte items.
    - De tijd van begin tijd, duur, proces tijd en uitvoerings methode.

U kunt ook informatie vinden over het resource gebruik van de processen voor elke werk nemer. Deze informatie bevindt zich in CSV-indeling en bevindt zich op `~/logs/sys/perf/overview.csv` . Informatie over elk proces is beschikbaar onder `~logs/sys/processes.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hoe kan ik logboek van mijn gebruikers script vanuit een externe context?
ParallelRunStep kan meerdere processen uitvoeren op één knoop punt op basis van process_count_per_node. Als u logboeken van elk proces wilt ordenen op basis van het knoop punt en afdruk-en logboek instructie combi neren, raden we u aan om ParallelRunStep logger te gebruiken zoals hieronder wordt weer gegeven. U ontvangt een logboek van EntryScript en zorgt ervoor dat de logboeken worden weer gegeven in **Logboeken/gebruikers** mappen in de portal.

**Een voor beeld van een invoer script met behulp van het logboek:**
```python
from azureml_user.parallel_run import EntryScript

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

De gebruiker kan referentie gegevens door geven aan een script met behulp van side_inputs para meter ParalleRunStep. Alle gegevens sets die als side_inputs worden gegeven, worden op elk worker-knoop punt gekoppeld. De gebruiker kan de locatie van de koppeling verkrijgen door het argument door te geven.

Maak een [gegevensset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) met de referentie gegevens en Registreer deze bij uw werk ruimte. Geef deze door aan de `side_inputs` para meter van uw `ParallelRunStep` . Daarnaast kunt u het pad in de sectie toevoegen `arguments` om eenvoudig toegang te krijgen tot het gekoppelde pad:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Nadat u dit hebt geopend in uw Afleidings script (bijvoorbeeld in de methode init ()), als volgt:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de SDK-Naslag informatie voor hulp met het pakket met de stappen voor de [azureml-pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) . Referentie [documentatie](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) voor de klasse ParallelRunStep weer geven.

* Volg de [Geavanceerde zelf studie](tutorial-pipeline-batch-scoring-classification.md) over het gebruik van pijp lijnen met ParallelRunStep. De zelf studie laat zien hoe u een ander bestand als een kantlijn invoer kunt door geven. 

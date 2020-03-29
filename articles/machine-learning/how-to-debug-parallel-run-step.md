---
title: Foutopsporing en oplossen van ParallelRunStep
titleSuffix: Azure Machine Learning
description: Foutopsporing en problemen oplossen Van ParallelRunStep in machine learning-pijplijnen in de Azure Machine Learning SDK voor Python. Leer veelvoorkomende valkuilen voor het ontwikkelen met pijplijnen en tips om u te helpen uw scripts te debuggen voor en tijdens uitvoering op afstand.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122962"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Foutopsporing en oplossen van ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de klasse [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) van de [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)opsporen en oplossen.

## <a name="testing-scripts-locally"></a>Scripts lokaal testen

Zie de [sectie Scripts lokaal testen](how-to-debug-pipelines.md#testing-scripts-locally) voor machine learning-pijplijnen. Uw ParallelRunStep wordt uitgevoerd als een stap in ML-pijplijnen, zodat hetzelfde antwoord op beide geldt.

## <a name="debugging-scripts-from-remote-context"></a>Scripts debuggen vanuit externe context

De overgang van het debuggen van een scorescript lokaal naar het debuggen van een scorescript in een werkelijke pijplijn kan een moeilijke sprong zijn. Voor informatie over het vinden van uw logboeken in de portal, de [machine learning pipelines sectie over het opsporen van scripts uit een externe context](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). De informatie in die sectie is ook van toepassing op een parallelle stap run.

Het logboekbestand `70_driver_log.txt` bevat bijvoorbeeld informatie van de controller die parallelle run-stapcode lanceert.

Vanwege de gedistribueerde aard van parallelle run taken, zijn er logs uit verschillende bronnen. Er worden echter twee geconsolideerde bestanden gemaakt die informatie op hoog niveau bieden:

- `~/logs/overview.txt`: Dit bestand biedt een hoog niveau info over het aantal mini-batches (ook bekend als taken) gemaakt tot nu toe en het aantal mini-batches verwerkt tot nu toe. Aan deze kant, het toont het resultaat van de baan. Als de taak is mislukt, wordt het foutbericht weergegeven en wordt het probleemoplossing gestart.

- `~/logs/sys/master.txt`: Dit bestand geeft de hoofdknooppunt (ook wel de orchestrator genoemd) weergave van de lopende taak. Inclusief taakcreatie, voortgangsbewaking, het runresultaat.

Logboeken die worden gegenereerd uit het invoerscript met Behulp van EntryScript.logger en afdrukinstructies worden gevonden in de volgende bestanden:

- `~/logs/user/<ip_address>/Process-*.txt`: Dit bestand bevat logboeken die zijn geschreven vanuit entry_script met Behulp van EntryScript.logger. Het bevat ook print statement (stdout) van entry_script.

Wanneer u een volledig inzicht nodig hebt in hoe elk knooppunt het scorescript heeft uitgevoerd, bekijkt u de afzonderlijke proceslogboeken voor elk knooppunt. De proceslogboeken zijn `sys/worker` te vinden in de map, gegroepeerd op werknemersknooppunten:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Dit bestand bevat gedetailleerde informatie over elke minibatch terwijl deze wordt opgehaald of ingevuld door een werknemer. Voor elke minibatch bevat dit bestand:

    - Het IP-adres en de PID van het werkproces. 
    - Het totale aantal objecten, het aantal verwerkte objecten met succes en het aantal mislukte objecten.
    - De begintijd, duur, procestijd en runmethodetijd.

U ook informatie vinden over het resourcegebruik van de processen voor elke werknemer. Deze informatie is in CSV-formaat en bevindt zich op `~/logs/sys/perf/<ip_address>/`. Voor één knooppunt zijn taakbestanden beschikbaar `~logs/sys/perf`onder . Wanneer u bijvoorbeeld controleert op het gebruik van resources, bekijkt u de volgende bestanden:

- `Process-*.csv`: Per werknemer verwerken resource gebruik. 
- `sys.csv`: Logboek per knooppunt.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hoe meld ik me af vanuit mijn gebruikersscript vanuit een externe context?
U een logger uit EntryScript krijgen zoals hieronder in de voorbeeldcode wordt weergegeven om de logboeken in **logboeken/gebruikersmap** in de portal te laten verschijnen.

**Een voorbeeld item script met behulp van de logger:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hoe kan ik een zijinvoer zoals een bestand of bestand(en) met een opzoektabel doorgeven aan al mijn werknemers?

Maak een [setgegevensobject](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) met de zijinvoer en registreer u bij uw werkruimte. Daarna u het als volgt openen in uw gevolgtrekkingsscript (bijvoorbeeld in uw methode init().

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Volgende stappen

* Zie de SDK-referentie voor hulp bij het [azureml-contrib-pipeline-step-pakket](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) en de [documentatie](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) voor de klasse ParallelRunStep.

* Volg de [geavanceerde zelfstudie](tutorial-pipeline-batch-scoring-classification.md) over het gebruik van pijplijnen met parallelle runstap.

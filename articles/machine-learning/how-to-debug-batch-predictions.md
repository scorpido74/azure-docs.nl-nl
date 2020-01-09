---
title: Fout opsporing en problemen oplossen ParallelRunStep
titleSuffix: Azure Machine Learning
description: Fouten opsporen en problemen oplossen machine learning pijp lijnen in de Azure Machine Learning SDK voor python. Leer veelvoorkomende Valk uilen voor het ontwikkelen met pijp lijnen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: 20e88da6721a6715a7f31f5c423e305625528383
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540605"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Fouten opsporen en oplossen met ParallelRun
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u fouten kunt opsporen en oplossen van de [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) -klasse in de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Scripts lokaal testen

Zie de [sectie scripts lokaal testen](how-to-debug-pipelines.md#testing-scripts-locally) voor machine learning pijp lijnen. Uw ParallelRunStep wordt uitgevoerd als een stap in ML-pijp lijnen, zodat hetzelfde antwoord van toepassing is op beide.

## <a name="debugging-scripts-from-remote-context"></a>Fouten opsporen in scripts in externe context

De overgang van het opsporen van fouten in een score script voor het opsporen van fouten in een score script in een pijp lijn kan lastig zijn. Voor informatie over het vinden van uw logboeken in de portal, het [gedeelte met machine learning pijp lijnen over het opsporen van fouten in scripts van een externe context](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). De informatie in deze sectie is ook van toepassing op een uitvoering van een batch-deinterferentie.

Het logboek bestand `70_driver_log.txt` bijvoorbeeld ook het volgende bevatten: 

* Alle afgedrukte instructies tijdens de uitvoering van uw script.
* De stack tracering van het script.

Vanwege de gedistribueerde aard van batch denaturie taken, zijn er logboeken van verschillende bronnen. Er worden echter twee geconsolideerde bestanden gemaakt die gegevens op hoog niveau bieden:

- `~/logs/overview.txt`: dit bestand bevat een hoog niveau informatie over het aantal Mini-batches (ook wel taken genoemd), dat tot nu toe is gemaakt en het aantal verwerkte mini batches tot nu toe. Nu wordt het resultaat van de taak weer gegeven. Als de taak is mislukt, wordt het fout bericht weer gegeven en wordt aangegeven waar de probleem oplossing moet worden gestart.

- `~/logs/master.txt`: dit bestand bevat het hoofd knooppunt (ook wel bekend als Orchestrator) van de taak die wordt uitgevoerd. Omvat het maken van taken, voortgangs bewaking, het resultaat van de uitvoering.

Als u wilt weten hoe elk knoop punt het Score script heeft uitgevoerd, bekijkt u de afzonderlijke proces logboeken voor elk knoop punt. De proces Logboeken kunt u vinden in de map `worker`, gegroepeerd op worker-knoop punten:

- `~/logs/worker/<ip_address>/Process-*.txt`: dit bestand bevat gedetailleerde informatie over elke mini-batch, zoals deze wordt opgehaald of voltooid door een werk nemer. Voor elke mini-batch bestaat dit bestand uit:

    - Het IP-adres en de PID van het werk proces. 
    - Het totale aantal items en het aantal items dat is verwerkt. 
    - De begin-en eind tijd in de klok tijden (`start1` en `end1`). 
    - De begin-en eind tijd in de gebruikte processor tijd (`start2` en `end2`). 

U kunt ook informatie vinden over het resource gebruik van de processen voor elke werk nemer. Deze informatie bevindt zich in CSV-indeling en bevindt zich in `~/logs/performance/<ip_address>/`. Bekijk bijvoorbeeld de volgende bestanden bij het controleren van het resource gebruik:

- `process_resource_monitor_<ip>_<pid>.csv`: resource gebruik per werk proces. 
- `sys_resource_monitor_<ip>.csv`: logboek voor het per knoop punt.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hoe kan ik logboek van mijn gebruikers script vanuit een externe context?
U kunt een logboek instellen met de onderstaande stappen om de logboeken weer te geven in de map **Logboeken/gebruikers** in de portal:
1. Sla het eerste code gedeelte hieronder op in file entry_script_helper. py en plaats het bestand in dezelfde map als uw invoer script. Deze klasse haalt het pad op in AmlCompute. Voor lokale tests kunt u `get_working_dir()` wijzigen om een lokale map te retour neren.
2. Een logboek configuratie in uw `init()`-methode configureren en vervolgens gebruiken. Het tweede code gedeelte hieronder is een voor beeld. 

**entry_script_helper. py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Een voor beeld van een invoer script met behulp van het logboek:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de SDK-Naslag informatie voor hulp met het pakket met de [contrib-pipeline-stap](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) en de [documentatie](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) voor de klasse ParallelRunStep.

* Volg de [Geavanceerde zelf studie](tutorial-pipeline-batch-scoring-classification.md) over het gebruik van pijp lijnen voor batch scores.

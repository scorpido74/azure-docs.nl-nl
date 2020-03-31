---
title: Fouten in Machine Learning-pijplijnen opsporen en oplossen
titleSuffix: Azure Machine Learning
description: Foutopsporing en problemen oplossen van machine learning-pijplijnen in de Azure Machine Learning SDK voor Python. Leer veelvoorkomende valkuilen voor het ontwikkelen van pijplijnen en tips om u te helpen uw scripts te debuggen voor en tijdens uitvoering op afstand. Meer informatie over het gebruik van Visual Studio Code om uw machine learning-pijplijnen interactief te debuggen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: b68efbb64e9634ade001373e8cd9d61355bf786f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388981"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten in Machine Learning-pijplijnen opsporen en oplossen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u [machine learning-pijplijnen](concept-ml-pipelines.md) opsporen en oplossen in de [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) en Azure Machine [Learning-ontwerper (preview).](https://docs.microsoft.com/azure/machine-learning/concept-designer) Er wordt informatie verstrekt over hoe:

* Foutopsporing met de Azure Machine Learning SDK
* Foutopsporing met de Azure Machine Learning-ontwerper
* Foutopsporing met toepassingsinzichten
* Foutopsporing interactief met behulp van Visual Studio Code (VS Code) en de Python Tools for Visual Studio (PTVSD)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Foutopsporing en problemen oplossen in de Azure Machine Learning SDK
De volgende secties geven een overzicht van de veelvoorkomende valkuilen bij het bouwen van pijplijnen en verschillende strategieën voor het debuggen van uw code die in een pijplijn wordt uitgevoerd. Gebruik de volgende tips wanneer u problemen ondervindt om een pijplijn te laten uitvoeren zoals verwacht.

### <a name="testing-scripts-locally"></a>Scripts lokaal testen

Een van de meest voorkomende fouten in een pijplijn is dat een bijgevoegd script (script voor gegevensreiniging, scorescript, enz.) niet wordt uitgevoerd zoals bedoeld, of runtimefouten bevat in de externe rekencontext die moeilijk te detecteren zijn in uw werkruimte in de Azure Machine Leerstudio. 

Pijplijnen zelf kunnen niet lokaal worden uitgevoerd, maar door de scripts afzonderlijk op uw lokale machine uit te voeren, u sneller debuggen omdat u niet hoeft te wachten op het proces voor het opbouwen van de gegevens en de omgeving. Hiervoor is enige ontwikkelingswerk nodig:

* Als uw gegevens zich in een clouddatastore bevinden, moet u gegevens downloaden en beschikbaar maken voor uw script. Het gebruik van een kleine steekproef van uw gegevens is een goede manier om runtime te verminderen en snel feedback te krijgen over het scriptgedrag
* Als u een tussenliggende pijplijnstap probeert te simuleren, moet u mogelijk handmatig de objecttypen bouwen die het specifieke script van de vorige stap verwacht
* U moet ook uw eigen omgeving definiëren en de afhankelijkheden repliceren die zijn gedefinieerd in uw externe compute-omgeving

Zodra u een script-instelling hebt om op uw lokale omgeving uit te voeren, is het veel gemakkelijker om foutopsporingstaken uit te voeren, zoals:

* Een aangepaste foutopsporingsconfiguratie toevoegen
* Uitvoering onderbreken en objectstatus inspecteren
* Het vangen van type of logische fouten die niet worden blootgesteld tot runtime

> [!TIP] 
> Zodra u controleren of uw script wordt uitgevoerd zoals verwacht, wordt het script in een pijplijn met één stap uitgevoerd voordat u probeert het in een pijplijn met meerdere stappen uit te voeren.

### <a name="debugging-scripts-from-remote-context"></a>Scripts debuggen vanuit externe context

Het testen van scripts lokaal is een geweldige manier om grote codefragmenten en complexe logica te debuggen voordat u begint met het bouwen van een pijplijn, maar op een gegeven moment zult u waarschijnlijk scripts moeten debuggen tijdens de eigenlijke pijplijnrun zelf, vooral bij het diagnosticeren van gedrag dat optreedt tijdens de interactie tussen pijplijnstappen. We raden u `print()` aan om uitlatingen in uw stapscripts te royeren, zodat u de objectstatus en verwachte waarden tijdens uitvoering op afstand zien, vergelijkbaar met hoe u JavaScript-code zou debuggen.

Het logboekbestand `70_driver_log.txt` bevat: 

* Alle afgedrukte instructies tijdens de uitvoering van uw script
* De stacktracering voor het script 

Als u deze en andere logboekbestanden in de portal wilt vinden, klikt u eerst op de pijplijn die in uw werkruimte wordt uitgevoerd.

![Lijstpagina pijplijnrun](./media/how-to-debug-pipelines/pipelinerun-01.png)

Navigeer naar de detailpagina pijplijnrun.

![Detailpagina pijplijnrun](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klik op de module voor de specifieke stap. Navigeer naar het tabblad **Logboeken.** Andere logboeken bevatten informatie over het proces voor het maken van uw omgevingafbeelding en het voorbereiden van stappenscripts.

![Tabblad Detailpaginalogboek van pijplijnuitvoeren](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Runs voor *gepubliceerde pijplijnen* zijn te vinden op het tabblad **Eindpunten** in uw werkruimte. Runs voor *niet-gepubliceerde pijplijnen* zijn te vinden in **experimenten** of **pijplijnen.**

### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tabel bevat veelvoorkomende problemen tijdens de ontwikkeling van pijpleidingen, met mogelijke oplossingen.

| Probleem | Mogelijke oplossing |
|--|--|
| Kan gegevens niet `PipelineData` doorgeven aan directory | Zorg ervoor dat u een map in het script hebt gemaakt die overeenkomt met de plaats waar de pijplijn de uitvoergegevens van de stap verwacht. In de meeste gevallen definieert een invoerargument de uitvoermap en maakt u de map expliciet. Gebruiken `os.makedirs(args.output_dir, exist_ok=True)` om de uitvoermap te maken. Zie de [zelfstudie](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) voor een voorbeeld van een scorescript dat dit ontwerppatroon weergeeft. |
| Afhankelijkheidsbugs | Als u scripts lokaal hebt ontwikkeld en getest, maar afhankelijkheidsproblemen vindt wanneer u op een externe compute in de pijplijn wordt uitgevoerd, moet u ervoor zorgen dat uw afhankelijkheden en versies van uw computeromgeving overeenkomen met uw testomgeving. (Zie [Milieugebouw, caching en hergebruik](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Dubbelzinnige fouten met rekendoelen | Het verwijderen en opnieuw maken van compute targets kan bepaalde problemen oplossen met compute targets. |
| Pijplijn die geen stappen hergebruikt | Hergebruik van stappen is standaard ingeschakeld, maar zorg ervoor dat u het niet hebt uitgeschakeld in een pijplijnstap. Als hergebruik is uitgeschakeld, wordt de `allow_reuse` parameter `False`in de stap ingesteld op . |
| Pijplijn wordt onnodig uitgevoerd | Als u ervoor wilt zorgen dat stappen alleen worden uitgevoerd wanneer de onderliggende gegevens of scripts worden gewijzigd, ontkoppelt u uw mappen voor elke stap. Als u dezelfde bronmap voor meerdere stappen gebruikt, u onnodige herhalingen ervaren. Gebruik `source_directory` de parameter op een pijplijnstapobject om naar uw geïsoleerde map te wijzen `source_directory` voor die stap en ervoor te zorgen dat u niet hetzelfde pad gebruikt voor meerdere stappen. |

### <a name="logging-options-and-behavior"></a>Opties en gedrag voor logboekregistratie

In de onderstaande tabel vindt u informatie over verschillende foutopsporingsopties voor pijplijnen. Het is geen uitputtende lijst, omdat er naast alleen de hier weergegeven Azure Machine Learning-, Python- en OpenCensus-opties bestaan.

| Bibliotheek                    | Type   | Voorbeeld                                                          | Doel                                  | Resources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning-SDK | Gegevens | `run.log(name, val)`                                             | Gebruikersinterface van Azure Machine Learning Portal             | [Experimenten volgen](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run, klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python afdrukken/logboekregistratie    | Logboek    | `print(val)`<br>`logging.info(message)`                          | Driver logs, Azure Machine Learning designer | [Experimenten volgen](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Python-logboekregistratie](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Logboek    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights - traces                | [Fouten met pijplijnen opsporen in Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python logging kookboek](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Voorbeeld van logboekopties

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Foutopsporing en problemen oplossen in Azure Machine Learning-ontwerper (voorbeeld)

In deze sectie vindt u een overzicht van het oplossen van pijplijnen in de ontwerper.
Voor pijplijnen die in de ontwerper zijn gemaakt, u de **logboekbestanden** vinden op de ontwerppagina of in de detailpagina pijplijnuitvoering.

### <a name="access-logs-from-the-authoring-page"></a>Logboeken openen vanaf de ontwerppagina

Wanneer u een pijplijnrun verzendt en op de ontwerppagina blijft, u de logboekbestanden vinden die voor elke module zijn gegenereerd.

1. Selecteer een module in het ontwerpcanvas.
1. Ga in het rechterdeelvenster van de module naar het tabblad **Uitvoer + logboeken.**
1. Selecteer het `70_driver_log.txt`logboekbestand .

    ![Logboeken van de paginamodule](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Logboeken van pijplijnuitvoeringen openen

U de logboekbestanden van specifieke uitvoeringen ook vinden in de detailpagina pijplijnuitvoering in de secties **Pijplijnen** of **Experimenten.**

1. Selecteer een pijplijnrun die is gemaakt in de ontwerper.
    ![Pagina Pijplijnrun](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Selecteer een module in het voorbeeldvenster.
1. Ga in het rechterdeelvenster van de module naar het tabblad **Uitvoer + logboeken.**
1. Selecteer het `70_driver_log.txt`logboekbestand .

## <a name="debug-and-troubleshoot-in-application-insights"></a>Foutopsporing en problemen oplossen in toepassingsinzichten
Zie deze handleiding: [Foutopsporingsen en oplossen van machine learning-pijplijnen in Application Insights](how-to-debug-pipelines-application-insights.md) voor meer informatie over het gebruik van de OpenCensus Python-bibliotheek op deze manier.

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Foutopsporing en problemen oplossen in Visual Studio Code

In sommige gevallen moet u mogelijk de Python-code die in uw ML-pijplijn wordt gebruikt, interactief debuggen. Door Visual Studio Code (VS Code) en de Python Tools for Visual Studio (PTVSD) te gebruiken, u de code aan de code koppelen terwijl deze wordt uitgevoerd in de trainingsomgeving.

### <a name="prerequisites"></a>Vereisten

* Een __Azure Machine Learning-werkruimte__ die is geconfigureerd om een __Azure Virtual Network__te gebruiken.
* Een __Azure Machine Learning-pijplijn__ die Python-scripts gebruikt als onderdeel van de pijplijnstappen. Bijvoorbeeld een PythonScriptStep.
* Een Azure Machine Learning Compute-cluster, dat __zich in het virtuele netwerk__ bevindt en wordt gebruikt door de pijplijn voor __training.__
* Een __ontwikkelomgeving__ die __zich in het virtuele netwerk bevindt.__ De ontwikkelomgeving kan een van de volgende:

    * Een Azure Virtual Machine in het virtuele netwerk
    * Een Compute-exemplaar van Notebook VM in het virtuele netwerk
    * Een clientmachine die met het virtuele netwerk is verbonden door een vpn (virtual private network).

Zie [Secure Azure ML-experimenten en inference-taken binnen een Azure Virtual Network](how-to-enable-virtual-network.md)voor meer informatie over het gebruik van een Azure Virtual Network met Azure Machine Learning.

### <a name="how-it-works"></a>Hoe werkt het?

In uw ML-pijplijnstappen worden Python-scripts uitgevoerd. Deze scripts worden aangepast om de volgende acties uit te voeren:
    
1. Log het IP-adres van de host aan waarop ze worden uitgevoerd. U gebruikt het IP-adres om de foutopsporing met het script te verbinden.

2. Start de component PTVSD-foutopsporing en wacht tot een foutopsporing verbinding maakt.

3. Vanuit uw ontwikkelomgeving controleert u de logboeken die door het trainingsproces zijn gemaakt om het IP-adres te vinden waar het script wordt uitgevoerd.

4. U vertelt VS Code het IP-adres om `launch.json` de foutopsporing te verbinden met behulp van een bestand.

5. U bevestigt de foutopsporing en stapt interactief door het script.

### <a name="configure-python-scripts"></a>Python-scripts configureren

Als u foutopsporing wilt inschakelen, voert u de volgende wijzigingen aan in het Python-script(en) dat wordt gebruikt door stappen in uw ML-pijplijn:

1. Voeg de volgende importinstructies toe:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Voeg de volgende argumenten toe. Met deze argumenten u de foutopsporing zo nodig inschakelen en de time-out instellen voor het koppelen van de foutopsporing:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Voeg de volgende instructies toe. Deze instructies laden de huidige runcontext, zodat u het IP-adres van het knooppunt waarop de code wordt uitgevoerd, registreren:

    ```python
    global run
    run = Run.get_context()
    ```

1. Voeg `if` een instructie toe die PTVSD start en wacht tot een foutopsporing is gekoppeld. Als er geen foutopsporing wordt gekoppeld vóór de time-out, gaat het script gewoon door.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

In het volgende Python-voorbeeld wordt een basisbestand `train.py` weergegeven waarmee foutopsporing mogelijk is:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML-pijplijn configureren

Als u de Python-pakketten wilt bieden die nodig zijn om `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`PTVSD te starten en de runcontext te krijgen, maakt u een omgeving en stelt u deze in. Wijzig de SDK-versie die overeenkomt met de versie die u gebruikt. In het volgende codefragment wordt uitgelegd hoe u een omgeving maakt:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

In de sectie [Python-scripts configureren](#configure-python-scripts) zijn twee nieuwe argumenten toegevoegd aan de scripts die worden gebruikt door de ml-pijplijnstappen. In het volgende codefragment wordt uitgelegd hoe u deze argumenten gebruiken om foutopsporing voor de component in te schakelen en een time-out in te stellen. Het toont ook aan hoe de omgeving `runconfig=run_config`die eerder is gemaakt te gebruiken door :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Wanneer de pijplijn wordt uitgevoerd, maakt elke stap een onderliggende run. Als foutopsporing is ingeschakeld, worden in het gewijzigde script `70_driver_log.txt` gegevens weergegeven die vergelijkbaar zijn met de volgende tekst in de run voor het kind:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Sla `ip_address` de waarde op. Het wordt gebruikt in de volgende sectie.

> [!TIP]
> U het IP-adres ook vinden in de run logs voor het onderliggende bedrijf voor deze pijplijnstap. Zie [Programma's en metrische gegevens van Azure ML-experiment controleren](how-to-track-experiments.md)voor meer informatie over het weergeven van deze informatie.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Als u de Python Tools for Visual Studio (PTVSD) wilt installeren op uw VS-code-ontwikkelomgeving, gebruikt u de volgende opdracht:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Zie [Afstandsbediening opsporen](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)voor meer informatie over het gebruik van PTVSD met VS-code.

1. Als u VS-code wilt configureren om te communiceren met de Azure Machine Learning-compute waarop de foutopsporing wordt uitgevoerd, maakt u een nieuwe foutopsporingsconfiguratie:

    1. Selecteer in VS-code het menu __Foutopsporing__ en selecteer __Vervolgens Configuraties openen__. Er wordt een bestand met de naam __launch.json__ geopend.

    1. Zoek in het bestand __launch.json__ `"configurations": [`de regel die de volgende tekst bevat en voeg de volgende tekst erachter in. Wijzig `"host": "10.3.0.5"` de vermelding van het IP-adres dat is geretourneerd in uw logboeken uit de vorige sectie. Wijzig `"localRoot": "${workspaceFolder}/code/step"` de vermelding in een lokale map met een kopie van het script dat wordt verwijderd:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Als er al andere vermeldingen in de sectie configuraties zijn, voegt u een komma (,) toe na de code die u hebt ingevoegd.

        > [!TIP]
        > De beste praktijk is om de bronnen voor scripts in `localRoot` afzonderlijke mappen `/code/step1`te houden, daarom verwijst de voorbeeldwaarde .
        >
        > Als u meerdere scripts debugt, in verschillende mappen, maakt u een afzonderlijke configuratiesectie voor elk script.

    1. Sla het __bestand launch.json__ op.

### <a name="connect-the-debugger"></a>De foutopsporing verbinden

1. Open VS Code en open een lokale kopie van het script.
2. Stel breekpunten in waar u wilt dat het script stopt zodra u hebt gekoppeld.
3. Terwijl het onderliggende proces het `Timeout for debug connection` script uitvoert en het wordt weergegeven in de logboeken, gebruikt u de F5-toets of selecteert __u Foutopsporing__. Selecteer de azure __machine learning compute: configuratie van foutopsporingsop afstand__ wanneer u daarom wordt gevraagd. U ook het foutopsporingspictogram selecteren op de zijbalk, de __Azure Machine Learning: externe foutopsporingsvermelding__ in het vervolgkeuzelijst Foutopsporing en vervolgens de groene pijl gebruiken om de foutopsporing toe te voegen.

    Op dit punt maakt VS Code verbinding met PTVSD op het compute-knooppunt en stopt bij het breekpunt dat u eerder hebt ingesteld. U nu stap door de code als het draait, variabelen bekijken, enz.

    > [!NOTE]
    > Als het logboek een `Debugger attached = False`vermelding weergeeft, is de time-out verlopen en is het script verder gegaan zonder de foutopsporing. Verzend de pijplijn opnieuw en maak `Timeout for debug connection` de foutopsporing na het bericht en voordat de time-out verloopt.

## <a name="next-steps"></a>Volgende stappen

* Zie de SDK-referentie voor hulp bij het [azureml-pipelines-core-pakket](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en het pakket [azureml-pipelines-stappen.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)

* Zie de lijst met [uitzonderingen en foutcodes](algorithm-module-reference/designer-error-codes.md)van ontwerpers .

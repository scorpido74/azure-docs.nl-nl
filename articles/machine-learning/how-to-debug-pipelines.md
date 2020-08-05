---
title: Fout opsporing & problemen met ML-pijp lijnen oplossen
titleSuffix: Azure Machine Learning
description: Fouten opsporen in uw Azure Machine Learning-pijp lijnen in python. Leer veelvoorkomende Valk uilen voor het ontwikkelen van pijp lijnen en tips voor het oplossen van fouten in scripts voor en tijdens externe uitvoering. Meer informatie over hoe u Visual Studio code kunt gebruiken om uw machine learning-pijp lijnen interactief op te sporen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.topic: conceptual
ms.custom: troubleshooting, tracking-python
ms.openlocfilehash: 21254f5d7171d67614b212007aa33f4429505574
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553050"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten in Machine Learning-pijplijnen opsporen en oplossen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u fouten opspoort en oplost [machine learning pijp lijnen](concept-ml-pipelines.md) in de [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) en [Azure machine learning Designer (preview)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informatie over het volgende:

* Fouten opsporen met behulp van de Azure Machine Learning SDK
* Fout opsporing met behulp van de Azure Machine Learning Designer
* Fouten opsporen met Application Insights
* Interactief fouten opsporen met Visual Studio code (VS code) en de Python Tools for Visual Studio (PTVSD)

## <a name="azure-machine-learning-sdk"></a>Azure Machine Learning-SDK
In de volgende secties vindt u een overzicht van algemene Valk uilen bij het bouwen van pijp lijnen en verschillende strategieën voor het opsporen van fouten in uw code die wordt uitgevoerd in een pijp lijn. Gebruik de volgende tips wanneer u problemen ondervindt bij het verkrijgen van een pijp lijn die naar verwachting kan worden uitgevoerd.

### <a name="testing-scripts-locally"></a>Scripts lokaal testen

Een van de meest voorkomende storingen in een pijp lijn is dat een bijgevoegd script (script voor het opschonen van gegevens, Score script, enzovoort) niet wordt uitgevoerd zoals bedoeld, of dat er runtime-fouten in de externe Compute-context staan die moeilijk te vinden zijn in uw werk ruimte in de Azure Machine Learning Studio. 

Pijp lijnen zelf kunnen niet lokaal worden uitgevoerd, maar als u de scripts in isolatie uitvoert op uw lokale computer, kunt u sneller fouten opsporen omdat u niet hoeft te wachten op het proces voor het bouwen van de berekening en de omgeving. Er is een aantal ontwikkel werkzaamheden vereist om dit te doen:

* Als uw gegevens zich in een Cloud-Data Store bevinden, moet u gegevens downloaden en deze beschikbaar maken voor uw script. Het gebruik van een klein voor beeld van uw gegevens is een goede manier om in runtime te verminderen en snel feedback te krijgen over het gedrag van scripts
* Als u probeert een tussenliggende pijplijn stap te simuleren, moet u mogelijk hand matig de object typen bouwen waarvan het specifieke script in de vorige stap verwacht.
* U moet ook uw eigen omgeving definiëren en de afhankelijkheden repliceren die zijn gedefinieerd in uw externe Compute-omgeving

Zodra u een script hebt ingesteld om uit te voeren op uw lokale omgeving, is het veel eenvoudiger om fouten op te sporen in taken zoals:

* Een aangepaste configuratie voor fout opsporing koppelen
* Uitvoering onderbreken en object status controleren
* Het opvangen van type of logische fouten die pas worden weer gegeven na runtime

> [!TIP] 
> Zodra u kunt controleren of uw script wordt uitgevoerd zoals verwacht, voert u een goede volgende stap uit om het script uit te voeren in een pijp lijn met één stap voordat u deze in een pijp lijn met meerdere stappen probeert uit te voeren.

### <a name="debugging-scripts-from-remote-context"></a>Fouten opsporen in scripts in externe context

Het lokaal testen van scripts is een uitstekende manier om fouten op te sporen in primaire code fragmenten en complexe logica voordat u begint met het bouwen van een pijp lijn, maar op een bepaald moment moet u waarschijnlijk fouten opsporen in scripts tijdens het uitvoeren van de werkelijke pijp lijn zelf, met name bij het vaststellen van het gedrag dat optreedt tijdens de interactie tussen de stappen van de pijp lijn. We raden u aan om `print()` de instructies in uw stap scripts te gebruiken, zodat u de object status en verwachte waarden tijdens externe uitvoering kunt zien, vergelijkbaar met hoe u fouten opspoort in Java script-code.

Het logboek bestand `70_driver_log.txt` bevat: 

* Alle afgedrukte instructies tijdens de uitvoering van uw script
* De stack-trace voor het script 

Als u deze en andere logboek bestanden wilt vinden in de portal, klikt u eerst op de pijplijn uitvoering in uw werk ruimte.

![Lijst pagina pijplijn uitvoering](./media/how-to-debug-pipelines/pipelinerun-01.png)

Ga naar de detail pagina van de pijplijn uitvoering.

![Detail pagina pijp lijn uitvoeren](./media/how-to-debug-pipelines/pipelinerun-02.png)

Klik op de module voor de specifieke stap. Ga naar het tabblad **Logboeken** . Andere logboeken bevatten informatie over het proces van het bouwen van de omgevings installatie kopie en stap voorbereidings scripts.

![Tabblad detail pagina voor uitvoering van pijp lijn](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Wordt uitgevoerd voor *gepubliceerde pijp lijnen* vindt u op het tabblad **eind punten** in uw werk ruimte. Wordt uitgevoerd voor *niet-gepubliceerde pijp lijnen* vindt u in **experimenten** of **pijp lijnen**.

### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tabel bevat veelvoorkomende problemen bij het ontwikkelen van pijp lijnen met mogelijke oplossingen.

| Probleem | Mogelijke oplossing |
|--|--|
| Kan geen gegevens door geven aan de `PipelineData` map | Zorg ervoor dat u een map hebt gemaakt in het script dat overeenkomt met de locatie van de gegevens van de stap uitvoer. In de meeste gevallen definieert een invoer argument de uitvoermap en maakt u de map expliciet. Gebruiken `os.makedirs(args.output_dir, exist_ok=True)` om de uitvoermap te maken. Raadpleeg de [zelf studie](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) voor een score voorbeeld script waarin dit ontwerp patroon wordt weer gegeven. |
| Afhankelijkheids fouten | Als u scripts lokaal hebt ontwikkeld en getest, maar u afhankelijkheids problemen ondervindt bij het uitvoeren van een externe Compute in de pijp lijn, moet u ervoor zorgen dat afhankelijkheden en versies van de reken omgeving overeenkomen met uw test omgeving. (Zie [omgeving bouwen, in cache plaatsen en opnieuw gebruiken](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Dubbel zinnige fouten met Compute-doelen | Het verwijderen en opnieuw maken van Compute-doelen kan bepaalde problemen met reken doelen oplossen. |
| Pijp lijn waarbij de stappen niet opnieuw worden gebruikt | Het opnieuw gebruiken van de stap is standaard ingeschakeld, maar zorg ervoor dat u deze niet hebt uitgeschakeld in een pijplijn stap. Als opnieuw gebruiken is uitgeschakeld, `allow_reuse` wordt de para meter in de stap ingesteld op `False` . |
| De pijp lijn wordt onnodig opnieuw uitgevoerd | Om ervoor te zorgen dat de stappen alleen opnieuw worden uitgevoerd wanneer de onderliggende gegevens of scripts worden gewijzigd, moet u de mappen voor elke stap loskoppelen. Als u dezelfde bron directory voor meerdere stappen gebruikt, kan het nodig zijn om niet-uitgevoerde opnieuw uit te voeren. Gebruik de `source_directory` para meter voor een pijplijn stap object om naar uw geïsoleerde map voor die stap te verwijzen en zorg ervoor dat u niet hetzelfde `source_directory` pad gebruikt voor meerdere stappen. |

### <a name="logging-options-and-behavior"></a>Opties voor logboek registratie en gedrag

De onderstaande tabel bevat informatie over verschillende opties voor fout opsporing voor pijp lijnen. Het is geen limitatieve lijst, omdat er andere opties zijn dan alleen de Azure Machine Learning, python en opentellingen die hier worden weer gegeven.

| Bibliotheek                    | Type   | Voorbeeld                                                          | Doel                                  | Resources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning-SDK | Gegevens | `run.log(name, val)`                                             | Gebruikers interface van Azure Machine Learning Portal             | [Experimenten bijhouden](how-to-track-experiments.md)<br>[de klasse azureml. core. run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python afdrukken/logboek registratie    | Log    | `print(val)`<br>`logging.info(message)`                          | Logboeken van Stuur Programma's, Azure Machine Learning Designer | [Experimenten bijhouden](how-to-track-experiments.md)<br><br>[Python-logboek registratie](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| Opentellingen python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights-traceringen                | [Fouten met pijplijnen opsporen in Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-logboek registratie Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Voor beeld van logboek registratie opties

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

## <a name="azure-machine-learning-designer-preview"></a>Azure Machine Learning-ontwerpfunctie (preview)

In deze sectie vindt u een overzicht van het oplossen van problemen met pijp lijnen in de ontwerp functie. Voor pijp lijnen die in de ontwerp functie zijn gemaakt, kunt u het **70_driver_log** bestand vinden op de pagina ontwerpen of op de detail pagina van de pijplijn uitvoering.

### <a name="enable-logging-for-real-time-endpoints"></a>Logboek registratie inschakelen voor realtime-eind punten

Voor het oplossen van problemen met realtime-eind punten in de ontwerp functie moet u logboek registratie van toepassingen inschakelen met behulp van de SDK. Met logboek registratie kunt u problemen met model implementatie en-gebruik oplossen. Zie [logboek registratie voor geïmplementeerde modellen](how-to-enable-logging.md#logging-for-deployed-models)voor meer informatie. 

### <a name="get-logs-from-the-authoring-page"></a>Logboeken ophalen via de pagina ontwerpen

Wanneer u een pijplijn uitvoering verzendt en op de bewerkings pagina blijft, kunt u de logboek bestanden vinden die voor elke module worden gegenereerd, wanneer elke module is voltooid.

1. Selecteer een module die op het ontwerp canvas is uitgevoerd.
1. Ga in het rechterdeel venster van de module naar het tabblad **uitvoer en logboeken** .
1. Vouw het rechterdeel venster uit en selecteer het **70_driver_log.txt** om het bestand in de browser weer te geven. U kunt Logboeken ook lokaal downloaden.

    ![Uitgevouwen uitvoer deel venster in de ontwerp functie](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Logboeken ophalen van pijplijn uitvoeringen

U kunt ook de logboek bestanden voor specifieke uitvoeringen vinden op de detail pagina pijp lijn uitvoeren, die u kunt vinden in de sectie **pijp lijnen** of **experimenten** van de Studio.

1. Selecteer een pijplijn uitvoering die in de ontwerp functie is gemaakt.

    ![Pagina pijplijn uitvoering](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Selecteer een module in het voorbeeld venster.
1. Ga in het rechterdeel venster van de module naar het tabblad **uitvoer en logboeken** .
1. Vouw het rechterdeel venster uit om het **70_driver_log.txt** -bestand in de browser weer te geven of selecteer het bestand om de logboeken lokaal te downloaden.

> [!IMPORTANT]
> Als u een pijp lijn wilt bijwerken op de pagina Details van de pijplijn uitvoering, moet u de pijplijn uitvoering **klonen** naar een nieuwe pijp lijn concept. Een pijplijn uitvoering is een moment opname van de pijp lijn. Het is vergelijkbaar met een logboek bestand en kan niet worden gewijzigd. 

## <a name="application-insights"></a>Application Insights
Voor meer informatie over het gebruik van de bibliotheek opentellingen python op deze manier raadpleegt u deze hand leiding: [fouten opsporen en problemen oplossen met machine learning pijp lijnen in Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="visual-studio-code"></a>Visual Studio Code

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die wordt gebruikt in uw ML-pijp lijn. Met behulp van Visual Studio code (VS code) en de Python Tools for Visual Studio (PTVSD) kunt u aan de code koppelen zoals deze wordt uitgevoerd in de trainings omgeving.

### <a name="prerequisites"></a>Vereisten

* Een __Azure machine learning-werk ruimte__ die is geconfigureerd voor het gebruik van een __Azure Virtual Network__.
* Een __Azure machine learning pijp lijn__ die gebruikmaakt van python-scripts als onderdeel van de pijplijn stappen. Bijvoorbeeld een PythonScriptStep.
* Een Azure Machine Learning Compute-Cluster, dat zich __in het virtuele netwerk__ bevindt en wordt __gebruikt door de pijp lijn voor training__.
* Een __ontwikkel omgeving__ die zich __in het virtuele netwerk__bevindt. De ontwikkel omgeving kan een van de volgende zijn:

    * Een virtuele machine van Azure in het virtuele netwerk
    * Een reken instantie van een notebook-VM in het virtuele netwerk
    * Een client computer die is verbonden met het virtuele netwerk via een virtueel particulier netwerk (VPN).

Zie voor meer informatie over het gebruik van een Azure Virtual Network met Azure Machine Learning [Secure Azure ml experimenten en de functies voor het](how-to-enable-virtual-network.md)afmaken van een azure-Virtual Network.

### <a name="how-it-works"></a>Hoe werkt het?

Met uw ML pijplijn stappen voert u python-scripts uit. Deze scripts zijn gewijzigd om de volgende acties uit te voeren:
    
1. Registreer het IP-adres van de host waarop ze worden uitgevoerd. U gebruikt het IP-adres om de fout opsporing aan het script te koppelen.

2. Start het onderdeel PTVSD debug en wacht tot er een fout opsporingsprogramma is om verbinding te maken.

3. Vanuit uw ontwikkel omgeving bewaakt u de logboeken die zijn gemaakt door het trainings proces om het IP-adres te vinden waarop het script wordt uitgevoerd.

4. U vertelt het IP-adres om het fout opsporingsprogramma te verbinden met behulp van een `launch.json` bestand.

5. U koppelt het fout opsporingsprogramma en interactieve stap door het script.

### <a name="configure-python-scripts"></a>Python-scripts configureren

Als u fout opsporing wilt inschakelen, moet u de volgende wijzigingen aanbrengen in de python-script (s) die worden gebruikt door de stappen in uw ML-pijp lijn:

1. Voeg de volgende import instructies toe:

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Voeg de volgende argumenten toe. Met deze argumenten kunt u de debugger zo nodig inschakelen en de time-out voor het koppelen van het fout opsporingsprogramma instellen:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Voeg de volgende-instructies toe. Met deze instructies wordt de huidige uitvoerings context geladen, zodat u het IP-adres van het knoop punt waarop de code wordt uitgevoerd, kunt vastleggen:

    ```python
    global run
    run = Run.get_context()
    ```

1. Voeg een `if` instructie toe die PTVSD start en wacht tot een fout opsporingsprogramma is gekoppeld. Als er voor de time-out geen fout opsporingsprogramma is gekoppeld, wordt het script gewoon voortgezet.

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

In het volgende python-voor beeld ziet u een basis `train.py` bestand dat fout opsporing mogelijk maakt:

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

### <a name="configure-ml-pipeline"></a>ML-pijp lijn configureren

Als u de Python-pakketten wilt opgeven die nodig zijn om PTVSD te starten en de uitvoerings context op te halen, moet u een omgeving maken en instellen `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']` . Wijzig de SDK-versie zodat deze overeenkomt met die die u gebruikt. Het volgende code fragment laat zien hoe u een omgeving maakt:

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

In de sectie [python-scripts configureren](#configure-python-scripts) zijn twee nieuwe argumenten toegevoegd aan de scripts die worden gebruikt door uw ml-pijplijn stappen. Het volgende code fragment laat zien hoe u deze argumenten kunt gebruiken om fout opsporing in te scha kelen voor het onderdeel en om een time-out in te stellen. Ook wordt gedemonstreerd hoe u de omgeving die u eerder hebt gemaakt, kunt gebruiken door het volgende in te stellen `runconfig=run_config` :

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

Wanneer de pijp lijn wordt uitgevoerd, maakt elke stap een onderliggende uitvoering. Als fout opsporing is ingeschakeld, wordt in het gewijzigde script informatie die lijkt op de volgende tekst in de `70_driver_log.txt` voor de onderliggende uitvoering:

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Sla de `ip_address` waarde op. Deze wordt gebruikt in de volgende sectie.

> [!TIP]
> U kunt ook het IP-adres van de uitvoerings logboeken voor de onderliggende uitvoering van deze pijplijn stap vinden. Zie voor meer informatie over het weer geven van deze informatie [Azure ml experimenten en metrische gegevens bewaken](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om de Python Tools for Visual Studio (PTVSD) te installeren op uw VS code Development Environment:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Zie [fout opsporing op afstand](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)voor meer informatie over het gebruik van PTVSD met VS-code.

1. Als u VS code wilt configureren om te communiceren met de Azure Machine Learning Compute waarop de fout opsporing wordt uitgevoerd, maakt u een nieuwe configuratie voor fout opsporing:

    1. Selecteer in VS code het menu __fout opsporing__ en selecteer vervolgens __Open configuraties__. Er wordt een bestand met de naam __launch.js__ geopend.

    1. Zoek in het bestand __launch.jsop__ de regel die bevat `"configurations": [` en voeg de volgende tekst toe. Wijzig de `"host": "10.3.0.5"` vermelding in het IP-adres dat in uw logboeken wordt weer gegeven in de vorige sectie. Wijzig de `"localRoot": "${workspaceFolder}/code/step"` vermelding in een lokale map met een kopie van het script waarin fouten worden opgespoord:

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
        > Als er al andere vermeldingen in de sectie configuraties staan, voegt u een komma (,) toe na de code die u hebt ingevoegd.

        > [!TIP]
        > De best practice bestaat uit het behoud van de resources voor scripts in afzonderlijke directory's, wat de `localRoot` voor beeld-waarde verwijst `/code/step1` .
        >
        > Als u fouten opspoort in meerdere scripts, maakt u in verschillende directory's een afzonderlijke configuratie sectie voor elk script.

    1. Sla de __launch.jsop in__ het bestand.

### <a name="connect-the-debugger"></a>De debugger verbinden

1. Open VS code en open een lokale kopie van het script.
2. Stel onderbrekings punten in waar het script moet worden gestopt zodra u het hebt gekoppeld.
3. Terwijl het onderliggende proces het script uitvoert en de `Timeout for debug connection` wordt weer gegeven in de logboeken, gebruikt u de F5-toets of selecteert u __fout opsporing__. Wanneer u hierom wordt gevraagd, selecteert u de __Azure machine learning berekenen: configuratie van externe fout opsporing__ . U kunt ook het pictogram voor fout opsporing selecteren in de zijbalk, het __Azure machine learning: externe fout opsporing__ in het vervolg keuzemenu voor fout opsporing en vervolgens de groene pijl gebruiken om het fout opsporingsprogramma te koppelen.

    Op dit punt verbindt de VS code met PTVSD op het reken knooppunt en stopt dit met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

    > [!NOTE]
    > Als in het logboek een vermelding wordt weer gegeven `Debugger attached = False` , is de time-out verlopen en wordt het script voortgezet zonder het fout opsporingsprogramma. Dien de pijp lijn opnieuw in en sluit de debugger na het `Timeout for debug connection` bericht en voordat de time-out is verlopen.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de SDK-Naslag informatie voor hulp met het kern pakket voor [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en het pakket met [azureml-pijp lijnen-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

* Bekijk de lijst met [uitzonde ringen en fout codes voor Designer](algorithm-module-reference/designer-error-codes.md).

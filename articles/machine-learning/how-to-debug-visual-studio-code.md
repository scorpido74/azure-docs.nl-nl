---
title: Interactieve fout opsporing met Visual Studio code
titleSuffix: Azure Machine Learning
description: Interactief fouten opsporen Azure Machine Learning code, pijp lijnen en implementaties met Visual Studio code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 08/06/2020
ms.openlocfilehash: 08859eb87431b59e8be68369ce8f3876aa7d9f21
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530298"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Interactieve fout opsporing met Visual Studio code

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Leer hoe u interactief fouten opspoort Azure Machine Learning pijp lijnen en implementaties met behulp van Visual Studio code (VS code) en [depugpy](https://github.com/microsoft/debugpy/).

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Fouten in Machine Learning-pijplijnen opsporen en oplossen

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die wordt gebruikt in uw ML-pijp lijn. U kunt met behulp van VS code en debugpy koppelen aan de code zoals deze wordt uitgevoerd in de trainings omgeving.

### <a name="prerequisites"></a>Vereisten

* Een __Azure machine learning-werk ruimte__ die is geconfigureerd voor het gebruik van een __Azure Virtual Network__.
* Een __Azure machine learning pijp lijn__ die gebruikmaakt van python-scripts als onderdeel van de pijplijn stappen. Bijvoorbeeld een PythonScriptStep.
* Een Azure Machine Learning Compute-Cluster, dat zich __in het virtuele netwerk__ bevindt en wordt __gebruikt door de pijp lijn voor training__.
* Een __ontwikkel omgeving__ die zich __in het virtuele netwerk__bevindt. De ontwikkel omgeving kan een van de volgende zijn:

  * Een virtuele machine van Azure in het virtuele netwerk
  * Een reken instantie van een notebook-VM in het virtuele netwerk
  * Een client computer met een particuliere netwerk verbinding met het virtuele netwerk, hetzij via VPN of via ExpressRoute.

Zie [Virtual Network-isolatie en privacy overview](how-to-network-security-overview.md)voor meer informatie over het gebruik van een Azure-Virtual Network met Azure machine learning.

> [!TIP]
> Hoewel u kunt werken met Azure Machine Learning-resources die zich niet achter een virtueel netwerk bevinden, wordt het gebruik van een virtueel netwerk aanbevolen.

### <a name="how-it-works"></a>Uitleg

Met uw ML pijplijn stappen voert u python-scripts uit. Deze scripts zijn gewijzigd om de volgende acties uit te voeren:

1. Registreer het IP-adres van de host waarop ze worden uitgevoerd. U gebruikt het IP-adres om de fout opsporing aan het script te koppelen.

2. Start het onderdeel debugpy debug en wacht tot er een fout opsporingsprogramma is om verbinding te maken.

3. Vanuit uw ontwikkel omgeving bewaakt u de logboeken die zijn gemaakt door het trainings proces om het IP-adres te vinden waarop het script wordt uitgevoerd.

4. U vertelt het IP-adres om het fout opsporingsprogramma te verbinden met behulp van een `launch.json` bestand.

5. U koppelt het fout opsporingsprogramma en interactieve stap door het script.

### <a name="configure-python-scripts"></a>Python-scripts configureren

Als u fout opsporing wilt inschakelen, moet u de volgende wijzigingen aanbrengen in de python-script (s) die worden gebruikt door de stappen in uw ML-pijp lijn:

1. Voeg de volgende import instructies toe:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Voeg de volgende argumenten toe. Met deze argumenten kunt u de debugger zo nodig inschakelen en de time-out voor het koppelen van het fout opsporingsprogramma instellen:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Voeg de volgende-instructies toe. Met deze instructies wordt de huidige uitvoerings context geladen, zodat u het IP-adres van het knoop punt waarop de code wordt uitgevoerd, kunt vastleggen:

    ```python
    global run
    run = Run.get_context()
    ```

1. Voeg een `if` instructie toe die debugpy start en wacht tot een fout opsporingsprogramma is gekoppeld. Als er voor de time-out geen fout opsporingsprogramma is gekoppeld, wordt het script gewoon voortgezet. Zorg ervoor dat u de `HOST` waarden en vervangt door `PORT` `listen` uw eigen waarde.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

In het volgende python-voor beeld ziet u een basis `train.py` bestand dat fout opsporing mogelijk maakt:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
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
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML-pijp lijn configureren

Als u de Python-pakketten wilt opgeven die nodig zijn om debugpy te starten en de uitvoerings context op te halen, moet u een omgeving maken en instellen `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . Wijzig de SDK-versie zodat deze overeenkomt met die die u gebruikt. Het volgende code fragment laat zien hoe u een omgeving maakt:

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
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

In de sectie [python-scripts configureren](#configure-python-scripts) zijn nieuwe argumenten toegevoegd aan de scripts die worden gebruikt door uw ml-pijplijn stappen. Het volgende code fragment laat zien hoe u deze argumenten kunt gebruiken om fout opsporing in te scha kelen voor het onderdeel en om een time-out in te stellen. Ook wordt gedemonstreerd hoe u de omgeving die u eerder hebt gemaakt, kunt gebruiken door het volgende in te stellen `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
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

1. Gebruik de volgende opdracht om debugpy te installeren op uw VS code Development Environment:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Zie [fout opsporing op afstand](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)voor meer informatie over het gebruik van DEBUGPY met VS-code.

1. Als u VS code wilt configureren om te communiceren met de Azure Machine Learning Compute waarop de fout opsporing wordt uitgevoerd, maakt u een nieuwe configuratie voor fout opsporing:

    1. Selecteer in VS code het menu __fout opsporing__ en selecteer vervolgens __Open configuraties__. Er wordt een bestand met de naam __launch.js__ geopend.

    1. Zoek in het bestand __launch.jsop__ de regel die bevat `"configurations": [` en voeg de volgende tekst toe. Wijzig de `"host": "<IP-ADDRESS>"` vermelding in het IP-adres dat in uw logboeken wordt weer gegeven in de vorige sectie. Wijzig de `"localRoot": "${workspaceFolder}/code/step"` vermelding in een lokale map met een kopie van het script waarin fouten worden opgespoord:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
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
        > De best practice, met name voor pijp lijnen, is om de resources voor scripts in afzonderlijke directory's te blijven, zodat de code alleen relevant is voor elk van de stappen. In dit voor beeld wordt `localRoot` naar de voorbeeld waarde verwezen `/code/step1` .
        >
        > Als u fouten opspoort in meerdere scripts, maakt u in verschillende directory's een afzonderlijke configuratie sectie voor elk script.

    1. Sla de __launch.jsop in__ het bestand.

### <a name="connect-the-debugger"></a>De debugger verbinden

1. Open VS code en open een lokale kopie van het script.
2. Stel onderbrekings punten in waar het script moet worden gestopt zodra u het hebt gekoppeld.
3. Terwijl het onderliggende proces het script uitvoert en de `Timeout for debug connection` wordt weer gegeven in de logboeken, gebruikt u de F5-toets of selecteert u __fout opsporing__. Wanneer u hierom wordt gevraagd, selecteert u de __Azure machine learning berekenen: configuratie van externe fout opsporing__ . U kunt ook het pictogram voor fout opsporing selecteren in de zijbalk, het __Azure machine learning: externe fout opsporing__ in het vervolg keuzemenu voor fout opsporing en vervolgens de groene pijl gebruiken om het fout opsporingsprogramma te koppelen.

    Op dit punt verbindt de VS code met debugpy op het reken knooppunt en stopt dit met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

    > [!NOTE]
    > Als in het logboek een vermelding wordt weer gegeven `Debugger attached = False` , is de time-out verlopen en wordt het script voortgezet zonder het fout opsporingsprogramma. Dien de pijp lijn opnieuw in en sluit de debugger na het `Timeout for debug connection` bericht en voordat de time-out is verlopen.

## <a name="debug-and-troubleshoot-deployments"></a>Problemen opsporen en oplossingen oplossen

In sommige gevallen moet u mogelijk interactief fouten opsporen in de python-code die in uw model implementatie is opgenomen. Als het script voor de vermelding bijvoorbeeld mislukt en de reden niet kan worden bepaald door aanvullende logboek registratie. U kunt met behulp van VS code en de debugpy koppelen aan de code die wordt uitgevoerd in de docker-container.

> [!IMPORTANT]
> Deze methode van fout opsporing werkt niet wanneer u `Model.deploy()` `LocalWebservice.deploy_configuration` een model gebruikt en lokaal implementeert. In plaats daarvan moet u een installatie kopie maken met behulp van de methode [model. package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-) .

Voor lokale web service-implementaties is een werkende docker-installatie op uw lokale systeem vereist. Raadpleeg de [docker-documentatie](https://docs.docker.com/)voor meer informatie over het gebruik van docker. Houd er rekening mee dat bij het werken met reken instanties al docker is geïnstalleerd.

### <a name="configure-development-environment"></a>De ontwikkelomgeving configureren

1. Gebruik de volgende opdracht om debugpy te installeren op uw lokale en ontwikkelings omgeving:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    Zie [fout opsporing op afstand](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)voor meer informatie over het gebruik van DEBUGPY met VS-code.

1. Als u VS code wilt configureren om met de docker-installatie kopie te communiceren, maakt u een nieuwe configuratie voor fout opsporing:

    1. Selecteer in VS code het menu __fout opsporing__ en selecteer vervolgens __Open configuraties__. Er wordt een bestand met de naam __launch.js__ geopend.

    1. Zoek in het bestand __launch.jsop__ de regel die bevat `"configurations": [` en voeg de volgende tekst toe:

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Als er al andere vermeldingen in de sectie configuraties staan, voegt u een komma (,) toe na de code die u hebt ingevoegd.

        Deze sectie wordt gekoppeld aan de docker-container via poort 5678.

    1. Sla de __launch.jsop in__ het bestand.

### <a name="create-an-image-that-includes-debugpy"></a>Een installatie kopie maken die debugpy bevat

1. Wijzig de Conda-omgeving voor uw implementatie, zodat deze debugpy bevat. In het volgende voor beeld ziet u hoe u het toevoegt met behulp van de `pip_packages` para meter:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Als u debugpy wilt starten en wilt wachten op een verbinding wanneer de service wordt gestart, voegt u het volgende toe aan de bovenkant van het `score.py` bestand:

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Maak een installatie kopie op basis van de omgevings definitie en haal de installatie kopie op in het lokale REGI ster. 

    > [!NOTE]
    > In dit voor beeld wordt ervan uitgegaan dat `ws` naar uw Azure machine learning-werk ruimte verwijst en dat `model` het model dat wordt geïmplementeerd. Het `myenv.yml` bestand bevat de Conda-afhankelijkheden die zijn gemaakt in stap 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Zodra de installatie kopie is gemaakt en gedownload, wordt het pad naar de afbeelding (inclusief opslag plaats, naam en label, in dit geval ook de samen vatting) weer gegeven in een bericht dat er ongeveer als volgt uitziet:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Gebruik de volgende opdracht om een tag toe te voegen om het gemakkelijker te maken om met de afbeelding te werken. Vervang door `myimagepath` de locatie waarde uit de vorige stap.

    ```bash
    docker tag myimagepath debug:1
    ```

    Voor de rest van de stappen kunt u de lokale installatie kopie `debug:1` gebruiken in plaats van de waarde van het volledige pad naar de afbeelding.

### <a name="debug-the-service"></a>Fouten opsporen in de service

> [!TIP]
> Als u een time-out instelt voor de debugpy-verbinding in het `score.py` bestand, moet u de VS-code verbinden met de foutopsporingssessie voordat de time-out verloopt. Start VS code, open het lokale exemplaar van `score.py` , stel een onderbrekings punt in en laat het gereed om door te gaan voordat u de stappen in deze sectie uitvoert.
>
> Zie [fout opsporing](https://code.visualstudio.com/Docs/editor/debugging)voor meer informatie over het opsporen van fouten en het instellen van onderbrekings punten.

1. Gebruik de volgende opdracht om een docker-container met behulp van de installatie kopie te starten:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_path_to_score.py>:/var/azureml-apps/score.py debug:1 /bin/bash
    ```

    Hiermee koppelt `score.py` u uw lokale map aan de container. Wijzigingen die in de editor zijn aangebracht, worden daarom automatisch doorgevoerd in de container.

1. Voer in de container de volgende opdracht uit in de shell

    ```bash
    runsvdir /var/runit
    ```

1. Als u VS code aan debugpy in de container wilt koppelen, opent u VS code en gebruikt u de toets F5 of selecteert u __fout opsporing__. Wanneer u hierom wordt gevraagd, selecteert u de __Azure machine learning implementatie: docker debug__ -configuratie. U kunt ook het debug-pictogram selecteren in de zijbalk, de __Azure machine learning implementatie: docker debug__ -vermelding in het vervolg keuzemenu voor fout opsporing en vervolgens de groene pijl gebruiken om het fout opsporingsprogramma te koppelen.

    ![Het pictogram fout opsporing, de knop fout opsporing starten en de configuratie kiezer](./media/how-to-troubleshoot-deployment/start-debugging.png)

Op dit punt verbindt de VS code met debugpy in de docker-container en stopt dit met het onderbrekings punt dat u eerder hebt ingesteld. U kunt nu de code door lopen terwijl deze wordt uitgevoerd, variabelen weer geven, enzovoort.

Zie [fouten opsporen in uw Python-code](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)voor meer informatie over het gebruik van VS code voor het opsporen van problemen met python.

### <a name="stop-the-container"></a>De container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht:

```bash
docker stop debug
```

## <a name="next-steps"></a>Volgende stappen

Nu u Visual Studio code Remote hebt ingesteld, kunt u een reken instantie gebruiken als externe Compute van Visual Studio code om uw code interactief te debuggen. 

[Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.

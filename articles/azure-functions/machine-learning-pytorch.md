---
title: Een PyTorch-model implementeren als een Azure Functions-toepassing
description: Gebruik een vooraf getraind ResNet 18 Neural-netwerk van PyTorch met Azure Functions om 1 van 1000 ImageNet-labels toe te wijzen aan een installatie kopie.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.openlocfilehash: 17acb7e351d5f1c009a6a8a14717e987fae3e895
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379897"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Zelf studie: een vooraf getraind afbeeldings classificatie model implementeren om te Azure Functions met PyTorch

In dit artikel leert u hoe u python, PyTorch en Azure Functions kunt gebruiken om een vooraf getraind model te laden voor het classificeren van een afbeelding op basis van de inhoud ervan. Omdat u lokaal werkt en geen Azure-resources in de Cloud maakt, zijn er geen kosten om deze zelf studie te volt ooien.

> [!div class="checklist"]
> * Initialiseer een lokale omgeving voor het ontwikkelen van Azure Functions in python.
> * Een vooraf getrainde PyTorch-machine learning model importeren in een functie-app.
> * Bouw een serverloze HTTP API voor het classificeren van een afbeelding als een van de 1000 ImageNet- [klassen](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a).
> * De API van een web-app gebruiken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 of hoger](https://www.python.org/downloads/release/python-374/). (Python 3.8. x en python 3.6. x worden ook gecontroleerd met Azure Functions.)
- De [Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Controle van vereisten

1. Voer `func --version` in een Terminal-of opdracht venster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger is.
1. Voer `python --version` (Linux/MacOS) of `py --version` (Windows) uit om uw python-versie rapporten te controleren 3.7. x.

## <a name="clone-the-tutorial-repository"></a>De zelfstudie bibliotheek klonen

1. Kloon in een Terminal-of opdracht venster de volgende opslag plaats met git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navigeer naar de map en Bekijk de inhoud ervan.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *Start* is de werkmap voor de zelf studie.
    - *End* is het uiteindelijke resultaat en de volledige implementatie voor uw referentie.
    - *resources* bevatten het machine learning model en helper-bibliotheken.
    - Front- *End* is een website die de functie-app aanroept.

## <a name="create-and-activate-a-python-virtual-environment"></a>Een virtuele python-omgeving maken en activeren

Ga naar de map *Start* en voer de volgende opdrachten uit om een virtuele omgeving met de naam `.venv`te maken en te activeren.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Als python het venv-pakket niet heeft geïnstalleerd in uw Linux-distributie, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (Als u de virtuele omgeving wilt afsluiten, voert u `deactivate`uit.)


## <a name="create-a-local-functions-project"></a>Een project met lokale functies maken

In Azure Functions is een functie project een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageert. Alle functies in een project delen dezelfde lokale en hosting configuraties. In deze sectie maakt u een functie project met een enkele standaard functie met de naam `classify` die een HTTP-eind punt biedt. U kunt meer specifieke code toevoegen in een latere sectie.

1. Gebruik in de map *Start* de Azure functions core tools voor het initialiseren van een python-functie-app:

    ```
    func init --worker-runtime python
    ```

    Na de initialisatie bevat de map *Start* diverse bestanden voor het project, met inbegrip van configuratie bestanden met de naam [Local. settings. json](functions-run-local.md#local-settings-file) en [host. json](functions-host-json.md). Omdat *Local. settings. json* geheimen kan bevatten die zijn gedownload van Azure, wordt het bestand standaard uitgesloten van broncode beheer in het *. gitignore* -bestand.

    > [!TIP]
    > Omdat een functie project is gekoppeld aan een specifieke runtime, moeten alle functies in het project met dezelfde taal worden geschreven.

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie specificeert. `func new` een submap te maken die overeenkomt met de naam van de functie die geschikt is voor de gekozen taal van het project en een configuratie bestand met de naam *Function. json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Met deze opdracht maakt u een map die overeenkomt met de naam van de functie, *classificeren*. In die map zijn twee bestanden: *\_\_init\_\_. py*, die de functie code en *Function. json*bevat, waarmee de trigger van de functie en de bijbehorende invoer-en uitvoer bindingen worden beschreven. Zie [de bestands inhoud](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) in de Snelstartgids voor python controleren voor meer informatie over de inhoud van deze bestanden.


## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Start de functie door de lokale Azure Functions runtime host te starten in de map *Start* :

    ```
    func start
    ```

1. Zodra het `classify`-eind punt wordt weer gegeven in de uitvoer, gaat u naar de URL ```http://localhost:7071/api/classify?name=Azure```. Het bericht Hello Azure! moet worden weer gegeven in de uitvoer.

1. Gebruik **Ctrl**-**C** om de host te stoppen.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Het PyTorch-model importeren en helpercode toevoegen

Als u de functie `classify` wilt wijzigen om een afbeelding te classificeren op basis van de inhoud, gebruikt u een vooraf getraind [ResNet](https://arxiv.org/abs/1512.03385) -model. Het vooraf getrainde model, dat afkomstig is van [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), classificeert een afbeelding in 1 van 1000 [ImageNet-klassen](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). U voegt vervolgens enkele hulp code en afhankelijkheden toe aan uw project.

1. Voer in de map *Start* de volgende opdracht uit om de Voorspellings code en labels te kopiëren naar de map *classificeren* .

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Controleer of de map *classificeren* bestanden bevat met de naam *predict.py* en *labels. txt*. Als dat niet het geval is, controleert u of de opdracht in de map *Start* is uitgevoerd.

1. Open *Start/requirements. txt* in een tekst editor en voeg de afhankelijkheden toe die vereist zijn voor de Help-code. deze moet er als volgt uitzien:

    ```txt
    azure-functions
    requests
    numpy==1.15.4
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-win_amd64.whl; sys_platform == 'win32' and python_version == '3.8'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.8'
    torchvision==0.5.0
    ```

1. Sla het bestand *Requirements. txt*op en voer de volgende opdracht uit vanuit de map *Start* om de afhankelijkheden te installeren.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

De installatie kan enkele minuten duren, gedurende welke tijd u kunt door gaan met het wijzigen van de functie in de volgende sectie.
> [!TIP]
> >In Windows kan de fout optreden ' kan pakketten niet installeren vanwege een EnvironmentError: [errno 2] bestand of map: ' gevolgd door een lange padnaam naar een bestand als *sharded_mutable_dense_hashtable. cpython-37. pyc*. Deze fout treedt meestal op omdat de diepte van het mappad te lang wordt. In dit geval moet u de register sleutel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` instellen op `1` om lange paden in te scha kelen. U kunt ook controleren waar uw Python-interpreter is geïnstalleerd. Als deze locatie een lang pad heeft, probeert u opnieuw te installeren in een map met een korter pad.

## <a name="update-the-function-to-run-predictions"></a>De functie bijwerken voor het uitvoeren van voor spellingen

1. Open *classificatie/\_\_init\_\_. py* in een tekst editor en voeg de volgende regels toe na de bestaande `import`-instructies voor het importeren van de standaard-JSON-bibliotheek en de voor *spel* -helpers:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Vervang de volledige inhoud van de functie `main` door de volgende code:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Deze functie ontvangt een afbeeldings-URL in een query reeks parameter met de naam `img`. Vervolgens wordt `predict_image_from_url` aangeroepen vanuit de helper-bibliotheek om de installatie kopie te downloaden en classificeren met behulp van het PyTorch-model. De functie retourneert vervolgens een HTTP-antwoord met de resultaten.

    > [!IMPORTANT]
    > Omdat dit HTTP-eind punt wordt aangeroepen door een webpagina die wordt gehost op een ander domein, bevat het antwoord een `Access-Control-Allow-Origin` header om te voldoen aan de vereisten voor CORS (cross-Origin Resource Sharing) van de browser.
    >
    > Wijzig in een productie toepassing `*` in de specifieke oorsprong van de webpagina voor extra beveiliging.

1. Sla de wijzigingen op en controleer of de installatie van afhankelijkheden is voltooid, start de lokale functie host opnieuw met `func start`. Zorg ervoor dat u de host in de map *Start* uitvoert, zodat de virtuele omgeving geactiveerd is. Anders wordt de host gestart, maar er worden fouten weer geven bij het aanroepen van de functie.

    ```
    func start
    ```

1. Open in een browser de volgende URL om de functie aan te roepen met de URL van een afbeelding van een Bernese Mountain Dog en bevestig dat de geretourneerde JSON de afbeelding als een Bernese Mountain Dog classificeert.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Zorg ervoor dat de host actief blijft omdat u deze in de volgende stap gebruikt.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Voer de front-end van de lokale web-app uit om de functie te testen

Voor het testen van het aanroepen van het functie-eind punt vanuit een andere web-app, is er een eenvoudige app in de map front- *End* van de opslag plaats.

1. Open een nieuwe terminal of opdracht prompt en activeer de virtuele omgeving (zoals eerder beschreven onder [Create and Activate a virtuele python-omgeving](#create-and-activate-a-python-virtual-environment)).

1. Ga naar de map front- *End* van de opslag plaats.

1. Een HTTP-server starten met python:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Ga in een browser naar `localhost:8000`en voer een van de volgende foto-Url's in het tekstvak in of gebruik de URL van een openbaar toegankelijke afbeelding.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Selecteer **verzenden** om het functie-eind punt aan te roepen voor het classificeren van de afbeelding.

    ![Scherm opname van voltooid project](media/machine-learning-pytorch/screenshot.png)

    Als de browser een fout meldt bij het verzenden van de afbeeldings-URL, controleert u de Terminal waarin u de functie-app uitvoert. Als er een fout bericht wordt weer gegeven als ' geen module gevonden ' PIL ' ', hebt u de functie-app mogelijk gestart in de map *Start* zonder eerst de virtuele omgeving te activeren die u eerder hebt gemaakt. Als u nog steeds fouten ziet, voert u `pip install -r requirements.txt` opnieuw uit met de virtuele omgeving geactiveerd en zoekt u naar fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Omdat de meeste van deze zelf studie lokaal op uw computer wordt uitgevoerd, zijn er geen Azure-resources of-services om op te schonen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een HTTP API-eind punt kunt bouwen en aanpassen met Azure Functions om installatie kopieën te classificeren met behulp van een PyTorch-model. U hebt ook geleerd hoe u de API aanroept vanuit een web-app. U kunt de technieken in deze zelf studie gebruiken om Api's van elke complexiteit te maken, terwijl deze worden uitgevoerd op het serverloos reken model van Azure Functions.

Zie ook:

- [Implementeer de functie in azure met behulp van Visual Studio code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Azure Functions python-ontwikkelaars handleiding](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [De functie implementeren naar Azure Functions met behulp van de Azure CLI-hand leiding](./functions-run-local.md#publish)

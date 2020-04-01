---
title: Een PyTorch-model implementeren als Azure-toepassingstoepassing
description: Gebruik een vooraf getraind ResNet 18 diep neuraal netwerk van PyTorch met Azure-functies om 1 van de 1000 ImageNet-labels aan een afbeelding toe te wijzen.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.openlocfilehash: 17acb7e351d5f1c009a6a8a14717e987fae3e895
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78379897"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Zelfstudie: Een vooraf getraind classificatiemodel voor afbeeldingen implementeren in Azure-functies met PyTorch

In dit artikel leert u hoe u Python-, PyTorch- en Azure-functies gebruikt om een vooraf opgeleid model te laden voor het classificeren van een afbeelding op basis van de inhoud ervan. Omdat u al het werk lokaal doet en geen Azure-bronnen in de cloud maakt, zijn er geen kosten verbonden aan het voltooien van deze zelfstudie.

> [!div class="checklist"]
> * Initialiseer een lokale omgeving voor het ontwikkelen van Azure-functies in Python.
> * Importeer een vooraf opgeleid PyTorch machine learning-model in een functie-app.
> * Maak een serverloze HTTP-API voor het classificeren van een afbeelding als een van de 1000 [ImageNet-klassen.](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)
> * Gebruik de API vanuit een web-app.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 3.7.4 of hoger](https://www.python.org/downloads/release/python-374/). (Python 3.8.x en Python 3.6.x worden ook geverifieerd met Azure-functies.)
- De [kernhulpprogramma's voor Azure-functies](functions-run-local.md#install-the-azure-functions-core-tools)
- Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Vereiste controle

1. Voer in een terminal- `func --version` of opdrachtvenster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger zijn.
1. Voer `python --version` (Linux/MacOS) `py --version` of (Windows) uit om uw Python-versierapporten 3.7.x te controleren.

## <a name="clone-the-tutorial-repository"></a>De zelfstudierepository klonen

1. Kloon in een terminal- of opdrachtvenster de volgende opslagplaats met Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navigeer in de map en bestudeer de inhoud ervan.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* is uw werkmap voor de zelfstudie.
    - *einde* is het eindresultaat en volledige implementatie voor uw referentie.
    - *bronnen* bevat het machine learning-model en de helperbibliotheken.
    - *frontend* is een website die de functie-app aanroept.

## <a name="create-and-activate-a-python-virtual-environment"></a>Een virtuele Python-omgeving maken en activeren

Navigeer naar de *startmap* en voer de volgende opdrachten `.venv`uit om een virtuele omgeving met de naam te maken en te activeren.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Als Python het venv-pakket niet op uw Linux-distributie heeft geïnstalleerd, voert u de volgende opdracht uit:

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

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (Voer de virtuele omgeving `deactivate`uit om de virtuele omgeving af te sluiten.


## <a name="create-a-local-functions-project"></a>Een project voor lokale functies maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk reageert op een specifieke trigger. Alle functies in een project delen dezelfde lokale en hostingconfiguraties. In deze sectie maakt u een functieproject met `classify` één boilerplate-functie met de naam een HTTP-eindpunt. U voegt meer specifieke code toe in een latere sectie.

1. Gebruik in de *startmap* de Azure Functions Core Tools om een Python-functie-app te initialiseren:

    ```
    func init --worker-runtime python
    ```

    Na de initialisatie bevat de *startmap* verschillende bestanden voor het project, waaronder configuraties bestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload van Azure, is het bestand standaard uitgesloten van bronbeheer in het *.gitignore-bestand.*

    > [!TIP]
    > Omdat een functieproject is gekoppeld aan een specifieke runtime, moeten alle functies in het project met dezelfde taal worden geschreven.

1. Voeg een functie toe aan uw project `--name` met behulp van de volgende `--template` opdracht, waarbij het argument de unieke naam van uw functie is en het argument de trigger van de functie opgeeft. `func new`een submap maken die overeenkomt met de functienaam die een codebestand bevat dat past bij de gekozen taal van het project en een configuratiebestand met de naam *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Met deze opdracht wordt een map gemaakt die overeenkomt met de naam van de functie en *classificeren*. In die map bevinden zich twee bestanden: * \_ \_init\_\_.py*, die de functiecode bevat, en *function.json*, die de trigger van de functie en de invoer- en uitvoerbindingen beschrijft. Zie [De bestandsinhoud](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) in de snelstart van Python onderzoeken voor meer informatie over de inhoud van deze bestanden.


## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Start de functie door de lokale runtime-host azure-functies in de *startmap te* starten:

    ```
    func start
    ```

1. Zodra u `classify` het eindpunt in de uitvoer ziet ```http://localhost:7071/api/classify?name=Azure```verschijnen, navigeert u naar de URL. Het bericht "Hallo Azure!" moet worden weergegeven in de uitvoer.

1. Gebruik **Ctrl**-**C** om de host te stoppen.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Het PyTorch-model importeren en helpercode toevoegen

Als u `classify` de functie wilt wijzigen om een afbeelding te classificeren op basis van de inhoud, gebruikt u een vooraf getraind [ResNet-model.](https://arxiv.org/abs/1512.03385) Het voorgetrainde model, dat afkomstig is van [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), classificeert een afbeelding in 1 van de 1000 [ImageNet-klassen.](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) Vervolgens voegt u enkele helpercode en afhankelijkheden toe aan uw project.

1. Voer in de *beginmap* de volgende opdracht uit om de voorspellingscode en -labels naar de *map classificeren* te kopiëren.

    # <a name="bash"></a>[Bash](#tab/bash)

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

1. Controleer of de *map classificeren* bestanden bevat met de naam *predict.py* en *labels.txt*. Als dit niet het zo is, controleert u of u de opdracht in de *startmap hebt* uitgevoerd.

1. Open *start/requirements.txt* in een teksteditor en voeg de afhankelijkheden toe die vereist zijn voor de helpercode, die er als volgt uit moet zien:

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

1. Opslaan *requirements.txt,* voer vervolgens de volgende opdracht uit vanuit de *startmap* om de afhankelijkheden te installeren.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

De installatie kan enkele minuten duren, gedurende welke tijd u doorgaan met het wijzigen van de functie in de volgende sectie.
> [!TIP]
> >Op Windows u de fout tegenkomen: Kon geen pakketten installeren als gevolg van een EnvironmentError: [Errno 2] Geen dergelijk bestand of map: gevolgd door een lange pathname naar een bestand als *sharded_mutable_dense_hashtable.cpython-37.pyc*. Deze fout treedt meestal op omdat de diepte van het mappad te lang wordt. Stel in dit geval `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` de `1` registersleutel in om lange paden in te schakelen. Controleer afwisselend waar uw Python-tolk is geïnstalleerd. Als die locatie een lang pad heeft, probeert u het opnieuw installeren in een map met een korter pad.

## <a name="update-the-function-to-run-predictions"></a>De functie bijwerken om voorspellingen uit te voeren

1. Open *\_\_classificeren/\_\_init .py* in een teksteditor `import` en voeg de volgende regels toe na de bestaande instructies om de standaard JSON-bibliotheek en de *helpehelpers te* importeren:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Vervang de volledige `main` inhoud van de functie door de volgende code:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Deze functie ontvangt een afbeeldings-URL `img`in een parameter querytekenreeks met de naam . Vervolgens belt `predict_image_from_url` het vanuit de helperbibliotheek om de afbeelding te downloaden en te classificeren met behulp van het PyTorch-model. De functie retourneert vervolgens een HTTP-antwoord met de resultaten.

    > [!IMPORTANT]
    > Omdat dit HTTP-eindpunt wordt aangeroepen door een webpagina die `Access-Control-Allow-Origin` op een ander domein wordt gehost, bevat het antwoord een koptekst om te voldoen aan de CORS-vereisten (Cross-Origin Resource Sharing) van de browser.
    >
    > Wijzig `*` in een productietoepassing de specifieke oorsprong van de webpagina voor extra beveiliging.

1. Sla uw wijzigingen op en ga ervan uit dat afhankelijkheden `func start`klaar zijn met installeren, start de lokale functiehost opnieuw met . Zorg ervoor dat u de host in de *startmap* uitvoert terwijl de virtuele omgeving is geactiveerd. Anders start de host, maar ziet u fouten bij het inroepen van de functie.

    ```
    func start
    ```

1. Open in een browser de volgende URL om de functie aan te roepen met de URL van een Berner Berghondafbeelding en bevestig dat de geretourneerde JSON de afbeelding classificert als een Berner berghond.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Houd de host draaiende omdat u deze in de volgende stap gebruikt.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>De front-end van de lokale web-app uitvoeren om de functie te testen

Om een beroep op het functieeindpunt van een andere web-app te testen, is er een eenvoudige app in de *frontend-map* van de repository.

1. Open een nieuwe terminal of opdrachtprompt en activeer de virtuele omgeving (zoals eerder beschreven onder [Een virtuele Python-omgeving maken en activeren).](#create-and-activate-a-python-virtual-environment)

1. Navigeer naar de *frontendmap van* de opslagplaats.

1. Een HTTP-server starten met Python:

    # <a name="bash"></a>[Bash](#tab/bash)

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

1. Navigeer in een `localhost:8000`browser naar een van de volgende URL's van de foto in het tekstvak of gebruik de URL van een openbaar toegankelijke afbeelding.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Selecteer **Verzenden** om het functieeindpunt aan te roepen om de afbeelding te classificeren.

    ![Schermafbeelding van voltooid project](media/machine-learning-pytorch/screenshot.png)

    Als de browser een fout meldt wanneer u de URL van de afbeelding verzendt, controleert u de terminal waarin u de functie-app uitvoert. Als u een fout ziet zoals 'Geen module gevonden 'PIL', hebt u de functie-app mogelijk gestart in de *startmap* zonder eerst de virtuele omgeving te activeren die u eerder hebt gemaakt. Als u nog steeds `pip install -r requirements.txt` fouten ziet, voert u opnieuw uit met de virtuele omgeving geactiveerd en zoekt u naar fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Omdat deze zelfstudie volledig op uw machine wordt uitgevoerd, zijn er geen Azure-bronnen of -services om op te schonen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een HTTP API-eindpunt met Azure-functies maken en aanpassen om afbeeldingen te classificeren met behulp van een PyTorch-model. U hebt ook geleerd hoe u de API aanroepen vanuit een web-app. U de technieken in deze zelfstudie gebruiken om API's van elke complexiteit uit te bouwen, terwijl u wordt uitgevoerd op het serverloze rekenmodel dat wordt geleverd door Azure-functies.

Zie ook:

- [Implementeer de functie naar Azure met behulp van Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Handleiding azure-functies Python-ontwikkelaar](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [De functie implementeren in Azure-functies met behulp van de Azure CLI-handleiding](./functions-run-local.md#publish)

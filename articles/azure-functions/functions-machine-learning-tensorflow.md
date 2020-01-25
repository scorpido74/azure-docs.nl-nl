---
title: Python en tensor flow gebruiken voor machine learning in azure
description: Gebruik python, tensor flow en Azure Functions met een machine learning model om een afbeelding te classificeren op basis van de inhoud ervan.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e98655dca7d682e5c42f3b0ae7f26c892bd12377
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710724"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Zelf studie: machine learning modellen Toep assen in Azure Functions met python en tensor flow

In dit artikel leert u hoe u python, tensor flow en Azure Functions gebruikt met een machine learning model om een afbeelding te classificeren op basis van de inhoud ervan. Omdat u lokaal werkt en geen Azure-resources in de Cloud maakt, zijn er geen kosten om deze zelf studie te volt ooien.

> [!div class="checklist"]
> * Initialiseer een lokale omgeving voor het ontwikkelen van Azure Functions in python.
> * Een aangepast tensor flow-machine learning model importeren in een functie-app.
> * Bouw een serverloze HTTP API voor het classificeren van een afbeelding met een hond of kat.
> * De API van een web-app gebruiken.

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 en python 3.6. x worden gecontroleerd met Azure Functions; Python 3,8 en latere versies worden nog niet ondersteund.)
- De [Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Controle van vereisten

1. Voer `func --version` in een Terminal-of opdracht venster uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger is.
1. Voer `python --version` (Linux/MacOS) of `py --version` (Windows) uit om uw python-versie rapporten te controleren 3.7. x.

## <a name="clone-the-tutorial-repository"></a>De zelfstudie bibliotheek klonen

1. Kloon in een Terminal-of opdracht venster de volgende opslag plaats met git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navigeer naar de map en Bekijk de inhoud ervan.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *Start* is de werkmap voor de zelf studie.
    - *End* is het uiteindelijke resultaat en de volledige implementatie voor uw referentie.
    - *resources* bevatten het machine learning model en helper-bibliotheken.
    - Front- *End* is een website die de functie-app aanroept.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Een virtuele python-omgeving maken en activeren

Ga naar de map *Start* en voer de volgende opdrachten uit om een virtuele omgeving met de naam `.venv`te maken en te activeren. Zorg ervoor dat u python 3,7 gebruikt, die wordt ondersteund door Azure Functions.


# <a name="bashtabbash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Als python het venv-pakket niet heeft geïnstalleerd in uw Linux-distributie, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -m venv .venv
```

```cmd
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

    Met deze opdracht maakt u een map die overeenkomt met de naam van de functie, *classificeren*. In die map zijn twee bestanden: *\_\_init\_\_. py*, die de functie code en *Function. json*bevat, waarmee de trigger van de functie en de bijbehorende invoer-en uitvoer bindingen worden beschreven. Zie voor meer informatie over de inhoud van deze bestanden [een HTTP-geactiveerde python-functie in azure maken: Bekijk de inhoud van het bestand](functions-create-first-function-python.md#optional-examine-the-file-contents).


## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Start de functie door de lokale Azure Functions runtime host te starten in de map *Start* :

    ```
    func start
    ```
    
1. Zodra het `classify`-eind punt wordt weer gegeven in de uitvoer, gaat u naar de URL ```http://localhost:7071/api/classify?name=Azure```. Het bericht Hello Azure! moet worden weer gegeven in de uitvoer.

1. Gebruik **Ctrl**-**C** om de host te stoppen.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Het tensor flow-model importeren en helpercode toevoegen

Als u de functie `classify` wilt wijzigen om een afbeelding te classificeren op basis van de inhoud, gebruikt u een vooraf gebouwd tensor flow-model dat is getraind en dat is geëxporteerd uit Azure Custom Vision Service. Het model, dat zich bevindt in de map *resources* van het voor beeld dat u eerder hebt gekloond, classificeert een installatie kopie op basis van het feit of het een hond of kat bevat. U voegt vervolgens enkele hulp code en afhankelijkheden toe aan uw project.

> [!TIP]
> Als u uw eigen model wilt bouwen met behulp van de gratis laag van de Custom Vision Service, volgt u de instructies in de [project opslagplaats](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)van het voor beeld.

1. Voer in de map *Start* de volgende opdracht uit om de model bestanden te kopiëren naar de map *classificeren* . Zorg ervoor dat u `\*` in de opdracht opneemt. 

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Controleer of de map *classificeren* bestanden bevat met de naam *model. PB* en *labels. txt*. Als dat niet het geval is, controleert u of de opdracht in de map *Start* is uitgevoerd.

1. Voer in de map *Start* de volgende opdracht uit om een bestand met hulp code te kopiëren naar de map *classificeren* :

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Controleer of de map *classificeren* nu een bestand bevat met de naam *predict.py*.

1. Open *Start/requirements. txt* in een tekst editor en voeg de volgende afhankelijkheden toe die vereist zijn voor de Help-code:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Sla het bestand *Requirements. txt*.

1. Installeer de afhankelijkheden door de volgende opdracht uit te voeren in de map *Start* . De installatie kan enkele minuten duren, gedurende welke tijd u kunt door gaan met het wijzigen van de functie in de volgende sectie.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    In Windows kan de fout optreden ' kan pakketten niet installeren vanwege een EnvironmentError: [errno 2] bestand of map: ' gevolgd door een lange padnaam naar een bestand als *sharded_mutable_dense_hashtable. cpython-37. pyc*. Deze fout treedt meestal op omdat de diepte van het mappad te lang wordt. In dit geval moet u de register sleutel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` instellen op `1` om lange paden in te scha kelen. U kunt ook controleren waar uw Python-interpreter is geïnstalleerd. Als deze locatie een lang pad heeft, probeert u opnieuw te installeren in een map met een korter pad.

> [!TIP]
> Bij het aanroepen van *predict.py* om de eerste voor spelling te maken, laadt een functie met de naam `_initialize` het tensor flow-model van de schijf en slaat deze op in globale variabelen. Deze cache versnelt de volgende voor spellingen. Raadpleeg de [Azure functions python-ontwikkelaars handleiding](functions-reference-python.md#global-variables)voor meer informatie over het gebruik van globale variabelen.

## <a name="update-the-function-to-run-predictions"></a>De functie bijwerken voor het uitvoeren van voor spellingen

1. Open *classificatie/\_\_init\_\_. py* in een tekst editor en voeg de volgende regels toe na de bestaande `import`-instructies voor het importeren van de standaard-JSON-bibliotheek en de voor *spel* -helpers:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Vervang de volledige inhoud van de functie `main` door de volgende code:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Deze functie ontvangt een afbeeldings-URL in een query reeks parameter met de naam `img`. Vervolgens wordt `predict_image_from_url` aangeroepen vanuit de helper-bibliotheek om de installatie kopie te downloaden en classificeren met behulp van het tensor flow-model. De functie retourneert vervolgens een HTTP-antwoord met de resultaten. 

    > [!IMPORTANT]
    > Omdat dit HTTP-eind punt wordt aangeroepen door een webpagina die wordt gehost op een ander domein, bevat het antwoord een `Access-Control-Allow-Origin` header om te voldoen aan de vereisten voor CORS (cross-Origin Resource Sharing) van de browser.
    >
    > Wijzig in een productie toepassing `*` in de specifieke oorsprong van de webpagina voor extra beveiliging.

1. Sla de wijzigingen op en controleer of de installatie van afhankelijkheden is voltooid, start de lokale functie host opnieuw met `func start`. Zorg ervoor dat u de host in de map *Start* uitvoert, zodat de virtuele omgeving geactiveerd is. Anders wordt de host gestart, maar er worden fouten weer geven bij het aanroepen van de functie.

    ```
    func start
    ```

1. Open in een browser de volgende URL om de functie aan te roepen met de URL van een kat-afbeelding en te controleren of de geretourneerde JSON de afbeelding als een kat heeft geclassificeerd.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Zorg ervoor dat de host actief blijft omdat u deze in de volgende stap gebruikt. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Voer de front-end van de lokale web-app uit om de functie te testen

Voor het testen van het aanroepen van het functie-eind punt vanuit een andere web-app, is er een eenvoudige app in de map front- *End* van de opslag plaats.

1. Open een nieuwe terminal of opdracht prompt en activeer de virtuele omgeving (zoals eerder beschreven onder [Create and Activate a virtuele python-omgeving](#create-and-activate-a-python-virtual-environment)).

1. Ga naar de map front- *End* van de opslag plaats.

1. Een HTTP-server starten met python:

    # <a name="bashtabbash"></a>[bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmdtabcmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Ga in een browser naar `localhost:8000`en voer een van de volgende foto-Url's in het tekstvak in of gebruik de URL van een openbaar toegankelijke afbeelding.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Selecteer **verzenden** om het functie-eind punt aan te roepen voor het classificeren van de afbeelding.

    ![Scherm opname van voltooid project](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Als de browser een fout meldt bij het verzenden van de afbeeldings-URL, controleert u de Terminal waarin u de functie-app uitvoert. Als er een fout bericht wordt weer gegeven als ' geen module gevonden ' PIL ' ', hebt u de functie-app mogelijk gestart in de map *Start* zonder eerst de virtuele omgeving te activeren die u eerder hebt gemaakt. Als u nog steeds fouten ziet, voert u `pip install -r requirements.txt` opnieuw uit met de virtuele omgeving geactiveerd en zoekt u naar fouten.

> [!NOTE]
> Het model classificeert de inhoud van de afbeelding altijd als kat of hond, ongeacht of de afbeelding de standaard instelling hond bevat. Installatie kopieën van Tigers en Panthers worden bijvoorbeeld doorgaans geclassificeerd als kat, maar afbeeldingen van olifanten, carrots of vlieg tuigen worden als hond ingedeeld.

## <a name="clean-up-resources"></a>Resources opschonen

Omdat de meeste van deze zelf studie lokaal op uw computer wordt uitgevoerd, zijn er geen Azure-resources of-services om op te schonen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een HTTP API-eind punt kunt bouwen en aanpassen met Azure Functions om installatie kopieën te classificeren met behulp van een tensor flow-model. U hebt ook geleerd hoe u de API aanroept vanuit een web-app. U kunt de technieken in deze zelf studie gebruiken om Api's van elke complexiteit te maken, terwijl deze worden uitgevoerd op het serverloos reken model van Azure Functions.

> [!div class="nextstepaction"]
> [De functie implementeren naar Azure Functions met behulp van de Azure CLI-hand leiding](./functions-run-local.md#publish)

Zie ook:

- [Implementeer de functie in azure met behulp van Visual Studio code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Azure Functions python-ontwikkelaars handleiding](./functions-reference-python.md)

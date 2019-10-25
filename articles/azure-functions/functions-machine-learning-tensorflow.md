---
title: 'Zelf studie: gebruik python en tensor flow in Azure Functions om machine learning deverzekers | Microsoft Docs'
description: In deze zelf studie wordt gedemonstreerd hoe u tensor flow machine learning modellen toepast in Azure Functions
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: dbf2b4b5113598fee742c3864bede782a624773c
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817456"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Zelf studie: machine learning modellen Toep assen in Azure Functions met python en tensor flow

In dit artikel wordt beschreven hoe u met behulp van Azure Functions python en tensor flow kunt gebruiken met een machine learning model om een afbeelding te classificeren op basis van de inhoud ervan.

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Een lokale omgeving initialiseren voor het ontwikkelen van Azure Functions in python
> * Een aangepast tensor flow-machine learning model importeren in een functie-app
> * Een serverloze HTTP API maken voor het voors pellen of een foto een hond of een kat bevat
> * De API van een webtoepassing gebruiken

![Scherm opname van voltooid project](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten 

Als u Azure Functions in python wilt maken, moet u een paar hulpprogram ma's installeren.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>De zelfstudie bibliotheek klonen

Als u wilt beginnen, opent u een Terminal en kloont u de volgende opslag plaats met git:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

De opslag plaats bevat een paar mappen.

- *Start*: dit is de werkmap voor de zelf studie
- *End*: dit is het uiteindelijke resultaat en de volledige implementatie voor uw referentie
- *resources*: bevat het machine learning model en de helper-bibliotheken
- Front- *End*: een website die de functie-app aanroept

## <a name="create-and-activate-a-python-virtual-environment"></a>Een virtuele python-omgeving maken en activeren

Voor Azure Functions is python 3.6. x vereist. U maakt een virtuele omgeving om ervoor te zorgen dat u gebruikmaakt van de vereiste python-versie.

Wijzig de huidige werkmap in de map *Start* . Maak en activeer een virtuele omgeving met de naam *. venv*. Afhankelijk van uw python-installatie kunnen de opdrachten voor het maken van een virtuele python 3,6-omgeving afwijken van de volgende instructies.

#### <a name="linux-and-macos"></a>Linux en macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

De terminal prompt wordt nu voorafgegaan door `(.venv)`, wat aangeeft dat de virtuele omgeving is geactiveerd. Bevestig dat `python` in de virtuele omgeving inderdaad python 3.6. x.

```console
python --version
```

> [!NOTE]
> Voor de rest van de zelf studie voert u opdrachten uit in de virtuele omgeving. Als u de virtuele omgeving in een Terminal opnieuw moet activeren, voert u de juiste opdracht Activate uit voor uw besturings systeem.

## <a name="create-an-azure-functions-project"></a>Een Azure Functions-project maken

Gebruik in de map *Start* de Azure functions core tools een python-functie-app te initialiseren.

```console
func init --worker-runtime python
```

Een functie-app kan een of meer Azure Functions bevatten. Open de map *Start* in een editor en controleer de inhoud.

- [*Local. settings. json*](functions-run-local.md#local-settings-file): bevat toepassings instellingen die worden gebruikt voor lokale ontwikkeling
- [*host. json*](functions-host-json.md): bevat instellingen voor de Azure functions-host en-extensies
- [*Requirements. txt*](functions-reference-python.md#python-version-and-package-management): bevat Python-pakketten die vereist zijn voor deze toepassing

## <a name="create-an-http-function"></a>Een HTTP-functie maken

Voor de toepassing is één HTTP API-eind punt vereist dat een afbeeldings-URL gebruikt als invoer en een voor spelling retourneert van het feit of de afbeelding een hond of een kat bevat.

Gebruik in de Terminal de Azure Functions Core Tools om een nieuwe HTTP-functie met de naam *classificeren*te gebruiken.

```console
func new --language python --template HttpTrigger --name classify
```

Er wordt een nieuwe map met de naam *classificering* gemaakt, die twee bestanden bevat.

- *\_\_init\_\_. py*: een bestand voor de hoofd functie
- *Function. json*: een bestand met een beschrijving van de trigger van de functie en de bijbehorende invoer-en uitvoer bindingen

### <a name="run-the-function"></a>De functie uitvoeren

Start in de Terminal met de python virtuele omgeving geactiveerd de functie-app.

```console
func start
```

Open een browser en ga naar de volgende URL. De functie moet worden uitgevoerd en *Hello Azure* wordt geretourneerd.

```
http://localhost:7071/api/classify?name=Azure
```

Gebruik `Ctrl-C` om de functie-app te stoppen.

## <a name="import-the-tensorflow-model"></a>Het tensor flow-model importeren

U gebruikt een vooraf gemaakt tensor flow-model dat is getraind en dat is geëxporteerd uit Azure Custom Vision Service.

> [!NOTE]
> Als u uw eigen wilt maken met behulp van de gratis laag van Custom Vision Service, kunt u de [instructies in de project opslagplaats van het voor beeld](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)volgen.

Het model bestaat uit twee bestanden in de map *< REPOSITORY_ROOT >/resources/model* : *model. PB* en *labels. txt*. Kopieer deze naar de map van de functie *classificeren* .

#### <a name="linux-and-macos"></a>Linux en macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Zorg ervoor dat u de \* in de bovenstaande opdracht opneemt. Controleer of *classificeren* nu bestanden bevat met de naam *model. PB* en *labels. txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>De Help-functies en-afhankelijkheden toevoegen

Sommige hulp functies voor het voorbereiden van de invoer installatie kopie en het maken van een voor spelling met behulp van tensor flow bevinden zich in een bestand met de naam *predict.py* in de map *resources* . Kopieer dit bestand naar de map van de functie *classificeren* .

#### <a name="linux-and-macos"></a>Linux en macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Controleer of *classificeren* nu een bestand met de naam *predict.py*bevat.

### <a name="install-dependencies"></a>Afhankelijkheden installeren

De helper-bibliotheek heeft enkele afhankelijkheden die moeten worden geïnstalleerd. Open *Start/requirements. txt* in de editor en voeg de volgende afhankelijkheden toe aan het bestand.

```txt
tensorflow==1.14
Pillow
requests
```

Sla het bestand op.

In de Terminal met de virtuele omgeving geactiveerd, voert u de volgende opdracht uit in de map *Start* om de afhankelijkheden te installeren. Het kan enkele minuten duren voordat bepaalde installatie stappen zijn voltooid.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Het model in de cache opslaan in globale variabelen

Open *predict.py* in de editor en Bekijk de functie `_initialize` boven aan het bestand. U ziet dat het tensor flow-model van de schijf wordt geladen wanneer de functie voor het eerst wordt uitgevoerd en wordt opgeslagen in globale variabelen. Het laden vanaf schijf wordt overgeslagen tijdens volgende uitvoeringen van de functie `_initialize`. Als u het model in het geheugen opslaat met deze techniek, versnelt u later de voor spellingen.

Raadpleeg de [Azure functions python-ontwikkelaars handleiding](functions-reference-python.md#global-variables)voor meer informatie over globale variabelen.

## <a name="update-function-to-run-predictions"></a>De functie update voor het uitvoeren van voor spellingen

Open *classificatie/\_\_init\_\_. py* in de editor. Importeer de bibliotheek voor voors *pellen* die u eerder hebt toegevoegd aan dezelfde map. Voeg de volgende `import`-instructies toe onder de andere invoer die al in het bestand voor komt.

```python
import json
from .predict import predict_image_from_url
```

Vervang de functie sjabloon code door het volgende.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Zorg ervoor dat u de wijzigingen opslaat.

Deze functie ontvangt een afbeeldings-URL in een query reeks parameter met de naam `img`. Er wordt `predict_image_from_url` aangeroepen vanuit de helper-bibliotheek die de installatie kopie downloadt en een voor spelling retourneert met behulp van het tensor flow-model. De functie retourneert vervolgens een HTTP-antwoord met de resultaten.

Omdat het HTTP-eind punt wordt aangeroepen door een webpagina die wordt gehost op een ander domein, bevat het HTTP-antwoord een `Access-Control-Allow-Origin`-header om te voldoen aan de vereisten voor de toepassing van het gebruik van meerdere resources (CORS) van de browser.

> [!NOTE]
> Wijzig in een productie toepassing `*` in de specifieke oorsprong van de webpagina voor extra beveiliging.

### <a name="run-the-function-app"></a>De functie-app uitvoeren

Zorg ervoor dat de virtuele python-omgeving nog steeds geactiveerd is en start de functie-app met behulp van de volgende opdracht.

```console
func start
```

Open in een browser deze URL die uw functie aanroept met de URL van een kat foto. Bevestig dat er een geldig Voorspellings resultaat wordt geretourneerd.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Zorg ervoor dat de functie-app wordt uitgevoerd.

### <a name="run-the-web-app"></a>De web-app uitvoeren

Er is een eenvoudige web-app in de map front- *End* waarin de HTTP-API in de functie-app wordt gebruikt.

Open een *afzonderlijke* Terminal en ga naar de map front- *End* . Start een HTTP-server met python 3,6.

#### <a name="linux-and-macos"></a>Linux en macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

Navigeer in een browser naar de URL van de HTTP-server die wordt weer gegeven in de Terminal. Er moet een web-app worden weer gegeven. Voer een van de volgende foto-Url's in het tekstvak in. U kunt ook een URL gebruiken van een openbaar toegankelijke kat of honden foto.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

Wanneer u op verzenden klikt, wordt de functie-app aangeroepen en wordt er een resultaat op de pagina weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen
Het geheel van deze zelf studie wordt lokaal op uw computer uitgevoerd, zodat er geen Azure-resources of-services zijn om op te schonen.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een HTTP-API kunt bouwen en aanpassen met Azure Functions om voor spellingen te maken met behulp van een tensor flow-model. U hebt ook geleerd hoe u de API aanroept vanuit een webtoepassing.

U kunt de technieken in deze zelf studie gebruiken om Api's van elke complexiteit te maken, terwijl deze worden uitgevoerd op het serverloos reken model van Azure Functions.

Als u de functie-app naar Azure wilt implementeren, gebruikt u de [Azure functions core tools](./functions-run-local.md#publish) of [Visual Studio code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).

> [!div class="nextstepaction"]
> [Azure Functions python-ontwikkelaars handleiding](./functions-reference-python.md)

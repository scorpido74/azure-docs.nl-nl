---
title: Python-ontwikkelaars referentie voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 49577f5ac274b4e34fa07415e5495329ff650aa5
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83676190"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions python-ontwikkelaars handleiding

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions met behulp van python. De onderstaande inhoud veronderstelt dat u de [hand leiding voor de Azure functions-ontwikkel aars](functions-reference.md)al hebt gelezen.

Zie de voor [beelden van python-functies](/samples/browse/?products=azure-functions&languages=python)voor voorbeeld projecten van een zelfstandige functie in python.

## <a name="programming-model"></a>Programmeermodel

Azure Functions verwacht dat een functie een stateless methode is in uw python-script dat invoer verwerkt en uitvoer produceert. Standaard verwacht de runtime dat de methode wordt geïmplementeerd als globale methode met `main()` de naam in het `__init__.py` bestand. U kunt ook [een alternatief invoer punt opgeven](#alternate-entry-point).

Gegevens van triggers en bindingen zijn gekoppeld aan de functie via methoden Attributes met behulp van de `name` eigenschap die in het bestand *Function. json* is gedefinieerd. Met de _functie. json_ hieronder wordt bijvoorbeeld een eenvoudige functie beschreven die wordt geactiveerd door een HTTP-aanvraag met de naam `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Op basis van deze definitie `__init__.py` ziet het bestand dat de functie code bevat, eruit als in het volgende voor beeld:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

U kunt ook expliciet de kenmerk typen en het retour type declareren in de functie met behulp van python-type aantekeningen. Dit helpt u bij het gebruik van de functies IntelliSense en automatisch aanvullen van vele Python-code-editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Gebruik de python-aantekeningen in het pakket [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python) om invoer en uitvoer aan uw methoden te koppelen.

## <a name="alternate-entry-point"></a>Alternatief invoer punt

U kunt het standaard gedrag van een functie wijzigen door optioneel de `scriptFile` `entryPoint` Eigenschappen en in het bestand *Function. json* op te geven. Met de _functie. json_ hieronder wordt bijvoorbeeld aangegeven dat de runtime de `customentry()` methode in het _Main.py_ -bestand moet gebruiken als het invoer punt voor uw Azure-functie.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Mapstructuur

De aanbevolen mapstructuur voor een python functions-project ziet eruit als in het volgende voor beeld:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 | - Dockerfile
 tests
```
De hoofdmap van het project ( \_ \_ app \_ \_ ) kan de volgende bestanden bevatten:

* *Local. settings. json*: wordt gebruikt voor het opslaan van app-instellingen en verbindings reeksen bij het lokaal uitvoeren. Dit bestand wordt niet gepubliceerd naar Azure. Zie [Local. settings. File](functions-run-local.md#local-settings-file)voor meer informatie.
* *Requirements. txt*: bevat de lijst met pakketten die door het systeem worden geïnstalleerd bij het publiceren naar Azure.
* *host. json*: bevat globale configuratie opties die van invloed zijn op alle functies in een functie-app. Dit bestand wordt gepubliceerd naar Azure. Niet alle opties worden ondersteund bij het lokaal uitvoeren. Zie [host. json](functions-host-json.md)voor meer informatie.
* *. funcignore*: (optioneel) declareert bestanden die niet naar Azure mogen worden gepubliceerd.
* *. gitignore*: (optioneel) declareert bestanden die zijn uitgesloten van een Git-opslag plaats, zoals local. settings. json.
* *Dockerfile*: (optioneel) gebruikt bij het publiceren van uw project in een [aangepaste container](functions-create-function-linux-custom-image.md).

Elke functie heeft een eigen code bestand en een bindings configuratie bestand (function. json).

Wanneer u uw project implementeert in een functie-app in azure, moet de volledige inhoud van de map main project (* \_ \_ app \_ \_ *) worden opgenomen in het pakket, maar niet in de map zelf. U wordt aangeraden uw tests te onderhouden in een map gescheiden van de projectmap, in dit voor beeld `tests` . Hierdoor kunt u geen test code implementeren met uw app. Zie [unit testen](#unit-testing)voor meer informatie.

## <a name="import-behavior"></a>Gedrag bij importeren

U kunt modules in uw functie code importeren met behulp van zowel expliciete relatieve als absolute verwijzingen. Op basis van de mappen structuur die hierboven wordt weer gegeven, werkt de volgende import vanuit de functie bestand * \_ \_ app \_ \_ \Mijn de \_ eerste \_ functie \\ _ \_ init \_ \_ . py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

De volgende Imports *werken niet* binnen hetzelfde bestand:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Gedeelde code moet worden bewaard in een afzonderlijke map in de * \_ \_ app \_ \_ *. U kunt met behulp van de volgende syntaxis verwijzen naar modules in de map *gedeelde \_ code* :

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Triggers en invoer

Invoer wordt onderverdeeld in twee categorieën in Azure Functions: invoer van trigger en aanvullende invoer. Hoewel ze verschillen in het `function.json` bestand, is het gebruik identiek in de python-code.  Verbindings reeksen of geheimen voor trigger-en invoer bronnen worden toegewezen aan waarden in het `local.settings.json` bestand tijdens het uitvoeren van lokaal en de toepassings instellingen wanneer ze worden uitgevoerd in Azure.

De volgende code toont bijvoorbeeld het verschil tussen de twee:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Wanneer de functie wordt aangeroepen, wordt de HTTP-aanvraag door gegeven aan de functie als `req` . Er wordt een item opgehaald uit de Azure-Blob Storage op basis van de id in de route _-_ URL en beschikbaar gemaakt als `obj` in de hoofd tekst van de functie.  Hier is het opgegeven opslag account het connection string gevonden in de app-instelling AzureWebJobsStorage. Dit is hetzelfde opslag account dat wordt gebruikt door de functie-app.


## <a name="outputs"></a>Uitvoer

Output kan worden uitgedrukt in retour waarde en uitvoer parameters. Als er slechts één uitvoer is, raden we u aan de retour waarde te gebruiken. Voor meerdere uitvoer moet u uitvoer parameters gebruiken.

Als u de retour waarde van een functie als de waarde van een uitvoer binding wilt gebruiken, moet u de `name` eigenschap van de binding instellen op `$return` in `function.json` .

Als u meerdere uitvoer wilt maken, gebruikt u de `set()` methode van de [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interface om een waarde toe te wijzen aan de binding. Met de volgende functie kan bijvoorbeeld een bericht naar een wachtrij worden gepusht en wordt ook een HTTP-antwoord geretourneerd.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Logboekregistratie

Toegang tot de runtime logger van Azure Functions is beschikbaar via een basis- [`logging`](https://docs.python.org/3/library/logging.html#module-logging) handler in uw functie-app. Deze logboek registratie is gekoppeld aan Application Insights en stelt u in staat om waarschuwingen en fouten te markeren die tijdens de uitvoering van de functie zijn aangetroffen.

In het volgende voor beeld wordt een info bericht geregistreerd wanneer de functie wordt aangeroepen via een HTTP-trigger.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Er zijn aanvullende logboek registratie methoden beschikbaar waarmee u naar de-console kunt schrijven op verschillende tracerings niveaus:

| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Schrijft een bericht met niveau kritiek op de hoofd logboek registratie.  |
| **`error(_message_)`**   | Schrijft een bericht met een niveau fout in de hoofd logboek registratie.    |
| **`warning(_message_)`**    | Schrijft een bericht met niveau waarschuwing in de hoofd logboek registratie.  |
| **`info(_message_)`**    | Schrijft een bericht met niveau-informatie in de hoofd logboek registratie.  |
| **`debug(_message_)`** | Schrijft een bericht met niveau DEBUG op de hoofd logboek registratie.  |

Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie over logboek registratie.

## <a name="http-trigger-and-bindings"></a>HTTP-trigger en bindingen

De HTTP-trigger wordt gedefinieerd in het bestand function. Jon. De `name` van de binding moet overeenkomen met de benoemde para meter in de functie.
In de vorige voor beelden wordt een bindings naam `req` gebruikt. Deze para meter is een [HttpRequest] -object en er wordt een [HttpResponse] -object geretourneerd.

Vanuit het object [HttpRequest] kunt u aanvraag headers, query parameters, route parameters en de bericht tekst ophalen.

Het volgende voor beeld is afkomstig uit de [sjabloon voor http-triggers voor python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python).

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

In deze functie wordt de waarde van de `name` query parameter opgehaald uit de `params` para meter van het [HttpRequest] -object. De hoofd tekst van de JSON-code ring wordt gelezen met behulp van de `get_json` methode.

U kunt ook de `status_code` en `headers` voor het antwoord bericht instellen in het geretourneerde [HttpResponse] -object.

## <a name="scaling-and-concurrency"></a>Schalen en gelijktijdigheid

Standaard controleert Azure Functions automatisch de belasting van uw toepassing en worden er indien nodig extra exemplaren van de host voor python gemaakt. Functies gebruiken ingebouwde (niet door de gebruiker te configureren) drempel waarden voor verschillende trigger typen om te bepalen wanneer instanties moeten worden toegevoegd, zoals de leeftijd van berichten en de grootte van de wachtrij voor Queue trigger. Zie [hoe het verbruik en de Premium-abonnementen werken](functions-scale.md#how-the-consumption-and-premium-plans-work)voor meer informatie.

Dit gedrag voor schalen is voldoende voor veel toepassingen. Toepassingen met een van de volgende kenmerken kunnen echter niet zo effectief worden geschaald:

- De toepassing moet veel gelijktijdige aanroepen verwerken.
- De toepassing verwerkt een groot aantal I/O-gebeurtenissen.
- De toepassing is I/O-gebonden.

In dergelijke gevallen kunt u de prestaties verder verbeteren door gebruik te maken van async-patronen en werk processen in meerdere talen te gebruiken.

### <a name="async"></a>Asynchroon

Omdat python een single-threaded runtime is, kan een host-exemplaar voor python slechts één functie aanroep tegelijk verwerken. Voor toepassingen die een groot aantal I/O-gebeurtenissen verwerken en/of I/O-gebonden zijn, kunt u de prestaties verbeteren door functions asynchroon uit te voeren.

Als u een functie asynchroon wilt uitvoeren, gebruikt u de `async def` -instructie, waarmee de functie rechtstreeks wordt uitgevoerd met [asyncio](https://docs.python.org/3/library/asyncio.html) :

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Een functie zonder `async` sleutel woord wordt automatisch uitgevoerd in een asyncio-thread groep:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Werk processen in meerdere talen gebruiken

Elk functions-exemplaar heeft standaard een werk proces met één taal. U kunt het aantal werk processen per host (Maxi maal 10) verhogen met behulp van de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassings instelling. Azure Functions probeert vervolgens gelijktijdige functie aanroepen voor deze werk nemers gelijkmatig te verdelen.

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die functies maakt wanneer uw toepassing wordt geschaald om aan de vraag te voldoen.

## <a name="context"></a>Context

Als u de aanroep context van een functie tijdens de uitvoering wilt ophalen, neemt u het [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) argument op in de hand tekening.

Bijvoorbeeld:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

De [**context**](/python/api/azure-functions/azure.functions.context?view=azure-python) klasse heeft de volgende teken reeks kenmerken:

`function_directory`De map waarin de functie wordt uitgevoerd.

`function_name`De naam van de functie.

`invocation_id`ID van de huidige functie aanroep.

## <a name="global-variables"></a>Globale variabelen

Het is niet zeker dat de status van uw app wordt bewaard voor toekomstige uitvoeringen. De Azure Functions runtime gebruikt echter vaak hetzelfde proces voor meerdere uitvoeringen van dezelfde app. Als u de resultaten van een dure berekening in de cache wilt opslaan, declareert u deze als een globale variabele.

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Omgevingsvariabelen

In functions worden [Toepassings instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen door te declareren `import os` en vervolgens te gebruiken `setting = os.environ["setting-name"]` .

In het volgende voor beeld wordt de [toepassings instelling](functions-how-to-use-azure-function-app-settings.md#settings)opgehaald met de sleutel met de naam `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Voor lokale ontwikkeling worden toepassings instellingen [onderhouden in het bestand local. settings. json](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Python-versie

Azure Functions ondersteunt de volgende python-versies:

| Functie versie | Python- <sup>*</sup> versies |
| ----- | ----- |
| controleert | 3,8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Officiële CPython-distributies

Als u een specifieke python-versie wilt aanvragen wanneer u de functie-app in azure maakt, gebruikt u de `--runtime-version` optie van de [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) opdracht. De runtime versie van functions wordt ingesteld met behulp van de `--functions-version` optie. De python-versie wordt ingesteld wanneer de functie-app wordt gemaakt en kan niet worden gewijzigd.

Wanneer lokaal wordt uitgevoerd, gebruikt de runtime de beschik bare python-versie.

## <a name="package-management"></a>Pakketbeheer

Wanneer u lokaal ontwikkelt met behulp van de Azure Functions Core Tools of Visual Studio code, voegt u de namen en versies van de vereiste pakketten toe aan het `requirements.txt` bestand en installeert u deze met behulp van `pip` .

De volgende vereisten bestand en PIP-opdracht kunnen bijvoorbeeld worden gebruikt om het pakket te installeren `requests` vanuit PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publiceren naar Azure

Wanneer u klaar bent om te publiceren, moet u ervoor zorgen dat alle openbaar beschik bare afhankelijkheden worden vermeld in het bestand requirements. txt, dat zich in de hoofdmap van de projectmap bevindt.

Project bestanden en-mappen die zijn uitgesloten van publiceren, met inbegrip van de map virtuele omgeving, worden weer gegeven in het funcignore-bestand.

Er zijn drie build-acties die worden ondersteund voor het publiceren van uw python-project naar Azure:

+ Externe build: afhankelijkheden worden op afstand opgehaald op basis van de inhoud van het bestand requirements. txt. [Externe build](functions-deployment-technologies.md#remote-build) is de aanbevolen methode build. Extern is ook de standaard optie voor het bouwen van Azure-hulpprogram ma's.
+ Lokale build: afhankelijkheden worden lokaal opgehaald op basis van de inhoud van het bestand requirements. txt.
+ Aangepaste afhankelijkheden: in uw project worden pakketten gebruikt die niet openbaar beschikbaar zijn voor onze tools. (Hiervoor is docker vereist.)

Als u uw afhankelijkheden wilt maken en publiceren met behulp van een systeem voor continue levering (CD), [gebruikt u Azure-pijp lijnen](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Externe build

Standaard vraagt de Azure Functions Core Tools een externe build aan wanneer u de volgende [func Azure functionapp Publish](functions-run-local.md#publish) -opdracht gebruikt om uw python-project naar Azure te publiceren.

```bash
func azure functionapp publish <APP_NAME>
```

Vervang door `<APP_NAME>` de naam van uw functie-app in Azure.

Met de [extensie Azure functions voor Visual Studio code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) wordt ook standaard een externe build aangevraagd.

### <a name="local-build"></a>Lokale build

U kunt voor komen dat een externe build wordt gemaakt met behulp van de volgende [func Azure functionapp Publish](functions-run-local.md#publish) -opdracht om te publiceren met een lokale build.

```command
func azure functionapp publish <APP_NAME> --build local
```

Vervang door `<APP_NAME>` de naam van uw functie-app in Azure.

Met de `--build local` optie worden Project afhankelijkheden uit het bestand requirements. txt gelezen en worden de afhankelijke pakketten lokaal gedownload en geïnstalleerd. Project bestanden en afhankelijkheden worden geïmplementeerd vanaf uw lokale computer naar Azure. Dit leidt ertoe dat een groter implementatie pakket wordt geüpload naar Azure. Als er om de een of andere reden geen afhankelijkheden van het bestand requirements. txt kunnen worden verkregen met de kern Hulpprogramma's, moet u de optie aangepaste afhankelijkheden gebruiken voor het publiceren.

### <a name="custom-dependencies"></a>Aangepaste afhankelijkheden

Als uw project pakketten gebruikt die niet openbaar beschikbaar zijn voor onze tools, kunt u ze beschikbaar maken voor uw app door ze te plaatsen in de \_ \_ \_ \_ map app/. python_packages. Voordat u publiceert, voert u de volgende opdracht uit om de afhankelijkheden lokaal te installeren:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Wanneer u aangepaste afhankelijkheden gebruikt, moet u de `--no-build` optie voor publiceren gebruiken omdat u de afhankelijkheden al hebt geïnstalleerd.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Vervang door `<APP_NAME>` de naam van uw functie-app in Azure.

## <a name="unit-testing"></a>Eenheids tests

Functies die zijn geschreven in python kunnen worden getest als andere python-code met behulp van standaard test raamwerken. Voor de meeste bindingen is het mogelijk om een invoer object voor een model te maken door een instantie van een geschikte klasse te maken vanuit het `azure.functions` pakket. Omdat het [`azure.functions`](https://pypi.org/project/azure-functions/) pakket niet onmiddellijk beschikbaar is, moet u het installeren via uw `requirements.txt` bestand zoals beschreven in de sectie [pakket beheer](#package-management) hierboven.

Het volgende is bijvoorbeeld een model test van een door HTTP geactiveerde functie:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Hier volgt nog een voor beeld van een door de wachtrij geactiveerde functie:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>Tijdelijke bestanden

De- `tempfile.gettempdir()` methode retourneert een tijdelijke map, die op Linux is `/tmp` . Uw toepassing kan deze directory gebruiken voor het opslaan van tijdelijke bestanden die door uw functies worden gegenereerd en gebruikt tijdens de uitvoering.

> [!IMPORTANT]
> Bestanden die naar de tijdelijke map worden geschreven, kunnen niet worden gegarandeerd in aanroepen. Tijdens uitschalen worden tijdelijke bestanden niet gedeeld tussen exemplaren.

In het volgende voor beeld wordt een tijdelijk bestand met de naam gemaakt in de tijdelijke map ( `/tmp` ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

U wordt aangeraden uw tests te onderhouden in een map gescheiden van de projectmap. Hierdoor kunt u geen test code implementeren met uw app.

## <a name="cross-origin-resource-sharing"></a>Cross-origin-resources delen

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS wordt volledig ondersteund voor python-functie-apps.

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

Dankzij uw waardevolle feedback kunnen we een lijst met richt lijnen voor probleem oplossing voor veelvoorkomende problemen bijhouden:

* [ModuleNotFoundError en ImportError](recover-module-not-found.md)

Alle bekende problemen en functie aanvragen worden bijgehouden met de lijst met [github-problemen](https://github.com/Azure/azure-functions-python-worker/issues) . Als u een probleem ondervindt en u het probleem niet kunt vinden in GitHub, opent u een nieuw probleem en voegt u een gedetailleerde beschrijving van het probleem toe.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Documentatie over de API voor Azure Functions-pakketten](/python/api/azure-functions/azure.functions?view=azure-python)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* [Blob Storage-bindingen](functions-bindings-storage-blob.md)
* [HTTP-en webhook-bindingen](functions-bindings-http-webhook.md)
* [Wachtrij opslag bindingen](functions-bindings-storage-queue.md)
* [Timertrigger](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python

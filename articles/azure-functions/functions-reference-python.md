---
title: Python-ontwikkelaars referentie voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, functies, gebeurtenis verwerking, dynamische compute, serverloze architectuur, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 01d8560ee2752f21eb52c00f4c337d1dca59b8fb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082704"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions python-ontwikkelaars handleiding

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions met behulp van python. De onderstaande inhoud veronderstelt dat u de [hand leiding voor de Azure functions-ontwikkel aars](functions-reference.md)al hebt gelezen. 

Zie de voor [beelden van python-functies](/samples/browse/?products=azure-functions&languages=python)voor voorbeeld projecten van een zelfstandige functie in python. 

## <a name="programming-model"></a>Programmeermodel

Azure Functions verwacht dat een functie een stateless methode is in uw python-script dat invoer verwerkt en uitvoer produceert. Standaard verwacht de runtime dat de methode wordt geïmplementeerd als globale methode met de naam `main()` in het `__init__.py` bestand. U kunt ook [een alternatief invoer punt opgeven](#alternate-entry-point).

Gegevens van triggers en bindingen zijn gekoppeld aan de functie via methoden Attributes met behulp van de eigenschap `name` die in het bestand *Function. json* is gedefinieerd. Met de _functie. json_ hieronder wordt bijvoorbeeld een eenvoudige functie beschreven die wordt geactiveerd door een HTTP-aanvraag met de naam `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

Het `__init__.py` bestand bevat de volgende functie code:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

u kunt ook expliciet de kenmerk typen en het retour type declareren in de functie met behulp van python-type aantekeningen. Dit helpt u bij het gebruik van de functies IntelliSense en automatisch aanvullen van vele Python-code-editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Gebruik de python-aantekeningen in het pakket [Azure. functions. *](/python/api/azure-functions/azure.functions?view=azure-python) om invoer en uitvoer aan uw methoden te koppelen.

## <a name="alternate-entry-point"></a>Alternatief invoer punt

U kunt het standaard gedrag van een functie wijzigen door optioneel de eigenschappen `scriptFile` en `entryPoint` op te geven in het bestand *Function. json* . Met de _functie. json_ hieronder wordt bijvoorbeeld aangegeven dat de runtime de `customentry()` methode in het _Main.py_ -bestand moet gebruiken als het toegangs punt voor uw Azure-functie.

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
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
De hoofdmap van het project (\_\_app\_\_) kan de volgende bestanden bevatten:

* *Local. settings. json*: wordt gebruikt voor het opslaan van app-instellingen en verbindings reeksen bij het lokaal uitvoeren. Dit bestand wordt niet gepubliceerd naar Azure. Zie [Local. settings. File](functions-run-local.md#local-settings-file)voor meer informatie.
* *Requirements. txt*: bevat de lijst met pakketten die door het systeem worden geïnstalleerd bij het publiceren naar Azure.
* *host. json*: bevat globale configuratie opties die van invloed zijn op alle functies in een functie-app. Dit bestand wordt gepubliceerd naar Azure. Niet alle opties worden ondersteund bij het lokaal uitvoeren. Zie [host. json](functions-host-json.md)voor meer informatie.
* *funcignore*: (optioneel) declareert bestanden die niet naar Azure mogen worden gepubliceerd.
* *gitignore*: (optioneel) declareert bestanden die zijn uitgesloten van een Git-opslag plaats, zoals local. settings. json.

Elke functie heeft een eigen code bestand en een bindings configuratie bestand (function. json). 

Gedeelde code moet worden bewaard in een afzonderlijke map in \_\_app\_\_. Als u wilt verwijzen naar modules in de map SharedCode, kunt u de volgende syntaxis gebruiken:

```python
from __app__.SharedCode import myFirstHelperFunction
```

Als u verwijst naar modules die lokaal zijn voor een functie, kunt u de relatieve import syntaxis als volgt gebruiken:

```python
from . import example
```

Wanneer u uw project implementeert in een functie-app in azure, moet de volledige inhoud van de map *FunctionApp* worden opgenomen in het pakket, maar niet in de map zelf. U wordt aangeraden uw tests te onderhouden in een map gescheiden van de projectmap, in dit voor beeld `tests`. Hierdoor kunt u geen test code implementeren met uw app. Zie [unit testen](#unit-testing)voor meer informatie.

## <a name="triggers-and-inputs"></a>Triggers en invoer

Invoer wordt onderverdeeld in twee categorieën in Azure Functions: invoer van trigger en aanvullende invoer. Hoewel ze verschillen in het `function.json`-bestand, is het gebruik identiek in de python-code.  Verbindings reeksen of geheimen voor trigger-en invoer bronnen worden toegewezen aan waarden in het `local.settings.json`-bestand wanneer het lokaal wordt uitgevoerd, en de toepassings instellingen wanneer ze worden uitgevoerd in Azure. 

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

Wanneer de functie wordt aangeroepen, wordt de HTTP-aanvraag door gegeven aan de functie als `req`. Een item wordt opgehaald uit de Azure-Blob Storage op basis van de _id_ in de route-URL en beschikbaar gemaakt als `obj` in de hoofd tekst van de functie.  Hier is het opgegeven opslag account het connection string gevonden in. Dit is hetzelfde opslag account dat wordt gebruikt door de functie-app.


## <a name="outputs"></a>Uitvoer

Output kan worden uitgedrukt in retour waarde en uitvoer parameters. Als er slechts één uitvoer is, raden we u aan de retour waarde te gebruiken. Voor meerdere uitvoer moet u uitvoer parameters gebruiken.

Als u de retour waarde van een functie wilt gebruiken als de waarde van een uitvoer binding, moet de eigenschap `name` van de binding worden ingesteld op `$return` in `function.json`.

Als u meerdere uitvoer wilt maken, gebruikt u de `set()` methode van de [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) -interface om een waarde toe te wijzen aan de binding. Met de volgende functie kan bijvoorbeeld een bericht naar een wachtrij worden gepusht en wordt ook een HTTP-antwoord geretourneerd.

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

Toegang tot de runtime logger van Azure Functions is beschikbaar via een root [`logging`](https://docs.python.org/3/library/logging.html#module-logging) -handler in uw functie-app. Deze logboek registratie is gekoppeld aan Application Insights en stelt u in staat om waarschuwingen en fouten te markeren die tijdens de uitvoering van de functie zijn aangetroffen.

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

De HTTP-trigger wordt gedefinieerd in het bestand function. Jon. De `name` van de binding moet overeenkomen met de benoemde para meter in de functie. In de vorige voor beelden wordt een bindings naam `req` gebruikt. Deze para meter is een [HttpRequest] -object en er wordt een [HttpResponse] -object geretourneerd.

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

In deze functie wordt de waarde van de `name` query parameter opgehaald uit de para meter `params` van het [HttpRequest] -object. De hoofd tekst van de JSON-code ring wordt gelezen met behulp van de `get_json` methode. 

Op dezelfde manier kunt u de `status_code` en `headers` instellen voor het antwoord bericht in het geretourneerde [HttpResponse] -object.

## <a name="concurrency"></a>Gelijktijdigheid

De functies python runtime kunnen standaard slechts één aanroep van een functie tegelijk verwerken. Dit gelijktijdigheids niveau kan niet toereikend zijn onder een of meer van de volgende voor waarden:

+ U probeert een aantal aanroepen op hetzelfde moment te verwerken.
+ U verwerkt een groot aantal I/O-gebeurtenissen.
+ Uw toepassing is I/O-gebonden.

In deze situaties kunt u de prestaties verbeteren door asynchroon en door gebruik te maken van werk processen in meerdere talen.  

### <a name="async"></a>Asynchroon

U wordt aangeraden de `async def`-instructie te gebruiken om de functie uit te voeren als een asynchrone coroutine.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Wanneer de functie `main()` synchroon is (zonder de `async` kwalificatie), wordt de functie automatisch uitgevoerd in een `asyncio` thread-pool.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Werk processen in meerdere talen gebruiken

Elk functions-exemplaar heeft standaard een werk proces met één taal. Er is echter wel ondersteuning voor meerdere taal werk processen per exemplaar van de host. Functie aanroepen kunnen vervolgens gelijkmatig worden verdeeld over deze werk processen van de taal. Gebruik de instelling [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassing om deze waarde te wijzigen. 

## <a name="context"></a>Context

Als u de aanroep context van een functie tijdens de uitvoering wilt ophalen, neemt u het argument [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) op in de hand tekening. 

Bijvoorbeeld:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

De [**context**](/python/api/azure-functions/azure.functions.context?view=azure-python) klasse heeft de volgende teken reeks kenmerken:

`function_directory`  
De map waarin de functie wordt uitgevoerd.

`function_name`  
De naam van de functie.

`invocation_id`  
ID van de huidige functie aanroep.

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

In functions worden [Toepassings instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen door `import os` te declareren en vervolgens `setting = os.environ["setting-name"]`te gebruiken.

In het volgende voor beeld wordt de [toepassings instelling](functions-how-to-use-azure-function-app-settings.md#settings)opgehaald met de sleutel met de naam `myAppSetting`:

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

Momenteel ondersteunt Azure Functions zowel python 3.6. x als 3.7. x (officiële CPython-distributies). Wanneer lokaal wordt uitgevoerd, gebruikt de runtime de beschik bare python-versie. Als u een specifieke python-versie wilt aanvragen wanneer u de functie-app in azure maakt, gebruikt u de optie `--runtime-version` van de [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) opdracht.  

## <a name="package-management"></a>Pakketbeheer

Wanneer u lokaal ontwikkelt met behulp van de Azure Functions Core Tools of Visual Studio code, voegt u de namen en versies van de vereiste pakketten toe aan het `requirements.txt` bestand en installeert u deze met behulp van `pip`. 

De volgende vereisten bestand en PIP-opdracht kunnen bijvoorbeeld worden gebruikt om het `requests`-pakket te installeren vanuit PyPI.

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

Vergeet niet om `<APP_NAME>` te vervangen door de naam van uw functie-app in Azure.

Met de [extensie Azure functions voor Visual Studio code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) wordt ook standaard een externe build aangevraagd. 

### <a name="local-build"></a>Lokale build

U kunt voor komen dat een externe build wordt gemaakt met behulp van de volgende [func Azure functionapp Publish](functions-run-local.md#publish) -opdracht om te publiceren met een lokale build. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Vergeet niet om `<APP_NAME>` te vervangen door de naam van uw functie-app in Azure. 

Met de optie `--build local` worden Project afhankelijkheden uit het bestand requirements. txt gelezen en worden de afhankelijke pakketten lokaal gedownload en geïnstalleerd. Project bestanden en afhankelijkheden worden geïmplementeerd vanaf uw lokale computer naar Azure. Dit leidt ertoe dat een groter implementatie pakket wordt geüpload naar Azure. Als er om de een of andere reden geen afhankelijkheden van het bestand requirements. txt kunnen worden verkregen met de kern Hulpprogramma's, moet u de optie aangepaste afhankelijkheden gebruiken voor het publiceren. 

### <a name="custom-dependencies"></a>Aangepaste afhankelijkheden

Als uw project pakketten gebruikt die niet openbaar beschikbaar zijn voor onze tools, kunt u ze beschikbaar maken voor uw app door ze te plaatsen in de \_\_app\_\_/. python_packages Directory. Voordat u publiceert, voert u de volgende opdracht uit om de afhankelijkheden lokaal te installeren:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Wanneer u aangepaste afhankelijkheden gebruikt, moet u de optie `--no-build` Publishing gebruiken, omdat u de afhankelijkheden al hebt geïnstalleerd.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Vergeet niet om `<APP_NAME>` te vervangen door de naam van uw functie-app in Azure.

## <a name="unit-testing"></a>Eenheids tests

Functies die zijn geschreven in python kunnen worden getest als andere python-code met behulp van standaard test raamwerken. Voor de meeste bindingen is het mogelijk om een invoer object voor een model te maken door een instantie van een geschikte klasse te maken vanuit het `azure.functions`-pakket. Omdat het [`azure.functions`](https://pypi.org/project/azure-functions/) -pakket niet onmiddellijk beschikbaar is, moet u het installeren via uw `requirements.txt`-bestand, zoals beschreven in de sectie [pakket beheer](#package-management) hierboven. 

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

De methode `tempfile.gettempdir()` retourneert een tijdelijke map, die op Linux `/tmp`is. Uw toepassing kan deze directory gebruiken voor het opslaan van tijdelijke bestanden die door uw functies worden gegenereerd en gebruikt tijdens de uitvoering. 

> [!IMPORTANT]
> Bestanden die naar de tijdelijke map worden geschreven, kunnen niet worden gegarandeerd in aanroepen. Tijdens uitschalen worden tijdelijke bestanden niet gedeeld tussen exemplaren. 

In het volgende voor beeld wordt een tijdelijk bestand met de naam gemaakt in de tijdelijke map (`/tmp`):

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

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

Alle bekende problemen en functie aanvragen worden bijgehouden met de lijst met [github-problemen](https://github.com/Azure/azure-functions-python-worker/issues) . Als u een probleem ondervindt en u het probleem niet kunt vinden in GitHub, opent u een nieuw probleem en voegt u een gedetailleerde beschrijving van het probleem toe.

### <a name="cross-origin-resource-sharing"></a>Cross-origin-resources delen

Azure Functions ondersteunt cross-Origin Resource Sharing (CORS). CORS wordt geconfigureerd [in de portal](functions-how-to-use-azure-function-app-settings.md#cors) en via de [Azure cli](/cli/azure/functionapp/cors). De lijst van toegestane CORS-oorsprong is van toepassing op het niveau van de functie-app. Als CORS is ingeschakeld, bevatten antwoorden de `Access-Control-Allow-Origin`-header. Zie voor meer informatie [Cross-origin-resources delen](functions-how-to-use-azure-function-app-settings.md#cors).

De lijst met toegestane oorsprongen [wordt momenteel niet ondersteund](https://github.com/Azure/azure-functions-python-worker/issues/444) voor python-functie-apps. Vanwege deze beperking moet u de `Access-Control-Allow-Origin`-header expliciet instellen in uw HTTP-functies, zoals wordt weer gegeven in het volgende voor beeld:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Zorg ervoor dat u ook uw functie. json bijwerkt voor de ondersteuning van de OPTIONS HTTP-methode:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Deze methode wordt gebruikt door de Chrome-browser om te onderhandelen over de lijst met toegestane oorsprongen. 

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

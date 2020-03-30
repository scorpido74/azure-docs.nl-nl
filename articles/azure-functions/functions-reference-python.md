---
title: Python-ontwikkelaarsreferentie voor Azure-functies
description: Begrijpen hoe u functies ontwikkelen met Python
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276684"
---
# <a name="azure-functions-python-developer-guide"></a>Handleiding voor Azure Functions Python-ontwikkelaars

Dit artikel is een inleiding tot het ontwikkelen van Azure-functies met Python. In de onderstaande inhoud wordt ervan uitgegaan dat u de [handleiding azure functions-ontwikkelaars](functions-reference.md)al hebt gelezen. 

Zie de voorbeelden van [Python-functies](/samples/browse/?products=azure-functions&languages=python)voor zelfstandige functievoorbeeldprojecten in Python. 

## <a name="programming-model"></a>Programmeermodel

Azure Functions verwacht dat een functie een stateloze methode is in uw Python-script die invoer verwerkt en uitvoer produceert. Standaard verwacht de runtime dat de methode wordt geïmplementeerd `main()` als `__init__.py` een globale methode die in het bestand wordt aangeroepen. U ook [een alternatief ingangspunt opgeven.](#alternate-entry-point)

Gegevens van triggers en bindingen zijn gebonden aan `name` de functie via methodekenmerken met behulp van de eigenschap gedefinieerd in het *function.json-bestand.* De _functie.json_ hieronder beschrijft bijvoorbeeld een eenvoudige functie `req`die wordt geactiveerd door een HTTP-aanvraag met de naam :

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Op basis van `__init__.py` deze definitie kan het bestand dat de functiecode bevat er als volgt uitzien:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

U ook expliciet de kenmerktypen en het retourtype in de functie declareren met behulp van aantekeningen van python-typen. Dit helpt u gebruik te maken van de intellisense en autocomplete functies die door veel Python-code editors.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Gebruik de Python-annotaties die zijn opgenomen in het pakket [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) om invoer en uitvoer aan uw methoden te binden.

## <a name="alternate-entry-point"></a>Alternatief toegangspunt

U het standaardgedrag van een functie `scriptFile` wijzigen `entryPoint` door optioneel de eigenschappen en eigenschappen in het *function.json-bestand* op te geven. De _functie.json_ hieronder geeft bijvoorbeeld de runtime aan om de `customentry()` methode in het _main.py-bestand_ te gebruiken als ingangspunt voor uw Azure-functie.

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

De aanbevolen mapstructuur voor een Python-functieproject ziet er als volgt uit:

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
 tests
```
De hoofdmap\_\_van\_\_het project (app) kan de volgende bestanden bevatten:

* *local.settings.json*: Wordt gebruikt om app-instellingen en verbindingstekenreeksen op te slaan wanneer u lokaal wordt uitgevoerd. Dit bestand wordt niet gepubliceerd in Azure. Zie [local.settings.file](functions-run-local.md#local-settings-file)voor meer informatie.
* *requirements.txt*: Bevat de lijst met pakketten die het systeem installeert bij het publiceren naar Azure.
* *host.json*: Bevat algemene configuratieopties die van invloed zijn op alle functies in een functie-app. Dit bestand wordt wel gepubliceerd in Azure. Niet alle opties worden ondersteund bij lokaal draaien. Zie [host.json](functions-host-json.md)voor meer informatie.
* *.funcignore*: (Optioneel) verklaart bestanden die niet mogen worden gepubliceerd in Azure.
* *.gitignore*: (Optioneel) declareert bestanden die zijn uitgesloten van een git repo, zoals local.settings.json.

Elke functie heeft zijn eigen codebestand en bindend configuratiebestand (function.json). 

Wanneer u uw project implementeert naar een functie-app in Azure, moet de volledige inhoud van de map van het hoofdproject*\_\_(app)\_* in het pakket worden opgenomen, maar niet de map zelf. In dit voorbeeld `tests`raden we u aan uw tests in een map apart van de projectmap te houden. Dit voorkomt dat u testcode implementeert met uw app. Zie [Unit testing voor](#unit-testing)meer informatie.

## <a name="import-behavior"></a>Importgedrag

U modules importeren in uw functiecode met behulp van zowel expliciete relatieve als absolute referenties. Op basis van de hierboven weergegeven mapstructuur werkt de volgende import vanuit de * \_ \_functiebestandsapp\_\_\_\mijn\_eerste functie\\_\_init\_\_.py*:

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

De volgende *importen werken niet* vanuit hetzelfde bestand:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Gedeelde code moet worden bewaard in een aparte map in * \_ \_de app\_*. Als u wilt verwijzen naar modules in de *\_gedeelde codemap,* u de volgende syntaxis gebruiken:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Triggers en ingangen

Ingangen zijn onderverdeeld in twee categorieën in Azure-functies: triggerinvoer en extra invoer. Hoewel ze verschillend `function.json` zijn in het bestand, is het gebruik identiek in Python-code.  Verbindingstekenreeksen of geheimen voor trigger- en `local.settings.json` invoerbronnen worden toegewezen aan waarden in het bestand wanneer deze lokaal worden uitgevoerd en de toepassingsinstellingen wanneer deze worden uitgevoerd in Azure. 

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

Wanneer de functie wordt aangeroepen, wordt de `req`HTTP-aanvraag doorgegeven aan de functie als . Een item wordt opgehaald uit de Azure _ID_ Blob Storage op basis van `obj` de ID in de route-URL en beschikbaar gesteld zoals in de functiebody.  Hier is het opgegeven opslagaccount de verbindingstekenreeks in de azurewebjobsstorage-app-instelling, hetzelfde opslagaccount dat wordt gebruikt door de functie-app.


## <a name="outputs"></a>Uitvoer

Output kan worden uitgedrukt in zowel retourwaarde als uitvoerparameters. Als er maar één uitvoer is, raden we u aan de retourwaarde te gebruiken. Voor meerdere uitvoermoet u uitvoerparameters gebruiken.

Als u de retourwaarde van een functie wilt `name` gebruiken als de waarde `$return` van `function.json`een uitvoerbinding, moet de eigenschap van de binding worden ingesteld op in .

Als u meerdere uitgangen `set()` wilt produceren, gebruikt u de methode die door de [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) interface wordt geboden om een waarde aan de binding toe te wijzen. De volgende functie kan bijvoorbeeld een bericht naar een wachtrij pushen en ook een HTTP-antwoord retourneren.

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

Toegang tot de runtimelogger Azure Functions [`logging`](https://docs.python.org/3/library/logging.html#module-logging) is beschikbaar via een roothandler in uw functie-app. Deze logger is gekoppeld aan Application Insights en stelt u in staat om waarschuwingen en fouten die tijdens de functieuitvoering zijn ondervonden, te markeren.

In het volgende voorbeeld wordt een infobericht bijgestuurd wanneer de functie wordt aangeroepen via een HTTP-trigger.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Er zijn aanvullende registratiemethoden beschikbaar waarmee u op verschillende traceringsniveaus naar de console schrijven:

| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Schrijft een bericht met niveau KRITIEK op de root logger.  |
| **`error(_message_)`**   | Schrijft een bericht met niveau FOUT op de root logger.    |
| **`warning(_message_)`**    | Schrijft een bericht met niveau WAARSCHUWING op de root logger.  |
| **`info(_message_)`**    | Schrijft een bericht met niveau INFO op de root logger.  |
| **`debug(_message_)`** | Hiermee schrijft u een bericht met niveau DEBUG op de rootlogger.  |

Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie over logboekregistratie.

## <a name="http-trigger-and-bindings"></a>HTTP-trigger en bindingen

De HTTP-trigger wordt gedefinieerd in het bestand function.jon. De `name` binding moet overeenkomen met de benoemde parameter in de functie. In de vorige voorbeelden wordt `req` een bindingsnaam gebruikt. Deze parameter is een [http-object Request] en een [http-antwoordobject] wordt geretourneerd.

Vanuit het [object HttpRequest] u aanvraagkoppen, queryparameters, routeparameters en de berichttekst ophalen. 

Het volgende voorbeeld is van de [HTTP-triggersjabloon voor Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

In deze functie wordt `name` de waarde van `params` de queryparameter verkregen uit de parameter van het object [HttpRequest.] De JSON-gecodeerde berichttekst wordt `get_json` met behulp van de methode gelezen. 

U ook het `status_code` `headers` antwoordbericht instellen en het antwoordbericht instellen in het geretourneerde [HttpResponse-object.]

## <a name="scaling-and-concurrency"></a>Schalen en gelijktijdigheid

Azure Functions controleert standaard automatisch de belasting van uw toepassing en maakt indien nodig extra hostinstances voor Python. Functies gebruiken ingebouwde (niet door de gebruiker configureerbare) drempels voor verschillende triggertypen om te bepalen wanneer instanties moeten worden toegevoegd, zoals de leeftijd van berichten en de grootte van de wachtrij voor QueueTrigger. Zie [Hoe de consumptie- en premieplannen werken](functions-scale.md#how-the-consumption-and-premium-plans-work)voor meer informatie .

Dit schaalgedrag is voldoende voor veel toepassingen. Aanvragen met een van de volgende kenmerken mogen echter niet zo doeltreffend worden geschaald:

- De toepassing moet veel gelijktijdige aanroepingen afhandelen.
- De applicatie verwerkt een groot aantal I/O-gebeurtenissen.
- De toepassing is I/O gebonden.

In dergelijke gevallen u de prestaties verder verbeteren door gebruik te maken van asyncpatronen en door gebruik te maken van meerdere taalwerkprocessen.

### <a name="async"></a>Async

Omdat Python een runtime met één thread is, kan een hostexemplaar voor Python slechts één functieaanroep tegelijk verwerken. Voor toepassingen die een groot aantal I/O-gebeurtenissen verwerken en/of I/O-gebonden zijn, u de prestaties verbeteren door functies asynchroon uit te voeren.

Als u een functie asynchroon `async def` wilt uitvoeren, gebruikt u de instructie, die de functie rechtstreeks met [asyncio](https://docs.python.org/3/library/asyncio.html) uitvoert:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Een functie `async` zonder het trefwoord wordt automatisch uitgevoerd in een asyncio-thread-pool:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Meerdere taalwerkprocessen gebruiken

Standaard heeft elke hostinstantie Functions één taalwerkproces. U het aantal werkprocessen per host (tot 10) verhogen met behulp van de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassingsinstelling. Azure Functions probeert vervolgens gelijktijdige functieaanroepen gelijkmatig over deze werknemers te distribueren. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die Functions maakt wanneer u uw toepassing uitschaalt om aan de vraag te voldoen. 

## <a name="context"></a>Context

Als u de aanroepcontext van een [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) functie tijdens de uitvoering wilt krijgen, neemt u het argument op in de handtekening. 

Bijvoorbeeld:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

De klasse [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) heeft de volgende tekenreekskenmerken:

`function_directory`  
De map waarin de functie wordt uitgevoerd.

`function_name`  
Naam van de functie.

`invocation_id`  
ID van de huidige functie aanroep.

## <a name="global-variables"></a>Globale variabelen

Het is niet gegarandeerd dat de status van uw app behouden blijft voor toekomstige uitvoeringen. De runtime azure-functies gebruikt echter vaak hetzelfde proces voor meerdere uitvoeringen van dezelfde app. Om de resultaten van een dure berekening in de cache te plaatsen, verklaart u deze als een globale variabele. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Omgevingsvariabelen

In Functies worden [toepassingsinstellingen](functions-app-settings.md), zoals serviceverbindingstekenreeksen, tijdens de uitvoering weergegeven als omgevingsvariabelen. U deze instellingen `import os` openen door `setting = os.environ["setting-name"]`te declareren en vervolgens te gebruiken,.

In het volgende voorbeeld wordt de `myAppSetting` [toepassingsinstelling](functions-how-to-use-azure-function-app-settings.md#settings)met de sleutel met de naam :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Voor lokale ontwikkeling worden de toepassingsinstellingen [gehandhaafd in het bestand local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Python-versie 

Azure Functions ondersteunt de volgende Python-versies:

| Versie functies | Python-versies<sup>*</sup> |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Officiële CPython-distributies

Als u een specifieke Python-versie wilt aanvragen wanneer `--runtime-version` u [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) uw functie-app in Azure maakt, gebruikt u de optie van de opdracht. De runtime-versie van `--functions-version` Functions wordt door de optie ingesteld. De Python-versie wordt ingesteld wanneer de functie-app is gemaakt en kan niet worden gewijzigd.  

Wanneer u lokaal wordt uitgevoerd, wordt in de runtime de beschikbare Python-versie gebruikt. 

## <a name="package-management"></a>Pakketbeheer

Voeg bij het lokaal ontwikkelen van de Azure Functions Core Tools of Visual `requirements.txt` Studio Code de `pip`namen en versies van de vereiste pakketten toe aan het bestand en installeer ze met behulp van . 

De volgende vereistenbestand en pip-opdracht kunnen bijvoorbeeld `requests` worden gebruikt om het pakket van PyPI te installeren.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publiceren naar Azure

Wanneer u klaar bent om te publiceren, moet u ervoor zorgen dat al uw openbaar beschikbare afhankelijkheden worden vermeld in het bestand requirements.txt, dat zich aan de basis van uw projectmap bevindt. 

Projectbestanden en mappen die niet mogen worden gepubliceerd, inclusief de map met virtuele omgeving, worden weergegeven in het bestand .funcignore.

Er zijn drie buildacties die worden ondersteund voor het publiceren van uw Python-project naar Azure:

+ Remote build: Afhankelijkheden worden op afstand verkregen op basis van de inhoud van het requirements.txt-bestand. [Remote build](functions-deployment-technologies.md#remote-build) is de aanbevolen buildmethode. Remote is ook de standaardbuildoptie van Azure-tooling. 
+ Lokale build: Afhankelijkheden worden lokaal verkregen op basis van de inhoud van het requirements.txt-bestand. 
+ Afhankelijkheden op maat: uw project maakt gebruik van pakketten die niet openbaar beschikbaar zijn voor onze hulpprogramma's. (Docker vereist.)

Als u uw afhankelijkheden wilt opbouwen en wilt publiceren met behulp van een cd-systeem (continuous delivery), [gebruikt u Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Op afstand bouwen

Standaard vraagt de Azure Functions Core Tools een externe build aan wanneer u de volgende [func azure functionapp-publicatieopdracht gebruikt](functions-run-local.md#publish) om uw Python-project naar Azure te publiceren. 

```bash
func azure functionapp publish <APP_NAME>
```

Vergeet niet `<APP_NAME>` om te vervangen door de naam van uw functie-app in Azure.

De [Azure Functions Extension for Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) vraagt standaard ook om een externe build. 

### <a name="local-build"></a>Lokaal bouwen

U voorkomen dat u een externe build maakt met behulp van de volgende [func azure functionapp-publicatieopdracht](functions-run-local.md#publish) om te publiceren met een lokale build. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Vergeet niet `<APP_NAME>` om te vervangen door de naam van uw functie-app in Azure. 

Met `--build local` behulp van de optie worden projectafhankelijkheden afgelezen uit het bestand requirements.txt en worden de afhankelijke pakketten lokaal gedownload en geïnstalleerd. Projectbestanden en afhankelijkheden worden geïmplementeerd vanaf uw lokale computer naar Azure. Dit resulteert in een groter implementatiepakket dat wordt geüpload naar Azure. Als om de een of andere reden afhankelijkheden in uw requirements.txt-bestand niet kunnen worden verkregen door Core Tools, moet u de optie aangepaste afhankelijkheden gebruiken voor publicatie. 

### <a name="custom-dependencies"></a>Aangepaste afhankelijkheden

Als uw project pakketten gebruikt die niet openbaar beschikbaar zijn voor onze hulpprogramma's, u ze beschikbaar stellen voor uw app door \_ \_ze in de app\_\_/.python_packages directory te plaatsen. Voer voor publicatie de volgende opdracht uit om de afhankelijkheden lokaal te installeren:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Wanneer u aangepaste afhankelijkheden gebruikt, moet u de `--no-build` publicatieoptie gebruiken, omdat u de afhankelijkheden al hebt geïnstalleerd.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Vergeet niet `<APP_NAME>` om te vervangen door de naam van uw functie-app in Azure.

## <a name="unit-testing"></a>Testen van eenheden

Functies die in Python zijn geschreven, kunnen worden getest zoals andere Python-code met behulp van standaard testframeworks. Voor de meeste bindingen is het mogelijk om een object met een `azure.functions` mock-invoer te maken door een instantie van een geschikte klasse uit het pakket te maken. Aangezien [`azure.functions`](https://pypi.org/project/azure-functions/) het pakket niet onmiddellijk beschikbaar is, `requirements.txt` moet u het installeren via uw bestand zoals beschreven in de [sectie package management](#package-management) hierboven. 

Hieronder volgt bijvoorbeeld een mocktest van een HTTP-geactiveerde functie:

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

Hier is nog een voorbeeld, met een wachtrij geactiveerdfunctie:

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

De `tempfile.gettempdir()` methode retourneert een tijdelijke `/tmp`map, die op Linux is . Uw toepassing kan deze map gebruiken om tijdelijke bestanden op te slaan die tijdens de uitvoering door uw functies zijn gegenereerd en gebruikt. 

> [!IMPORTANT]
> Bestanden die naar de tijdelijke map zijn geschreven, blijven niet bestaan bij aanroepen. Tijdens het uitschalen worden tijdelijke bestanden niet gedeeld tussen instanties. 

In het volgende voorbeeld wordt een benoemd`/tmp`tijdelijk bestand gemaakt in de tijdelijke map ( ):

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

We raden u aan uw tests in een map apart van de projectmap te houden. Dit voorkomt dat u testcode implementeert met uw app. 

## <a name="known-issues-and-faq"></a>Bekende problemen en veelgestelde vragen

Alle bekende problemen en functieaanvragen worden bijgehouden met behulp van [github-problemenlijst.](https://github.com/Azure/azure-functions-python-worker/issues) Als u een probleem tegenkomt en het probleem niet vinden in GitHub, opent u een nieuw probleem en geeft u een gedetailleerde beschrijving van het probleem.

### <a name="cross-origin-resource-sharing"></a>Cross-origin-resources delen

Azure Functions ondersteunt cross-origin resource sharing (CORS). CORS is geconfigureerd [in de portal](functions-how-to-use-azure-function-app-settings.md#cors) en via de Azure [CLI](/cli/azure/functionapp/cors). De CORS-lijst met toegestane oorsprong is van toepassing op functie-app-niveau. Als CORS is ingeschakeld, `Access-Control-Allow-Origin` bevatten de reacties de koptekst. Zie voor meer informatie [Cross-origin-resources delen](functions-how-to-use-azure-function-app-settings.md#cors).

De lijst met toegestane oorsprongen [wordt momenteel niet ondersteund](https://github.com/Azure/azure-functions-python-worker/issues/444) voor Python-functie-apps. Vanwege deze beperking moet u de `Access-Control-Allow-Origin` koptekst in uw HTTP-functies uitdrukkelijk instellen, zoals in het volgende voorbeeld wordt weergegeven:

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

Zorg ervoor dat u ook uw function.json bijwerkt om de HTTP-methode OPTIES te ondersteunen:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Deze HTTP-methode wordt door webbrowsers gebruikt om te onderhandelen over de lijst met toegestane oorsprong. 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [API-documentatie voor Azure Functions-pakket](/python/api/azure-functions/azure.functions?view=azure-python)
* [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
* [Azure Functions triggers en bindingen](functions-triggers-bindings.md)
* [Blob-opslagbindingen](functions-bindings-storage-blob.md)
* [HTTP- en Webhook-bindingen](functions-bindings-http-webhook.md)
* [Opslagbindingen in wachtrij](functions-bindings-storage-queue.md)
* [Timertrigger](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python

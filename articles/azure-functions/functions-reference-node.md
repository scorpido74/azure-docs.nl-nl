---
title: JavaScript-ontwikkelaarsverwijzing voor Azure-functies
description: Meer informatie over het ontwikkelen van functies met JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276827"
---
# <a name="azure-functions-javascript-developer-guide"></a>JavaScript-ontwikkelaarshandleiding voor Azure Functions

Deze handleiding bevat informatie over de fijne kneepjes van het schrijven van Azure-functies met JavaScript.

Een JavaScript-functie is `function` een geëxporteerde die wordt uitgevoerd wanneer deze wordt geactiveerd[(triggers worden geconfigureerd in function.json).](functions-triggers-bindings.md) Het eerste argument dat aan `context` elke functie wordt doorgegeven, is een object dat wordt gebruikt voor het ontvangen en verzenden van bindende gegevens, logboekregistratie en communicatie met de runtime.

In dit artikel wordt ervan uitgegaan dat u de verwijzing naar de [ontwikkelaar van Azure-functies](functions-reference.md)al hebt gelezen. Voltooi de functies snelstart om uw eerste functie te maken, met behulp van [Visual Studio Code](functions-create-first-function-vs-code.md) of in de [portal](functions-create-first-azure-function.md).

Dit artikel ondersteunt ook [de ontwikkeling van typescript-apps](#typescript).

## <a name="folder-structure"></a>Mapstructuur

De vereiste mapstructuur voor een JavaScript-project ziet er als volgt uit. Deze standaardinstelling kan worden gewijzigd. Zie het gedeelte [scriptBestand](#using-scriptfile) hieronder voor meer informatie.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Aan de basis van het project is er een gedeeld [host.json-bestand](functions-host-json.md) dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft een map met een eigen codebestand (.js) en bindend configuratiebestand (function.json). De naam `function.json`van 's bovenliggende directory is altijd de naam van uw functie.

De bindingsextensies die vereist zijn in [versie 2.x](functions-versions.md) van de runtime Functies worden gedefinieerd in het `extensions.csproj` bestand, met de werkelijke bibliotheekbestanden in de `bin` map. Wanneer u lokaal ontwikkelt, moet u [bindende extensies registreren.](./functions-bindings-register.md#extension-bundles) Bij het ontwikkelen van functies in de Azure-portal wordt deze registratie voor u uitgevoerd.

## <a name="exporting-a-function"></a>Een functie exporteren

JavaScript-functies moeten worden [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) geëxporteerd [`exports`](https://nodejs.org/api/modules.html#modules_exports)via (of ). Uw geëxporteerde functie moet een JavaScript-functie zijn die wordt uitgevoerd wanneer deze wordt geactiveerd.

Standaard zoekt de runtime Functies naar `index.js`uw `index.js` functie in , waarin `function.json`dezelfde bovenliggende map wordt gedeeld als de bijbehorende . In het standaardgeval moet uw geëxporteerde functie de enige export `run` `index`zijn uit het bestand of de exportnaam of . Als u de bestandslocatie en exportnaam van uw functie wilt configureren, leest u hieronder het [ingangspunt van uw functie](functions-reference-node.md#configure-function-entry-point) configureren.

Uw geëxporteerde functie wordt doorgegeven aan een aantal argumenten voor uitvoering. Het eerste argument dat `context` nodig is, is altijd een object. Als uw functie synchroon is (geen belofte retourneert), moet u het `context` object doorgeven, omdat aanroepen `context.done` vereist is voor correct gebruik.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Een async-functie exporteren
Wanneer u [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) de declaratie of de eenvoudige JavaScript-beloften in versie 2.x [`context.done`](#contextdone-method) van de runtime Functies gebruikt, hoeft u niet expliciet de callback te bellen om aan te geven dat uw functie is voltooid. [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) Uw functie wordt voltooid wanneer de geëxporteerde async-functie/Belofte is voltooid. Voor functies die zich richten op de versie [`context.done`](#contextdone-method) 1.x runtime, moet u nog steeds bellen wanneer uw code is uitgevoerd.

Het volgende voorbeeld is een eenvoudige functie die registreert dat het werd geactiveerd en voltooit onmiddellijk uitvoering.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Wanneer u een async-functie exporteert, u `return` ook een uitvoerbinding configureren om de waarde te nemen. Dit wordt aanbevolen als u slechts één uitvoerbinding hebt.

Als u een `return`uitvoer `name` wilt `$return` toewijzen `function.json`met behulp van , wijzigt u de eigenschap in .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

In dit geval moet uw functie er als volgt uitzien:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bindingen 
In JavaScript worden [bindingen](functions-triggers-bindings.md) geconfigureerd en gedefinieerd in de function.json van een functie. Functies werken op een aantal manieren samen met bindingen.

### <a name="inputs"></a>Invoer
Invoer is onderverdeeld in twee categorieën in Azure-functies: de ene is de trigger-invoer en de andere is de extra invoer. Trigger- en andere invoerbindingen `direction === "in"`(bindingen van) kunnen door een functie op drie manieren worden gelezen:
 - **_[Aanbevolen]_ Als parameters doorgegeven aan uw functie.** Ze worden doorgegeven aan de functie in dezelfde volgorde dat ze zijn gedefinieerd in *function.json*. De `name` eigenschap gedefinieerd in *function.json* hoeft niet overeen te komen met de naam van uw parameter, hoewel dit wel zou moeten.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Als leden [`context.bindings`](#contextbindings-property) van het object.** Elk lid wordt `name` benoemd door de eigenschap gedefinieerd in *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Als invoer met [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) het JavaScript-object.** Dit is in wezen hetzelfde als het passeren van ingangen als parameters, maar stelt u in staat om dynamisch te hanteren ingangen.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Uitvoer
Outputs (bindingen `direction === "out"`van) kunnen door een functie op een aantal manieren worden geschreven. In alle gevallen `name` komt de eigenschap van de binding zoals gedefinieerd in *function.json* overeen met de naam van het objectlid dat in uw functie is geschreven. 

U gegevens op een van de volgende manieren toewijzen aan uitvoerbindingen (combineer deze methoden niet):

- **_[Aanbevolen voor meerdere uitgangen]_ Een object retourneren.** Als u een retourfunctie async/Promise gebruikt, u een object retourneren met toegewezen uitvoergegevens. In het onderstaande voorbeeld worden de uitvoerbindingen 'httpResponse' en 'queueOutput' genoemd in *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Als u een synchrone functie gebruikt, [`context.done`](#contextdone-method) kunt u dit object retourneren met (zie voorbeeld).
- **_[Aanbevolen voor één uitvoer]_ Een waarde rechtstreeks retourneren en de $return bindende naam gebruiken.** Dit werkt alleen voor async / Promise terugkerende functies. Zie voorbeeld bij [het exporteren van een async-functie](#exporting-an-async-function). 
- **Waarden toewijzen `context.bindings` aan** U waarden rechtstreeks toewijzen aan context.bindingen.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Gegevenstype Bindingen

Als u het gegevenstype voor een `dataType` invoerbinding wilt definiëren, gebruikt u de eigenschap in de bindingsdefinitie. Als u bijvoorbeeld de inhoud van een HTTP-aanvraag `binary`in binaire indeling wilt lezen, gebruikt u het type :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opties `dataType` voor `binary`zijn: `stream` `string`, , en .

## <a name="context-object"></a>contextobject
De runtime `context` maakt gebruik van een object om gegevens door te geven aan en van uw functie en om u te laten communiceren met de runtime. Het contextobject kan worden gebruikt voor het lezen en instellen `context.done` van gegevens uit bindingen, het schrijven van logboeken en het gebruik van de callback wanneer uw geëxporteerde functie synchroon is.

Het `context` object is altijd de eerste parameter voor een functie. Het moet worden opgenomen omdat het `context.done` belangrijke `context.log`methoden heeft, zoals en . U het object een naam geven `ctx` `c`wat u wilt (bijvoorbeeld of ).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindingen eigenschap

```js
context.bindings
```

Retourneert een benoemd object dat wordt gebruikt om bindende gegevens te lezen of toe te wijzen. Invoer- en triggerbindingsgegevens kunnen worden `context.bindings`geopend door eigenschappen te lezen op . Uitvoerbindingsgegevens kunnen worden toegewezen door gegevens toe te voegen aan`context.bindings`

Met de volgende bindende definities in uw function.json u `context.bindings.myInput` bijvoorbeeld de inhoud van `context.bindings.myOutput`een wachtrij openen en uitvoer toewijzen aan een wachtrij met behulp van.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

U ervoor kiezen om `context.done` uitvoerbindingsgegevens `context.binding` te definiëren met behulp van de methode in plaats van het object (zie hieronder).

### <a name="contextbindingdata-property"></a>context.bindingData, eigenschap

```js
context.bindingData
```

Retourneert een benoemd object dat triggermetagegevens `sys.utcNow` `sys.randGuid`en functieaanroepgegevens bevat (`invocationId`, `sys.methodName`, , ). Zie dit voorbeeld van [gebeurtenishubs](functions-bindings-event-hubs-trigger.md)voor een voorbeeld van triggermetagegevens .

### <a name="contextdone-method"></a>context.done-methode

```js
context.done([err],[propertyBag])
```

Laat de runtime weten dat uw code is voltooid. Wanneer uw functie [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) de aangifte gebruikt, `context.done()`hoeft u deze niet te gebruiken. De `context.done` callback wordt impliciet genoemd. Async-functies zijn beschikbaar in Node 8 of een latere versie, waarvoor versie 2.x van de runtime Functies vereist is.

Als uw functie geen async-functie is, **moet u bellen** `context.done` om de runtime te informeren dat uw functie is voltooid. De uitvoering is een moment uit als het ontbreekt.

Met `context.done` de methode u zowel een door de gebruiker gedefinieerde fout doorgeven aan de runtime als een JSON-object met uitvoerbindingsgegevens. Eigenschappen die `context.done` worden doorgegeven `context.bindings` om alles wat op het object is ingesteld, te overschrijven.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log-methode  

```js
context.log(message)
```

Hiermee u schrijven naar de streamingfunctielogboeken op het standaardtraceringsniveau. Op `context.log`zijn er aanvullende registratiemethoden beschikbaar waarmee u functielogboeken op andere traceringsniveaus schrijven:


| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **fout(_bericht_)**   | Schrijft naar logboekregistratie op foutniveau of lager.   |
| **waarschuwen(_bericht_)**    | Hiermee schrijft u naar logboekregistratie op waarschuwingsniveau of lager. |
| **info(_bericht_)**    | Schrijft naar het registreren op infoniveau of lager.    |
| **verbose(_bericht_)** | Schrijft naar verbose niveau logging.           |

In het volgende voorbeeld wordt een logboek geschreven op het niveau van de waarschuwingstracering:

```javascript
context.log.warn("Something has happened."); 
```

U [de drempelwaarde op traceniveau configureren voor het inloggen](#configure-the-trace-level-for-console-logging) in het bestand host.json. Zie hieronder het schrijven [van trace outputs](#writing-trace-output-to-the-console) voor meer informatie over het schrijven van logboeken.

Lees [Azure-functies voor het bewaken van Azure-functies](functions-monitoring.md) voor meer informatie over het weergeven en opvragen van functielogboeken.

## <a name="writing-trace-output-to-the-console"></a>Trace-uitvoer naar de console schrijven 

In Functies gebruikt `context.log` u de methoden om traceoutput naar de console te schrijven. In Functies v2.x worden `console.log` trace-uitvoermetbehulp van de functie-app vastgelegd op het niveau van de functie-app. Dit betekent dat `console.log` uitvoer uit niet zijn gekoppeld aan een specifieke functieaanroep en niet worden weergegeven in de logboeken van een specifieke functie. Ze verspreiden zich echter wel naar Application Insights. In Functies v1.x kunt `console.log` u niet naar de console schrijven.

Wanneer u `context.log()`belt, wordt uw bericht naar de console geschreven op het standaardtraceringsniveau, dat is het niveau van de infotraceringswaarde. _info_ De volgende code schrijft naar de console op het niveau van de infotracerings:

```javascript
context.log({hello: 'world'});  
```

Deze code is gelijk aan de bovenstaande code:

```javascript
context.log.info({hello: 'world'});  
```

Deze code wordt op foutniveau naar de console geschreven:

```javascript
context.log.error("An error has occurred.");  
```

Omdat _fout_ het hoogste traceerniveau is, wordt dit spoor naar de uitvoer geschreven op alle spoorniveaus, zolang logboekregistratie is ingeschakeld.

Alle `context.log` methoden ondersteunen dezelfde parameterindeling die wordt ondersteund door de methode Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Overweeg de volgende code, die functielogboeken schrijft met behulp van het standaardtraceringsniveau:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

U dezelfde code ook in de volgende indeling schrijven:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Het traceerniveau voor consolelogboekregistratie configureren

Met functies 1.x u het drempelspoorniveau definiëren voor het schrijven naar de console, waardoor u eenvoudig de manier bepalen waarop sporen naar de console worden geschreven vanuit uw functie. Als u de drempelwaarde wilt instellen voor `tracing.consoleLevel` alle traces die naar de console zijn geschreven, gebruikt u de eigenschap in het bestand host.json. Deze instelling is van toepassing op alle functies in uw functie-app. In het volgende voorbeeld wordt de traceerdrempel ingesteld om verboselogging in te schakelen:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Waarden van **consoleNiveau** komen overeen met de namen van de `context.log` methoden. Als u alle tracelogging op de console wilt uitschakelen, stelt u **consoleNiveau** _in op uit_. Zie [host.json referentie voor](functions-host-json-v1.md)meer informatie .

## <a name="http-triggers-and-bindings"></a>HTTP-triggers en bindingen

HTTP- en webhook-triggers en HTTP-uitvoerbindingen gebruiken aanvraag- en antwoordobjecten om de HTTP-berichten weer te geven.  

### <a name="request-object"></a>Object Aanvragen

Het `context.req` object (aanvraag) heeft de volgende eigenschappen:

| Eigenschap      | Beschrijving                                                    |
| ------------- | -------------------------------------------------------------- |
| _Lichaam_        | Een object dat de hoofdtekst van het verzoek bevat.               |
| _Headers_     | Een object dat de aanvraagkoppen bevat.                   |
| _Methode_      | De HTTP-methode van de aanvraag.                                |
| _originalUrl (originalUrl)_ | De URL van het verzoek.                                        |
| _Params_      | Een object dat de routeringsparameters van de aanvraag bevat. |
| _Query_       | Een object dat de queryparameters bevat.                  |
| _rawBody rawBody_     | Het lichaam van het bericht als een string.                           |


### <a name="response-object"></a>Responsobject

Het `context.res` object (respons) heeft de volgende eigenschappen:

| Eigenschap  | Beschrijving                                               |
| --------- | --------------------------------------------------------- |
| _Lichaam_    | Een object dat de hoofdtekst van de reactie bevat.         |
| _Headers_ | Een object dat de antwoordkoppen bevat.             |
| _isRaw_   | Geeft aan dat de opmaak wordt overgeslagen voor het antwoord.    |
| _status_  | De HTTP-statuscode van het antwoord.                     |
| _Cookies_ | Een array met HTTP-cookieobjecten die zijn ingesteld in het antwoord. Een HTTP-cookieobject `name` `value`heeft een , en `maxAge` andere `sameSite`cookie-eigenschappen, zoals of . |

### <a name="accessing-the-request-and-response"></a>Toegang tot het verzoek en antwoord 

Wanneer u met HTTP-triggers werkt, hebt u op verschillende manieren toegang tot de HTTP-aanvraag- en antwoordobjecten:

+ **Van `req` `res` en eigenschappen `context` op het object.** Op deze manier u het conventionele patroon gebruiken om toegang te krijgen `context.bindings.name` tot HTTP-gegevens vanuit het contextobject, in plaats van het volledige patroon te gebruiken. In het volgende voorbeeld `req` ziet `res` u `context`hoe u toegang krijgt tot de objecten en objecten op :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Van de benoemde invoer- en uitvoerbindingen.** Op deze manier werken de HTTP-trigger en bindingen hetzelfde als elke andere binding. In het volgende voorbeeld wordt het `response` antwoordobject ingesteld met een benoemde binding: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Alleen reactie]_ Door `context.res.send(body?: any)`te bellen.** Er wordt een HTTP-antwoord gemaakt met invoer `body` als antwoordinstantie. `context.done()`wordt impliciet genoemd.

+ **_[Alleen reactie]_ Door `context.done()`te bellen.** Een speciaal type HTTP-binding retourneert `context.done()` het antwoord dat wordt doorgegeven aan de methode. In de volgende HTTP-uitvoerbinding wordt een `$return` uitvoerparameter gedefinieerd:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Schalen en gelijktijdigheid

Azure Functions controleert standaard automatisch de belasting van uw toepassing en maakt indien nodig extra hostexemplaren voor Node.js. Functies gebruiken ingebouwde (niet door de gebruiker configureerbare) drempels voor verschillende triggertypen om te bepalen wanneer instanties moeten worden toegevoegd, zoals de leeftijd van berichten en de grootte van de wachtrij voor QueueTrigger. Zie [Hoe de consumptie- en premieplannen werken](functions-scale.md#how-the-consumption-and-premium-plans-work)voor meer informatie .

Dit schaalgedrag is voldoende voor veel Node.js-toepassingen. Voor CPU-gebonden toepassingen u de prestaties verder verbeteren door gebruik te maken van meerdere taalwerkprocessen.

Standaard heeft elke hostinstantie Functions één taalwerkproces. U het aantal werkprocessen per host (tot 10) verhogen met behulp van de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassingsinstelling. Azure Functions probeert vervolgens gelijktijdige functieaanroepen gelijkmatig over deze werknemers te distribueren. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die Functions maakt wanneer u uw toepassing uitschaalt om aan de vraag te voldoen. 

## <a name="node-version"></a>Knooppuntversie

In de volgende tabel worden de huidige ondersteunde Node.js-versies voor elke belangrijke versie van de runtime functies per besturingssysteem weergegeven:

| Versie functies | Knooppuntversie (Windows) | Node Versie (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (vergrendeld door de runtime) | N.v.t. |
| 2.x  | ~8.<br/>~10 (aanbevolen)<br/>~12<sup>*</sup> | ~8 (aanbevolen)<br/>~10  |
| 3.x | ~10<br/>~12 (aanbevolen)  | ~10<br/>~12 (aanbevolen) |

<sup>*</sup>Node ~12 is momenteel toegestaan op versie 2.x van de runtime functies. Voor de beste prestaties raden we echter aan functies runtime versie 3.x met Node ~12 te gebruiken. 

U de huidige versie zien die de runtime gebruikt `process.version` door de bovenstaande app-instelling te controleren of door af te drukken vanuit een functie. Target de versie in Azure door de instelling van de [WEBSITE_NODE_DEFAULT_VERSION-app in](functions-how-to-use-azure-function-app-settings.md#settings) te stellen op een ondersteunde LTS-versie, zoals `~10`.

## <a name="dependency-management"></a>Beheer van afhankelijkheden
Als u communitybibliotheken wilt gebruiken in uw JavaScript-code, zoals in het onderstaande voorbeeld wordt weergegeven, moet u ervoor zorgen dat alle afhankelijkheden zijn geïnstalleerd op uw functie-app in Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> U moet `package.json` een bestand definiëren aan de basis van uw functie-app. Als u het bestand definieert, kunnen alle functies in de app dezelfde pakketten in de cache delen, wat de beste prestaties biedt. Als er een versieconflict ontstaat, kunt `package.json` u dit oplossen door een bestand toe te voegen in de map van een specifieke functie.  

Bij het implementeren van functie-apps vanuit bronbeheer wordt `package.json` elk `npm install` bestand dat in uw repo aanwezig is, geactiveerd in de map tijdens de implementatie. Maar bij het implementeren via de Portal of CLI, moet u de pakketten handmatig installeren.

Er zijn twee manieren om pakketten in uw functie-app te installeren: 

### <a name="deploying-with-dependencies"></a>Implementeren met afhankelijkheden
1. Installeer alle benodigde pakketten `npm install`lokaal door te draaien.

2. Implementeer uw code en `node_modules` zorg ervoor dat de map is opgenomen in de implementatie. 


### <a name="using-kudu"></a>Kudu gebruiken
1. Ga naar `https://<function_app_name>.scm.azurewebsites.net`.

2. Klik op Console > **CMD** **debuggen**.

3. Ga `D:\home\site\wwwroot`naar , en sleep vervolgens uw package.json-bestand naar de **map wwwroot** aan de bovenste helft van de pagina.  
    U bestanden ook op andere manieren uploaden naar uw functie-app. Zie [App-bestanden voor](functions-reference.md#fileupdate)meer informatie bijwerken . 

4. Nadat het bestand package.json is `npm install` geüpload, voert u de opdracht uit in de **kudu-externe uitvoeringsconsole**.  
    Met deze actie worden de pakketten die in het bestand package.json zijn aangegeven, gedownload en wordt de functie-app opnieuw gestart.

## <a name="environment-variables"></a>Omgevingsvariabelen

In Functies worden [app-instellingen](functions-app-settings.md), zoals serviceverbindingstekenreeksen, tijdens de uitvoering weergegeven als omgevingsvariabelen. U toegang `process.env`krijgen tot deze instellingen met behulp `context.log()` van , `AzureWebJobsStorage` `WEBSITE_SITE_NAME` zoals hier weergegeven in de tweede en derde gesprekken naar waar we de en de omgeving variabelen log:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Wanneer u lokaal wordt uitgevoerd, worden de app-instellingen gelezen in het projectbestand [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="configure-function-entry-point"></a>Functie-invoerpunt configureren

De `function.json` `scriptFile` eigenschappen `entryPoint` en kunnen worden gebruikt om de locatie en naam van uw geëxporteerde functie te configureren. Deze eigenschappen kunnen belangrijk zijn wanneer uw JavaScript wordt getranspileerd.

### <a name="using-scriptfile"></a>`scriptFile` gebruiken

Standaard wordt een JavaScript-functie `index.js`uitgevoerd vanuit een bestand dat dezelfde `function.json`bovenliggende map deelt als het bijbehorende .

`scriptFile`kan worden gebruikt om een mapstructuur te krijgen die lijkt op het volgende voorbeeld:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

De `function.json` `myNodeFunction` optie moet `scriptFile` een eigenschap bevatten die naar het bestand wijst met de geëxporteerde functie die moet worden uitgevoerd.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` gebruiken

In `scriptFile` (of), `index.js`moet een functie `module.exports` worden geëxporteerd met behulp van te worden gevonden en uitgevoerd. Standaard is de functie die wordt uitgevoerd wanneer geactiveerd, de `run`enige export uit `index`dat bestand, de naam export of de export met de naam .

Dit kan worden `entryPoint` geconfigureerd `function.json`met behulp van in , zoals in het volgende voorbeeld:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In Functies v2.x, `this` die de parameter in gebruikersfuncties ondersteunt, kan de functiecode dan zijn zoals in het volgende voorbeeld:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In dit voorbeeld is het belangrijk op te merken dat, hoewel een object wordt geëxporteerd, er geen garanties zijn voor het behoud van de staat tussen executies.

## <a name="local-debugging"></a>Lokale foutopsporing

Wanneer gestart `--inspect` met de parameter, luistert een Node.js-proces naar een foutopsporingsclient op de opgegeven poort. In Azure Functions 2.x u argumenten opgeven die u wilt doorgeven aan het Node.js-proces waarbij uw code wordt uitgevoerd door de omgevingsvariabele of app-instelling toe te voegen. `languageWorkers:node:arguments = <args>` 

Als u lokaal wilt `"languageWorkers:node:arguments": "--inspect=5858"` `Values` debuggen, voegt u onder in het bestand [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) toe en voegt u een foutopsporing toe aan poort 5858.

Wanneer u debugging met `--inspect` VS-code gebruikt, `port` wordt de parameter automatisch toegevoegd met behulp van de waarde in het launch.json-bestand van het project.

In versie 1.x `languageWorkers:node:arguments` werkt de instelling niet. De foutopsporingspoort kan worden [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) geselecteerd met de parameter op Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Wanneer u versie 2.x van de runtime van de functies target, u met zowel [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) als de Azure Functions Core [Tools](functions-run-local.md) functie-apps maken met behulp van een sjabloon die typeScript-functie-appprojecten ondersteunt. De sjabloon `package.json` `tsconfig.json` genereert en projecteert bestanden waarmee javascriptfuncties gemakkelijker kunnen worden getransoneerd, uitgevoerd en gepubliceerd met deze hulpprogramma's.

Een `.funcignore` gegenereerd bestand wordt gebruikt om aan te geven welke bestanden worden uitgesloten wanneer een project wordt gepubliceerd in Azure.  

TypeScript-bestanden (.ts) worden in de `dist` uitvoermap getransderpileerd naar JavaScript-bestanden (.js). TypeScript-sjablonen [ `scriptFile` ](#using-scriptfile) gebruiken `function.json` de parameter in om de locatie `dist` van het bijbehorende .js-bestand in de map aan te geven. De uitvoerlocatie wordt door de `outDir` sjabloon `tsconfig.json` ingesteld met behulp van parameter in het bestand. Als u deze instelling of de naam van de map wijzigt, kan de runtime de code die moet worden uitgevoerd niet vinden.

> [!NOTE]
> Experimentele ondersteuning voor TypeScript bestaat versie 1.x van de runtime Functies. De experimentele versie transpiles TypeScript-bestanden in JavaScript-bestanden wanneer de functie wordt aangeroepen. In versie 2.x is deze experimentele ondersteuning vervangen door de toolgestuurde methode die transpilatie doet voordat de host wordt geïnitialiseerd en tijdens het implementatieproces.

De manier waarop u lokaal een TypeScript-project ontwikkelt en implementeert, is afhankelijk van uw ontwikkeltool.

### <a name="visual-studio-code"></a>Visual Studio Code

Met de extensie [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) u uw functies ontwikkelen met TypeScript. De Core Tools is een vereiste van de Azure Functions-extensie.

Als u een TypeScript-functie-app `TypeScript` wilt maken in Visual Studio Code, kiest u als taal wanneer u een functie-app maakt.

Wanneer u op **F5** drukt om de app lokaal uit te voeren, wordt transpilatie uitgevoerd voordat de host (func.exe) wordt geïnitialiseerd. 

Wanneer u uw functie-app implementeert naar Azure met de knop **Deploy to function-app...** genereert de Azure Functions-extensie eerst een productieklare build van JavaScript-bestanden uit de TypeScript-bronbestanden.

### <a name="azure-functions-core-tools"></a>Core-hulpprogramma's voor Azure-functies

Er zijn verschillende manieren waarop een TypeScript-project verschilt van een JavaScript-project wanneer u de Core-hulpprogramma's gebruikt.

#### <a name="create-project"></a>Project maken

Als u een typeScript-functie-appproject wilt maken met Core Tools, moet u de optie TypeScript-taal opgeven wanneer u de functie-app maakt. U dit op een van de volgende manieren doen:

- Voer `func init` de opdracht `node` uit, selecteer als `typescript`taalstapel en selecteer .

- Voer de opdracht `func init --worker-runtime typescript` uit.

#### <a name="run-local"></a>Lokaal uitvoeren

Als u de functie-app-code lokaal wilt uitvoeren met `func host start`Core Tools, gebruikt u de volgende opdrachten in plaats van: 

```command
npm install
npm start
```

De `npm start` opdracht is gelijk aan de volgende opdrachten:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publiceren naar Azure

Voordat u [`func azure functionapp publish`] de opdracht gebruikt om te implementeren in Azure, maakt u een productieklare build van JavaScript-bestanden uit de TypeScript-bronbestanden. 

De volgende opdrachten bereiden en publiceren uw TypeScript-project met behulp van Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Vervang in deze `<APP_NAME>` opdracht de naam van uw functie-app.

## <a name="considerations-for-javascript-functions"></a>Overwegingen voor JavaScript-functies

Wanneer u met JavaScript-functies werkt, moet u rekening houden met de overwegingen in de volgende secties.

### <a name="choose-single-vcpu-app-service-plans"></a>Kies abonnementen voor één vCPU-appservice

Wanneer u een functie-app maakt die het App Service-abonnement gebruikt, raden we u aan een enkel-vCPU-abonnement te selecteren in plaats van een abonnement met meerdere vCPU's. Tegenwoordig worden JavaScript-functies efficiënter uitgevoerd op vm's met één vCPU en het gebruik van grotere VM's levert niet de verwachte prestatieverbeteringen op. Indien nodig u handmatig uitschalen door meer single-vCPU VM-exemplaren toe te voegen, of u automatisch schalen inschakelen. Zie [Het aantal instance per eer handmatig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)voor meer informatie .

### <a name="cold-start"></a>Koude start

Bij het ontwikkelen van Azure-functies in het serverloze hostingmodel zijn koude starts een realiteit. *Koude start* verwijst naar het feit dat wanneer uw functie-app voor het eerst start na een periode van inactiviteit, het langer duurt om op te starten. Voor JavaScript-functies met grote afhankelijkheidsbomen in het bijzonder kan koude start aanzienlijk zijn. Voer uw functies als [pakketbestand](run-functions-from-deployment-package.md) waar mogelijk uit om het koude startproces te versnellen. Veel implementatiemethoden gebruiken standaard de run van het pakketmodel, maar als u grote koude starts ondervindt en niet op deze manier wordt uitgevoerd, kan deze wijziging een aanzienlijke verbetering bieden.

### <a name="connection-limits"></a>Verbindingslimieten

Wanneer u een servicespecifieke client gebruikt in een Azure Functions-toepassing, maakt u geen nieuwe client bij elke functieaanroep. Maak in plaats daarvan één statische client in het globale bereik. Zie [Verbindingen beheren in Azure-functies](manage-connections.md)voor meer informatie.

### <a name="use-async-and-await"></a>Gebruik `async` en`await`

Wanneer u Azure-functies in JavaScript schrijft, moet u code schrijven met de `async` trefwoorden en `await` trefwoorden. Het schrijven `async` `await` van code met `.then` behulp `.catch` van en in plaats van callbacks of en met Beloften helpt voorkomen dat twee veelvoorkomende problemen:
 - Het gooien van niet-gevangen uitzonderingen die [crashen de Node.js proces](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), mogelijk van invloed op de uitvoering van andere functies.
 - Onverwacht gedrag, zoals ontbrekende logboeken uit context.log, veroorzaakt door asynchrone oproepen die niet goed worden afgewacht.

In het onderstaande voorbeeld `fs.readFile` wordt de asynchrone methode aangeroepen met een error-first callback-functie als tweede parameter. Deze code veroorzaakt beide bovengenoemde problemen. Een uitzondering die niet expliciet is gevangen in de juiste scope crashte het hele proces (probleem #1). Bellen `context.done()` buiten het bereik van de callback-functie betekent dat de functieaanroep kan eindigen voordat het bestand wordt gelezen (probleem #2). In dit voorbeeld `context.done()` resulteert het te vroeg bellen `Data from file:`in ontbrekende logboekvermeldingen die beginnen met .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Het `async` gebruik `await` van de en trefwoorden helpt voorkomen dat beide fouten. U moet de hulpprogramma [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) functie Node.js gebruiken om fout-eerste callback-achtige functies om te zetten in wachtbare functies.

In het onderstaande voorbeeld mislukken onverwerkte uitzonderingen die tijdens de functieuitvoering worden gegooid, alleen de individuele aanroep die een uitzondering heeft gegenereerd. Het `await` trefwoord betekent `readFileAsync` dat stappen `readFile` die volgen pas worden uitgevoerd nadat ze zijn voltooid. Met `async` `await`en , u hoeft ook `context.done()` niet te bellen terug.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

+ [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
+ [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)
+ [Azure Functions triggers en bindingen](functions-triggers-bindings.md)

['func azure functionapp publish']: functions-run-local.md#project-file-deployment

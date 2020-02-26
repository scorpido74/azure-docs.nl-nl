---
title: Naslag informatie over Java script-ontwikkel aars voor Azure Functions
description: Meer informatie over het ontwikkelen van functies met behulp van Java script.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: b0cd9541deac106525cfe80244d1867f513825f0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77584486"
---
# <a name="azure-functions-javascript-developer-guide"></a>Ontwikkelaars handleiding voor Azure Functions java script

Deze hand leiding bevat informatie over de complexiteit voor het schrijven van Azure Functions met Java script.

Een Java script-functie is een geëxporteerde `function` die wordt uitgevoerd wanneer geactiveerd ([triggers worden geconfigureerd in function. json](functions-triggers-bindings.md)). Het eerste argument dat aan elke functie is door gegeven, is een `context`-object, dat wordt gebruikt voor het ontvangen en verzenden van bindings gegevens, logboek registratie en communicatie met de runtime.

In dit artikel wordt ervan uitgegaan dat u de [Azure functions Naslag informatie voor ontwikkel aars](functions-reference.md)al hebt gelezen. Voltooi de Quick Start van functies om uw eerste functie te maken met behulp van [Visual Studio code](functions-create-first-function-vs-code.md) of [in de portal](functions-create-first-azure-function.md).

Dit artikel biedt ook ondersteuning voor [type script app-ontwikkeling](#typescript).

## <a name="folder-structure"></a>Mapstructuur

De vereiste mapstructuur voor een Java script-project ziet er als volgt uit. Deze standaard instelling kan worden gewijzigd. Zie het gedeelte [script](#using-scriptfile) voor meer informatie.

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

In de hoofdmap van het project bevindt zich een gedeeld [host. json](functions-host-json.md) -bestand dat kan worden gebruikt voor het configureren van de functie-app. Elke functie heeft een map met een eigen code bestand (. js) en een bindings configuratie bestand (function. json). De naam van de bovenliggende map van `function.json`is altijd de naam van uw functie.

De bindings uitbreidingen vereist in [versie 2. x](functions-versions.md) van de functions runtime worden gedefinieerd in het `extensions.csproj` bestand, met de daad werkelijke bibliotheek bestanden in de map `bin`. Wanneer u lokaal ontwikkelt, moet u [bindings uitbreidingen registreren](./functions-bindings-register.md#extension-bundles). Bij het ontwikkelen van functies in de Azure Portal, wordt deze registratie voor u uitgevoerd.

## <a name="exporting-a-function"></a>Een functie exporteren

Java script-functies moeten worden geëxporteerd via [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (of [`exports`](https://nodejs.org/api/modules.html#modules_exports)). De geëxporteerde functie moet een Java script-functie die wordt uitgevoerd wanneer deze wordt geactiveerd.

De functies runtime zoekt standaard naar uw functie in `index.js`, waarbij `index.js` dezelfde bovenliggende map deelt als de overeenkomstige `function.json`. In het standaard geval moet de geëxporteerde functie de enige export zijn van het bestand of de export met de naam `run` of `index`. Meer informatie over het configureren van het [toegangs punt van uw functie](functions-reference-node.md#configure-function-entry-point) vindt u in de bestands locatie en export naam van uw functie.

De geëxporteerde functie heeft een aantal argumenten door gegeven bij de uitvoering. Het eerste argument dat wordt gebruikt, is altijd een `context`-object. Als uw functie synchroon is (geen belofte retourneert), moet u het `context`-object door geven, omdat het aanroepen van `context.done` vereist is voor het juiste gebruik.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Een async-functie exporteren
Wanneer u de [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaratie of de enkelvoudige java script- [belofte](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) in versie 2. x van de functions-runtime gebruikt, hoeft u de [`context.done`](#contextdone-method) call back niet expliciet aan te roepen om aan te geven dat de functie is voltooid. De functie wordt voltooid wanneer de geëxporteerde async-functie/Promise is voltooid. Voor functies die zijn gericht op versie 1. x runtime moet u [`context.done`](#contextdone-method) aanroepen wanneer uw code wordt uitgevoerd.

Het volgende voor beeld is een eenvoudige functie die logboeken aanmeldt dat deze is geactiveerd en de uitvoering onmiddellijk voltooit.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Bij het exporteren van een async-functie kunt u ook een uitvoer binding configureren om de `return` waarde te nemen. Dit wordt aanbevolen als u slechts één uitvoer binding hebt.

Als u een uitvoer wilt toewijzen met behulp van `return`, wijzigt u de eigenschap `name` in `$return` in `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

In dit geval moet uw functie eruitzien zoals in het volgende voor beeld:

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
In Java script worden [bindingen](functions-triggers-bindings.md) geconfigureerd en gedefinieerd in de functie Function. json van een functie. Functies werken op een aantal manieren met bindingen.

### <a name="inputs"></a>Invoer
De invoer is onderverdeeld in twee categorieën in Azure Functions: een is de invoer van de trigger en de andere is de extra invoer. Triggers en andere invoer bindingen (bindingen van `direction === "in"`) kunnen op drie manieren worden gelezen door een functie:
 - **_[Aanbevolen]_ Als para meters die zijn door gegeven aan de functie.** Ze worden door gegeven aan de functie in dezelfde volg orde als waarin ze zijn gedefinieerd in *Function. json*. De eigenschap `name` die in *Function. json* is gedefinieerd, hoeft niet overeen te komen met de naam van uw para meter, hoewel het moet.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Als leden van het object [`context.bindings`](#contextbindings-property) .** Elk lid krijgt de naam van de `name` eigenschap die in *Function. json*is gedefinieerd.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Als invoer met behulp van het Java script [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) -object.** Dit is in wezen hetzelfde als het door voeren van invoer als para meters, maar biedt u de mogelijkheid om invoer dynamisch te verwerken.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Uitvoer
Outputs (bindingen van `direction === "out"`) kunnen op verschillende manieren worden geschreven naar een functie. In alle gevallen komt de eigenschap `name` van de binding zoals gedefinieerd in *Function. json* overeen met de naam van het object lid dat is geschreven in uw functie. 

U kunt gegevens aan uitvoer bindingen op een van de volgende manieren toewijzen (deze methoden niet combi neren):

- **_[Aanbevolen voor meerdere uitvoer]_ Een object retour neren.** Als u een functie van async/Promise retourneert, kunt u een object retour neren met de toegewezen uitvoer gegevens. In het onderstaande voor beeld zijn de uitvoer bindingen de naam ' httpResponse ' en ' queueOutput ' in *Function. json*.

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

  Als u een synchrone functie gebruikt, kunt u dit object retour neren met behulp van [`context.done`](#contextdone-method) (Zie voor beeld).
- **_[Aanbevolen voor één uitvoer]_ Een waarde rechtstreeks en met de naam van de $return binding wordt geretourneerd.** Dit werkt alleen voor async/Promise-functies. Zie voor beelden van [het exporteren van een async-functie](#exporting-an-async-function). 
- **Waarden toewijzen aan `context.bindings`** U kunt waarden rechtstreeks aan context. bindingen toewijzen.

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

### <a name="bindings-data-type"></a>Gegevens type bindingen

Als u het gegevens type voor een invoer binding wilt definiëren, gebruikt u de eigenschap `dataType` in de bindings definitie. Als u de inhoud van een HTTP-aanvraag in binaire indeling wilt lezen, gebruikt u bijvoorbeeld het type `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opties voor `dataType` zijn: `binary`, `stream`en `string`.

## <a name="context-object"></a>context object
De runtime gebruikt een `context`-object voor het door geven van gegevens van en naar uw functie en om u te laten communiceren met de runtime. Het context object kan worden gebruikt voor het lezen en instellen van gegevens van bindingen, het schrijven van Logboeken en het gebruik van de `context.done` terugbellen wanneer de geëxporteerde functie synchroon is.

Het `context`-object is altijd de eerste para meter voor een functie. Deze moet worden opgenomen, omdat deze belang rijke methoden heeft, zoals `context.done` en `context.log`. U kunt het object een naam, ongeacht wat u wilt, bijvoorbeeld `ctx` of `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context. bindings, eigenschap

```js
context.bindings
```

Retourneert een benoemd object dat wordt gebruikt om bindings gegevens te lezen of toe te wijzen. Invoer-en trigger gegevens voor bindingen kunnen worden geopend door het lezen van eigenschappen op `context.bindings`. Uitvoer binding gegevens kunnen worden toegewezen door gegevens toe te voegen aan `context.bindings`

Bijvoorbeeld, de volgende bindings definities in uw functie. json bieden u de mogelijkheid om de inhoud van een wachtrij te openen vanaf `context.bindings.myInput` en om uitvoer te koppelen aan een wachtrij met behulp van `context.bindings.myOutput`.

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

U kunt ervoor kiezen om uitvoer binding gegevens te definiëren met behulp van de methode `context.done` in plaats van het `context.binding`-object (zie hieronder).

### <a name="contextbindingdata-property"></a>context. bindingData eigenschap

```js
context.bindingData
```

Retourneert een benoemd object dat trigger-meta gegevens en functie aanroepgegevens bevat (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Voor een voor beeld van meta gegevens van triggers raadpleegt u dit [voor beeld van Event hubs](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>context. Done-methode

```js
context.done([err],[propertyBag])
```

Laat de runtime weten dat uw code is voltooid. Wanneer uw functie gebruikmaakt van de [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaratie, hoeft u `context.done()`niet te gebruiken. De `context.done` call back wordt impliciet aangeroepen. Asynchrone functies zijn beschikbaar in knoop punt 8 of een latere versie, waarvoor versie 2. x van de functions-runtime vereist is.

Als uw functie geen async-functie is, **moet u `context.done` aanroepen** om de runtime te informeren dat de functie is voltooid. Er wordt een time-out uitgevoerd als deze ontbreekt.

Met de `context.done` methode kunt u zowel een door de gebruiker gedefinieerde fout terugsturen naar de runtime als een JSON-object dat uitvoer bindings gegevens bevat. Eigenschappen die aan `context.done` worden door gegeven, overschrijven alle sets op het `context.bindings`-object.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context. log-methode  

```js
context.log(message)
```

Hiermee kunt u naar de streaming-functie Logboeken schrijven op het standaard tracerings niveau. Op `context.log`zijn er aanvullende logboek registratie methoden beschikbaar waarmee u functie Logboeken kunt schrijven op andere tracerings niveaus:


| Methode                 | Beschrijving                                |
| ---------------------- | ------------------------------------------ |
| **fout (_bericht_)**   | Schrijft naar logboek registratie op fout niveau of lager.   |
| **Warning (_bericht_)**    | Schrijft naar logboek registratie op waarschuwings niveau of lager. |
| **info (_bericht_)**    | Schrijft naar logboek registratie op info niveau of lager.    |
| **uitgebreid (_bericht_)** | Schrijft naar uitgebreide logboek registratie.           |

In het volgende voor beeld wordt een logboek op het tracerings niveau waarschuwing geschreven:

```javascript
context.log.warn("Something has happened."); 
```

U kunt [de drempel waarde tracerings niveau voor logboek registratie configureren](#configure-the-trace-level-for-console-logging) in het bestand host. json. Zie voor meer informatie over het schrijven van Logboeken [trace-uitvoer](#writing-trace-output-to-the-console) .

Lees de [controle Azure functions](functions-monitoring.md) voor meer informatie over het weer geven en opvragen van functie Logboeken.

## <a name="writing-trace-output-to-the-console"></a>Tracerings uitvoer naar de console schrijven 

In functies gebruikt u de `context.log`-methoden voor het schrijven van uitvoer naar de-console. In functions v2. x, traceer uitvoer met `console.log` worden vastgelegd op functie-app niveau. Dit betekent dat de uitvoer van `console.log` niet is gekoppeld aan een specifieke functie aanroep en niet wordt weer gegeven in de logboeken van een specifieke functie. Ze worden echter door gegeven aan Application Insights. In functions v1. x kunt u `console.log` niet gebruiken om te schrijven naar de-console.

Wanneer u `context.log()`aanroept, wordt uw bericht naar de-console geschreven op het niveau van de standaard tracering. Dit is het tracerings niveau _info_ . Met de volgende code wordt naar de-console op het tracerings niveau info geschreven:

```javascript
context.log({hello: 'world'});  
```

Deze code is gelijk aan de bovenstaande code:

```javascript
context.log.info({hello: 'world'});  
```

Deze code schrijft naar de-console op fout niveau:

```javascript
context.log.error("An error has occurred.");  
```

Omdat de _fout_ het hoogste traceer niveau is, wordt deze tracering naar de uitvoer op alle tracerings niveaus geschreven zolang logboek registratie is ingeschakeld.

Alle `context.log`-methoden ondersteunen dezelfde parameter indeling die wordt ondersteund door de methode node. js [util. Format](https://nodejs.org/api/util.html#util_util_format_format). Bekijk de volgende code, waarmee functie Logboeken worden geschreven met behulp van het standaard tracerings niveau:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

U kunt ook dezelfde code in de volgende indeling schrijven:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Het tracerings niveau voor console logboek registratie configureren

Met de functie 1. x kunt u het tracerings niveau van de drempel waarde voor het schrijven naar de-console definiëren, zodat u gemakkelijk kunt bepalen hoe traceringen naar de console worden geschreven vanuit uw functie. Als u de drempel waarde wilt instellen voor alle traceringen die naar de-console worden geschreven, gebruikt u de eigenschap `tracing.consoleLevel` in het bestand host. json. Deze instelling is van toepassing op alle functies in uw functie-app. In het volgende voor beeld wordt de drempel voor tracering ingesteld om uitgebreide logboek registratie in te scha kelen:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

De waarden van **consoleLevel** komen overeen met de namen van de `context.log`-methoden. Als u alle traceer logboek registratie wilt uitschakelen voor de-console, stelt u **consoleLevel** in op _uit_. Zie voor meer informatie [host. json Reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP-triggers en-bindingen

HTTP-en webhook-triggers en HTTP-uitvoer bindingen gebruiken aanvraag-en antwoord objecten om de HTTP-berichten te vertegenwoordigen.  

### <a name="request-object"></a>Aanvraag object

Het `context.req`-object (Request) heeft de volgende eigenschappen:

| Eigenschap      | Beschrijving                                                    |
| ------------- | -------------------------------------------------------------- |
| _organen_        | Een object dat de hoofd tekst van de aanvraag bevat.               |
| _koppen_     | Een object dat de aanvraag headers bevat.                   |
| _methode_      | De HTTP-methode van de aanvraag.                                |
| _originalUrl_ | De URL van de aanvraag.                                        |
| _params_      | Een object dat de routerings parameters van de aanvraag bevat. |
| _ophalen_       | Een object dat de query parameters bevat.                  |
| _rawBody_     | De hoofd tekst van het bericht als een teken reeks.                           |


### <a name="response-object"></a>Responsobject

Het object `context.res` (Response) heeft de volgende eigenschappen:

| Eigenschap  | Beschrijving                                               |
| --------- | --------------------------------------------------------- |
| _organen_    | Een object dat de hoofd tekst van het antwoord bevat.         |
| _koppen_ | Een object dat de antwoord headers bevat.             |
| _isRaw_   | Hiermee wordt aangegeven dat de opmaak voor het antwoord wordt overgeslagen.    |
| _hebben_  | De HTTP-status code van het antwoord.                     |

### <a name="accessing-the-request-and-response"></a>De aanvraag en het antwoord openen 

Wanneer u met HTTP-triggers werkt, kunt u op een aantal manieren toegang krijgen tot de HTTP-aanvraag-en-antwoord objecten:

+ **Van `req`-en `res` eigenschappen van het `context`-object.** Op deze manier kunt u het conventionele patroon gebruiken om toegang te krijgen tot HTTP-gegevens van het context object, in plaats van het volledige `context.bindings.name` patroon te gebruiken. In het volgende voor beeld ziet u hoe u toegang krijgt tot de `req`-en `res` objecten op de `context`:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Van de benoemde invoer-en uitvoer bindingen.** Op deze manier werken de HTTP-trigger en de bindingen hetzelfde als elke andere binding. In het volgende voor beeld wordt het object Response ingesteld met behulp van een benoemde `response` binding: 

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
+ **_[Alleen antwoord]_ Door `context.res.send(body?: any)`aan te roepen.** Er wordt een HTTP-antwoord gemaakt met invoer `body` als de antwoord tekst. `context.done()` wordt impliciet aangeroepen.

+ **_[Alleen antwoord]_ Door `context.done()`aan te roepen.** Een speciaal type HTTP-binding retourneert het antwoord dat is door gegeven aan de `context.done()` methode. De volgende HTTP-uitvoer binding definieert een `$return` uitvoer parameter:

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

Standaard controleert Azure Functions automatisch de belasting van uw toepassing en worden er indien nodig extra exemplaren van de host voor node. js gemaakt. Functies gebruiken ingebouwde (niet door de gebruiker te configureren) drempel waarden voor verschillende trigger typen om te bepalen wanneer instanties moeten worden toegevoegd, zoals de leeftijd van berichten en de grootte van de wachtrij voor Queue trigger. Zie [hoe het verbruik en de Premium-abonnementen werken](functions-scale.md#how-the-consumption-and-premium-plans-work)voor meer informatie.

Dit gedrag van schalen is voldoende voor veel node. js-toepassingen. Voor CPU-gebonden toepassingen kunt u de prestaties verder verbeteren door gebruik te maken van werk processen in meerdere talen.

Elk functions-exemplaar heeft standaard een werk proces met één taal. U kunt het aantal werk processen per host (Maxi maal 10) verhogen met behulp van de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) toepassings instelling. Azure Functions probeert vervolgens gelijktijdige functie aanroepen voor deze werk nemers gelijkmatig te verdelen. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die functies maakt wanneer uw toepassing wordt geschaald om aan de vraag te voldoen. 

## <a name="node-version"></a>Knooppunt versie

In de volgende tabel ziet u de huidige ondersteunde node. js-versies voor elke primaire versie van de functions-runtime, door het besturings systeem:

| Functie versie | Knooppunt versie (Windows) | Knooppunt versie (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (vergrendeld door de runtime) | N.v.t. |
| 2.x  | ~ 8<br/>~ 10 (aanbevolen)<br/>~ 12<sup>*</sup> | ~ 8 (aanbevolen)<br/>~ 10  |
| controleert | ~ 10<br/>~ 12 (aanbevolen)  | ~ 10<br/>~ 12 (aanbevolen) |

<sup>*</sup> Het knoop punt ~ 12 is momenteel toegestaan op versie 2. x van de functions-runtime. Voor de beste prestaties raden we u echter aan functions runtime versie 3. x met het knoop punt ~ 12 te gebruiken. 

U kunt de huidige versie bekijken die door de runtime wordt gebruikt door de bovenstaande app-instelling te controleren of door `process.version` af te drukken vanuit een functie. Richt de versie in Azure in door de WEBSITE_NODE_DEFAULT_VERSION [app-instelling](functions-how-to-use-azure-function-app-settings.md#settings) in te stellen op een ondersteunde versie van LTS, zoals `~10`.

## <a name="dependency-management"></a>Beheer van afhankelijkheden
Als u Community-bibliotheken in uw Java script-code wilt gebruiken, zoals in het onderstaande voor beeld wordt weer gegeven, moet u ervoor zorgen dat alle afhankelijkheden zijn geïnstalleerd op uw functie-app in Azure.

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
> U moet een `package.json`-bestand definiëren in de hoofdmap van uw functie-app. Als u het bestand definieert, kunnen alle functies in de app dezelfde pakketten in de cache delen, wat de beste prestaties biedt. Als er een versie conflict ontstaat, kunt u dit oplossen door een `package.json` bestand toe te voegen aan de map van een specifieke functie.  

Wanneer u functie-apps vanuit broncode beheer implementeert, wordt in de map van elk `package.json` bestand dat in uw opslag plaats aanwezig is, een `npm install` geactiveerd tijdens de implementatie. Maar wanneer u implementeert via de portal of CLI, moet u de pakketten hand matig installeren.

Er zijn twee manieren om pakketten te installeren op uw functie-app: 

### <a name="deploying-with-dependencies"></a>Implementeren met afhankelijkheden
1. Installeer alle vereiste pakketten lokaal door `npm install`uit te voeren.

2. Implementeer uw code en zorg ervoor dat de map `node_modules` is opgenomen in de implementatie. 


### <a name="using-kudu"></a>Kudu gebruiken
1. Ga naar `https://<function_app_name>.scm.azurewebsites.net`.

2. Klik op **debug Console** > **cmd**.

3. Ga naar `D:\home\site\wwwroot`en sleep het bestand Package. json naar de map **wwwroot** in het bovenste gedeelte van de pagina.  
    U kunt ook op andere manieren bestanden uploaden naar uw functie-app. Zie de [functie-app-bestanden bijwerken](functions-reference.md#fileupdate)voor meer informatie. 

4. Nadat het bestand Package. json is geüpload, voert u de `npm install` opdracht uit in de **kudu-console voor externe uitvoering**.  
    Met deze actie worden de pakketten gedownload die in het bestand Package. json zijn aangegeven en wordt de functie-app opnieuw gestart.

## <a name="environment-variables"></a>Omgevingsvariabelen

In functions worden [app-instellingen](functions-app-settings.md), zoals teken reeksen voor service verbindingen, weer gegeven als omgevings variabelen tijdens de uitvoering. U kunt deze instellingen openen met behulp van `process.env`, zoals hier wordt weer gegeven in de tweede en derde aanroepen naar `context.log()` waar de `AzureWebJobsStorage` en `WEBSITE_SITE_NAME` omgevings variabelen worden geregistreerd:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Wanneer u lokaal uitvoert, worden de app-instellingen gelezen uit het bestand [Local. settings. json](functions-run-local.md#local-settings-file) project.

## <a name="configure-function-entry-point"></a>Functie-ingangs punt configureren

De `function.json` eigenschappen `scriptFile` en `entryPoint` kunnen worden gebruikt voor het configureren van de locatie en de naam van de geëxporteerde functie. Deze eigenschappen kunnen belang rijk zijn wanneer uw Java script wordt transmaald.

### <a name="using-scriptfile"></a>`scriptFile` gebruiken

Een Java script-functie wordt standaard uitgevoerd vanuit `index.js`, een bestand met dezelfde bovenliggende map als de bijbehorende `function.json`.

`scriptFile` kan worden gebruikt om een mapstructuur te verkrijgen die eruitziet als in het volgende voor beeld:

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

De `function.json` voor `myNodeFunction` moet een `scriptFile` eigenschap bevatten die verwijst naar het bestand met de geëxporteerde functie om uit te voeren.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` gebruiken

In `scriptFile` (of `index.js`) moet een functie worden geëxporteerd met behulp van `module.exports` om te vinden en uit te voeren. De functie die wordt uitgevoerd wanneer de trigger wordt geactiveerd, is standaard de enige export vanuit dat bestand, de export met de naam `run`, of de export met de naam `index`.

Dit kan worden geconfigureerd met behulp van `entryPoint` in `function.json`, zoals in het volgende voor beeld:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

In functions v2. x, dat de para meter `this` in gebruikers functies ondersteunt, kan de functie code in het volgende voor beeld worden gebruikt:

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

In dit voor beeld is het belang rijk te weten dat er een object wordt geëxporteerd, maar er zijn geen garanties voor het behoud van de status tussen uitvoeringen.

## <a name="local-debugging"></a>Lokale fout opsporing

Wanneer een node. js-proces wordt gestart met de para meter `--inspect`, wordt geluisterd naar een client voor fout opsporing op de opgegeven poort. In Azure Functions 2. x kunt u argumenten opgeven die moeten worden door gegeven aan het node. js-proces dat uw code uitvoert door de omgevings variabele of app-instelling `languageWorkers:node:arguments = <args>`toe te voegen. 

Als u lokaal fouten wilt opsporen, voegt u `"languageWorkers:node:arguments": "--inspect=5858"` onder `Values` in het bestand [Local. settings. json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) toe en koppelt u een fout opsporingsprogramma aan poort 5858.

Als u fouten opspoort met behulp van VS code, wordt de para meter `--inspect` automatisch toegevoegd met behulp van de `port` waarde in het bestand Launch. json van het project.

In versie 1. x kan het instellen van `languageWorkers:node:arguments` niet worden uitgevoerd. De poort voor fout opsporing kan worden geselecteerd met de para meter [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) op Azure functions core tools.

## <a name="typescript"></a>TypeScript

Wanneer u versie 2. x van de functions runtime richt, hebben zowel [Azure functions voor Visual Studio code](functions-create-first-function-vs-code.md) als de [Azure functions core tools](functions-run-local.md) u functie-apps kunnen maken met behulp van een sjabloon die type script functie-app-projecten ondersteunt. De sjabloon genereert `package.json` en `tsconfig.json` project bestanden die het eenvoudiger maken om Java script-functies van type script-code te delen, uit te voeren en te publiceren met deze hulpprogram ma's.

Een gegenereerd `.funcignore` bestand wordt gebruikt om aan te geven welke bestanden worden uitgesloten wanneer een project wordt gepubliceerd naar Azure.  

Type script-bestanden (. TS) worden omgezet in Java script-bestanden (. js) in de uitvoer Directory `dist`. Type script-sjablonen gebruiken de [para meter`scriptFile`](#using-scriptfile) in `function.json` om de locatie van het overeenkomstige js-bestand in de map `dist` aan te geven. De uitvoer locatie wordt ingesteld door de sjabloon met behulp van `outDir` para meter in het `tsconfig.json`-bestand. Als u deze instelling of de naam van de map wijzigt, kan de runtime niet vinden welke code moet worden uitgevoerd.

> [!NOTE]
> Experimentele ondersteuning voor type script bestaat uit versie 1. x van de functions-runtime. Met de experimentele versie worden type script-bestanden omgezet in Java script-bestanden wanneer de functie wordt aangeroepen. In versie 2. x is deze experimentele ondersteuning vervangen door de methode die wordt gebruikt door het hulp programma dat transpilation voordat de host wordt geïnitialiseerd en tijdens het implementatie proces.

De manier waarop u lokaal een type script-project ontwikkelt en implementeert, is afhankelijk van uw ontwikkel programma.

### <a name="visual-studio-code"></a>Visual Studio Code

Met de [Azure functions voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) -extensie kunt u uw functies ontwikkelen met behulp van type script. De belangrijkste hulp middelen zijn vereist voor de uitbrei ding Azure Functions.

Als u een type script-functie-app in Visual Studio code wilt maken, kiest u `TypeScript` als taal wanneer u een functie-app maakt.

Wanneer u op **F5** drukt om de app lokaal uit te voeren, wordt transpilation uitgevoerd voordat de host (func. exe) is geïnitialiseerd. 

Wanneer u de functie-app in azure implementeert met behulp van de knop **implementeren in functie app...** , genereert de Azure functions extensie eerst een productie-app-kant van Java script-bestanden van de type script-bron bestanden.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Er zijn verschillende manieren waarop een type script-project verschilt van een Java script-project wanneer de kern Hulpprogramma's worden gebruikt.

#### <a name="create-project"></a>Project maken

Als u een type script-functie-app-project wilt maken met behulp van basis Hulpprogramma's, moet u de optie type script taal opgeven wanneer u de functie-app maakt. U kunt dit op een van de volgende manieren doen:

- Voer de `func init` opdracht uit, selecteer `node` als taal stack en selecteer vervolgens `typescript`.

- Voer de opdracht `func init --worker-runtime typescript` uit.

#### <a name="run-local"></a>Lokaal uitvoeren

Als u de code van de functie-app lokaal wilt uitvoeren met behulp van basis Hulpprogramma's, gebruikt u de volgende opdrachten in plaats van `func host start`: 

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

Voordat u de opdracht [`func azure functionapp publish`] gebruikt om te implementeren in azure, maakt u een productie-gereed build van Java script-bestanden van de type script-bron bestanden. 

Met de volgende opdrachten wordt uw type script-project voor bereid en gepubliceerd met kern Hulpprogramma's: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Vervang `<APP_NAME>` in deze opdracht door de naam van uw functie-app.

## <a name="considerations-for-javascript-functions"></a>Overwegingen voor Java script-functies

Wanneer u werkt met Java script-functies, moet u rekening houden met de overwegingen in de volgende secties.

### <a name="choose-single-vcpu-app-service-plans"></a>VCPU plannen voor eenmalige App Service kiezen

Wanneer u een functie-app maakt die gebruikmaakt van het App Service-abonnement, wordt u aangeraden een schema met één vCPU te selecteren in plaats van een plan met meerdere Vcpu's. Vandaag voeren functies java script-functies efficiënter uit op virtuele machines met één vCPU, en het gebruik van grotere Vm's produceert niet de verwachte prestatie verbeteringen. Als dat nodig is, kunt u hand matig uitschalen door meer VM-exemplaren met één vCPU toe te voegen of automatisch schalen in te scha kelen. Zie [aantal exemplaren hand matig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)voor meer informatie.

### <a name="cold-start"></a>Koude start

Bij het ontwikkelen van Azure Functions in het serverloze hosting model is koude start een werkelijkheid. *Koude start* verwijst naar het feit dat het starten van de functie-app voor de eerste keer na een periode van inactiviteit langer duurt. Voor Java script-functies met grote afhankelijkheids structuren met name kan koude start aanzienlijk zijn. Als u het koude start proces wilt versnellen, [voert u indien mogelijk uw functies als pakket bestand uit](run-functions-from-deployment-package.md) . Bij veel implementatie methoden wordt standaard het model voor uitvoeren vanaf pakket gebruikt, maar als u een grote koude start ondervindt die niet op deze manier wordt uitgevoerd, kan deze wijziging een aanzienlijke verbetering opleveren.

### <a name="connection-limits"></a>Verbindings limieten

Wanneer u een servicespecifieke client gebruikt in een Azure Functions-toepassing, moet u geen nieuwe client maken bij elke functie aanroep. Maak in plaats daarvan een enkele statische client in het globale bereik. Zie [verbindingen beheren in azure functions](manage-connections.md)voor meer informatie.

### <a name="use-async-and-await"></a>`async` en `await` gebruiken

Wanneer u Azure Functions in Java script schrijft, moet u code schrijven met behulp van de `async` en `await` tref woorden. Het schrijven van code met behulp van `async` en `await` in plaats van retour aanroepen of `.then` en `.catch` met beloftes helpt twee veelvoorkomende problemen te voor komen:
 - Niet-onderschepte uitzonde ringen veroorzaken waardoor [het node. js-proces vastloopt](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), waardoor de uitvoering van andere functies kan worden beïnvloed.
 - Onverwacht gedrag, zoals ontbrekende logboeken van context. log, veroorzaakt door asynchrone aanroepen die niet goed zijn gewacht.

In het onderstaande voor beeld wordt de asynchrone methode `fs.readFile` aangeroepen met een fout-eerste call back functie als de tweede para meter. Met deze code worden beide hierboven vermelde problemen veroorzaakt. Een uitzonde ring die niet expliciet is gevangen in het juiste bereik, heeft het hele proces vastlopen (probleem #1). Het aanroepen van `context.done()` buiten het bereik van de call back-functie betekent dat de functie aanroep kan eindigen voordat het bestand wordt gelezen (probleem #2 oplossen). In dit voor beeld roept `context.done()` te vroeg resultaten aan in ontbrekende logboek vermeldingen die beginnen met `Data from file:`.

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

Als u de sleutel woorden `async` en `await` gebruikt, kunt u beide fouten voor komen. U moet de functie node. js Utility gebruiken [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) de functies van fout eerste gebruik in te scha kelen in functies die kunnen worden teruggebeld.

In het onderstaande voor beeld mislukken alle niet-verwerkte uitzonde ringen die tijdens de uitvoering van de functie worden gegenereerd de afzonderlijke aanroep die een uitzonde ring heeft veroorzaakt. Het sleutel woord `await` houdt in dat de stappen na het `readFileAsync` alleen worden uitgevoerd nadat `readFile` is voltooid. Met `async` en `await`hoeft u de `context.done()` call back niet aan te roepen.

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

[' func Azure functionapp Publish ']: functions-run-local.md#project-file-deployment

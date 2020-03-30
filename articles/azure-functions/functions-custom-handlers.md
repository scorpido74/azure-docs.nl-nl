---
title: Aangepaste handlers voor Azure Functions (voorbeeld)
description: Leer Azure-functies te gebruiken met elke taal of runtime-versie.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479252"
---
# <a name="azure-functions-custom-handlers-preview"></a>Aangepaste handlers voor Azure Functions (voorbeeld)

Elke functie-app wordt uitgevoerd door een taalspecifieke handler. Hoewel Azure Functions standaard veel [taalhandlers](./supported-languages.md) ondersteunt, zijn er gevallen waarin u mogelijk extra controle wilt over de uitvoeringsomgeving van de app. Aangepaste handlers geven u deze extra controle.

Aangepaste handlers zijn lichtgewicht webservers die gebeurtenissen ontvangen van de functions-host. Elke taal die HTTP-primitieven ondersteunt, kan een aangepaste handler implementeren.

Aangepaste handlers zijn het meest geschikt voor situaties waarin u:

- Een functie-app implementeren in een taal die verder gaat dan de officieel ondersteunde talen
- Een functie-app implementeren in een taalversie of runtime die niet standaard wordt ondersteund
- Gedetailleerde controle hebben over de uitvoeringsomgeving van de app

Met aangepaste handlers worden alle [triggers en invoer- en uitvoerbindingen](./functions-triggers-bindings.md) ondersteund via [extensiebundels.](./functions-bindings-register.md)

## <a name="overview"></a>Overzicht

In het volgende diagram ziet u de relatie tussen de host functies en een webserver die is geïmplementeerd als een aangepaste handler.

![Overzicht van aangepaste handlers voor Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Gebeurtenissen activeren een aanvraag die naar de host Functies is verzonden. De gebeurtenis draagt ofwel een ruwe HTTP-payload (voor HTTP-geactiveerde functies zonder bindingen), of een payload die invoerbindende gegevens voor de functie bevat.
- De host functies proxies vervolgens het verzoek aan de webserver door de afgifte van een [aanvraag payload](#request-payload).
- De webserver voert de afzonderlijke functie uit en retourneert een [antwoordpayload](#response-payload) naar de host Functies.
- De functies host proxy's de reactie als een output bindende payload aan het doel.

Een Azure Functions-app die is geïmplementeerd als een aangepaste handler, moet de *bestanden host.json* en *function.json* configureren volgens een aantal conventies.

## <a name="application-structure"></a>Toepassingsstructuur

Als u een aangepaste handler wilt implementeren, hebt u de volgende aspecten van uw toepassing nodig:

- Een *host.json-bestand* aan de basis van uw app
- Een *function.json-bestand* voor elke functie (in een map die overeenkomt met de functienaam)
- Een opdracht, script of uitvoerbare, waarop een webserver wordt uitgevoerd

In het volgende diagram ziet u hoe deze bestanden er uitzien op het bestandssysteem voor een functie met de naam "order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuratie

De toepassing is geconfigureerd via het *host.json-bestand.* Dit bestand vertelt de host functies waar aanvragen moeten worden verzonden door te wijzen naar een webserver die HTTP-gebeurtenissen kan verwerken.

Een aangepaste handler wordt gedefinieerd door het *host.json-bestand* te configureren met `httpWorker` details over hoe u de webserver via de sectie uitvoeren.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

De `httpWorker` sectie verwijst naar een doel `defaultExecutablePath`zoals gedefinieerd door de . Het uitvoeringsdoel kan een opdracht zijn, uitvoerbaar of bestand waarin de webserver is geïmplementeerd.

Voor gescripte `defaultExecutablePath` apps wijst u naar de `defaultWorkerPath` runtime van de scripttaal en wijst naar de locatie van het scriptbestand. In het volgende voorbeeld ziet u hoe een JavaScript-app in Node.js is geconfigureerd als een aangepaste handler.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

U ook argumenten `arguments` doorgeven met behulp van de array:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Argumenten zijn nodig voor veel foutopsporingsinstellingen. Zie de sectie [Foutopsporing](#debugging) voor meer details.

> [!NOTE]
> Het *bestand host.json* moet zich op hetzelfde niveau in de mapstructuur bevinden als de draaiende webserver. Sommige talen en toolchains plaatsen dit bestand mogelijk niet standaard bij de toepassingsroot.

#### <a name="bindings-support"></a>Ondersteuning voor bindingen

Standaardtriggers en invoer- en uitvoerbindingen zijn beschikbaar door te verwijzen naar [extensiebundels](./functions-bindings-register.md) in uw *host.json-bestand.*

### <a name="function-metadata"></a>Functiemetagegevens

Bij gebruik met een aangepaste handler verschilt de inhoud van *function.json* niet van hoe u een functie onder een andere context zou definiëren. De enige vereiste is dat *function.json-bestanden* zich in een map moeten bevinden die is vernoemd naar de functienaam.

### <a name="request-payload"></a>Payload aanvragen

Het verzoek payload voor pure HTTP functies is de ruwe HTTP verzoek payload. Pure HTTP-functies worden gedefinieerd als functies zonder invoer- of uitvoerbindingen, die een HTTP-antwoord retourneren.

Elk ander type functie dat invoer, uitvoerbindingen of wordt geactiveerd via een andere gebeurtenisbron dan HTTP, heeft een aangepaste aanvraagpayload.

De volgende code vertegenwoordigt een payload van een voorbeeldaanvraag. De payload bevat een JSON `Data` structuur `Metadata`met twee leden: en .

Het `Data` lid bevat toetsen die overeenkomen met invoer- en triggernamen zoals gedefinieerd in de bindingenarray in het *function.json-bestand.*

Het `Metadata` lid bevat [metagegevens die zijn gegenereerd uit de gebeurtenisbron.](./functions-bindings-expressions-patterns.md#trigger-metadata)

Gezien de bindingen die zijn gedefinieerd in het volgende *function.json-bestand:*

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Een aanvraagpayload die vergelijkbaar is met dit voorbeeld wordt geretourneerd:

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>Reactiepayload

Per conventie worden functiereacties opgemaakt als sleutel-/waardeparen. Ondersteunde toetsen zijn:

| <nobr>Payload-toets</nobr>   | Gegevenstype | Opmerkingen                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Houdt responswaarden zoals gedefinieerd `bindings` door de array het *function.json-bestand.*<br /><br />Als een functie bijvoorbeeld is geconfigureerd met een blob-opslaguitvoerbinding met de naam 'blob', bevat deze een `Outputs` sleutel met de naam `blob`, die is ingesteld op de waarde van de blob. |
| `Logs`        | matrix     | Berichten worden weergegeven in de aanroeplogboeken voor functies.<br /><br />Wanneer u wordt uitgevoerd in Azure, worden berichten weergegeven in Toepassingsinzichten. |
| `ReturnValue` | tekenreeks    | Wordt gebruikt om een antwoord te `$return` geven wanneer een uitvoer is geconfigureerd zoals in het *function.json-bestand.* |

Zie het [voorbeeld voor een monsterpayload.](#bindings-implementation)

## <a name="examples"></a>Voorbeelden

Aangepaste handlers kunnen worden geïmplementeerd in elke taal die HTTP-gebeurtenissen ondersteunt. Hoewel Azure Functions [JavaScript en Node.js volledig ondersteunt,](./functions-reference-node.md)laten de volgende voorbeelden zien hoe u een aangepaste handler implementeert met JavaScript in Node.js ten behoeve van instructie.

> [!TIP]
> Hoewel het een handleiding is om te leren hoe je een aangepaste handler in andere talen implementeert, kunnen de voorbeelden die hier op Node.js worden weergegeven ook handig zijn als u een functie-app wilt uitvoeren in een niet-ondersteunde versie van Node.js.

## <a name="http-only-function"></a>Http-only, functie

In het volgende voorbeeld wordt uitgelegd hoe u een HTTP-geactiveerde functie configureert zonder extra bindingen of uitvoer. Het scenario dat in dit voorbeeld `http` wordt geïmplementeerd, bevat een functie met de naam a `GET` of `POST` .

Het volgende fragment geeft aan hoe een aanvraag voor de functie is samengesteld.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementatie

In een map met de naam *http*configureert het *bestand function.json* de functie HTTP-geactiveerd.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

De functie is geconfigureerd `GET` om `POST` beide en aanvragen te accepteren `res`en de resultaatwaarde wordt geleverd via een argument met de naam .

Aan de hoofdtekst van de app is het bestand *host.json* geconfigureerd `server.js` om Node.js uit te voeren en het bestand te gebruiken.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Het bestand file *server.js* implementeert een webserver en HTTP-functie.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

In dit voorbeeld wordt Express gebruikt om een webserver te maken voor het `FUNCTIONS_HTTPWORKER_PORT`afhandelen van HTTP-gebeurtenissen en is ingesteld om naar aanvragen te luisteren via de .

De functie wordt gedefinieerd op `/hello`het pad van . `GET`verzoeken worden afgehandeld door een eenvoudig `POST` JSON-object terug te `req.body`sturen en aanvragen hebben toegang tot de aanvraaginstantie via .

De route voor de `/hello` bestelfunctie `/api/hello` hier is en niet omdat de host Functies de aanvraag proxy's naar de aangepaste handler.

>[!NOTE]
>Het `FUNCTIONS_HTTPWORKER_PORT` is niet de openbare aangenomen poort die wordt gebruikt om de functie aan te roepen. Deze poort wordt gebruikt door de functiehost om de aangepaste handler aan te roepen.

## <a name="function-with-bindings"></a>Functie met bindingen

Het scenario dat in dit voorbeeld `order` wordt geïmplementeerd, bevat een functie met de naam die een `POST` functie accepteert met een payload die een productorder vertegenwoordigt. Als een order naar de functie wordt geplaatst, wordt een wachtrijopslagbericht gemaakt en wordt een HTTP-antwoord geretourneerd.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementatie

In een *map*met de naam volgorde configureert het *bestand function.json* de functie HTTP-triggered.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

Deze functie wordt gedefinieerd als een [HTTP-geactiveerde functie](./functions-bindings-http-webhook-trigger.md) die een [HTTP-antwoord retourneert](./functions-bindings-http-webhook-output.md) en een [wachtrijopslagbericht](./functions-bindings-storage-queue-output.md) retourneert.

Aan de hoofdtekst van de app is het bestand *host.json* geconfigureerd `server.js` om Node.js uit te voeren en het bestand te gebruiken.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Het bestand file *server.js* implementeert een webserver en HTTP-functie.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

In dit voorbeeld wordt Express gebruikt om een webserver te maken voor het `FUNCTIONS_HTTPWORKER_PORT`afhandelen van HTTP-gebeurtenissen en is ingesteld om naar aanvragen te luisteren via de .

De functie wordt gedefinieerd op `/order` het pad van .  De route voor de `/order` bestelfunctie `/api/order` hier is en niet omdat de host Functies de aanvraag proxy's naar de aangepaste handler.

Als `POST` aanvragen naar deze functie worden verzonden, worden gegevens via een paar punten weergegeven:

- De aanvraaginstantie is beschikbaar via`req.body`
- De gegevens die naar de functie worden geplaatst, zijn beschikbaar via`req.body.Data.req.Body`

Het antwoord van de functie is opgemaakt in `Outputs` een sleutel/waardepaar waarbij het lid een JSON-waarde heeft waarbij de toetsen overeenkomen met de uitvoer zoals gedefinieerd in het *function.json-bestand.*

Door `message` het bericht dat uit de aanvraag `res` is binnengekomen, in te stellen en met het verwachte HTTP-antwoord, wordt in deze functie een bericht naar Wachtrijopslag uitgevoerd en wordt een HTTP-antwoord geretourneerd.

## <a name="debugging"></a>Foutopsporing

Als u de aangepaste handler-app Functies wilt debuggen, moet u argumenten toevoegen die geschikt zijn voor de taal en de runtime om foutopsporing in te schakelen.

Als u bijvoorbeeld een Node.js-toepassing `--inspect` wilt debuggen, wordt de vlag doorgegeven als argument in het bestand *host.json.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> De foutopsporingsconfiguratie maakt deel uit van uw *host.json-bestand,* wat betekent dat u de argumenten mogelijk moet verwijderen voordat u naar productie wordt geïmplementeerd.

Met deze configuratie u het hostproces van de functie starten met de volgende opdracht:

```bash
func host start
```

Zodra het proces is gestart, u een debugger en hit breakpoints.

### <a name="visual-studio-code"></a>Visual Studio Code

Het volgende voorbeeld is een voorbeeldconfiguratie die laat zien hoe u uw *launch.json-bestand* instellen om uw app te verbinden met de foutopsporing van de Visual Studio Code.

Dit voorbeeld is voor Node.js, dus het kan zijn dat u dit voorbeeld moet wijzigen voor andere talen of looptijden.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Implementeren

Een aangepaste handler kan worden geïmplementeerd in bijna elke Azure Functions-hostingoptie (zie [beperkingen).](#restrictions) Als uw handler aangepaste afhankelijkheden vereist (zoals een taalruntime), moet u mogelijk een [aangepaste container](./functions-create-function-linux-custom-image.md)gebruiken.

## <a name="restrictions"></a>Beperkingen

- Aangepaste handlers worden niet ondersteund in Linux-consumptieplannen.
- De webserver moet binnen 60 seconden starten.

## <a name="samples"></a>Voorbeelden

Raadpleeg de [aangepaste handlervoorbeelden GitHub repo](https://github.com/Azure-Samples/functions-custom-handlers) voor voorbeelden van het implementeren van functies in verschillende talen.

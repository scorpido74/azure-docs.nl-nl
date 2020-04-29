---
title: Aangepaste handlers Azure Functions (preview-versie)
description: Meer informatie over het gebruik van Azure Functions met elke taal of runtime versie.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479252"
---
# <a name="azure-functions-custom-handlers-preview"></a>Aangepaste handlers Azure Functions (preview-versie)

Elke functions-app wordt uitgevoerd door een taalspecifieke handler. Hoewel Azure Functions veel [taal ingangen](./supported-languages.md) ondersteunt, zijn er situaties waarin u mogelijk extra controle wilt over de app Execution Environment. Aangepaste handlers bieden u dit extra besturings element.

Aangepaste handlers zijn Lightweight webservers die gebeurtenissen ontvangen van de host functions. Elke taal die HTTP-primitieven ondersteunt, kan een aangepaste handler implementeren.

Aangepaste handlers zijn het meest geschikt voor situaties waar u het volgende wilt doen:

- Implementeer een functions-app in een andere taal dan de officieel ondersteunde talen
- Een functions-app implementeren in een taal versie of-runtime die standaard niet wordt ondersteund
- Nauw keurige controle hebben over de uitvoerings omgeving van de app

Met aangepaste handlers worden alle [Triggers en invoer-en uitvoer bindingen](./functions-triggers-bindings.md) ondersteund via [uitbreidings bundels](./functions-bindings-register.md).

## <a name="overview"></a>Overzicht

In het volgende diagram ziet u de relatie tussen de host functions en een webserver die is geïmplementeerd als een aangepaste handler.

![Overzicht van Azure Functions aangepaste handler](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Gebeurtenissen activeren een aanvraag die wordt verzonden naar de host functions. De gebeurtenis voert een onbewerkte HTTP-nettolading (voor functies die met HTTP worden geactiveerd zonder bindingen) of een Payload die invoer bindings gegevens voor de functie bevat.
- De host functions stuurt vervolgens de aanvraag door naar de webserver door een [aanvraag lading](#request-payload)uit te geven.
- De webserver voert de afzonderlijke functie uit en retourneert een nettolading van de [reactie](#response-payload) op de host functions.
- De functions host het antwoord als een nettolading van de uitvoer binding aan het doel.

Een Azure Functions-app die als aangepaste handler is geïmplementeerd, moet de *host. json* -en *Function. json* -bestanden configureren volgens een aantal conventies.

## <a name="application-structure"></a>Toepassings structuur

Als u een aangepaste handler wilt implementeren, hebt u de volgende aspecten nodig voor uw toepassing:

- Een *host. json* -bestand in de hoofdmap van uw app
- Een *Function. json* -bestand voor elke functie (in een map die overeenkomt met de naam van de functie)
- Een opdracht, script of uitvoerbaar bestand, waarmee een webserver wordt uitgevoerd

In het volgende diagram ziet u hoe deze bestanden eruitzien op het bestands systeem voor een functie met de naam ' order '.

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuratie

De toepassing wordt geconfigureerd via het bestand *host. json* . Dit bestand vertelt de functions-host waar aanvragen worden verzonden door te verwijzen naar een webserver die HTTP-gebeurtenissen kan verwerken.

Er wordt een aangepaste handler gedefinieerd door het bestand *host. json* te configureren met informatie over het uitvoeren van de webserver `httpWorker` via de sectie.

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

De `httpWorker` sectie verwijst naar een doel zoals gedefinieerd door de `defaultExecutablePath`. Het doel van de uitvoering kan een opdracht, een uitvoerbaar bestand of een file zijn waarin de webserver is geïmplementeerd.

Voor scripted apps `defaultExecutablePath` verwijst naar de runtime van de script taal en `defaultWorkerPath` verwijst naar de locatie van het script bestand. In het volgende voor beeld ziet u hoe een Java script-app in node. js wordt geconfigureerd als een aangepaste handler.

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

U kunt ook argumenten door geven met `arguments` behulp van de matrix:

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

Er zijn argumenten nodig voor een groot aantal instellingen voor fout opsporing. Zie de sectie [fout opsporing](#debugging) voor meer details.

> [!NOTE]
> Het *JSON* -bestand van de host moet zich op hetzelfde niveau bein de mapstructuur als de actieve webserver. In sommige talen en toolchains wordt dit bestand niet standaard op de hoofdmap van de toepassing geplaatst.

#### <a name="bindings-support"></a>Ondersteuning voor bindingen

Standaard triggers samen met invoer-en uitvoer bindingen zijn beschikbaar door te verwijzen naar [uitbreidings bundels](./functions-bindings-register.md) in het JSON-bestand van de *host* .

### <a name="function-metadata"></a>Functie-meta gegevens

Wanneer u gebruikt met een aangepaste handler, is de *functie. json* -inhoud niet anders dan hoe u een functie onder elke andere context definieert. De enige vereiste is die *functie. json* -bestanden moeten zich in een map met de naam bevinden die overeenkomt met de functie naam.

### <a name="request-payload"></a>Payload aanvragen

De aanvraag lading voor zuivere HTTP-functies is de onbewerkte HTTP-aanvraag nettolading. Zuivere HTTP-functies worden gedefinieerd als functions zonder invoer-of uitvoer bindingen, die een HTTP-antwoord retour neren.

Elk ander type functie dat ofwel invoer, uitvoer bindingen of wordt geactiveerd via een andere gebeurtenis bron dan HTTP, heeft een aangepaste aanvraag lading.

De volgende code vertegenwoordigt een nettolading van de voorbeeld aanvraag. De payload bevat een JSON-structuur met twee leden `Data` : `Metadata`en.

Het `Data` lid bevat sleutels die overeenkomen met de namen van de invoer en de trigger, zoals gedefinieerd in de bindingen matrix in het bestand *Function. json* .

Het `Metadata` lid bevat [meta gegevens die zijn gegenereerd op basis van de bron van de gebeurtenis](./functions-bindings-expressions-patterns.md#trigger-metadata).

Op basis van de bindingen die zijn gedefinieerd in het volgende *Function. json* -bestand:

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

Een aanvraag lading vergelijkbaar met dit voor beeld wordt geretourneerd:

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

### <a name="response-payload"></a>Nettolading van reactie

Per Conventie worden functie reacties opgemaakt als sleutel/waarde-paren. Ondersteunde sleutels zijn onder andere:

| <nobr>Payload-sleutel</nobr>   | Gegevenstype | Opmerkingen                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Bevat reactie waarden zoals gedefinieerd door de `bindings` matrix het bestand *Function. json* .<br /><br />Als een functie bijvoorbeeld is geconfigureerd met een Blob Storage-uitvoer binding met de naam ' BLOB ', `Outputs` dan bevat een sleutel `blob`met de naam, die is ingesteld op de waarde van de blob. |
| `Logs`        | matrix     | Berichten worden weer gegeven in de functie Logboeken van functies.<br /><br />Bij het uitvoeren in Azure worden berichten weer gegeven in Application Insights. |
| `ReturnValue` | tekenreeks    | Wordt gebruikt om een reactie te geven wanneer een uitvoer is `$return` geconfigureerd als in het bestand *Function. json* . |

Zie het [voor beeld voor een nettolading van een](#bindings-implementation)voor beeld.

## <a name="examples"></a>Voorbeelden

Aangepaste handlers kunnen worden geïmplementeerd in elke taal waarin HTTP-gebeurtenissen worden ondersteund. Hoewel Azure Functions [Java script en node. js volledig ondersteunt](./functions-reference-node.md), ziet u in de volgende voor beelden hoe u een aangepaste handler kunt implementeren met behulp van Java script in node. js.

> [!TIP]
> Hoewel u een hand leiding hebt voor het implementeren van een aangepaste handler in andere talen, kunnen de node. js-voor beelden die hier worden weer gegeven, ook nuttig zijn als u een functions-app wilt uitvoeren in een niet-ondersteunde versie van node. js.

## <a name="http-only-function"></a>Alleen HTTP-functie

In het volgende voor beeld ziet u hoe u een door HTTP geactiveerde functie kunt configureren zonder extra bindingen of uitvoer. Het scenario dat in dit voor beeld wordt geïmplementeerd, `http` bevat een functie `GET` met `POST` de naam die een of accepteert.

Het volgende code fragment vertegenwoordigt hoe een aanvraag voor de functie is samengesteld.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementatie

In een map met de naam *http*, wordt met het bestand *Function. json* de door http geactiveerde functie geconfigureerd.

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

De functie is geconfigureerd om zowel `GET` als `POST` -aanvragen te accepteren en de resultaat waarde wordt opgegeven via een `res`argument met de naam.

In de hoofdmap van de app is het bestand *host. json* geconfigureerd om node. js uit te voeren en het `server.js` bestand te laten wijzen.

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

Het bestand *server. js* -bestand implementeert een webserver en http-functie.

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

In dit voor beeld wordt Express gebruikt om een webserver te maken voor het afhandelen van HTTP-gebeurtenissen en wordt ingesteld `FUNCTIONS_HTTPWORKER_PORT`om te Luis teren naar aanvragen via de.

De functie wordt gedefinieerd in het pad van `/hello`. `GET`aanvragen worden verwerkt door een eenvoudig JSON-object te retour `POST` neren en aanvragen hebben toegang tot de hoofd `req.body`tekst van de aanvraag via.

De route voor de functie order is `/hello` hier niet `/api/hello` mogelijk omdat de host functions de aanvraag naar de aangepaste handler in de proxy verstuurt.

>[!NOTE]
>De `FUNCTIONS_HTTPWORKER_PORT` is niet de open bare poort die wordt gebruikt om de functie aan te roepen. Deze poort wordt gebruikt door de host functions om de aangepaste handler aan te roepen.

## <a name="function-with-bindings"></a>Functie met bindingen

Het scenario dat in dit voor beeld wordt geïmplementeerd, `order` bevat een functie `POST` met de naam die een-Payload accepteert die een product order vertegenwoordigt. Als een order wordt gepost naar de functie, wordt een Queue Storage bericht gemaakt en wordt een HTTP-antwoord geretourneerd.

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

In een map met de naam *order*, wordt met het bestand *Function. json* de door http geactiveerde functie geconfigureerd.

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

Deze functie is gedefinieerd als een [http-geactiveerde functie](./functions-bindings-http-webhook-trigger.md) die een [http-antwoord](./functions-bindings-http-webhook-output.md) retourneert en een [wachtrij-opslag](./functions-bindings-storage-queue-output.md) bericht uitvoert.

In de hoofdmap van de app is het bestand *host. json* geconfigureerd om node. js uit te voeren en het `server.js` bestand te laten wijzen.

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

Het bestand *server. js* -bestand implementeert een webserver en http-functie.

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

In dit voor beeld wordt Express gebruikt om een webserver te maken voor het afhandelen van HTTP-gebeurtenissen en wordt ingesteld `FUNCTIONS_HTTPWORKER_PORT`om te Luis teren naar aanvragen via de.

De functie wordt gedefinieerd in het pad van `/order` .  De route voor de functie order is `/order` hier niet `/api/order` mogelijk omdat de host functions de aanvraag naar de aangepaste handler in de proxy verstuurt.

Wanneer `POST` er aanvragen worden verzonden naar deze functie, worden gegevens via een paar punten weer gegeven:

- De aanvraag tekst is beschikbaar via`req.body`
- De gegevens die naar de functie worden gepost, zijn beschikbaar via`req.body.Data.req.Body`

De reactie van de functie is ingedeeld in een sleutel/waarde-paar `Outputs` waarbij het lid een JSON-waarde bevat waarin de sleutels overeenkomen met de uitvoer zoals gedefinieerd in het bestand *Function. json* .

Als de `message` instelling gelijk is aan het bericht dat is ontvangen van de aanvraag `res` en aan het verwachte HTTP-antwoord, voert deze functie een bericht uit dat Queue Storage en wordt een HTTP-antwoord geretourneerd.

## <a name="debugging"></a>Foutopsporing

Als u fouten wilt opsporen in uw aangepaste handler-app voor functies, moet u argumenten toevoegen die geschikt zijn voor de taal en runtime om fout opsporing in te scha kelen.

Als u bijvoorbeeld fouten wilt opsporen in een node. js `--inspect` -toepassing, wordt de vlag door gegeven als een argument in het bestand *host. json* .

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
> De configuratie van fout opsporing maakt deel uit van uw *host. json* -bestand, wat betekent dat u de enige argumenten moet verwijderen voordat u de productie implementeert.

Met deze configuratie kunt u het hostproces van de functie starten met de volgende opdracht:

```bash
func host start
```

Zodra het proces is gestart, kunt u een debugger en bezoekers-onderbrekings punten koppelen.

### <a name="visual-studio-code"></a>Visual Studio Code

In het volgende voor beeld ziet u hoe u het bestand *Launch. json* kunt instellen om uw app te verbinden met het fout opsporingsprogramma van Visual Studio code.

Dit voor beeld is voor node. js, dus u moet dit voor beeld wellicht aanpassen voor andere talen of Runtimes.

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

## <a name="deploying"></a>Installeert

Een aangepaste handler kan naar bijna elke Azure Functions Hosting optie worden geïmplementeerd (Zie [beperkingen](#restrictions)). Als voor uw handler aangepaste afhankelijkheden (zoals een language runtime) zijn vereist, moet u mogelijk een [aangepaste container](./functions-create-function-linux-custom-image.md)gebruiken.

## <a name="restrictions"></a>Beperkingen

- Aangepaste handlers worden niet ondersteund in Linux-verbruiks abonnementen.
- De webserver moet binnen 60 seconden beginnen.

## <a name="samples"></a>Voorbeelden

Raadpleeg de aangepaste handler-voor beelden [github opslag plaats](https://github.com/Azure-Samples/functions-custom-handlers) voor voor beelden van hoe functies in verschillende talen kunnen worden geïmplementeerd.

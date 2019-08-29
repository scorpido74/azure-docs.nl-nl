---
title: HTTP-Api's in Durable Functions-Azure
description: Meer informatie over het implementeren van HTTP-Api's in de Durable Functions-extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087273"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP-Api's in Durable Functions (Azure Functions)

De uitbrei ding duurzame taak bevat een reeks HTTP-Api's die kunnen worden gebruikt om de volgende taken uit te voeren:

* De status van een Orchestration-exemplaar ophalen.
* Een gebeurtenis verzenden naar een Waiting-instantie.
* Een actief Orchestration-exemplaar beëindigen.

Elk van deze HTTP-Api's is een webhook-bewerking die rechtstreeks door de extensie van de duurzame taak wordt verwerkt. Ze zijn niet specifiek voor een functie in de functie-app.

> [!NOTE]
> Deze bewerkingen kunnen ook rechtstreeks worden aangeroepen met behulp van de instance Management-Api's in de [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klasse. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

## <a name="http-api-url-discovery"></a>URL-detectie van HTTP API

De klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) toont een [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) -API die kan worden gebruikt voor het genereren van een nettolading van http-antwoorden die koppelingen bevat naar alle ondersteunde bewerkingen. Hier volgt een voor beeld van een HTTP-activerings functie die laat zien hoe u deze API gebruikt:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

In deze voorbeeld functies worden de volgende gegevens van de JSON-respons gegenereerd. Het gegevens type van alle velden is `string`.

| Veld                   |Description                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |De ID van het Orchestration-exemplaar. |
| **`statusQueryGetUri`**     |De status-URL van het Orchestrator-exemplaar. |
| **`sendEventPostUri`**      |De URL van de functie ' raise ' van het Orchestrator-exemplaar. |
| **`terminatePostUri`**      |De ' Terminate ' URL van het Orchestrator-exemplaar. |
| **`purgeHistoryDeleteUri`** |De URL voor het verwijderen van de geschiedenis van het Orchestrator-exemplaar. |
| **`rewindPostUri`**         |evaluatie De ' rewinde ' URL van het Orchestrator-exemplaar. |

Hier volgt een voor beeld van een antwoord:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> De indeling van de webhook-Url's kan verschillen, afhankelijk van welke versie van de Azure Functions-host u uitvoert. Het bovenstaande voor beeld is voor de host Azure Functions 2. x.

## <a name="async-operation-tracking"></a>Asynchrone bewerkingen bijhouden

Het HTTP-antwoord dat eerder is vermeld, is ontworpen om te helpen met het implementeren van langlopende HTTP async-Api's met Durable Functions. Dit wordt soms ook wel het polling Consumer- *patroon*genoemd. De client/server-stroom werkt als volgt:

1. De client geeft een HTTP-aanvraag voor het starten van een langlopend proces, zoals een Orchestrator-functie.
2. De http-trigger van het doel retourneert een http 202 `Location` -antwoord met `statusQueryGetUri` een header met de waarde.
3. De client pollt de URL in de `Location` koptekst. Er worden nog http 202-antwoorden weer geven `Location` met een header.
4. Wanneer het exemplaar is voltooid (of mislukt), retourneert het eind punt `Location` in de header HTTP 200.

Met dit protocol kunnen langlopende processen worden gecooerdineerd met externe clients of services die ondersteuning bieden voor het pollen van `Location` een http-eind punt en het volgen van de header. De fundamentele onderdelen zijn al ingebouwd in de Durable Functions HTTP-Api's.

> [!NOTE]
> Standaard worden alle op HTTP gebaseerde acties die worden geboden door [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) het standaard asynchrone bewerkings patroon ondersteunen. Deze mogelijkheid maakt het mogelijk een langdurige, duurzame functie in te sluiten als onderdeel van een Logic Apps werk stroom. Meer informatie over Logic Apps ondersteuning voor asynchrone HTTP-patronen vindt u in de [Azure Logic apps werk stroom acties en triggers-documentatie](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP API-verwijzing

Alle HTTP-Api's die door de extensie worden geïmplementeerd, hebben de volgende para meters. Het gegevens type van alle para meters is `string`.

| Parameter        | Parametertype  | Description |
|------------------|-----------------|-------------|
| **`taskHub`**    | Querytekenreeks    | De naam van de [taak hub](durable-functions-task-hubs.md). Indien niet opgegeven, wordt ervan uitgegaan dat de naam van de taak-hub van de huidige functie-app wordt gebruikt. |
| **`connection`** | Querytekenreeks    | De **naam** van de Connection String voor het opslag account. Als deze niet wordt opgegeven, wordt aangenomen dat de standaard connection string voor de functie-app wordt gebruikt. |
| **`systemKey`**  | Querytekenreeks    | De autorisatie sleutel die is vereist om de API aan te roepen. |

`systemKey`is een autorisatie sleutel die automatisch door de Azure Functions-host wordt gegenereerd. Het geeft specifiek toegang tot de duurzame Api's voor taak uitbreidingen en kan op dezelfde manier worden beheerd als [andere autorisatie sleutels](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). De eenvoudigste manier om de `systemKey` waarde te detecteren is met behulp van de `CreateCheckStatusResponse` eerder vermelde API.

In de volgende secties worden de specifieke HTTP-Api's beschreven die worden ondersteund door de uitbrei ding en worden voor beelden gegeven van hoe ze kunnen worden gebruikt.

### <a name="get-instance-status"></a>Status van exemplaar ophalen

Hiermee wordt de status van een opgegeven Orchestrator-exemplaar opgehaald.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                   | Parametertype  | Description |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | De ID van het Orchestration-exemplaar. |
| **`showInput`**         | Querytekenreeks    | Optionele para meter. Als deze optie `false`is ingesteld op, wordt de functie-invoer niet opgenomen in de nettolading van het antwoord.|
| **`showHistory`**       | Querytekenreeks    | Optionele para meter. Als deze is `true`ingesteld op, wordt de geschiedenis van de Orchestration-uitvoering opgenomen in de nettolading van de reactie.|
| **`showHistoryOutput`** | Querytekenreeks    | Optionele para meter. Als deze optie `true`is ingesteld op, worden de functie-uitvoer opgenomen in de geschiedenis van de Orchestration-uitvoering.|
| **`createdTimeFrom`**   | Querytekenreeks    | Optionele para meter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijds tempel gefilterd.|
| **`createdTimeTo`**     | Querytekenreeks    | Optionele para meter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijds tempel gefilterd.|
| **`runtimeStatus`**     | Querytekenreeks    | Optionele para meter. Hiermee wordt indien opgegeven de lijst met geretourneerde instanties gefilterd op basis van de runtime status. Zie het onderwerp [query's in instanties](durable-functions-instance-management.md) voor een lijst met mogelijke runtime status waarden. |

#### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 200 (OK)** : Het opgegeven exemplaar heeft de status voltooid.
* **HTTP 202 (geaccepteerd)** : Het opgegeven exemplaar wordt uitgevoerd.
* **HTTP 400 (ongeldige aanvraag)** : Het opgegeven exemplaar is mislukt of beëindigd.
* **HTTP 404 (niet gevonden)** : Het opgegeven exemplaar bestaat niet of is niet gestart.
* **HTTP 500 (interne server fout)** : Het opgegeven exemplaar is mislukt vanwege een onverwerkte uitzonde ring.

De nettolading van de reactie voor de **http 200-** en **http 202** -CASEs is een JSON-object met de volgende velden:

| Veld                 | Gegevenstype | Description |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | De runtime status van het exemplaar. Waarden zijn onder andere *actief*, *in behandeling*, *mislukt*, *geannuleerd*, *beëindigd*, *voltooid*. |
| **`input`**           | JSON      | De JSON-gegevens die worden gebruikt voor het initialiseren van het exemplaar. Dit veld is `null` als de `showInput` query teken reeks parameter is ingesteld `false`op.|
| **`customStatus`**    | JSON      | De JSON-gegevens die worden gebruikt voor de aangepaste indelings status. Dit veld is `null` niet ingesteld. |
| **`output`**          | JSON      | De JSON-uitvoer van het exemplaar. Dit veld is `null` een niet-voltooide status van het exemplaar. |
| **`createdTime`**     | string    | Het tijdstip waarop het exemplaar is gemaakt. Maakt gebruik van ISO 8601 Extended-notatie. |
| **`lastUpdatedTime`** | string    | Het tijdstip waarop het exemplaar het laatst is bewaard. Maakt gebruik van ISO 8601 Extended-notatie. |
| **`historyEvents`**   | JSON      | Een JSON-matrix met de geschiedenis van de Orchestration-uitvoering. Dit veld is `null` tenzij de `showHistory` query teken reeks parameter is ingesteld `true`op. |

Hier volgt een voor beeld van een nettolading met de geschiedenis van de Orchestration-uitvoering en uitvoer van de activiteit (opgemaakt voor de Lees baarheid):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Het **http 202-** antwoord bevat ook een **locatie** antwoord header die verwijst naar dezelfde URL als `statusQueryGetUri` het eerder genoemde veld.

### <a name="get-all-instances-status"></a>Status van alle instanties ophalen

U kunt ook een query uitvoeren op de status van alle instanties `instanceId` door de uit de aanvraag ' exemplaar status ophalen ' te verwijderen. In dit geval zijn de basis parameters hetzelfde als de Get-exemplaar status. Query reeks parameters voor filteren worden ook ondersteund.

Een ding die u moet onthouden `connection` is `code` dat en is optioneel. Als u anonieme verificatie hebt voor de functie, is code niet vereist.
Als u geen andere opslag connection string wilt gebruiken dan gedefinieerd in de app-instelling voor AzureWebJobsStorage, kunt u de teken reeks parameter voor de verbindings query veilig negeren.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                   | Parametertype  | Description |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | De ID van het Orchestration-exemplaar. |
| **`showInput`**         | Querytekenreeks    | Optionele para meter. Als deze optie `false`is ingesteld op, wordt de functie-invoer niet opgenomen in de nettolading van het antwoord.|
| **`showHistory`**       | Querytekenreeks    | Optionele para meter. Als deze is `true`ingesteld op, wordt de geschiedenis van de Orchestration-uitvoering opgenomen in de nettolading van de reactie.|
| **`showHistoryOutput`** | Querytekenreeks    | Optionele para meter. Als deze optie `true`is ingesteld op, worden de functie-uitvoer opgenomen in de geschiedenis van de Orchestration-uitvoering.|
| **`createdTimeFrom`**   | Querytekenreeks    | Optionele para meter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijds tempel gefilterd.|
| **`createdTimeTo`**     | Querytekenreeks    | Optionele para meter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijds tempel gefilterd.|
| **`runtimeStatus`**     | Querytekenreeks    | Optionele para meter. Hiermee wordt indien opgegeven de lijst met geretourneerde instanties gefilterd op basis van de runtime status. Zie het onderwerp [query's in instanties](durable-functions-instance-management.md) voor een lijst met mogelijke runtime status waarden. |
| **`top`**               | Querytekenreeks    | Optionele para meter. Hiermee wordt het aantal exemplaren dat door de query wordt geretourneerd, beperkt. |

#### <a name="response"></a>Antwoord

Hier volgt een voor beeld van reactie payloads, inclusief de indelings status (opgemaakt voor de Lees baarheid):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Deze bewerking kan erg duur zijn in Azure Storage I/O als er veel rijen in de tabel instanties zijn. Meer informatie over de exemplaar tabel vindt u in de documentatie over [prestaties en schaal baarheid in Durable functions (Azure functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Als er meer resultaten bestaan, wordt in de reactie header een vervolg token geretourneerd.  De naam van de koptekst is `x-ms-continuation-token`.

Als u de vervolg token waarde in de volgende aanvraag header instelt, kunt u de volgende pagina met resultaten ophalen. Dit is ook `x-ms-continuation-token`de naam van de aanvraag header.

### <a name="purge-single-instance-history"></a>Geschiedenis van één exemplaar opschonen

Hiermee verwijdert u de geschiedenis en gerelateerde artefacten voor een opgegeven Orchestration-exemplaar.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld             | Parametertype  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |

#### <a name="response"></a>Antwoord

De volgende HTTP-status code waarden kunnen worden geretourneerd.

* **HTTP 200 (OK)** : De exemplaar geschiedenis is verwijderd.
* **HTTP 404 (niet gevonden)** : Het opgegeven exemplaar bestaat niet.

De nettolading van de reactie voor de **HTTP 200-** case is een JSON-object met het volgende veld:

| Veld                  | Gegevenstype | Description |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Het aantal verwijderde exemplaren. Voor het geval van één exemplaar moet deze waarde altijd zijn `1`. |

Hier volgt een voor beeld van een Payload-reactie (opgemaakt voor de Lees baarheid):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Geschiedenis van meerdere exemplaren opschonen

U kunt ook de geschiedenis en gerelateerde artefacten voor meerdere exemplaren binnen een taak hub verwijderen door de `{instanceId}` uit de aanvraag verwijderen van één exemplaar van de geschiedenis te verwijderen. Als u de instantie geschiedenis selectief wilt opschonen, gebruikt u dezelfde filters die in de aanvraag ' alle instanties ophalen ' worden beschreven.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                 | Parametertype  | Description |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Querytekenreeks    | Hiermee wordt de lijst met verwijderde exemplaren gefilterd die zijn gemaakt op of na de opgegeven ISO8601-tijds tempel.|
| **`createdTimeTo`**   | Querytekenreeks    | Optionele para meter. Hiermee wordt de lijst met verwijderde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijds tempel gefilterd.|
| **`runtimeStatus`**   | Querytekenreeks    | Optionele para meter. Hiermee wordt indien opgegeven de lijst met verwijderde exemplaren gefilterd op basis van de runtime status. Zie het onderwerp [query's in instanties](durable-functions-instance-management.md) voor een lijst met mogelijke runtime status waarden. |

> [!NOTE]
> Deze bewerking kan erg kostbaar zijn in termen van Azure Storage I/O als er veel rijen in de tabellen instanties en/of geschiedenis zijn. Meer informatie over deze tabellen vindt u in de documentatie over [prestaties en schaal baarheid in Durable functions (Azure functions)](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Antwoord

De volgende HTTP-status code waarden kunnen worden geretourneerd.

* **HTTP 200 (OK)** : De exemplaar geschiedenis is verwijderd.
* **HTTP 404 (niet gevonden)** : Er zijn geen exemplaren gevonden die overeenkomen met de filter expressie.

De nettolading van de reactie voor de **HTTP 200-** case is een JSON-object met het volgende veld:

| Veld                   | Gegevenstype | Description |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Het aantal verwijderde exemplaren. |

Hier volgt een voor beeld van een Payload-reactie (opgemaakt voor de Lees baarheid):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Gebeurtenis verhogen

Hiermee verzendt u een bericht over gebeurtenis meldingen naar een actief Orchestration-exemplaar.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld             | Parametertype  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |
| **`eventName`**   | URL             | De naam van de gebeurtenis waarop het doel exemplaar van de Orchestrator-instantie wacht. |
| **`{content}`**   | Inhoud van aanvraag | De nettolading van de JSON-indeling. |

#### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)** : De verhoogde gebeurtenis is geaccepteerd voor verwerking.
* **HTTP 400 (ongeldige aanvraag)** : De inhoud van de aanvraag is niet `application/json` van het type of is geen geldige JSON.
* **HTTP 404 (niet gevonden)** : Het opgegeven exemplaar is niet gevonden.
* **HTTP 410 (verdwenen)** : Het opgegeven exemplaar is voltooid of mislukt en kan geen geactiveerde gebeurtenissen verwerken.

Hier volgt een voorbeeld aanvraag waarmee de JSON-teken `"incr"` reeks wordt verzonden naar een instantie die wacht op een gebeurtenis **bewerking**met de naam:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

De antwoorden voor deze API bevatten geen inhoud.

### <a name="terminate-instance"></a>Exemplaar beëindigen

Hiermee beëindigt u een actief Orchestration-exemplaar.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meter.

| Veld             | Parametertype  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |
| **`reason`**      | Querytekenreeks    | Optioneel. De reden voor het beëindigen van het Orchestrator-exemplaar. |

#### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)** : De aanvraag voor het beëindigen is geaccepteerd voor verwerking.
* **HTTP 404 (niet gevonden)** : Het opgegeven exemplaar is niet gevonden.
* **HTTP 410 (verdwenen)** : Het opgegeven exemplaar is voltooid of mislukt.

Hier volgt een voorbeeld aanvraag waarmee een actief exemplaar wordt beëindigd en een reden voor het uitvoeren van **fouten**wordt opgegeven:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

De antwoorden voor deze API bevatten geen inhoud.

### <a name="rewind-instance-preview"></a>Exemplaar terugspoelen (preview-versie)

Hiermee herstelt u een mislukte Orchestrator-instantie in een actieve status door de meest recente mislukte bewerkingen te herhalen.

#### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meter.

| Veld             | Parametertype  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |
| **`reason`**      | Querytekenreeks    | Optioneel. De reden voor het terugspoelen van het Orchestration-exemplaar. |

#### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)** : De aanvraag voor terugspoelen is geaccepteerd voor verwerking.
* **HTTP 404 (niet gevonden)** : Het opgegeven exemplaar is niet gevonden.
* **HTTP 410 (verdwenen)** : Het opgegeven exemplaar is voltooid of beëindigd.

Hier volgt een voorbeeld aanvraag voor het terugspoelen van een mislukt exemplaar en de reden van een **vast**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

De antwoorden voor deze API bevatten geen inhoud.

### <a name="signal-entity-preview"></a>Signaal entiteit (preview-versie)

Verzendt een eenrichtings bewerkings bericht naar een [duurzame entiteit](durable-functions-types-features-overview.md#entity-functions). Als de entiteit niet bestaat, wordt deze automatisch gemaakt.

#### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt ingedeeld (er worden meerdere regels weer gegeven voor de duidelijkheid):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld             | Parametertype  | Description |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Het type van de entiteit. |
| **`entityKey`**   | URL             | De unieke naam van de entiteit. |
| **`op`**          | Querytekenreeks    | Optioneel. De naam van de door de gebruiker gedefinieerde bewerking die moet worden aangeroepen. |
| **`{content}`**   | Inhoud van aanvraag | De nettolading van de JSON-indeling. |

Hier volgt een voorbeeld aanvraag waarmee een door de gebruiker gedefinieerde "add"-bericht naar `Counter` een entiteit `steps`met de naam wordt verzonden. De inhoud van het bericht is de waarde `5`. Als de entiteit nog niet bestaat, wordt deze gemaakt door deze aanvraag:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Antwoord

Deze bewerking heeft verschillende mogelijke reacties:

* **HTTP 202 (geaccepteerd)** : De signaal bewerking is geaccepteerd voor asynchrone verwerking.
* **HTTP 400 (ongeldige aanvraag)** : De aanvraag inhoud is niet van het `application/json`type, is geen geldige JSON of heeft een ongeldige `entityKey` waarde.
* **HTTP 404 (niet gevonden)** : De opgegeven `entityType` is niet gevonden.

Een geslaagde HTTP-aanvraag bevat geen inhoud in het antwoord. Een mislukte HTTP-aanvraag kan fout informatie in JSON-indeling bevatten in de antwoord inhoud.

### <a name="query-entity-preview"></a>Query-entiteit (preview-versie)

Hiermee wordt de status van de opgegeven entiteit opgehaald.

#### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt ingedeeld (er worden meerdere regels weer gegeven voor de duidelijkheid):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Antwoord

Deze bewerking heeft twee mogelijke antwoorden:

* **HTTP 200 (OK)** : De opgegeven entiteit bestaat al.
* **HTTP 404 (niet gevonden)** : Kan de opgegeven entiteit niet vinden.

Een geslaagd antwoord bevat de JSON-serialisatie status van de entiteit als de inhoud.

#### <a name="example"></a>Voorbeeld
Hier volgt een voor beeld van een HTTP-aanvraag die de status van een bestaande `Counter` entiteit met `steps`de naam:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Als de `Counter` entiteit simpelweg een aantal stappen bevat die zijn opgeslagen in `currentValue` een veld, kan de antwoord inhoud er als volgt uitzien (opgemaakt voor de Lees baarheid):

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het afhandelen van fouten](durable-functions-error-handling.md)

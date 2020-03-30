---
title: HTTP-API's in duurzame functies - Azure-functies
description: Meer informatie over het implementeren van HTTP API's in de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278166"
---
# <a name="http-api-reference"></a>HTTP API-verwijzing

De extensie Duurzame functies legt een set ingebouwde HTTP-API's bloot die kunnen worden gebruikt om beheertaken uit te voeren op [orkestraties](durable-functions-types-features-overview.md#orchestrator-functions), [entiteiten](durable-functions-types-features-overview.md#entity-functions)en [taakhubs](durable-functions-task-hubs.md). Deze HTTP-API's zijn extensibility webhooks die zijn geautoriseerd door de Azure Functions-host, maar die rechtstreeks worden beheerd door de extensie Duurzame functies.

Alle HTTP-API's die door de extensie zijn geïmplementeerd, vereisen de volgende parameters. Het gegevenstype van `string`alle parameters is .

| Parameter        | Parametertype  | Beschrijving |
|------------------|-----------------|-------------|
| **`taskHub`**    | Queryreeks    | De naam van de [taakhub](durable-functions-task-hubs.md). Als dit niet is opgegeven, wordt de naam van de taakhub van de huidige functie-app aangenomen. |
| **`connection`** | Queryreeks    | De **naam** van de verbindingstekenreeks voor het opslagaccount. Als dit niet is opgegeven, wordt de standaardverbindingstekenreeks voor de functie-app aangenomen. |
| **`systemKey`**  | Queryreeks    | De autorisatiesleutel die nodig is om de API aan te roepen. |

`systemKey`is een autorisatiesleutel die automatisch wordt gegenereerd door de Azure Functions-host. Het verleent specifiek toegang tot de API's voor duurzame taakuitbreiding en kan op dezelfde manier worden beheerd als [andere autorisatiesleutels.](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API) U URL's genereren `taskHub` `connection`die `systemKey` de juiste, en querytekenreekswaarden bevatten met [behulp van orchestration client binding](durable-functions-bindings.md#orchestration-client) API's, zoals de `CreateCheckStatusResponse` API's en `CreateHttpManagementPayload` API's in .NET, of de `createCheckStatusResponse` API's en `createHttpManagementPayload` API's in JavaScript.

De volgende secties hebben betrekking op de specifieke HTTP API's ondersteund door de extensie en geven voorbeelden van hoe ze kunnen worden gebruikt.

## <a name="start-orchestration"></a>Orkestratie starten

Hiermee wordt gestart met het uitvoeren van een nieuwe instantie van de opgegeven orchestrator-functie.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld              | Parametertype  | Beschrijving |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | De naam van de orchestrator functie om te beginnen. |
| **`instanceId`**   | URL             | Optionele parameter. De ID van de instantie orchestration. Als dit niet is opgegeven, begint de orchestrator-functie met een willekeurige instantie-id. |
| **`{content}`**    | Inhoud aanvragen | Optioneel. De JSON-geformatteerde orchestrator-functie-invoer. |

### <a name="response"></a>Antwoord

Verschillende mogelijke statuscodewaarden kunnen worden geretourneerd.

* **HTTP 202 (Geaccepteerd)**: De opgegeven orchestrator-functie zou worden uitgevoerd. De `Location` antwoordkop bevat een URL voor het peilen van de orchestration-status.
* **HTTP 400 (Slechte aanvraag)**: De opgegeven orchestrator-functie bestaat niet, de opgegeven instantie-id was niet geldig of de inhoud van het verzoek was geen geldige JSON.

Het volgende is een voorbeeldaanvraag waarmee een `RestartVMs` orchestrator-functie wordt gestart en die de laadlading van het JSON-object bevat:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

De responspayload voor de **HTTP 202-aanvragen** is een JSON-object met de volgende velden:

| Veld                       | Beschrijving                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |De ID van de instantie orchestration. |
| **`statusQueryGetUri`**     |De status-URL van de instantie orchestration. |
| **`sendEventPostUri`**      |De URL 'evenement verhogen' van de instantie orchestration. |
| **`terminatePostUri`**      |De URL 'beëindigen' van het orchestration-exemplaar. |
| **`purgeHistoryDeleteUri`** |De URL 'zuiveringsgeschiedenis' van het instantie orchestration. |
| **`rewindPostUri`**         |(preview) De URL 'terugspoelen' van de instantie orchestration. |

Het gegevenstype van `string`alle velden is .

Hier is een voorbeeld reactie payload `abc123` voor een orchestration instantie met als de ID (opgemaakt voor leesbaarheid):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Het HTTP-antwoord is bedoeld om compatibel te zijn met het *Polling Consumer Pattern*. Het bevat ook de volgende opmerkelijke reactieheaders:

* **Locatie:** de URL van het statuseindpunt. Deze URL bevat dezelfde `statusQueryGetUri` waarde als het veld.
* **Opnieuw proberen-Na:** Het aantal seconden om te wachten tussen polling operaties. De standaardwaarde is `10`.

Zie de [http-documentatie voor het bijhouden van de http-async-bewerking voor](durable-functions-http-features.md#async-operation-tracking) meer informatie over het asynchrone HTTP-pollingpatroon.

## <a name="get-instance-status"></a>Instantiestatus opdoen

Krijgt de status van een opgegeven orchestration-instantie.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld                   | Parametertype  | Beschrijving |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | De ID van de instantie orchestration. |
| **`showInput`**         | Queryreeks    | Optionele parameter. Indien ingesteld `false`op , wordt de functie-ingang niet opgenomen in de responspayload.|
| **`showHistory`**       | Queryreeks    | Optionele parameter. Indien ingesteld `true`op, zal de orkestratie uitvoeringsgeschiedenis worden opgenomen in de reactie payload.|
| **`showHistoryOutput`** | Queryreeks    | Optionele parameter. Als ingesteld `true`op , worden de functie-uitgangen opgenomen in de geschiedenis van de uitvoering van de orkestratie.|
| **`createdTimeFrom`**   | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijdstempel.|
| **`createdTimeTo`**     | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijdstempel.|
| **`runtimeStatus`**     | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde exemplaren op basis van hun runtime-status. Zie het artikel [Query-instanties](durable-functions-instance-management.md) om de lijst met mogelijke statuswaarden voor runtime te bekijken. |

### <a name="response"></a>Antwoord

Verschillende mogelijke statuscodewaarden kunnen worden geretourneerd.

* **HTTP 200 (OK)**: De opgegeven instantie bevindt zich in een voltooide status.
* **HTTP 202 (Geaccepteerd)**: De opgegeven instantie is aan de gang.
* **HTTP 400 (Slecht verzoek)**: De opgegeven instantie is mislukt of is beëindigd.
* **HTTP 404 (Niet gevonden):** de opgegeven instantie bestaat niet of is niet gestart.
* **HTTP 500 (interne serverfout):** de opgegeven instantie is mislukt met een niet-afgehandelde uitzondering.

Het antwoordlaadvermogen voor de **HTTP 200-** en **HTTP 202-aanvragen** is een JSON-object met de volgende velden:

| Veld                 | Gegevenstype | Beschrijving |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | tekenreeks    | De runtime-status van de instantie. Waarden omvatten *Lopend*, *In behandeling ,* *Mislukt*, *Geannuleerd*, *Beëindigd*, *Voltooid*. |
| **`input`**           | JSON      | De JSON-gegevens worden gebruikt om de instantie te initialiseren. Dit veld `null` is `showInput` als de parameter `false`querytekenreeks is ingesteld op .|
| **`customStatus`**    | JSON      | De JSON-gegevens die worden gebruikt voor de aangepaste orchestration-status. Dit veld `null` is zo niet ingesteld. |
| **`output`**          | JSON      | De JSON-uitvoer van de instantie. Dit veld `null` is als de instantie zich niet in een voltooide status bevindt. |
| **`createdTime`**     | tekenreeks    | Het tijdstip waarop de instantie is gemaakt. Maakt gebruik van ISO 8601 extended notatie. |
| **`lastUpdatedTime`** | tekenreeks    | Het tijdstip waarop de instantie voor het laatst voortduurde. Maakt gebruik van ISO 8601 extended notatie. |
| **`historyEvents`**   | JSON      | Een JSON-array met de geschiedenis van de uitvoering van orkestratie. Dit veld `null` is `showHistory` tenzij de parameter `true`querytekenreeks is ingesteld op . |

Hier is een voorbeeld reactie payload met inbegrip van de orchestration uitvoeringsgeschiedenis en activiteit outputs (opgemaakt voor leesbaarheid):

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

Het **HTTP 202-antwoord** bevat ook een antwoordkop **locatie** die verwijst naar dezelfde URL als het `statusQueryGetUri` eerder genoemde veld.

## <a name="get-all-instances-status"></a>Alle instantiesstatus opdoen

U ook de status van `instanceId` alle instanties opvragen door de aanvraag 'Instantiestatus ophalen' te verwijderen. In dit geval zijn de basisparameters hetzelfde als de 'Instantiestatus opdoen'. Querytekenreeksparameters voor filteren worden ook ondersteund.

Een ding om `connection` te `code` onthouden is dat en zijn optioneel. Als u anonieme auth op `code` de functie hebt, is dan niet vereist.
Als u geen andere opslagverbindingstekenreeks wilt gebruiken dan gedefinieerd in de instelling azurewebjobsStorage-app, u de parameter verbindingsquerytekenreeks veilig negeren.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

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

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

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

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld                   | Parametertype  | Beschrijving |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | De ID van de instantie orchestration. |
| **`showInput`**         | Queryreeks    | Optionele parameter. Indien ingesteld `false`op , wordt de functie-ingang niet opgenomen in de responspayload.|
| **`showHistory`**       | Queryreeks    | Optionele parameter. Indien ingesteld `true`op, zal de orkestratie uitvoeringsgeschiedenis worden opgenomen in de reactie payload.|
| **`showHistoryOutput`** | Queryreeks    | Optionele parameter. Als ingesteld `true`op , worden de functie-uitgangen opgenomen in de geschiedenis van de uitvoering van de orkestratie.|
| **`createdTimeFrom`**   | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijdstempel.|
| **`createdTimeTo`**     | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijdstempel.|
| **`runtimeStatus`**     | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde exemplaren op basis van hun runtime-status. Zie het artikel [Query-instanties](durable-functions-instance-management.md) om de lijst met mogelijke statuswaarden voor runtime te bekijken. |
| **`top`**               | Queryreeks    | Optionele parameter. Wanneer opgegeven, beperkt het aantal exemplaren dat door de query wordt geretourneerd. |

### <a name="response"></a>Antwoord

Hier is een voorbeeld van respons payloads met inbegrip van de orchestration status (opgemaakt voor leesbaarheid):

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
> Deze bewerking kan erg duur zijn in termen van Azure Storage I/O als er veel rijen in de instantiestabel staan. Meer details over de instantietabel zijn te vinden in de documentatie [Prestaties en schaal in Azure Functions (Sustainable Functions).](durable-functions-perf-and-scale.md#instances-table)
>

Als er meer resultaten zijn, wordt een vervolgtoken geretourneerd in de reactiekoptekst.  De naam van `x-ms-continuation-token`de koptekst is .

Als u de waarde van vervolgtoken instelt in de volgende aanvraagkoptekst, u de volgende pagina met resultaten krijgen. Deze naam van de `x-ms-continuation-token`aanvraagheader is ook .

## <a name="purge-single-instance-history"></a>Eén instantiegeschiedenis wissen

Hiermee verwijdert u de geschiedenis en gerelateerde artefacten voor een opgegeven orchestration-instantie.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van de instantie orchestration. |

### <a name="response"></a>Antwoord

De volgende HTTP-statuscodewaarden kunnen worden geretourneerd.

* **HTTP 200 (OK)**: De instantiegeschiedenis is met succes gewist.
* **HTTP 404 (Niet gevonden)**: De opgegeven instantie bestaat niet.

Het antwoordlaadvermogen voor de **HTTP 200-aanvraag** is een JSON-object met het volgende veld:

| Veld                  | Gegevenstype | Beschrijving |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | geheel getal   | Het aantal verwijderde exemplaren. Voor het geval van één instantie `1`moet deze waarde altijd . |

Hier is een voorbeeld reactie payload (opgemaakt voor leesbaarheid):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Meerdere instantiegeschiedenissen wissen

U ook de geschiedenis en gerelateerde artefacten voor meerdere `{instanceId}` instanties binnen een taakhub verwijderen door het verzoek om één instantie te verwijderen. Als u de instantiegeschiedenis selectief wilt wissen, gebruikt u dezelfde filters die zijn beschreven in de aanvraag status 'Alle instanties verwijderen'.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld                 | Parametertype  | Beschrijving |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Queryreeks    | Hiermee filtert u de lijst met gewiste exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijdstempel.|
| **`createdTimeTo`**   | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met gewiste exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijdstempel.|
| **`runtimeStatus`**   | Queryreeks    | Optionele parameter. Wanneer deze is opgegeven, filtert u de lijst met gewiste exemplaren op basis van hun runtime-status. Zie het artikel [Query-instanties](durable-functions-instance-management.md) om de lijst met mogelijke statuswaarden voor runtime te bekijken. |

> [!NOTE]
> Deze bewerking kan erg duur zijn in termen van Azure Storage I/O als er veel rijen zijn in de tabellen Instanties en/of Geschiedenis. Meer details over deze tabellen vindt u in de documentatie [Prestaties en schaal in Azure Functions (Sustainable Functions).](durable-functions-perf-and-scale.md#instances-table)

### <a name="response"></a>Antwoord

De volgende HTTP-statuscodewaarden kunnen worden geretourneerd.

* **HTTP 200 (OK)**: De instantiegeschiedenis is met succes gewist.
* **HTTP 404 (Niet gevonden)**: Er zijn geen exemplaren gevonden die overeenkomen met de filterexpressie.

Het antwoordlaadvermogen voor de **HTTP 200-aanvraag** is een JSON-object met het volgende veld:

| Veld                   | Gegevenstype | Beschrijving |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | geheel getal   | Het aantal verwijderde exemplaren. |

Hier is een voorbeeld reactie payload (opgemaakt voor leesbaarheid):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Evenement verhogen

Hiermee verzendt u een bericht over een gebeurtenismelding naar een gebeurtenisinstantie.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van de instantie orchestration. |
| **`eventName`**   | URL             | De naam van de gebeurtenis waarop de doelorkestratie-instantie wacht. |
| **`{content}`**   | Inhoud aanvragen | De JSON-geformatteerde gebeurtenis payload. |

### <a name="response"></a>Antwoord

Verschillende mogelijke statuscodewaarden kunnen worden geretourneerd.

* **HTTP 202 (Geaccepteerd)**: De verhoogde gebeurtenis werd geaccepteerd voor verwerking.
* **HTTP 400 (Slecht verzoek)**: De `application/json` inhoud van het verzoek was niet van type of was niet geldig JSON.
* **HTTP 404 (Niet gevonden)**: De opgegeven instantie is niet gevonden.
* **HTTP 410 (Gone)**: De opgegeven instantie is voltooid of mislukt en kan geen verhoogde gebeurtenissen verwerken.

Hier is een voorbeeldverzoek dat `"incr"` de JSON-tekenreeks naar een instantie stuurt die wacht op een bewerking **met**de naam gebeurtenis:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

De antwoorden voor deze API bevatten geen inhoud.

## <a name="terminate-instance"></a>Instantie beëindigen

Beëindigt een instantie voor lopende orkestratie.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameter.

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van de instantie orchestration. |
| **`reason`**      | Queryreeks    | Optioneel. De reden voor het beëindigen van de orchestration instance. |

### <a name="response"></a>Antwoord

Verschillende mogelijke statuscodewaarden kunnen worden geretourneerd.

* **HTTP 202 (Geaccepteerd)**: Het beëindigingsverzoek is geaccepteerd voor verwerking.
* **HTTP 404 (Niet gevonden)**: De opgegeven instantie is niet gevonden.
* **HTTP 410 (Verdwenen):** de opgegeven instantie is voltooid of mislukt.

Hier is een voorbeeldverzoek dat een lopende instantie beëindigt en een reden van **buggy**opgeeft:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

De antwoorden voor deze API bevatten geen inhoud.

## <a name="rewind-instance-preview"></a>Instantie terugspoelen (voorbeeld)

Hiermee herstelt u een mislukte orchestration-instantie in een bedrijfsstatus door de meest recente mislukte bewerkingen opnieuw af te spelen.

### <a name="request"></a>Aanvraag

Voor versie 1.x van de runtime van functies wordt de aanvraag als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In versie 2.x van de runtime van functies heeft de URL-indeling allemaal dezelfde parameters, maar met een iets ander voorvoegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameter.

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van de instantie orchestration. |
| **`reason`**      | Queryreeks    | Optioneel. De reden voor het terugspoelen van de orchestration instantie. |

### <a name="response"></a>Antwoord

Verschillende mogelijke statuscodewaarden kunnen worden geretourneerd.

* **HTTP 202 (Geaccepteerd)**: Het terugspoelverzoek is geaccepteerd voor verwerking.
* **HTTP 404 (Niet gevonden)**: De opgegeven instantie is niet gevonden.
* **HTTP 410 (Gone)**: De opgegeven instantie is voltooid of beëindigd.

Hier is een voorbeeldverzoek dat een mislukte instantie opnieuw afspoelt en een reden van **vaststaat:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

De antwoorden voor deze API bevatten geen inhoud.

## <a name="signal-entity"></a>Signaalentiteit

Hiermee stuurt u een bericht over een enkele handeling naar een [duurzame entiteit](durable-functions-types-features-overview.md#entity-functions). Als de entiteit niet bestaat, wordt deze automatisch gemaakt.

> [!NOTE]
> Duurzame entiteiten zijn beschikbaar vanaf duurzame functies 2.0.

### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | De naam (type) van de entiteit. |
| **`entityKey`**   | URL             | De sleutel (unieke ID) van de entiteit. |
| **`op`**          | Queryreeks    | Optioneel. De naam van de door de gebruiker gedefinieerde bewerking om aan te roepen. |
| **`{content}`**   | Inhoud aanvragen | De JSON-geformatteerde gebeurtenis payload. |

Hier is een voorbeeldverzoek dat een door de gebruiker `Counter` gedefinieerd `steps`bericht 'Toevoegen' verzendt naar een entiteit met de naam . De inhoud van het `5`bericht is de waarde . Als de entiteit nog niet bestaat, wordt deze gemaakt door deze aanvraag:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Standaard met [op klasse gebaseerde entiteiten in .NET,](durable-functions-dotnet-entities.md#defining-entity-classes)waarbij de `op` waarde van `delete` de status van een entiteit wordt verwijderd. Als de entiteit echter `delete`een bewerking met de naam definieert, wordt die door de gebruiker gedefinieerde bewerking in plaats daarvan aangeroepen.

### <a name="response"></a>Antwoord

Deze bewerking heeft verschillende mogelijke reacties:

* **HTTP 202 (Geaccepteerd)**: De signaalbewerking werd geaccepteerd voor asynchrone verwerking.
* **HTTP 400 (Slecht verzoek)**: De `application/json`inhoud van het verzoek was niet `entityKey` van type, was niet geldig JSON of had een ongeldige waarde.
* **HTTP 404 (Niet gevonden)**: De opgegeven `entityName` werd niet gevonden.

Een geslaagdhttp-verzoek bevat geen inhoud in het antwoord. Een mislukte HTTP-aanvraag kan foutgegevens met JSON-formaat bevatten in de antwoord-inhoud.

## <a name="get-entity"></a>Entiteit oppakken

Krijgt de status van de opgegeven entiteit.

### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Antwoord

Deze bewerking heeft twee mogelijke antwoorden:

* **HTTP 200 (OK)**: De opgegeven entiteit bestaat.
* **HTTP 404 (Niet gevonden)**: De opgegeven entiteit is niet gevonden.

Een succesvol antwoord bevat de JSON-serialized status van de entiteit als inhoud.

### <a name="example"></a>Voorbeeld
In het volgende voorbeeld http-aanvraag `Counter` wordt `steps`de status van een bestaande entiteit met de naam :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Als `Counter` de entiteit slechts een aantal stappen `currentValue` bevat die in een veld zijn opgeslagen, kan de antwoordinhoud er als volgt uitzien (opgemaakt voor leesbaarheid):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Entiteiten vermelden

U voor meerdere entiteiten zoeken op basis van de naam van de entiteit of op de laatste bewerkingsdatum.

### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt opgemaakt (meerdere regels worden weergegeven voor de duidelijkheid):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

De aanvraagparameters voor deze API omvatten de eerder genoemde standaardset en de volgende unieke parameters:

| Veld                       | Parametertype  | Beschrijving |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Optioneel. Wanneer deze is opgegeven, filtert u de lijst met geretourneerde entiteiten op hun entiteitsnaam (case-ongevoelig). |
| **`fetchState`**            | Queryreeks    | Optionele parameter. Als ingesteld `true`op , wordt de entiteitsstatus opgenomen in de reactiepayload. |
| **`lastOperationTimeFrom`** | Queryreeks    | Optionele parameter. Als deze is opgegeven, filtert u de lijst met geretourneerde entiteiten die bewerkingen hebben verwerkt na de opgegeven ISO8601-tijdstempel. |
| **`lastOperationTimeTo`**   | Queryreeks    | Optionele parameter. Als deze is opgegeven, filtert u de lijst met geretourneerde entiteiten die bewerkingen hebben verwerkt vóór de opgegeven ISO8601-tijdstempel. |
| **`top`**                   | Queryreeks    | Optionele parameter. Wanneer opgegeven, beperkt het aantal entiteiten dat door de query wordt geretourneerd. |


### <a name="response"></a>Antwoord

Een succesvol HTTP 200-antwoord bevat een JSON-serie van entiteiten en optioneel de status van elke entiteit.

Standaard retourneert de bewerking de eerste 100 entiteiten die overeenkomen met de querycriteria. De beller kan een parameterwaarde `top` voor querytekenreeksopgeven om een ander maximumaantal resultaten te retourneren. Als er meer resultaten bestaan dan wat wordt geretourneerd, wordt een vervolgtoken ook geretourneerd in de antwoordkop. De naam van `x-ms-continuation-token`de koptekst is .

Als u de waarde van vervolgtoken instelt in de volgende aanvraagkoptekst, u de volgende pagina met resultaten krijgen. Deze naam van de `x-ms-continuation-token`aanvraagheader is ook .

### <a name="example---list-all-entities"></a>Voorbeeld - alle entiteiten weergeven

In het volgende voorbeeld van http-aanvraag worden alle entiteiten in de taakhub weergegeven:

```http
GET /runtime/webhooks/durabletask/entities
```

Het antwoord JSON kan er als volgt uitzien (opgemaakt voor leesbaarheid):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Voorbeeld : de lijst met entiteiten filteren

In het volgende voorbeeld van http-aanvraag `counter` worden alleen de eerste twee entiteiten van het type weergegeven en wordt ook de status opgehaald:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

Het antwoord JSON kan er als volgt uitzien (opgemaakt voor leesbaarheid):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van Application Insights om uw duurzame functies te bewaken](durable-functions-diagnostics.md)
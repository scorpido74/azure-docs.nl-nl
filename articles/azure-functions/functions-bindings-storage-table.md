---
title: Azure Table Storage-bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Table Storage-bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277178"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Table Storage-bindingen in Azure Functions. Azure Functions ondersteunt invoer-en uitvoer bindingen voor Azure Table-opslag.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De tabel opslag bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De tabel opslag bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Invoer

Gebruik de Azure Table Storage-invoer binding om een tabel in een Azure Storage-account te lezen.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Eén entiteit

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee één tabelrij wordt gelezen. Voor elke record die in de tabel wordt ingevoegd, wordt de functie geactiveerd.

De waarde {Queue trigger} van de rij geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee meerdere tabel rijen worden gelezen waarvan de `MyPoco` klasse is afgeleid van `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook een `CloudTable` methode parameter gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier volgt een voor beeld van een functie die een Azure Functions-logboek tabel opvraagt:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Zie [aan de slag met Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

### <a name="one-entity"></a>Eén entiteit

In het volgende voor beeld ziet u een tabel-invoer binding in een *Function. json* -bestand en [ C# script](functions-reference-csharp.md) code die gebruikmaken van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

Het bestand *Function. json* bevat een `partitionKey` en een `rowKey`. De `rowKey` waarde {Queue trigger} geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

In het volgende voor beeld ziet u een tabel-invoer binding in een *Function. json* -bestand en [ C# script](functions-reference-csharp.md) code die gebruikmaken van de binding. De functie leest entiteiten voor een partitie sleutel die is opgegeven in een wachtrij bericht.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Met C# de script code wordt een verwijzing toegevoegd aan de Azure Storage SDK, zodat het entiteits type kan worden afgeleid van `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` wordt niet ondersteund in de functions-runtime voor [versie 2. x en hoger)](functions-versions.md). U kunt ook een `CloudTable` methode parameter gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier volgt een voor beeld van een functie die een Azure Functions-logboek tabel opvraagt:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Zie [aan de slag met Azure Table Storage](../cosmos-db/table-storage-how-to-use-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *Function. json* -bestand en [Java script-code](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

Het bestand *Function. json* bevat een `partitionKey` en een `rowKey`. De `rowKey` waarde {Queue trigger} geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Enkele tabelrij 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een HTTP-geactiveerde functie die een lijst retourneert met objecten van de persoon die zich in een opgegeven partitie in tabel opslag bevinden. In het voor beeld wordt de partitie sleutel geëxtraheerd uit de http-route en de tabel naam en de verbinding zijn afkomstig uit de functie-instellingen. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

De TableInput-aantekening kan ook de bindingen ophalen uit de JSON-hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt getoond.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

In de volgende voor beelden wordt het filter gebruikt om een query uit te zoeken voor personen met een specifieke naam in een Azure-tabel en wordt het aantal mogelijke overeenkomsten beperkt tot 10 resultaten.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Invoer kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

 Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken voor het configureren van een tabel-invoer binding:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  De constructor van het kenmerk heeft de tabel naam, de partitie sleutel en de rij-sleutel. Het kenmerk kan worden gebruikt op een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  U kunt de eigenschap `Connection` instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Zie invoer- C# voor beeld voor een volledig voor beeld.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de parameter, klasseniveau of methode. Het volgende voorbeeld toont het klasseniveau en methode:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Het opslag account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De eigenschap `Connection` van het `Table`-kenmerk.
* Het `StorageAccount` kenmerk dat wordt toegepast op dezelfde para meter als het kenmerk `Table`.
* Het `StorageAccount` kenmerk dat wordt toegepast op de functie.
* Het `StorageAccount` kenmerk dat wordt toegepast op de klasse.
* Het standaard opslag account voor de functie-app (de app-instelling AzureWebJobsStorage).

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@TableInput` annotatie op para meters waarvan de waarde afkomstig is uit de tabel opslag.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van `Optional<T>`.

---

## <a name="input---configuration"></a>Invoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `Table`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `table`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die de tabel of entiteit in functie code vertegenwoordigt. | 
|**tableName** | **TableName** | De naam van de tabel.| 
|**partitionKey** | **PartitionKey** |Optioneel. De partitie sleutel van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | Optioneel. De rij van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Houd** |**Houd** | Optioneel. Het maximum aantal entiteiten dat in Java script kan worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Filterwebonderdelen** |**Filterwebonderdelen** | Optioneel. Een OData-filter expressie voor tabel invoer in Java script. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Combi** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer - gebruik

# <a name="c"></a>[C#](#tab/csharp)

* **Eén rij in**

  Stel `partitionKey` en `rowKey`in. Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` is doorgaans een type dat `ITableEntity` of een afleiding van `TableEntity`implementeert. De eigenschappen `filter` en `take` worden niet in dit scenario gebruikt.

* **Een of meer rijen lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` moet een type zijn dat `ITableEntity` of afgeleid van `TableEntity`implementeert. U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De eigenschappen `partitionKey`, `rowKey`, `filter`en `take` worden niet in dit scenario gebruikt.  

  > [!NOTE]
  > `IQueryable` wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

* **Eén rij in**

  Stel `partitionKey` en `rowKey`in. Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` is doorgaans een type dat `ITableEntity` of een afleiding van `TableEntity`implementeert. De eigenschappen `filter` en `take` worden niet in dit scenario gebruikt.

* **Een of meer rijen lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` moet een type zijn dat `ITableEntity` of afgeleid van `TableEntity`implementeert. U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De eigenschappen `partitionKey`, `rowKey`, `filter`en `take` worden niet in dit scenario gebruikt.  

  > [!NOTE]
  > `IQueryable` wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Stel de eigenschappen `filter` en `take` in. Stel `partitionKey` of `rowKey`niet in. De entiteit (of entiteiten) van de invoer tabel openen met behulp van `context.bindings.<BINDING_NAME>`. De gedeserialiseerd objecten hebben `RowKey`-en `PartitionKey`-eigenschappen.

# <a name="python"></a>[Python](#tab/python)

Tabel gegevens worden door gegeven aan de functie als een JSON-teken reeks. Deserialiseren van het bericht door `json.loads` aan te roepen, zoals wordt weer gegeven in het [voor beeld](#input)van de invoer.

# <a name="java"></a>[Java](#tab/java)

Met het kenmerk [TableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) krijgt u toegang tot de tabelrij waarin de functie is geactiveerd.

---

## <a name="output"></a>Uitvoer

Gebruik een Azure Table Storage-uitvoer binding om entiteiten te schrijven naar een tabel in een Azure Storage-account.

> [!NOTE]
> Deze uitvoer binding biedt geen ondersteuning voor het bijwerken van bestaande entiteiten. Gebruik de bewerking `TableOperation.Replace` [van de Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) om een bestaande entiteit bij te werken.

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die gebruikmaakt van een http-trigger om één tabelrij te schrijven. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *Function. json* -bestand en [ C# script](functions-reference-csharp.md) code die gebruikmaken van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Hier is het bestand *Function. json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u de Table Storage-uitvoer binding kunt gebruiken. De `table` binding wordt geconfigureerd in de *functie. json* door waarden toe te wijzen aan `name`, `tableName`, `partitionKey`en `connection`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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

Met de volgende functie wordt een unieke UUI gegenereerd voor de `rowKey` waarde en wordt het bericht in tabel opslag bewaard.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een Java-functie die gebruikmaakt van een HTTP-trigger om één tabelrij te schrijven.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

In het volgende voor beeld ziet u een Java-functie die gebruikmaakt van een HTTP-trigger om meerdere tabel rijen te schrijven.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Uitvoer-kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

De constructor van het kenmerk heeft de tabel naam. Het kenmerk kan worden gebruikt op een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

U kunt de eigenschap `Connection` instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer- C# voor beeld](#output).

U kunt het kenmerk `StorageAccount` gebruiken om het opslag account op te geven bij klasse, methode of parameter niveau. Zie [invoer kenmerken](#input---attributes-and-annotations)voor meer informatie.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) -aantekening voor para meters voor het schrijven van waarden naar tabel opslag.

Zie het voor [beeld voor meer informatie](#output).

---

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `Table`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `table`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `out`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code die de tabel of entiteit vertegenwoordigt. Ingesteld op `$return` om te verwijzen naar de retour waarde van de functie.| 
|**tableName** |**TableName** | De naam van de tabel.| 
|**partitionKey** |**PartitionKey** | De partitie sleutel van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#output---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | De rij van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#output---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Combi** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

# <a name="c"></a>[C#](#tab/csharp)

Open de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName` waarbij `T` de eigenschappen `PartitionKey` en `RowKey` bevat. Deze eigenschappen gaan vaak vergezeld van de implementatie van `ITableEntity` of het overnemen van `TableEntity`.

U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Open de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName` waarbij `T` de eigenschappen `PartitionKey` en `RowKey` bevat. Deze eigenschappen gaan vaak vergezeld van de implementatie van `ITableEntity` of het overnemen van `TableEntity`. De `paramName` waarde is opgegeven in de eigenschap `name` van *Function. json*.

U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Open de uitvoer gebeurtenis met behulp van `context.bindings.<name>` waarbij `<name>` de waarde is die is opgegeven in de eigenschap `name` van *Function. json*.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitvoeren van een tabelrij bericht in tabel opslag van een functie:

- **Retour waarde**: Stel de eigenschap `name` in *Function. json* in op `$return`. Met deze configuratie wordt de retour waarde van de functie persistent gemaakt als tabelrij.

- Verplicht **: Geef**een waarde door aan de methode [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) van de para meter die is gedeclareerd als een [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) -type. De waarde die aan `set` is door gegeven, wordt persistent gemaakt als een event hub-bericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitvoeren van een rij in een tabel opslag van een functie met behulp van de [TableStorageOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) -aantekening:

- **Retour waarde**: door de aantekening toe te passen op de functie zelf, wordt de geretourneerde waarde van de functie persistent gemaakt als een rij in tabel opslag.

- Verplicht **: als**u de bericht waarde expliciet wilt instellen, past u de aantekening toe op een specifieke para meter van het type [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), waarbij `T` de eigenschappen `PartitionKey` en `RowKey` bevat. Deze eigenschappen gaan vaak vergezeld van de implementatie van `ITableEntity` of het overnemen van `TableEntity`.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| Tabel | [Fout codes voor tabellen](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabel, wachtrij | [Opslag fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij | [Problemen oplossen](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

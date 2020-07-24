---
title: Azure Table Storage-bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Table Storage-bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: b6d8ca41c56239bc994b34119600dfa9db60ada6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083086"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Table Storage-bindingen in Azure Functions. Azure Functions ondersteunt invoer-en uitvoer bindingen voor Azure Table-opslag.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

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

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee één tabelrij wordt gelezen. Voor elk bericht dat naar de wachtrij wordt verzonden, wordt de functie geactiveerd.

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

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee meerdere tabel rijen worden gelezen waarvan de `MyPoco` klasse is afgeleid `TableEntity` .

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

`IQueryable`wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook een `CloudTable` methode parameter gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier volgt een voor beeld van een functie die een Azure Functions-logboek tabel opvraagt:

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

Zie [aan de slag met Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

### <a name="one-entity"></a>Eén entiteit

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *function.jsvoor* de bestands-en [C#-script](functions-reference-csharp.md) code die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

Het *function.js* bestand bevat een `partitionKey` en een `rowKey` . De `rowKey` waarde {Queue trigger} geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

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

Dit is de C#-script code:

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

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *function.jsvoor* de bestands-en [C#-script](functions-reference-csharp.md) code die gebruikmaakt van de binding. De functie leest entiteiten voor een partitie sleutel die is opgegeven in een wachtrij bericht.

Dit is de *function.jsvoor* het volgende bestand:

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

Met de C#-script code wordt een verwijzing toegevoegd aan de Azure Storage SDK, zodat het entiteits type kan worden afgeleid van `TableEntity` :

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

`IQueryable`wordt niet ondersteund in de functions-runtime voor [versie 2. x en hoger)](functions-versions.md). U kunt ook een `CloudTable` methode parameter gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier volgt een voor beeld van een functie die een Azure Functions-logboek tabel opvraagt:

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

Zie [aan de slag met Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.


# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *function.jsvoor* de bestands-en [Java script-code](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

Het *function.js* bestand bevat een `partitionKey` en een `rowKey` . De `rowKey` waarde {Queue trigger} geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

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

Dit is de Java script-code:

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

 Gebruik in [C# class libraries](functions-dotnet-class-library.md)de volgende kenmerken om een tabel-invoer binding te configureren:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  De constructor van het kenmerk heeft de tabel naam, de partitie sleutel en de rij-sleutel. Het kenmerk kan worden gebruikt voor een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

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

  U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

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

  Zie input-C#-voor beeld voor een volledig voor beeld.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de para meter, methode of class-niveau. In het volgende voor beeld wordt het niveau klasse en de methode weer gegeven:

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

* De `Table` eigenschap van het kenmerk `Connection` .
* Het `StorageAccount` kenmerk dat wordt toegepast op dezelfde para meter als het `Table` kenmerk.
* Het `StorageAccount` kenmerk dat wordt toegepast op de functie.
* Het `StorageAccount` kenmerk dat wordt toegepast op de klasse.
* Het standaard opslag account voor de functie-app (de app-instelling AzureWebJobsStorage).

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@TableInput` aantekening voor para meters waarvan de waarde afkomstig is uit de tabel opslag.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met `Optional<T>` .

---

## <a name="input---configuration"></a>Invoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `Table` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `table` . Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in` . Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die de tabel of entiteit in functie code vertegenwoordigt. | 
|**tableName** | **TableName** | De naam van de tabel.| 
|**partitionKey** | **PartitionKey** |Optioneel. De partitie sleutel van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | Optioneel. De rij van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**take** |**Houd** | Optioneel. Het maximum aantal entiteiten dat in Java script kan worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Filterwebonderdelen** |**Filter** | Optioneel. Een OData-filter expressie voor tabel invoer in Java script. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Combi** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. De instelling kan de naam zijn van een vooringestelde app-instelling voor ' AzureWebJobs ' of connection string naam. Als de naam van de instelling bijvoorbeeld ' AzureWebJobsMyStorage ' is, kunt u hier ' mijn opslag ' opgeven. De functions-runtime zoekt automatisch naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer gebruik

# <a name="c"></a>[C#](#tab/csharp)

* **Eén rij in**

  Instellen `partitionKey` en `rowKey` . Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>` . In C#-script `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. `T`is doorgaans een type dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . De `filter` `take` Eigenschappen en worden niet gebruikt in dit scenario.

* **Een of meer rijen lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>` . In C#-script `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. `T`moet een type zijn dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De `partitionKey` `rowKey` Eigenschappen,, `filter` en `take` worden niet gebruikt in dit scenario.  

  > [!NOTE]
  > `IQueryable`wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

* **Eén rij in**

  Instellen `partitionKey` en `rowKey` . Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>` . In C#-script `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. `T`is doorgaans een type dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . De `filter` `take` Eigenschappen en worden niet gebruikt in dit scenario.

* **Een of meer rijen lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>` . In C#-script `paramName` is de waarde die is opgegeven in de `name` eigenschap van *function.jsop*. `T`moet een type zijn dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De `partitionKey` `rowKey` Eigenschappen,, `filter` en `take` worden niet gebruikt in dit scenario.  

  > [!NOTE]
  > `IQueryable`wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Stel de `filter` Eigenschappen en in `take` . Niet instellen `partitionKey` of `rowKey` . Open de entiteit (of entiteiten) van de invoer tabel met behulp van `context.bindings.<BINDING_NAME>` . De gedeserialiseerd objecten hebben `RowKey` en `PartitionKey` Eigenschappen.

# <a name="python"></a>[Python](#tab/python)

Tabel gegevens worden door gegeven aan de functie als een JSON-teken reeks. Deserialiseren van het bericht door aan te roepen `json.loads` zoals wordt weer gegeven in het invoer [voorbeeld](#input).

# <a name="java"></a>[Java](#tab/java)

Met het kenmerk [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) krijgt u toegang tot de tabelrij waarin de functie is geactiveerd.

---

## <a name="output"></a>Uitvoer

Gebruik een Azure Table Storage-uitvoer binding om entiteiten te schrijven naar een tabel in een Azure Storage-account.

> [!NOTE]
> Deze uitvoer binding biedt geen ondersteuning voor het bijwerken van bestaande entiteiten. Gebruik de `TableOperation.Replace` bewerking [van de Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) om een bestaande entiteit bij te werken.

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die gebruikmaakt van een http-trigger om één tabelrij te schrijven. 

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

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

In het volgende voor beeld ziet u een tabel-uitvoer binding in eenfunction.jsin bestands-en [C#-script](functions-reference-csharp.md) code die gebruikmaakt *van* de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Dit is de *function.jsvoor* het volgende bestand:

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

Dit is de C#-script code:

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

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Dit is de *function.jsvoor* het volgende bestand:

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

Dit is de Java script-code:

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

In het volgende voor beeld ziet u hoe u de Table Storage-uitvoer binding kunt gebruiken. De `table` binding wordt geconfigureerd in de *function.jsop* door waarden toe te wijzen aan `name` , `tableName` , en `partitionKey` `connection` :

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

Met de volgende functie wordt een unieke UUI voor de `rowKey` waarde gegenereerd en wordt het bericht in tabel opslag bewaard.

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

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

De constructor van het kenmerk heeft de tabel naam. Het kenmerk kan worden gebruikt voor een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

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

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

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

Zie [output-C#-](#output)voor beeld voor een volledig voor beeld.

U kunt het- `StorageAccount` kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie [invoer kenmerken](#input---attributes-and-annotations)voor meer informatie.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) -aantekening voor para meters voor het schrijven van waarden naar tabel opslag.

Zie het voor [beeld voor meer informatie](#output).

---

## <a name="output---configuration"></a>Uitvoer-configuratie

De volgende tabel bevat uitleg over de binding configuratie-eigenschappen die u hebt ingesteld in de *function.jsvoor* het bestand en het `Table` kenmerk.

|function.jsbij eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `table` . Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `out` . Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code die de tabel of entiteit vertegenwoordigt. Instellen op `$return` om te verwijzen naar de functie retour waarde.| 
|**tableName** |**TableName** | De naam van de tabel.| 
|**partitionKey** |**PartitionKey** | De partitie sleutel van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#output---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | De rij van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#output---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Combi** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer gebruik

# <a name="c"></a>[C#](#tab/csharp)

Toegang tot de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName` waar `T` de en-eigenschappen zijn opgenomen `PartitionKey` `RowKey` . Deze eigenschappen gaan vaak vergezeld van implementatie `ITableEntity` of overname `TableEntity` .

U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Toegang tot de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName` waar `T` de en-eigenschappen zijn opgenomen `PartitionKey` `RowKey` . Deze eigenschappen gaan vaak vergezeld van implementatie `ITableEntity` of overname `TableEntity` . De `paramName` waarde wordt opgegeven in de `name` eigenschap van *function.jsop*.

U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

U krijgt toegang tot de uitvoer gebeurtenis door gebruik te maken `context.bindings.<name>` `<name>` van de waarde die is opgegeven in de `name` eigenschap van *function.jsop*.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitvoeren van een tabelrij bericht in tabel opslag van een functie:

- **Retour waarde**: Stel de `name` eigenschap in *function.jsin op* aan `$return` . Met deze configuratie wordt de retour waarde van de functie persistent gemaakt als tabelrij.

- Verplicht **: Geef**een waarde door aan de methode [set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) van de para meter die is gedeclareerd als een [out](/python/api/azure-functions/azure.functions.out?view=azure-python) -type. De waarde die is door gegeven aan `set` , wordt persistent gemaakt als een event hub-bericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitvoeren van een rij in een tabel opslag van een functie met behulp van de [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) -aantekening:

- **Retour waarde**: door de aantekening toe te passen op de functie zelf, wordt de geretourneerde waarde van de functie persistent gemaakt als een rij in tabel opslag.

- Verplicht **: als**u de bericht waarde expliciet wilt instellen, past u de aantekening toe op een specifieke para meter van het type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , waarbij `T` de `PartitionKey` Eigenschappen en zijn opgenomen `RowKey` . Deze eigenschappen gaan vaak vergezeld van implementatie `ITableEntity` of overname `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>Uitzonde ringen en retour codes

| Binding | Verwijzing |
|---|---|
| Tabel | [Fout codes voor tabellen](/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabel, wachtrij | [Opslag fout codes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij | [Problemen oplossen](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)

---
title: Azure Table-opslagbindingen voor Azure-functies
description: Meer informatie over het gebruik van Azure Table-opslagbindingen in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1aa3537679ee37cbc6085344d2f31ae4043d32bb
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520675"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table-opslagbindingen voor Azure-functies

In dit artikel wordt uitgelegd hoe u werken met Azure Table-opslagbindingen in Azure-functies. Azure Functions ondersteunt invoer- en uitvoerbindingen voor Azure Table-opslag.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

De tabelopslagbindingen worden geleverd in het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten - Functies 2.x en hoger

De tabelopslagbindingen worden geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, versie 3.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk.](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Invoer

Gebruik de binding van azure table-opslaginvoer om een tabel in een Azure Storage-account te lezen.

# <a name="c"></a>[C #](#tab/csharp)

### <a name="one-entity"></a>Eén entiteit

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die één tabelrij leest. Voor elke record die in de tabel wordt ingevoegd, wordt de functie geactiveerd.

De rijsleutelwaarde {queueTrigger}, geeft aan dat de rijsleutel afkomstig is van de tekenreeks van het wachtrijbericht.

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

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die meerdere tabelrijen leest waar de `MyPoco` klasse van `TableEntity`is afgeleid .

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

`IQueryable`wordt niet ondersteund in de [runtime van functies v2.](functions-versions.md) Een alternatief is `CloudTable` om een methodeparameter te gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier vindt u een voorbeeld van een functie die een logboektabel voor Azure-functies opstelt:

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

Zie [Aan de slag met Azure Table-opslag](../cosmos-db/table-storage-how-to-use-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert `CloudTable` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](#azure-storage-sdk-version-in-functions-1x)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="one-entity"></a>Eén entiteit

In het volgende voorbeeld wordt een tabelinvoerbinding weergegeven in een *function.json-bestand* en [C#-scriptcode](functions-reference-csharp.md) die de binding gebruikt. De functie gebruikt een wachtrijtrigger om één tabelrij te lezen. 

Het *bestand function.json* geeft a `partitionKey` en a `rowKey`. De `rowKey` waarde {queueTrigger}geeft aan dat de rijsleutel afkomstig is van de tekenreeks van het wachtrijbericht.

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

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

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

In het volgende voorbeeld wordt een tabelinvoerbinding weergegeven in een *function.json-bestand* en [C#-scriptcode](functions-reference-csharp.md) die de binding gebruikt. De functie leest entiteiten voor een partitiesleutel die is opgegeven in een wachtrijbericht.

Hier is het *function.json* bestand:

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

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

De C#-scriptcode voegt een verwijzing toe naar de Azure `TableEntity`Storage SDK, zodat het entiteitstype kan worden afgeleid uit:

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

`IQueryable`wordt niet ondersteund in de runtime van functies voor [versies 2.x en hoger)](functions-versions.md). Een alternatief is `CloudTable` om een methodeparameter te gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier vindt u een voorbeeld van een functie die een logboektabel voor Azure-functies opstelt:

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

Zie [Aan de slag met Azure Table-opslag](../cosmos-db/table-storage-how-to-use-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert `CloudTable` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](#azure-storage-sdk-version-in-functions-1x)


# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een binding van tabelinvoer weergegeven in een *function.json-bestand* en [JavaScript-code](functions-reference-node.md) die de binding gebruikt. De functie gebruikt een wachtrijtrigger om één tabelrij te lezen. 

Het *bestand function.json* geeft a `partitionKey` en a `rowKey`. De `rowKey` waarde {queueTrigger}geeft aan dat de rijsleutel afkomstig is van de tekenreeks van het wachtrijbericht.

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

In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

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

In het volgende voorbeeld wordt een HTTP-geactiveerde functie weergegeven die een lijst met persoonsobjecten retourneert die zich in een opgegeven partitie in tabelopslag bevinden. In het voorbeeld wordt de partitiesleutel uit de http-route geëxtraheerd en de tabelNaam en verbinding zijn afkomstig uit de functie-instellingen. 

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

De tableinput-annotatie kan ook de bindingen uit de json-hoofdtekst van de aanvraag extraheren, zoals in het volgende voorbeeld wordt weergegeven.

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

In de volgende voorbeelden wordt het filter gebruikt om te zoeken naar personen met een specifieke naam in een Azure-tabel en wordt het aantal mogelijke overeenkomsten beperkt tot 10 resultaten.

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

## <a name="input---attributes-and-annotations"></a>Invoer - kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

 Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)de volgende kenmerken om een tabelinvoerbinding te configureren:

* [Tabelkenmerk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  De constructor van het kenmerk neemt de tabelnaam, partitiesleutel en rijsleutel. Het kenmerk kan worden `out` gebruikt op een parameter of op de retourwaarde van de functie, zoals in het volgende voorbeeld wordt weergegeven:

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

  U `Connection` instellen dat de eigenschap het opslagaccount opgeeft dat moet worden gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

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

  Zie Voorbeeld input - C# voor een volledig voorbeeld.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het te gebruiken opslagaccount op te geven. De constructeur neemt de naam aan van een app-instelling die een tekenreeks voor opslagverbindingen bevat. Het kenmerk kan worden toegepast op parameter-, methode- of klassenniveau. In het volgende voorbeeld ziet u het niveau van de klasse en het methodeniveau:

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

De te gebruiken opslagrekening wordt in de volgende volgorde bepaald:

* De `Table` eigenschap `Connection` van het attribuut.
* Het `StorageAccount` kenmerk dat is toegepast `Table` op dezelfde parameter als het kenmerk.
* Het `StorageAccount` kenmerk dat op de functie wordt toegepast.
* Het `StorageAccount` kenmerk dat op de klasse is toegepast.
* Het standaardopslagaccount voor de functie-app ('AzureWebJobsStorage'-appinstelling).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de annotatie op parameters waarvan de `@TableInput` waarde afkomstig zou zijn van tabelopslag.  Deze annotatie kan worden gebruikt met native Java-typen, `Optional<T>`POJOs of nullable waarden met behulp van .

---

## <a name="input---configuration"></a>Invoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Table` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `table`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet ingesteld `in`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal. |
|**Naam** | N.v.t. | De naam van de variabele die de tabel of entiteit in functiecode vertegenwoordigt. | 
|**tableName** | **Tabelnaam** | De naam van de tafel.| 
|**partitionKey** | **PartitionKey** |Optioneel. De partitiesleutel van de tabelentiteit die moet worden gelezen. Zie het [gebruiksgedeelte](#input---usage) voor richtlijnen over het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | Optioneel. De rijsleutel van de tabelentiteit om te lezen. Zie het [gebruiksgedeelte](#input---usage) voor richtlijnen over het gebruik van deze eigenschap.| 
|**Nemen** |**Nemen** | Optioneel. Het maximum aantal entiteiten dat moet worden gelezen in JavaScript. Zie het [gebruiksgedeelte](#input---usage) voor richtlijnen over het gebruik van deze eigenschap.| 
|**Filter** |**Filter** | Optioneel. Een OData-filterexpressie voor tabelinvoer in JavaScript. Zie het [gebruiksgedeelte](#input---usage) voor richtlijnen over het gebruik van deze eigenschap.| 
|**verbinding** |**Verbinding** | De naam van een app-instelling die de tekenreeks Opslagverbinding bevat die u voor deze binding wilt gebruiken. De instelling kan de naam zijn van een vooraf vastgestelde app-instelling of verbindingstekenreeksnaam met azurewebjobs. Als uw instellingsnaam bijvoorbeeld 'AzureWebJobsMyStorage' is, u hier 'MyStorage' opgeven. De runtime van de functies zoekt automatisch naar een app-instelling met de naam "AzureWebJobsMyStorage". Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer - gebruik

# <a name="c"></a>[C #](#tab/csharp)

* **Lees één rij in**

  Instellen `partitionKey` `rowKey`en . Toegang tot de tabelgegevens `T <paramName>`met behulp van een methodeparameter . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. `T`is meestal een type `ITableEntity` dat implementeert `TableEntity`of is afgeleid van . De `filter` `take` eigenschappen en eigenschappen worden in dit scenario niet gebruikt.

* **Een of meer rijen lezen**

  Toegang tot de tabelgegevens `IQueryable<T> <paramName>`met behulp van een methodeparameter . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. `T`moet een type zijn `ITableEntity` dat zich `TableEntity`implementeert of ontleent aan . U kunt `IQueryable` methoden gebruiken om alle vereiste filtering uit te werken. De `partitionKey` `rowKey`eigenschappen `filter`, `take` en eigenschappen worden in dit scenario niet gebruikt.  

  > [!NOTE]
  > `IQueryable`wordt niet ondersteund in de [runtime van functies v2.](functions-versions.md) Een alternatief is het [gebruik van een parameter van de CloudTable-parameter paramName-methode](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de Azure Storage SDK. Als u probeert `CloudTable` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](#azure-storage-sdk-version-in-functions-1x)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **Lees één rij in**

  Instellen `partitionKey` `rowKey`en . Toegang tot de tabelgegevens `T <paramName>`met behulp van een methodeparameter . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. `T`is meestal een type `ITableEntity` dat implementeert `TableEntity`of is afgeleid van . De `filter` `take` eigenschappen en eigenschappen worden in dit scenario niet gebruikt.

* **Een of meer rijen lezen**

  Toegang tot de tabelgegevens `IQueryable<T> <paramName>`met behulp van een methodeparameter . In het script `paramName` C# is `name` de waarde die is opgegeven in de eigenschap *function.json*. `T`moet een type zijn `ITableEntity` dat zich `TableEntity`implementeert of ontleent aan . U kunt `IQueryable` methoden gebruiken om alle vereiste filtering uit te werken. De `partitionKey` `rowKey`eigenschappen `filter`, `take` en eigenschappen worden in dit scenario niet gebruikt.  

  > [!NOTE]
  > `IQueryable`wordt niet ondersteund in de [runtime van functies v2.](functions-versions.md) Een alternatief is het [gebruik van een parameter van de CloudTable-parameter paramName-methode](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de Azure Storage SDK. Als u probeert `CloudTable` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](#azure-storage-sdk-version-in-functions-1x)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Stel `filter` de `take` eigenschappen en eigenschappen in. Niet instellen `partitionKey` of. `rowKey` Toegang tot de entiteit van de `context.bindings.<BINDING_NAME>`invoertabel (of entiteiten) met behulp van . De gedeserialiseerde `RowKey` `PartitionKey` objecten hebben en eigenschappen.

# <a name="python"></a>[Python](#tab/python)

Tabelgegevens worden doorgegeven aan de functie als JSON-tekenreeks. De-serialiseren van het `json.loads` bericht door aan te roepen zoals weergegeven in het [invoervoorbeeld](#input).

# <a name="java"></a>[Java](#tab/java)

Met het kenmerk [Tabelinvoer](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) hebt u toegang tot de tabelrij die de functie heeft geactiveerd.

---

## <a name="output"></a>Uitvoer

Gebruik een azure table-opslaguitvoerbinding om entiteiten naar een tabel in een Azure Storage-account te schrijven.

> [!NOTE]
> Deze uitvoerbinding biedt geen ondersteuning voor het bijwerken van bestaande entiteiten. Gebruik `TableOperation.Replace` de bewerking [van de Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) om een bestaande entiteit bij te werken.

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die een HTTP-trigger gebruikt om één tabelrij te schrijven. 

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld wordt een tabeluitvoerbinding weergegeven in een *function.json-bestand* en [C#-scriptcode](functions-reference-csharp.md) die de binding gebruikt. De functie schrijft meerdere tabelentiteiten.

Hier is het *function.json* bestand:

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

In de [configuratiesectie](#output---configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een tabeluitvoerbinding weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie schrijft meerdere tabelentiteiten.

Hier is het *function.json* bestand:

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

In de [configuratiesectie](#output---configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

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

In het volgende voorbeeld wordt uitgelegd hoe u de binding voor tabelopslaguitvoer gebruikt. De `table` binding is geconfigureerd in *function.json* door `name` `tableName`waarden `partitionKey`toe `connection`te wijsen aan , , en :

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

De volgende functie genereert een unieke `rowKey` uui voor de waarde en blijft het bericht in tabelopslag.

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

In het volgende voorbeeld wordt een Java-functie weergegeven die een HTTP-trigger gebruikt om één tabelrij te schrijven.

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

In het volgende voorbeeld wordt een Java-functie weergegeven die een HTTP-trigger gebruikt om meerdere tabelrijen te schrijven.

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

## <a name="output---attributes-and-annotations"></a>Uitvoer - kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het [tabelkenmerk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

De constructor van het kenmerk neemt de tabelnaam aan. Het kenmerk kan worden `out` gebruikt op een parameter of op de retourwaarde van de functie, zoals in het volgende voorbeeld wordt weergegeven:

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

U `Connection` instellen dat de eigenschap het opslagaccount opgeeft dat moet worden gebruikt, zoals in het volgende voorbeeld wordt weergegeven:

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

Zie Voorbeeld uitvoer [- C#](#output)voor een volledig voorbeeld .

U `StorageAccount` het kenmerk gebruiken om het opslagaccount op klasse-, methode- of parameterniveau op te geven. Zie [Invoer - kenmerken](#input---attributes-and-annotations)voor meer informatie .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de [tableoutput-annotatie](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) op parameters om waarden in tabelopslag te schrijven.

Zie het [voorbeeld voor meer details](#output).

---

## <a name="output---configuration"></a>Uitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `Table` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet ingesteld `table`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet ingesteld `out`zijn op. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure-portal. |
|**Naam** | N.v.t. | De variabele naam die wordt gebruikt in functiecode die de tabel of entiteit vertegenwoordigt. Ingesteld `$return` op de referentiewaarde voor het retourresultaat van de functie.| 
|**tableName** |**Tabelnaam** | De naam van de tafel.| 
|**partitionKey** |**PartitionKey** | De partitiesleutel van de tabelentiteit om te schrijven. Zie het [gebruiksgedeelte](#output---usage) voor richtlijnen over het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | De rijsleutel van de tabelentiteit om te schrijven. Zie het [gebruiksgedeelte](#output---usage) voor richtlijnen over het gebruik van deze eigenschap.| 
|**verbinding** |**Verbinding** | De naam van een app-instelling die de tekenreeks Opslagverbinding bevat die u voor deze binding wilt gebruiken. Als de naam van de app-instelling begint met 'AzureWebJobs', u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyStorage', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'MyStorage'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de `AzureWebJobsStorage`standaardverbindingstekenreeks voor opslag in de app-instelling met de naam .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

# <a name="c"></a>[C #](#tab/csharp)

Toegang tot de entiteit uitvoertabel `ICollector<T> paramName` `IAsyncCollector<T> paramName` met `T` behulp `PartitionKey` `RowKey` van een methodeparameter of waar de eigenschappen en eigenschappen zijn opgenomen. Deze eigenschappen gaan vaak `ITableEntity` gepaard met `TableEntity`het implementeren of erven.

U ook `CloudTable` een methodeparameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert `CloudTable` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](#azure-storage-sdk-version-in-functions-1x)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Toegang tot de entiteit uitvoertabel `ICollector<T> paramName` `IAsyncCollector<T> paramName` met `T` behulp `PartitionKey` `RowKey` van een methodeparameter of waar de eigenschappen en eigenschappen zijn opgenomen. Deze eigenschappen gaan vaak `ITableEntity` gepaard met `TableEntity`het implementeren of erven. De `paramName` waarde wordt `name` opgegeven in de eigenschap van *function.json*.

U ook `CloudTable` een methodeparameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert `CloudTable` te binden aan en een foutbericht ontvangt, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage SDK-versie.](#azure-storage-sdk-version-in-functions-1x)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot de `context.bindings.<name>` uitvoergebeurtenis met behulp van waar `<name>` is de waarde opgegeven in de `name` eigenschap van *function.json*.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitplaatsen van een bericht met de opslagrij van tabel vanuit een functie:

- **Retourwaarde:** Stel `name` de eigenschap in `$return` *function.json* in op . Met deze configuratie blijft de retourwaarde van de functie bestaan als een rij tabelopslag.

- **Noodzakelijk:** Geef een waarde door aan de [ingestelde](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) methode van de parameter die is opgegeven als [een uit-type.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) De waarde `set` die wordt doorgegeven aan wordt gehandhaafd als een Gebeurtenishub-bericht.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitzetten van een tabelopslagrij vanuit een functie met de annotatie [TableStorageOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Retourwaarde:** Door de annotatie toe te passen op de functie zelf, blijft de retourwaarde van de functie bestaan als een rij tabelopslag.

- **Noodzakelijk:** Als u de berichtwaarde expliciet wilt instellen, past u [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)de `T` aantekening toe op een specifieke parameter van het type , waar de `PartitionKey` eigenschappen en `RowKey` eigenschappen zijn. Deze eigenschappen gaan vaak `ITableEntity` gepaard met `TableEntity`het implementeren of erven.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Naslaginformatie |
|---|---|
| Tabel | [Tabelfoutcodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Tabel, Wachtrij | [Opslagfoutcodes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabel, Wachtrij | [Problemen oplossen](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)

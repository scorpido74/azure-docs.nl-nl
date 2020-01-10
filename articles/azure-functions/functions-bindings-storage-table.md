---
title: Azure Table Storage-bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Table Storage-bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 766bf1ba8e1070a3224bb9c50c527f6c709eb9a4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769435"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Table Storage-bindingen in Azure Functions. Azure Functions ondersteunt invoer-en uitvoer bindingen voor Azure Table-opslag.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

De tabel opslag bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet versie 2. x. Broncode voor het pakket is in de [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub-opslagplaats.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De tabel opslag bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-pakket, versie 3. x. Broncode voor het pakket is in de [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub-opslagplaats.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Invoer

Gebruik de Azure Table Storage-invoer binding om een tabel in een Azure Storage-account te lezen.

## <a name="input---example"></a>Invoer-voor beeld

Zie het voorbeeld taalspecifieke:

* [C#Eén entiteit lezen](#input---c-example---one-entity)
* [C#binden aan IQueryable](#input---c-example---iqueryable)
* [C#verbinden met CloudTable](#input---c-example---cloudtable)
* [C#script voor het lezen van één entiteit](#input---c-script-example---one-entity)
* [C#script binding met IQueryable](#input---c-script-example---iqueryable)
* [C#script binding met CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Invoer- C# voor beeld-één entiteit

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee één tabelrij wordt gelezen. 

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

### <a name="input---c-example---iqueryable"></a>Invoer- C# voor beeld-IQueryable

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee meerdere tabel rijen worden gelezen. Houd er rekening mee dat de klasse `MyPoco` is afgeleid van `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Invoer- C# voor beeld-CloudTable

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

### <a name="input---c-script-example---one-entity"></a>Voor beeld C# van invoer script-één entiteit

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

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

### <a name="input---c-script-example---iqueryable"></a>Invoer C# script-voor beeld-IQueryable

In het volgende voor beeld ziet u een tabel-invoer binding in een *Function. json* -bestand en [ C# script](functions-reference-csharp.md) code die gebruikmaken van de binding. De functie leest entiteiten voor een partitie sleutel die is opgegeven in een wachtrij bericht.

Hier volgt de *function.json* bestand:

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

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

### <a name="input---c-script-example---cloudtable"></a>Voor beeld C# van invoer script-CloudTable

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

### <a name="input---f-example"></a>Invoer- F# voor beeld

In het volgende voor beeld ziet u een tabel-invoer binding in een *Function. json* -bestand en [ F# script](functions-reference-fsharp.md) code die gebruikmaken van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Hier volgt de F# code:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Invoer-java script-voor beeld

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

De [configuratie](#input---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Invoer-java-voor beeld

In het volgende voor beeld ziet u een HTTP-geactiveerde functie die het totale aantal items in een opgegeven partitie in tabel opslag retourneert.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Invoer - kenmerken
 
Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken voor het configureren van een tabel-invoer binding:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  De constructor van het kenmerk heeft de tabel naam, de partitie sleutel en de rij-sleutel. Het kan worden gebruikt op een uitvoer parameter of op de geretourneerde waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

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

* De `Table` van kenmerk `Connection` eigenschap.
* De `StorageAccount` kenmerk toegepast op de dezelfde parameter als de `Table` kenmerk.
* De `StorageAccount` kenmerk toegepast op de functie.
* De `StorageAccount` kenmerk toegepast op de klasse.
* Het standaard opslag account voor de functie-app (de app-instelling AzureWebJobsStorage).

## <a name="input---java-annotations"></a>Invoer-java-aantekeningen

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@TableInput` annotatie op para meters waarvan de waarde afkomstig is uit de tabel opslag.  Deze aantekening kan worden gebruikt met systeem eigen Java-typen, Pojo's of nullable-waarden met behulp van optionele\<T >. 

## <a name="input---configuration"></a>Invoer - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `Table` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op `table`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | n.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**De naam** | n.v.t. | De naam van de variabele die de tabel of entiteit in functie code vertegenwoordigt. | 
|**tableName** | **TableName** | De naam van de tabel.| 
|**partitionKey** | **partitionKey** |Optioneel. De partitie sleutel van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | Optioneel. De rij van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**take** |**Houd** | Optioneel. Het maximum aantal entiteiten dat in Java script kan worden gelezen. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**filter** |**Filter** | Optioneel. Een OData-filter expressie voor tabel invoer in Java script. Zie de sectie [gebruik](#input---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**verbinding** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Invoer - gebruik

De invoer binding voor tabel opslag ondersteunt de volgende scenario's:

* **Eén rij in C# of C# script lezen**

  Stel `partitionKey` en `rowKey`in. Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` is doorgaans een type dat `ITableEntity` of een afleiding van `TableEntity`implementeert. De eigenschappen `filter` en `take` worden niet in dit scenario gebruikt. 

* **Een of meer rijen in C# een C# script lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` moet een type zijn dat `ITableEntity` of afgeleid van `TableEntity`implementeert. U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De eigenschappen `partitionKey`, `rowKey`, `filter`en `take` worden niet in dit scenario gebruikt.  

  > [!NOTE]
  > `IQueryable` wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt.

* **Een of meer rijen in Java script lezen**

  Stel de eigenschappen `filter` en `take` in. Stel `partitionKey` of `rowKey`niet in. De entiteit (of entiteiten) van de invoer tabel openen met behulp van `context.bindings.<BINDING_NAME>`. De gedeserialiseerd objecten hebben `RowKey`-en `PartitionKey`-eigenschappen.

## <a name="output"></a>Uitvoer

Gebruik een Azure Table Storage-uitvoer binding om entiteiten te schrijven naar een tabel in een Azure Storage-account.

> [!NOTE]
> Deze uitvoer binding biedt geen ondersteuning voor het bijwerken van bestaande entiteiten. Gebruik de juiste [`TableOperation`](/dotnet/api/microsoft.azure.cosmos.table.tableoperation?view=azure-dotnet) van de [Azure Storage SDK](/azure/cosmos-db/tutorial-develop-table-dotnet#insert-or-merge-an-entity) om een bestaande entiteit zo nodig bij te werken.   

## <a name="output---example"></a>Uitvoer - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Uitvoer - voorbeeld met C#

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

### <a name="output---c-script-example"></a>Uitvoer - voorbeeld van C#-script

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *Function. json* -bestand en [ C# script](functions-reference-csharp.md) code die gebruikmaken van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Hier volgt de *function.json* bestand:

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

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

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

### <a name="output---f-example"></a>Uitvoer - F# voorbeeld

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *Function. json* -bestand en [ F# script](functions-reference-fsharp.md) code die gebruikmaken van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Hier volgt de *function.json* bestand:

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

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

Hier volgt de F# code:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Uitvoer - JavaScript-voorbeeld

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Hier volgt de *function.json* bestand:

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

De [configuratie](#output---configuration) sectie wordt uitgelegd dat deze eigenschappen.

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

## <a name="output---attributes"></a>Uitvoer - kenmerken

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

De constructor van het kenmerk heeft de tabel naam. Het kan worden gebruikt op een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

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

Zie voor een compleet voorbeeld [uitvoer - voorbeeld met C#](#output---c-example).

U kunt het kenmerk `StorageAccount` gebruiken om het opslag account op te geven bij klasse, methode of parameter niveau. Zie [invoer kenmerken](#input---attributes)voor meer informatie.

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel beschrijft de binding configuratie-eigenschappen die u instelt in de *function.json* bestand en de `Table` kenmerk.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op `table`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | n.v.t. | Moet worden ingesteld op `out`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**De naam** | n.v.t. | De naam van de variabele die wordt gebruikt in de functie code die de tabel of entiteit vertegenwoordigt. Ingesteld op `$return` om te verwijzen naar de retour waarde van de functie.| 
|**tableName** |**TableName** | De naam van de tabel.| 
|**partitionKey** |**partitionKey** | De partitie sleutel van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#output---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | De rij van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#output---usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**verbinding** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' mijn opslag ', zoekt de runtime van functions naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag connection string in de app-instelling met de naam `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uitvoer - gebruik

De tabel opslag-uitvoer binding ondersteunt de volgende scenario's:

* **Eén rij in een wille keurige taal schrijven**

  In C# en C# script opent u de entiteit uitvoer tabel met behulp van een methode parameter, zoals `out T paramName` of de retour waarde van de functie. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` kan elk wille keurig serialiseerbaar type zijn als de partitie sleutel en de rij sleutel worden gegeven door het bestand *Function. json* of het kenmerk `Table`. Anders moet `T` een type zijn dat `PartitionKey` en `RowKey` eigenschappen bevat. In dit scenario implementeert `T` meestal `ITableEntity` of is afgeleid van `TableEntity`, maar dit is niet nodig.

* **Een of meer rijen in C# een C# script schrijven**

  In C# en C# script opent u de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName`. In C# script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *Function. json*. `T` Hiermee geeft u het schema op van de entiteiten die u wilt toevoegen. Normaal gesp roken `T` afgeleid van `TableEntity` of wordt `ITableEntity`geïmplementeerd, maar dit is niet nodig. De waarden voor de partitie sleutel en de rijwaarden in *Function. json* of de `Table` kenmerk-constructor worden niet in dit scenario gebruikt.

  U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` en een fout bericht ontvangt, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](#azure-storage-sdk-version-in-functions-1x)hebt. Zie voor een voor beeld van code die is gekoppeld aan `CloudTable`de voor beelden van de [C#](#input---c-example---cloudtable) invoer bindingen voor of [ C# het script](#input---c-script-example---cloudtable) eerder in dit artikel.

* **Een of meer rijen schrijven in Java script**

  In Java script-functies opent u de tabel uitvoer met behulp van `context.bindings.<BINDING_NAME>`.

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Tabel | [Fout codes voor tabellen](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabel, wachtrij | [Opslag fout codes](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij | [Probleemoplossing](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en bindingen](functions-triggers-bindings.md)

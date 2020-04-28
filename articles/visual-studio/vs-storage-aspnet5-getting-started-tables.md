---
title: Aan de slag met tabel opslag met Visual Studio (ASP.NET Core)
description: Hoe kunt u aan de slag gaan met Azure Table Storage in een ASP.NET Core-project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298811"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Aan de slag met Azure Table Storage en Visual Studio Connected Services

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Table Storage in Visual Studio nadat u een Azure-opslag account in een ASP.NET Core project hebt gemaakt of hiernaar wordt verwezen met behulp van de functie voor **verbonden services** van Visual Studio. **Met de verbinding Connected Services** worden de juiste NuGet-pakketten geïnstalleerd voor toegang tot Azure Storage in uw project en wordt de Connection String voor het opslag account toegevoegd aan uw project configuratie bestanden. (Zie [opslag documentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.)

Met de Azure Table Storage-service kunt u grote hoeveel heden gestructureerde gegevens opslaan. De service is een NoSQL-gegevens archief dat geverifieerde aanroepen binnen en buiten de Azure-Cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Zie [aan de slag met Azure Table Storage met .net](../storage/storage-dotnet-how-to-use-tables.md)voor meer algemene informatie over het gebruik van Azure Table Storage.

Maak eerst een tabel in uw opslag account om aan de slag te gaan. In dit artikel wordt uitgelegd hoe u een tabel in C# maakt en hoe u elementaire tabel bewerkingen uitvoert, zoals het toevoegen, wijzigen, lezen en verwijderen van tabel items.  De code maakt gebruik van de Azure Storage-client bibliotheek voor .NET. Zie [ASP.net](https://www.asp.net)voor meer informatie over ASP.net.

Sommige van de Azure Storage Api's zijn asynchroon en de code in dit artikel gaat ervan uit dat er asynchrone methoden worden gebruikt. Zie [asynchrone programmering](https://docs.microsoft.com/dotnet/csharp/async) voor meer informatie.

## <a name="access-tables-in-code"></a>Access-tabellen in code

Als u toegang wilt krijgen tot tabellen in ASP.NET Core projecten, moet u de volgende items toevoegen aan alle C#-bron bestanden die toegang hebben tot Azure-tabel opslag.

1. De benodigde `using` instructies toevoegen:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Een `CloudStorageAccount` object ophalen dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code met behulp van de naam van uw opslag account en de account sleutel, die u in de opslag connection string in appSettings. json kunt vinden:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Een `CloudTableClient` object ophalen om te verwijzen naar de tabel objecten in uw opslag account:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Een `CloudTable` referentie object ophalen om te verwijzen naar een specifieke tabel en entiteiten:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Een tabel in code maken

Als u de Azure-tabel wilt maken, maakt u een async-methode `CreateIfNotExistsAsync()`en roept u deze aan:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse waarmee de eigenschappen van uw entiteit worden gedefinieerd. `CustomerEntity` Met de volgende code wordt een entiteit klasse gedefinieerd met de naam die de voor naam van de klant als de rij-en achternaam als de partitie sleutel gebruikt.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Tabel bewerkingen waarbij entiteiten worden gebruikt `CloudTable` , maken gebruik van het object dat u eerder hebt gemaakt in [Access-tabellen in code](#access-tables-in-code). Het `TableOperation` object vertegenwoordigt de bewerking die moet worden uitgevoerd. In het volgende code voorbeeld ziet u hoe u `CloudTable` een object en `CustomerEntity` een object maakt. Voor het voorbereiden van de bewerking `TableOperation` wordt een gemaakt om de klant entiteit in de tabel in te voegen. Ten slotte wordt de bewerking uitgevoerd door aan `CloudTable.ExecuteAsync`te roepen.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Een batch entiteiten invoegen

U kunt in één schrijf bewerking meerdere entiteiten invoegen in een tabel. Het volgende code voorbeeld maakt twee entiteits objecten ("Jeff Smith" en "ben Smith"), voegt deze toe `TableBatchOperation` aan een object `Insert` met behulp van de-methode en start `CloudTable.ExecuteBatchAsync`de bewerking door aan te roepen.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Gebruik een `TableQuery` -object om een tabel voor alle entiteiten in een partitie op te vragen. Het volgende codevoorbeeld geeft een filter voor entiteiten waarbij 'Smith' de partitiesleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Eén entiteit ophalen

U kunt een query schrijven om één specifieke entiteit te verkrijgen. De volgende code gebruikt een `TableOperation` -object om een klant met de naam ' ben Smith ' op te geven. De methode retourneert slechts één entiteit, in plaats van een verzameling, en de geretourneerde `TableResult.Result` waarde in `CustomerEntity` is een object. Het opgeven van zowel de partitie-als de rijwaarden in een query is de snelste manier om één entiteit `Table` uit de service op te halen.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen

U kunt een entiteit verwijderen nadat u deze hebt gevonden. Met de volgende code wordt een klant entiteit met de naam ' ben Smith ' gezocht en verwijderd:

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

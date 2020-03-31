---
title: Aan de slag met tabelopslag met Visual Studio (ASP.NET Core)
description: Aan de slag met Azure Table-opslag in een ASP.NET Core-project in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met visual studio-verbonden services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298811"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Aan de slag met Azure Table-opslag en Visual Studio connected services

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

In dit artikel wordt beschreven hoe u aan de slag met Azure Table-opslag in Visual Studio nadat u een Azure-opslagaccount hebt gemaakt of verwezen in een ASP.NET **Core-project** met behulp van de functie Visual Studio Connected Services. De **bewerking Verbonden Services** installeert de juiste NuGet-pakketten om toegang te krijgen tot Azure-opslag in uw project en voegt de verbindingstekenreeks voor het opslagaccount toe aan uw projectconfiguratiebestanden. (Zie [Opslagdocumentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.)

Met de Azure Table-opslagservice u grote hoeveelheden gestructureerde gegevens opslaan. De service is een NoSQL-gegevensarchief dat geverifieerde oproepen van binnen en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens. Zie [Aan de slag met Azure Table-opslag met .NET](../storage/storage-dotnet-how-to-use-tables.md)voor meer algemene informatie over het gebruik van Azure Table-opslag.

Maak eerst een tabel in uw opslagaccount om aan de slag te gaan. In dit artikel wordt vervolgens uitgelegd hoe u een tabel in C# maakt en hoe u basistabelbewerkingen uitvoeren, zoals het toevoegen, wijzigen, lezen en verwijderen van tabelvermeldingen.  De code maakt gebruik van de Azure Storage Client Library voor .NET. Zie [ASP.NET](https://www.asp.net)voor meer informatie over ASP.NET.

Sommige Azure Storage API's zijn asynchroon en de code in dit artikel gaat ervan uit dat async-methoden worden gebruikt. Zie [Asynchrone programmering](https://docs.microsoft.com/dotnet/csharp/async) voor meer informatie.

## <a name="access-tables-in-code"></a>Toegangstabellen in code

Als u toegang wilt krijgen tot tabellen in ASP.NET Core-projecten, moet u de volgende items opnemen in c#-bronbestanden die toegang hebben tot Azure-tabelopslag.

1. Voeg de `using` nodige instructies toe:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Download `CloudStorageAccount` een object dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code met de naam van uw opslagaccount en de accountsleutel, die u vinden in de tekenreeks opslagverbinding in appSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Zorg `CloudTableClient` ervoor dat een object verwijst naar de tabelobjecten in uw opslagaccount:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Een `CloudTable` referentieobject weergeven om naar een specifieke tabel en entiteiten te verwijzen:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Een tabel in code maken

Als u de Azure-tabel wilt maken, maakt `CreateIfNotExistsAsync()`u een async-methode en roept u:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Als u een entiteit aan een tabel wilt toevoegen, maakt u een klasse die de eigenschappen van uw entiteit definieert. De volgende code definieert `CustomerEntity` een entiteitsklasse met de naam van de klant als de rijsleutel en achternaam als partitiesleutel.

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

Tabelbewerkingen waarbij entiteiten `CloudTable` betrokken zijn, gebruiken het object dat u eerder in [Access-tabellen in code hebt](#access-tables-in-code)gemaakt. Het `TableOperation` object vertegenwoordigt de bewerking die moet worden uitgevoerd. In het volgende codevoorbeeld `CloudTable` ziet u `CustomerEntity` hoe u een object en een object maakt. Om de bewerking `TableOperation` voor te bereiden, wordt een bewerking gemaakt om de klantentiteit in de tabel in te voegen. Ten slotte wordt de bewerking `CloudTable.ExecuteAsync`uitgevoerd door aan te roepen .

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

U meerdere entiteiten in voegen in een tabel in één schrijfbewerking. In het volgende codevoorbeeld worden twee entiteitsobjecten ('Jeff Smith' en 'Ben Smith') gemaakt, wordt deze toegevoegd aan een `TableBatchOperation` object met behulp van de `Insert` methode en wordt de bewerking gestart door aanteroepen `CloudTable.ExecuteBatchAsync`.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Alle entiteiten in een partitie oppakken

Als u een tabel wilt opvragen voor alle `TableQuery` entiteiten in een partitie, gebruikt u een object. Het volgende codevoorbeeld geeft een filter voor entiteiten waarbij 'Smith' de partitiesleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.

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

## <a name="get-a-single-entity"></a>Eén entiteit oppakken

U een query schrijven om één specifieke entiteit te krijgen. De volgende code `TableOperation` gebruikt een object om een klant met de naam 'Ben Smith' op te geven. De methode retourneert slechts één entiteit, in `TableResult.Result` plaats `CustomerEntity` van een verzameling, en de geretourneerde waarde in is een object. Het opgeven van zowel partitie- als rijsleutels in een query `Table` is de snelste manier om één entiteit uit de service op te halen.

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

U een entiteit verwijderen nadat u deze hebt gevonden. De volgende code zoekt naar en verwijdert een klantentiteit met de naam "Ben Smith":

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

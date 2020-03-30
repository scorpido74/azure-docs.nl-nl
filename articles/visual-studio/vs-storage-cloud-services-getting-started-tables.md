---
title: Aan de slag met tabelopslag met Visual Studio (cloudservices)
description: Aan de slag met Azure Table-opslag in een cloudserviceproject in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met visual studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299987"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Aan de slag met aan Azure Table Storage en Visual Studio verbonden services (cloudserviceprojecten)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag met Azure-tabelopslag in Visual Studio nadat u een Azure-opslagaccount in een cloudservicesproject hebt gemaakt of ernaar hebt verwezen met behulp van het dialoogvenster **Verbonden services toevoegen** van Visual Studio. Met de bewerking **Verbonden services toevoegen** worden de juiste NuGet-pakketten geïnstalleerd om toegang te krijgen tot Azure-opslag in uw project en wordt de verbindingstekenreeks voor het opslagaccount toegevoegd aan uw projectconfiguratiebestanden.

Met de Azure Table-opslagservice u grote hoeveelheden gestructureerde gegevens opslaan. De service is een NoSQL-gegevensarchief dat geverifieerde oproepen van binnen en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens.

Om aan de slag te gaan, moet je eerst een tabel maken in je opslagaccount. We laten u zien hoe u een Azure-tabel in code maakt en ook hoe u basisbewerkingen van tabel- en entiteiten uitvoeren, zoals het toevoegen, wijzigen, lezen en lezen van tabelentiteiten. De voorbeelden zijn\# geschreven in C-code en gebruiken de [Microsoft Azure Storage-clientbibliotheek voor .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**LET OP:** Sommige API's die oproepen uitvoeren naar Azure-opslag zijn asynchroon. Zie [Asynchrone programmering met Async en wacht op](https://msdn.microsoft.com/library/hh191443.aspx) meer informatie. De onderstaande code gaat ervan uit dat async-programmeermethoden worden gebruikt.

* Zie [Aan de slag met Azure Table-opslag met .NET](../storage/storage-dotnet-how-to-use-tables.md) voor meer informatie over programmatisch manipuleren van tabellen.
* Zie [Opslagdocumentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.
* Zie [Cloud Services-documentatie](https://azure.microsoft.com/documentation/services/cloud-services/) voor algemene informatie over Azure-cloudservices.
* Zie [ASP.NET](https://www.asp.net) voor meer informatie over het programmeren ASP.NET toepassingen.

## <a name="access-tables-in-code"></a>Toegangstabellen in code
Als u toegang wilt krijgen tot tabellen in cloudserviceprojecten, moet u de volgende items opnemen in c#-bronbestanden die toegang hebben tot Azure-tabelopslag.

1. Zorg ervoor dat de naamruimtedeclaratie boven aan het C#-bestand deze **bevat met behulp van** instructies.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Download een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de gegevens van de opslagverbinding en het opslagaccount uit de Azure-serviceconfiguratie te halen.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Gebruik alle bovenstaande code voor de code in de volgende monsters.
   > 
   > 
3. Zorg ervoor dat een **CloudTableClient-object** verwijst naar de tabelobjecten in uw opslagaccount.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Zorg ervoor dat een **CloudTable-referentieobject** verwijst naar een specifieke tabel en entiteiten.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Een tabel in code maken
Als u de Azure-tabel wilt maken, voegt u een aanroep toe aan **CreateIfNotExistsAsync** aan de nadat u een **CloudTable-object** hebt gekregen zoals beschreven in de sectie 'Toegangstabellen in code'.

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel
Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert. De volgende code definieert een entiteitsklasse met de naam **CustomerEntity** die de voornaam van de klant gebruikt als de rijsleutel en de achternaam als partitiesleutel.

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

Tabelbewerkingen met entiteiten worden uitgevoerd met het **cloudobject CloudTable** dat u eerder hebt gemaakt in 'Toegangstabellen in code'. Het object **TableOperation** vertegenwoordigt de bewerking die moet worden uitgevoerd. In het volgende codevoorbeeld ziet u hoe u een **CloudTable-object** en een **Object CustomerEntity** maakt. Om de bewerking voor te bereiden, wordt een **TableOperation** gemaakt om de klantentiteit in de tabel in te voegen. Ten slotte wordt de bewerking uitgevoerd door **CloudTable.ExecuteAsync aan**te roepen.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Een batch entiteiten invoegen
U meerdere entiteiten in voegen in een tabel in één schrijfbewerking. In het volgende codevoorbeeld worden twee entiteitsobjecten ('Jeff Smith' en 'Ben Smith') ingevoegd, wordt deze toegevoegd aan een object **TableBatchOperation** met behulp van de methode Invoegen en wordt de bewerking gestart door **CloudTable.ExecuteBatchAsync aan**te roepen.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Alle entiteiten in een partitie oppakken
Als u een tabel wilt opvragen voor alle entiteiten in een partitie, gebruikt u een object **TableQuery.** Het volgende codevoorbeeld geeft een filter voor entiteiten waarbij 'Smith' de partitiesleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

    return View();


## <a name="get-a-single-entity"></a>Eén entiteit oppakken
U een query schrijven om één specifieke entiteit te krijgen. De volgende code gebruikt een **object TableOperation** om een klant met de naam 'Ben Smith' op te geven. Met deze methode wordt slechts één entiteit geretourneerd in plaats van een verzameling en de geretourneerde waarde in **TabelResultaat.Resultaat** is een object **CustomerEntity.** Het opgeven van zowel partitie- als rijsleutels in een query is de snelste manier om één entiteit uit de **tabelservice** op te halen.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Een entiteit verwijderen
U een entiteit verwijderen nadat u deze hebt gevonden. De volgende code zoekt naar een klantentiteit met de naam "Ben Smith", en als deze deze wordt gevonden, wordt deze verwijderd.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

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

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]


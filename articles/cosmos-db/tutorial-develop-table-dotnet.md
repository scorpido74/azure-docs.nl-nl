---
title: Aan de slag met Azure Cosmos DB Table-API met behulp van de .NET Standard SDK
description: Sla gestructureerde gegevens op in de Cloud met behulp van de Azure Cosmos DB Table-API.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 05/20/2019
ms.openlocfilehash: 677ea48244f8417670a2645ab67fa08c1f869f1a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142588"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Aan de slag met Azure Cosmos DB Table-API en Azure-tabel opslag met behulp van de .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

U kunt de Azure Cosmos DB Table-API of Azure-tabel opslag gebruiken om gestructureerde NoSQL-gegevens op te slaan in de Cloud, waarbij u een sleutel/kenmerk opslag oplevert met een schema dat minder hoeft te worden ontworpen. Omdat Azure Cosmos DB Table-API en tabel opslag minder schema's zijn, is het eenvoudig om uw gegevens aan te passen aan de ontwikkeling van uw toepassing. U kunt Azure Cosmos DB Table-API of de tabel opslag gebruiken voor het opslaan van flexibele gegevens sets, zoals gebruikers gegevens voor webtoepassingen, adres boeken, apparaatgegevens of andere soorten meta gegevens die uw service nodig heeft. 

In deze zelf studie wordt een voor beeld beschreven van het gebruik [van de Microsoft Azure Cosmos DB-tabel bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) met Azure Cosmo DB-Table-API en Scenario's voor Azure-tabel opslag. U moet de verbinding die specifiek is voor de Azure-service gebruiken. Deze scenario's worden beschreven met voor C# beelden van het maken van tabellen, invoegen/bijwerken van gegevens, query's uitvoeren op gegevens en de tabellen verwijderen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig voor dit voorbeeld:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB-tabel bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) : deze bibliotheek is momenteel beschikbaar voor .NET Standard en .NET Framework. 

* [Azure Cosmos DB Table-API-account](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Een Azure Cosmos DB Table-API-account maken

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Een .NET-console project maken

Maak in Visual Studio een nieuwe .NET-console toepassing. De volgende stappen laten zien hoe u een console toepassing maakt in Visual Studio 2019. U kunt de Azure Cosmos DB-tabel bibliotheek gebruiken in elk type .NET-toepassing, waaronder een Azure-Cloud service of web-app, en desktop-en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

1. Selecteer **Bestand** > **Nieuw** > **Project**.

1. Kies **console-app (.net core)** en selecteer **volgende**.

1. Voer in het veld **project naam** een naam in voor uw toepassing, zoals **CosmosTableSamples**. (U kunt desgewenst een andere naam opgeven.)

1. Selecteer **Maken**.

Alle code voorbeelden in dit voor beeld kunnen worden toegevoegd aan de methode Main () van het **Program.cs** -bestand van uw console toepassing.

## <a name="install-the-required-nuget-package"></a>Het vereiste NuGet-pakket installeren

Voer de volgende stappen uit om het NuGet-pakket te verkrijgen:

1. Klik met de rechtermuisknop op het project in **Solution Explorer** en kies **NuGet-pakketten beheren**.

1. `Microsoft.Azure.Cosmos.Table`Zoek online naar, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`enselecteerinstalleren om de Microsoft Azure Cosmos DB-tabel bibliotheek te installeren. `Microsoft.Extensions.Configuration.Binder`

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

1. Ga vanuit het [Azure Portal](https://portal.azure.com/)naar uw Azure Cosmos-account of het Table Storage-account. 

1. Open de **verbindings reeks** of het deel venster **toegangs sleutels** . Gebruik de kopieerknop aan de rechterkant van het venster om de **PRIMARY CONNECTION STRING** te kopiëren.

   ![De PRIMARY CONNECTION STRING in het deelvenster Verbindingsreeks weergeven en kopiëren](./media/create-table-dotnet/connection-string.png)
   
1. Als u uw connection string wilt configureren, klikt u in Visual Studio met de rechter muisknop op uw project **CosmosTableSamples**.

1. Selecteer **toevoegen** en vervolgens **Nieuw item**. Maak een nieuwe bestands **instellingen. json** met het bestands type **type script JSON-configuratie** bestand. 

1. Vervang de code in het bestand settings. json door de volgende code en wijs uw primaire connection string toe:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klik met de rechter muisknop op uw project **CosmosTableSamples**. Selecteer **toevoegen**, **Nieuw item** en voeg een klasse toe met de naam **AppSettings.cs**.

1. Voeg de volgende code toe aan het AppSettings.cs-bestand. Dit bestand leest de connection string van het bestand settings. json en wijst het toe aan de configuratie parameter:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>De verbindings gegevens parseren en valideren 

1. Klik met de rechter muisknop op uw project **CosmosTableSamples**. Selecteer **toevoegen**, **Nieuw item** en voeg een klasse toe met de naam **common.cs**. U schrijft code voor het valideren van de verbindings gegevens en het maken van een tabel in deze klasse.

1. Definieer een methode `CreateStorageAccountFromConnectionString` zoals hieronder wordt weer gegeven. Met deze methode worden de connection string gegevens geparseerd en wordt gecontroleerd of de account naam en de gegevens van de account sleutel in het bestand settings. json geldig zijn. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Een tabel maken 

Met behulp van de [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient)-klasse kunt u tabellen en entiteiten ophalen die zijn opgeslagen in de Table Storage. Omdat we geen tabellen hebben in de Cosmos db table-API-account, gaan we de `CreateTableAsync` methode toevoegen aan de klasse **common.cs** om een tabel te maken:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>De entiteit definiëren 

Entiteiten worden toegewezen C# aan objecten met behulp van een aangepaste klasse die is afgeleid van [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert.

Klik met de rechter muisknop op uw project **CosmosTableSamples**. Selecteer **toevoegen**, **nieuwe map** en geef deze de naam **model**. Voeg in de map model een klasse toe met de naam **CustomerEntity.cs** en voeg hieraan de volgende code toe.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Deze code definieert een entiteits klasse die de voor naam van de klant als de rij-en achternaam als de partitie sleutel gebruikt. De entiteit wordt in de tabel op unieke wijze geïdentificeerd door diens partitie- en rijsleutel. Entiteiten met dezelfde partitie sleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitie sleutels, maar het gebruik van verschillende partitie sleutels maakt een grotere schaal baarheid van parallelle bewerkingen mogelijk. Entiteiten die moeten worden opgeslagen in tabellen moeten van een ondersteund type zijn, bijvoorbeeld afgeleid van de [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) -klasse. De entiteitseigenschappen die u in een tabel wilt opslaan, moeten openbare eigenschappen van het type zijn. Bovendien moeten ze zowel het ophalen als het instellen van waarden ondersteunen. Daarnaast moet het type entiteit een constructor zonder para meters weer geven.

## <a name="insert-or-merge-an-entity"></a>Een entiteit invoegen of samen voegen

In het volgende code voorbeeld wordt een entiteits object gemaakt en toegevoegd aan de tabel. De methode InsertOrMerge binnen de klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) wordt gebruikt om een entiteit in te voegen of samen te voegen. De methode [CloudTable. ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) wordt aangeroepen om de bewerking uit te voeren. 

Klik met de rechter muisknop op uw project **CosmosTableSamples**. Selecteer **toevoegen**, **Nieuw item** en voeg een klasse toe met de naam **SamplesUtils.cs**. In deze klasse wordt alle code opgeslagen die is vereist voor het uitvoeren van ruwe bewerkingen op de entiteiten. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Een entiteit uit een partitie ophalen

U kunt de entiteit ophalen uit een partitie met de methode ophalen onder de klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . In het volgende code voorbeeld worden de partitie sleutel, het e-mail adres en het telefoon nummer van een klant entiteit opgehaald. In dit voor beeld worden ook de verbruikte aanvraag eenheden afgedrukt om een query voor de entiteit uit te kunnen zien. Als u een query wilt uitvoeren voor een entiteit, voegt u de volgende code toe aan het **SamplesUtils.cs** -bestand: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Een entiteit verwijderen

U kunt een entiteit gemakkelijk verwijderen nadat u deze hebt opgehaald. Hiervoor gebruikt u hetzelfde patroon dat is getoond voor het bijwerken van een entiteit. Met de volgende code wordt een klantentiteit opgehaald en verwijderd. Als u een entiteit wilt verwijderen, voegt u de volgende code toe aan het **SamplesUtils.cs** -bestand: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>De ruwe bewerkingen uitvoeren op voorbeeld gegevens

Nadat u de methoden voor het maken van een tabel, het invoegen of samen voegen van een entiteit hebt gedefinieerd, voert u deze methoden uit op de voorbeeld gegevens. Hiertoe klikt u met de rechter muisknop op uw project **CosmosTableSamples**. Selecteer **toevoegen**, **Nieuw item** en voeg een klasse toe met de naam **BasicSamples.cs** en voeg hieraan de volgende code toe. Met deze code wordt een tabel gemaakt waaraan entiteiten worden toegevoegd. Als u de entiteit en tabel aan het einde van het project wilt verwijderen, verwijdert u de opmerkingen `table.DeleteIfExistsAsync()` uit `SamplesUtils.DeleteEntityAsync(table, customer)` en-methoden uit de volgende code:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Met de vorige code wordt een tabel gemaakt die begint met ' demo ' en de gegenereerde GUID wordt toegevoegd aan de tabel naam. Vervolgens wordt een klant entiteit met de voor-en achternaam toegevoegd als ' harp Walter ' en wordt het telefoon nummer van deze gebruiker later bijgewerkt. 

In deze zelf studie hebt u code gemaakt voor het uitvoeren van elementaire ruwe bewerkingen op de gegevens die zijn opgeslagen in Table-API-account. U kunt ook geavanceerde bewerkingen uitvoeren, zoals – batch invoegen van gegevens, het opvragen van alle gegevens in een partitie, het opvragen van een bereik van gegevens in een partitie, het weer geven van tabellen in het account waarvan de namen beginnen met het opgegeven voor voegsel. U kunt het volledige voorbeeld formulier downloaden van [Azure-Cosmos-Table-DotNet-core-Getting-Started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) github-opslag plaats. De klasse [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) heeft meer bewerkingen die u kunt uitvoeren op de gegevens.  

## <a name="run-the-project"></a>Het project uitvoeren

Vanuit uw project **CosmosTableSamples**. Open de klasse met de naam **Program.cs** en voeg de volgende code toe voor het aanroepen van BasicSamples wanneer het project wordt uitgevoerd.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Bouw nu de oplossing en druk op F5 om het project uit te voeren. Wanneer het project wordt uitgevoerd, wordt de volgende uitvoer weer gegeven in de opdracht prompt:

![Uitvoer vanaf opdracht prompt](./media/tutorial-develop-table-standard/output-from-sample.png)

Als er een fout bericht wordt weer gegeven met de tekst instellingen. JSON-bestand is niet gevonden bij het uitvoeren van het project, kunt u dit oplossen door de volgende XML-vermelding toe te voegen aan de project instellingen. Klik met de rechter muisknop op CosmosTableSamples, selecteer Bewerken CosmosTableSamples. csproj en voeg de volgende itemGroup toe: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
U kunt zich nu aanmelden bij de Azure Portal en controleren of de gegevens in de tabel bestaan. 

![Resultaten in Portal](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met de volgende zelf studie en meer informatie over het migreren van gegevens naar Azure Cosmos DB Table-API-account. 

> [!div class="nextstepaction"]
>[Query's uitvoeren op gegevens](../cosmos-db/table-import.md)

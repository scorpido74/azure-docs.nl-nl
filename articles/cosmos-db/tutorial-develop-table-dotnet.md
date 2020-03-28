---
title: Azure Cosmos DB Table API met .NET Standard SDK
description: Meer informatie over het opslaan en opvragen van de gestructureerde gegevens in Azure Cosmos DB Table API-account
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238452"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Aan de slag met Azure Cosmos DB Table-API en Azure Table-opslag met de .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

U de Azure Cosmos DB Table API of Azure Table-opslag gebruiken om gestructureerde NoSQL-gegevens op te slaan in de cloud, zodat u een sleutel-/kenmerkarchief hebt met een schema dat minder is. Omdat Azure Cosmos DB Table API en Tabelopslag schemaloos zijn, is het eenvoudig om uw gegevens aan te passen naarmate de behoeften van uw toepassing evolueren. U Azure Cosmos DB Table API of de tabelopslag gebruiken om flexibele gegevenssets op te slaan, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens of andere typen metagegevens die uw service vereist. 

In deze zelfstudie wordt een voorbeeld beschreven waarin u laat zien hoe u de [Microsoft Azure Cosmos DB Table Library voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) gebruiken met Azure Cosmos DB Table API en Azure Table storage scenario's. U moet de verbinding die specifiek is voor de Azure-service gebruiken. Deze scenario's worden onderzocht aan de hand van C#-voorbeelden die illustreren hoe u tabellen maakt, gegevens invoegt/ bijwerkt, querygegevens opvraagt en de tabellen verwijdert.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig voor dit voorbeeld:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB-tabelbibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Deze bibliotheek is momenteel beschikbaar voor .NET Standard en .NET framework. 

* [Azure Cosmos DB Table API-account](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Een Azure Cosmos DB Table-API-account maken

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Een .NET-consoleproject maken

Maak in Visual Studio een nieuwe .NET-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. U de Azure Cosmos DB-tabelbibliotheek gebruiken in elk type .NET-toepassing, inclusief een Azure-cloudservice of web-app, en desktop- en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

1. Selecteer Nieuw**New** > **project** **bestand** > .

1. Kies **Console-app (.NET-kern)** en selecteer **Volgende**.

1. Voer in het veld **Projectnaam** een naam in voor uw toepassing, zoals **CosmosTableSamples**. (U indien nodig een andere naam opgeven.)

1. Selecteer **Maken**.

Alle codevoorbeelden in dit voorbeeld kunnen worden toegevoegd aan de methode Main() van het **Program.cs** bestand van uw consoletoepassing.

## <a name="install-the-required-nuget-package"></a>Installeer het vereiste NuGet-pakket

Voer de volgende stappen uit om het NuGet-pakket te verkrijgen:

1. Klik met de rechtermuisknop op het project in **Solution Explorer** en kies **NuGet-pakketten beheren**.

1. Zoek online [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration)naar [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) , en selecteer **Installeren** om de Microsoft Azure Cosmos DB-tabelbibliotheek te installeren.

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

1. Navigeer vanuit de [Azure-portal](https://portal.azure.com/)naar uw Azure Cosmos-account of het tabelopslagaccount. 

1. Open het deelvenster **Verbindingstekenreeks** of **Toegangstoetsen.** Gebruik de kopieerknop aan de rechterkant van het venster om de **PRIMARY CONNECTION STRING** te kopiëren.

   ![De PRIMARY CONNECTION STRING in het deelvenster Verbindingsreeks weergeven en kopiëren](./media/create-table-dotnet/connection-string.png)
   
1. Als u uw verbindingstekenreeks wilt configureren, klikt u met de rechtermuisknop op uw project **CosmosTableSamples**.

1. Selecteer **Toevoegen** en vervolgens **Nieuw item**. Maak een nieuw bestands **Instellingen.json** met bestandstype als **TypeScript JSON Configuration** File. 

1. Vervang de code in het bestand Settings.json door de volgende code en wijs de primaire verbindingstekenreeks toe:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen,** **Nieuw item** en voeg een klasse toe met de naam **AppSettings.cs**.

1. Voeg de volgende code toe aan het AppSettings.cs bestand. Dit bestand leest de verbindingstekenreeks van het bestand Settings.json en wijst deze toe aan de configuratieparameter:

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

## <a name="parse-and-validate-the-connection-details"></a>Parse en valideren van de verbindingsgegevens 

1. Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen,** **Nieuw item** en voeg een klasse toe met de naam **Common.cs**. U schrijft code om de verbindingsgegevens te valideren en een tabel binnen deze klasse te maken.

1. Definieer een `CreateStorageAccountFromConnectionString` methode zoals hieronder wordt weergegeven. Deze methode ontleden de details van de verbindingstekenreeks en valideert of de accountnaam en de accountsleutelgegevens in het bestand 'Instellingen.json' geldig zijn. 

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

Met behulp van de [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient)-klasse kunt u tabellen en entiteiten ophalen die zijn opgeslagen in de Table Storage. Omdat we geen tabellen hebben in het Cosmos DB Table API-account, voegen we de `CreateTableAsync` methode toe aan de klasse **Common.cs** om een tabel te maken:

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

Als u een fout van de "503-service niet beschikbaar uitzondering" krijgt, is het mogelijk dat de vereiste poorten voor de connectiviteitsmodus worden geblokkeerd door een firewall. Als u dit probleem wilt oplossen, opent u de vereiste poorten of gebruikt u de gatewaymoduszoals in de volgende code wordt weergegeven:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>De entiteit definiëren 

Entiteiten worden toegewezen aan C#-objecten met behulp van een aangepaste klasse die is afgeleid van [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert.

Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen,** **Nieuwe map** en geef deze een naam als **model**. Voeg in de map Model een klasse met de naam **CustomerEntity.cs** toe en voeg er de volgende code aan toe.

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

Deze code definieert een entiteitsklasse die de voornaam van de klant gebruikt als de rijsleutel en achternaam als partitiesleutel. De entiteit wordt in de tabel op unieke wijze geïdentificeerd door diens partitie- en rijsleutel. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitiesleutels, maar met behulp van diverse partitiesleutels u de parallelle bewerkingen groter maken. De entiteiten die u in tabellen wilt opslaan, moeten van een ondersteund type zijn. Ze moeten bijvoorbeeld zijn afgeleid van de [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)-klasse. De entiteitseigenschappen die u in een tabel wilt opslaan, moeten openbare eigenschappen van het type zijn. Bovendien moeten ze zowel het ophalen als het instellen van waarden ondersteunen. Ook moet uw entiteitstype een parameterloze constructor bevatten.

## <a name="insert-or-merge-an-entity"></a>Een entiteit invoegen of samenvoegen

In het volgende codevoorbeeld wordt een entiteitsobject gemaakt en aan de tabel toegevoegd. De methode InsertOrMerge binnen de klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) wordt gebruikt om een entiteit in te voegen of samen te voegen. De [methode CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) wordt aangeroepen om de bewerking uit te voeren. 

Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen,** **Nieuw item** en voeg een klasse toe met de naam **SamplesUtils.cs**. Deze klasse slaat alle code op die nodig is om CRUD-bewerkingen uit te voeren op de entiteiten. 

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

### <a name="get-an-entity-from-a-partition"></a>Een entiteit ophalen uit een partitie

U entiteit uit een partitie halen met de methode Ophalen onder de klasse [TableOperation.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) In het volgende codevoorbeeld wordt de sleutel van de partitiesleutel, e-mail en telefoonnummer van een klantentiteit ontvangen. In dit voorbeeld worden ook de aanvraageenheden afgedrukt die zijn verbruikt om voor de entiteit te zoeken. Als u een query voor een entiteit wilt uitvoeren, wordt de volgende code toegevoegd aan **SamplesUtils.cs** bestand: 

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

U kunt een entiteit gemakkelijk verwijderen nadat u deze hebt opgehaald. Hiervoor gebruikt u hetzelfde patroon dat is getoond voor het bijwerken van een entiteit. Met de volgende code wordt een klantentiteit opgehaald en verwijderd. Als u een entiteit wilt verwijderen, maakt u de volgende code toe aan **SamplesUtils.cs** bestand: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>De CRUD-bewerkingen uitvoeren op voorbeeldgegevens

Nadat u de methoden hebt gedefinieerd om tabelte maken, entiteiten in voegen of samenvoegen, voert u deze methoden uit op de voorbeeldgegevens. Klik hiervoor met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen**, **Nieuw item** en voeg een klasse met de naam **BasicSamples.cs** toe en voeg er de volgende code aan toe. Deze code maakt een tabel, voegt entiteiten toe. Als u de entiteit en tabel aan het einde van `table.DeleteIfExistsAsync()` `SamplesUtils.DeleteEntityAsync(table, customer)` het project wilt verwijderen, verwijdert u de opmerkingen en de methoden uit de volgende code:

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

De vorige code maakt een tabel die begint met 'demo' en de gegenereerde GUID wordt toegevoegd aan de tabelnaam. Het voegt vervolgens een klantentiteit toe met voor- en achternaam als "Harp Walter" en werkt later het telefoonnummer van deze gebruiker bij. 

In deze zelfstudie hebt u code gebouwd om basis-CRUD-bewerkingen uit te voeren op de gegevens die zijn opgeslagen in tabel-API-account. U ook geavanceerde bewerkingen uitvoeren, zoals : batch invoegen van gegevens, alle gegevens in een partitie opvragen, een gegevensbereik binnen een partitie opvragen, tabellen in het account weergeven waarvan de namen beginnen met het opgegeven voorvoegsel. U het volledige voorbeeldformulier [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub repository downloaden. De [klasse AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) heeft meer bewerkingen die u op de gegevens uitvoeren.  

## <a name="run-the-project"></a>Het project uitvoeren

Van uw project **CosmosTableSamples**. Open de klasse met de naam **Program.cs** en voeg er de volgende code aan toe voor het aanroepen van BasicSamples wanneer het project wordt uitgevoerd.

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

Bouw nu de oplossing en druk op F5 om het project uit te voeren. Wanneer het project wordt uitgevoerd, ziet u de volgende uitvoer in de opdrachtprompt:

![Uitvoer van opdrachtprompt](./media/tutorial-develop-table-standard/output-from-sample.png)

Als u een fout ontvangt met de vermelding Settings.json-bestand dat niet kan worden gevonden tijdens het uitvoeren van het project, u dit oplossen door de volgende XML-vermelding toe te voegen aan de projectinstellingen. Klik met de rechtermuisknop op CosmosTableSamples, selecteer CosmosTableSamples.csproj bewerken en voeg de volgende itemGroep toe: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
U zich nu aanmelden bij de Azure-portal en controleren of de gegevens in de tabel aanwezig zijn. 

![Resultaten in portal](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan naar de volgende zelfstudie en meer te weten komen over het migreren van gegevens naar Azure Cosmos DB Table API-account. 

> [!div class="nextstepaction"]
>[Gegevens opvragen](../cosmos-db/table-import.md)

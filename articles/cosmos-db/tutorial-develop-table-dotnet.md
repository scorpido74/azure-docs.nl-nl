---
title: Azure Cosmos DB Table-API met .NET Standard SDK
description: Ontdek hoe u de gestructureerde gegevens in een Azure Cosmos DB Table-API-account opslaat en er query’s op uitvoert
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.openlocfilehash: 2133b68bf942cee87548783fb40d08c9bfe876ff
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851635"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Aan de slag met de Azure Cosmos DB Table-API en Azure Table Storage met behulp van de .NET-SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

U kunt de Azure Cosmos DB Table-API of Azure Table Storage gebruiken om gestructureerde NoSQL-gegevens op te slaan in de cloud, waardoor u beschikt over een sleutel/kenmerkarchief met een ontwerp zonder schema. Omdat de Azure Cosmos DB Table-API en Table Storage schemaloos zijn, kunt u uw gegevens eenvoudig aanpassen naarmate de behoeften van uw toepassing veranderen. U kunt de Azure Cosmos DB Table-API of Table Storage gebruiken voor de opslag van flexibele gegevenssets, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens of andere typen metagegevens die uw service nodig heeft. 

In deze zelfstudie wordt een voorbeeld beschreven dat u laat zien hoe u de [Microsoft Azure Cosmos DB Table-bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) gebruikt in scenario’s met de Azure Cosmos DB Table-API en Azure Table Storage. U moet de verbinding gebruiken die specifiek is voor de Azure-service. Deze scenario’s worden verkend met behulp van C#-voorbeelden die laten zien hoe u tabellen maakt, gegevens invoegt/bijwerkt, query’s uitvoert op gegevens en de tabellen verwijdert.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig voor dit voorbeeld:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure Cosmos DB Table-bibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): deze bibliotheek is momenteel beschikbaar voor .NET Standard en .NET Framework. 

* [Azure Cosmos DB Table-API-account](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Een Azure Cosmos DB Table-API-account maken

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Een .NET-consoleproject maken

Maak in Visual Studio een nieuwe .NET-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. U kunt de Azure Cosmos DB Table-bibliotheek gebruiken in elk type .NET-toepassing, waaronder een Azure-cloudservice of -web-app en bureaubladtoepassingen en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

1. Selecteer **Bestand** > **Nieuw** > **Project**.

1. Kies **Console-app (.NET Core)** en selecteer **Volgende**.

1. Typ in het veld **Projectnaam** een naam voor uw toepassing, zoals **CosmosTableSamples**. (U kunt desgewenst een andere naam opgeven.)

1. Selecteer **Maken**.

Alle codevoorbeelden in dit voorbeeld kunnen worden toegevoegd aan de methode Main() van het bestand **Program.cs** van de consoletoepassing.

## <a name="install-the-required-nuget-package"></a>Het vereiste NuGet-pakket installeren

Volg deze stappen voor het verkrijgen van het NuGet-pakket:

1. Klik met de rechtermuisknop op het project in **Solution Explorer** en kies **NuGet-pakketten beheren**.

1. Zoek online naar [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration), [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json), [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) en selecteer **Installeren** om de Microsoft Azure Cosmos DB Table-bibliotheek te installeren.

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

1. Navigeer in de [Azure-portal](https://portal.azure.com/) naar uw Azure Cosmos-account of het Table Storage-account. 

1. Open het deelvenster **Verbindingsreeks** of **Toegangssleutels**. Gebruik de kopieerknop aan de rechterkant van het venster om de **PRIMARY CONNECTION STRING** te kopiëren.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="De PRIMARY CONNECTION STRING in het deelvenster Verbindingsreeks weergeven en kopiëren":::
   
1. Om uw verbindingsreeks te configureren, klikt u in Visual Studio met de rechtermuisknop op uw project **CosmosTableSamples**.

1. Selecteer **Toevoegen** en selecteer vervolgens **Nieuw item**. Maak een nieuw bestand **Settings.json** met **TypeScript JSON-configuratiebestand** als bestandstype. 

1. Vervang de code in het bestand Settings.json door de volgende code en wijs uw primaire verbindingsreeks toe:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen**, **Nieuw item** en voeg een klasse genaamd **AppSettings.cs** toe.

1. Voeg de volgende code toe aan het bestand AppSettings.cs. Dit bestand leest de verbindingsreeks uit het bestand Settings.json en wijst deze toe aan de configuratieparameter:

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

## <a name="parse-and-validate-the-connection-details"></a>De verbindingsgegevens parseren en valideren 

1. Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen**, **Nieuw item** en voeg een klasse genaamd **Common.cs** toe. U zult code schrijven om de verbindingsgegevens te valideren en een tabel te maken in deze klasse.

1. Definieer een methode `CreateStorageAccountFromConnectionString`, zoals hieronder weergegeven. Met deze methode worden de verbindingsgegevens geparseerd en wordt gevalideerd dat de accountnaam en -sleutel die in het bestand Settings.json zijn opgegeven, geldig zijn. 

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

Met behulp van de [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient)-klasse kunt u tabellen en entiteiten ophalen die zijn opgeslagen in de Table Storage. Omdat we in het Cosmos DB Table-API-account geen tabellen hebben, voegen we de methode `CreateTableAsync` toe aan de klasse **Common.cs** om een tabel te maken:

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

Als u de foutmelding ‘503 - Service niet beschikbaar’ ziet, kan het zijn dat de vereiste poorten voor de verbindingsmodus worden geblokkeerd door een firewall. Om dit probleem op te lossen, opent u de vereiste poorten of gebruikt u de verbinding in gatewaymodus zoals weergegeven in de volgende code:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>De entiteit definiëren 

Entiteiten worden toegewezen aan C#-objecten met behulp van een aangepaste klasse die is afgeleid van [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert.

Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen**, **Nieuwe map** en noem de map **Model**. In de map Model voegt u een klasse genaamd **CustomerEntity.cs** toe en voegt u er de volgende code aan toe.

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

Met deze code definieert u een entiteitsklasse die de voornaam van de klant als de rijsleutel en de achternaam als de partitiesleutel gebruikt. De entiteit wordt in de tabel op unieke wijze geïdentificeerd door diens partitie- en rijsleutel. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitiesleutels, maar het gebruik van verschillende partitiesleutels maakt een grotere schaalbaarheid van parallelle bewerkingen mogelijk. De entiteiten die u in tabellen wilt opslaan, moeten van een ondersteund type zijn. Ze moeten bijvoorbeeld zijn afgeleid van de [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity)-klasse. De entiteitseigenschappen die u in een tabel wilt opslaan, moeten openbare eigenschappen van het type zijn. Bovendien moeten ze zowel het ophalen als het instellen van waarden ondersteunen. Ook moet uw entiteitstype een parameterloze constructor bevatten.

## <a name="insert-or-merge-an-entity"></a>Een entiteit invoegen of samenvoegen

Met het volgende codevoorbeeld wordt een entiteitsobject gemaakt en aan de tabel toegevoegd. De methode InsertOrMerge in de klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) wordt gebruikt om een entiteit in te voegen of samen te voegen. De methode [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) wordt aangeroepen om de bewerking uit te voeren. 

Klik met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen**, **Nieuw item** en voeg een klasse genaamd **SamplesUtils.cs** toe. In deze klasse wordt alle code opgeslagen die vereist is om CRUD-bewerkingen op de entiteiten uit te voeren. 

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

## <a name="get-an-entity-from-a-partition"></a>Een entiteit uit een partitie ophalen

U kunt een entiteit uit een partitie ophalen door de methode Retrieve onder de klasse [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) te gebruiken. Met het volgende codevoorbeeld worden de rijsleutel van de partitiesleutel, het e-mailadres en het telefoonnummer van een klantentiteit opgehaald. Met dit voorbeeld worden ook de aanvraageenheden afgedrukt die zijn verbruikt om query’s uit te voeren voor de entiteit. Als u query’s wilt uitvoeren voor een entiteit, voegt u de volgende code toe aan het bestand **SamplesUtils.cs**: 

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

U kunt een entiteit gemakkelijk verwijderen nadat u deze hebt opgehaald. Hiervoor gebruikt u hetzelfde patroon dat is getoond voor het bijwerken van een entiteit. Met de volgende code wordt een klantentiteit opgehaald en verwijderd. Als u een entiteit wilt verwijderen, voegt u de volgende code toe aan het bestand **SamplesUtils.cs**: 

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

Nadat u de methoden hebt gedefinieerd om een tabel te maken en entiteiten in te voegen of samen te voegen, voert u deze methoden uit op de voorbeeldgegevens. Hiertoe klikt u met de rechtermuisknop op uw project **CosmosTableSamples**. Selecteer **Toevoegen**, **Nieuw item**, voeg een klasse genaamd **BasicSamples.cs** toe en voeg de volgende code eraan toe. Met deze code wordt een tabel gemaakt en worden er entiteiten aan toegevoegd. Als u de entiteit en tabel aan het eind van het project wilt verwijderen, verwijdert u de opmerkingen van de methoden `table.DeleteIfExistsAsync()` en `SamplesUtils.DeleteEntityAsync(table, customer)` uit de volgende code:

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

Met de vorige code wordt een tabel gemaakt die begint met ‘demo’ en wordt de gegenereerde GUID toegevoegd aan de tabelnaam. Vervolgens wordt een klantentiteit toegevoegd met ‘Harp Walter’ als voor- en achternaam en wordt het telefoonnummer van deze gebruiker later bijgewerkt. 

In deze zelfstudie hebt u code gebouwd om eenvoudige CRUD-bewerkingen uit te voeren op de gegevens die in het Table-API-account zijn opgeslagen. U kunt ook geavanceerde bewerkingen uitvoeren, zoals batchinvoeging van gegevens, query’s uitvoeren op alle gegevens in een partitie, query’s uitvoeren op een reeks gegevens in een partitie, en tabellen in het account weergeven waarvan de namen beginnen met het opgegeven voorvoegsel. U kunt het volledige voorbeeld downloaden in de GitHub-opslagplaats [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started). De klasse [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) bevat nog meer bewerkingen die u op de gegevens kunt uitvoeren.  

## <a name="run-the-project"></a>Het project uitvoeren

Ga naar uw project **CosmosTableSamples**. Open de klasse genaamd **Program.cs** en voeg de volgende code eraan toe om BasicSamples op te roepen wanneer het project wordt uitgevoerd.

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

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Uitvoer van opdrachtprompt":::

Als er een foutbericht wordt weergegeven met de tekst dat het Settings.json-bestand niet gevonden is bij het uitvoeren van het project, kunt u dit oplossen door de volgende XML-vermelding toe te voegen aan de projectinstellingen. Klik met de rechtermuisknop op CosmosTableSamples, selecteer Bewerken CosmosTableSamples.csproj en voeg de volgende itemGroup toe: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
U kunt zich nu aanmelden bij de Azure-portal en verifiëren dat de gegevens bestaan in de tabel. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Resultaten in de portal":::

## <a name="next-steps"></a>Volgende stappen

U kunt nu verdergaan met de volgende zelfstudie om te leren hoe u gegevens migreert naar het Azure Cosmos DB Table-API-account. 

> [!div class="nextstepaction"]
>[Query’s uitvoeren op gegevens](../cosmos-db/table-import.md)

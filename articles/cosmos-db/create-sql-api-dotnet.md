---
title: Snelstart - Een .NET-console-app maken om Azure Cosmos DB SQL API-bronnen te beheren
description: Meer informatie over het bouwen van een .NET-console-app om Azure Cosmos DB SQL API-accountbronnen in deze quickstart te beheren.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240413"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Snelstart: een .NET-console-app maken om Azure Cosmos DB SQL API-bronnen te beheren

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ga aan de slag met de Azure Cosmos DB SQL API-clientbibliotheek voor .NET. Volg de stappen in dit document om het .NET-pakket te installeren, een app te bouwen en de voorbeeldcode voor basis-CRUD-bewerkingen uit te proberen op de gegevens die zijn opgeslagen in Azure Cosmos DB. 

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. U Azure Cosmos DB gebruiken om snel toets-/waarde-, document- en grafiekdatabases te maken en op te vragen. Gebruik de Azure Cosmos DB SQL API-clientbibliotheek voor .NET om:

* Een Azure Cosmos-database en een container maken
* Voorbeeldgegevens toevoegen aan de container
* Query’s uitvoeren voor de gegevens 
* De database verwijderen

[API-naslagdocumentatie](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-cosmos-dotnet-v3) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/) of u Azure Cosmos DB gratis [proberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement, gratis en verplichtingen. 
* De [.NET Core 2.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Instellen

In deze sectie u een Azure Cosmos-account maken en een project instellen dat azure cosmos DB SQL API-clientbibliotheek voor .NET gebruikt om resources te beheren. De voorbeeldcode die in `FamilyDatabase` dit artikel wordt beschreven, maakt een database en gezinsleden (elk gezinslid is een item) in die database. Elk gezinslid heeft `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`eigenschappen zoals . De `LastName` eigenschap wordt gebruikt als partitiesleutel voor de container. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Een Azure Cosmos-account maken

Als u de optie [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) uitproberen gebruikt om een Azure Cosmos-account te maken, moet u een Azure Cosmos DB-account van het type SQL API **maken.** Er is al een Azure Cosmos DB-testaccount voor u gemaakt. U hoeft het account niet expliciet aan te maken, zodat u deze sectie overslaan en naar de volgende sectie gaan.

Als u uw eigen Azure-abonnement hebt of een gratis abonnement hebt gemaakt, moet u expliciet een Azure Cosmos-account maken. Met de volgende code wordt een Azure Cosmos-account gemaakt met sessieconsistentie. Het account wordt `South Central US` gerepliceerd `North Central US`in en .  

U Azure Cloud Shell gebruiken om het Azure Cosmos-account te maken. Azure Cloud Shell is een interactieve, geverifieerde, via de browser toegankelijke shell voor het beheren van Azure-resources. Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken: Bash of PowerShell. Kies voor deze quickstart de **Bash-modus.** Azure Cloud Shell vereist ook een opslagaccount, u er een maken wanneer u daarom wordt gevraagd.

Selecteer de knop **Uitproberen** naast de volgende code, kies **De Bash-modus** selecteer **een opslagaccount maken** en inloggen bij Cloud Shell. Kopieer en plak vervolgens de volgende code naar azure cloudshell en voer deze uit. De naam van het Azure Cosmos-account moet `mysqlapicosmosdb` wereldwijd uniek zijn, zorg ervoor dat de waarde wordt bijgewerkt voordat u de opdracht uitvoert.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Het maken van het Azure Cosmos-account duurt even, zodra de bewerking succesvol is, u de bevestigingsuitvoer zien. Nadat de opdracht is voltooid, meldt u zich aan bij de [Azure-portal](https://portal.azure.com/) en controleert u of het Azure Cosmos-account met de opgegeven naam bestaat. U het Azure Cloud Shell-venster sluiten nadat de bron is gemaakt. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Een nieuwe .NET-app maken

Maak een nieuwe .NET-toepassing in uw voorkeurseditor of IDE. Open de opdrachtprompt van Windows of een Terminal-venster vanaf uw lokale computer. U voert alle opdrachten in de volgende secties uit vanaf de opdrachtprompt of terminal.  Voer de volgende dotnet nieuwe opdracht uit `todo`om een nieuwe app met de naam te maken. Met de parameter --langVersion wordt de eigenschap LangVersion ingesteld in het gemaakte projectbestand.

```console
dotnet new console --langVersion 7.1 -n todo
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```console
cd todo
dotnet build
```

De verwachte output van de build moet er ongeveer zo uitzien:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Het Azure Cosmos DB-pakket installeren

Installeer de Azure Cosmos DB-clientbibliotheek voor .NET Core met behulp van de opdracht Dotnet add package terwijl u zich nog in de toepassingsmap bevindt.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Uw Azure Cosmos-accountreferenties kopiëren vanuit de Azure-portal

De voorbeeldtoepassing moet worden geverifieerd naar uw Azure Cosmos-account. Als u wilt verifiëren, moet u de azure cosmos-accountreferenties doorgeven aan de toepassing. Haal uw Azure Cosmos-accountreferenties op door de volgende stappen te volgen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos-account.

1. Open het deelvenster **Sleutels** en kopieer de **URI-** en **primaire sleutel** van uw account. In de volgende stap voegt u de uri- en sleutelswaarden toe aan een omgevingsvariabele.

### <a name="set-the-environment-variables"></a>De omgevingsvariabelen instellen

Nadat u de **URI-** en **PRIMAIRE SLEUTEL** van uw account hebt gekopieerd, slaat u deze op in een nieuwe omgevingsvariabele op de lokale machine waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en voert u de volgende opdracht uit. Zorg ervoor `<Your_Azure_Cosmos_account_URI>` dat `<Your_Azure_Cosmos_account_PRIMARY_KEY>` u vervangt en waarden.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Macos**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a name="object-model"></a><a id="object-model"></a>Objectmodel

Voordat u begint met het bouwen van de toepassing, kijken we naar de hiërarchie van resources in Azure Cosmos DB en het objectmodel dat wordt gebruikt om deze bronnen te maken en te openen. De Azure Cosmos DB maakt resources in de volgende volgorde:

* Azure Cosmos-account 
* Databases 
* Containers 
* Items

Zie het [werken met databases, containers en items in](databases-containers-items.md) het artikel Azure Cosmos DB voor meer informatie over de hiërarchie van verschillende entiteiten. U gebruikt de volgende .NET-klassen om met deze bronnen te communiceren:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) - Deze klasse biedt een logische weergave aan de clientzijde voor de Azure Cosmos DB-service. Het clientobject wordt gebruikt om aanvragen tegen de service te configureren en uit te voeren.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) - Deze methode maakt (als deze niet bestaat) of krijgt (als er al bestaat) een databasebron als een asynchrone bewerking. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet)- - Deze methode maakt (als deze niet bestaat) of krijgt (als deze al bestaat) een container als een asynchrone bewerking. U de statuscode uit het antwoord controleren om te bepalen of de container is gemaakt (201) of dat een bestaande container is geretourneerd (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) - Met deze methode wordt een item in de container gemaakt. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) - Met deze methode wordt een item in de container gemaakt als het item nog niet bestaat of als het item al bestaat. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) - Met deze methode wordt een query gemaakt voor items onder een container in een Azure Cosmos-database met behulp van een SQL-instructie met geparameteriseerde waarden. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) - Verwijdert de opgegeven database uit uw Azure Cosmos-account. `DeleteAsync`methode verwijdert alleen de database. Het verwijderen `Cosmosclient` van de instantie moet afzonderlijk gebeuren (wat het doet in de DeleteDatabaseAndCleanupAsync-methode. 

 ## <a name="code-examples"></a><a id="code-examples"></a>Codevoorbeelden

De voorbeeldcode die in dit artikel wordt beschreven, maakt een familiedatabase in Azure Cosmos DB. De familiedatabase bevat familiegegevens zoals naam, adres, locatie, de bijbehorende ouders, kinderen en huisdieren. Voordat u de gegevens aanuw Azure Cosmos-account beschrijft, definieert u de eigenschappen van een gezinsitem. Maak een nieuwe `Family.cs` klasse met de naam op basisniveau van uw voorbeeldtoepassing en voeg er de volgende code aan toe:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        // The ToString() method is used to format the output, it's used for demo purpose only. It's not required by Azure Cosmos DB
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>De gebruiksrichtlijnen toevoegen & het clientobject definiëren

Open in de projectmap het `Program.cs` bestand in uw editor en voeg de volgende gebruiksrichtlijnen boven aan uw toepassing toe:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Voeg aan het **Program.cs-bestand** code toe om de omgevingsvariabelen te lezen die u in de vorige stap hebt ingesteld. Definieer `CosmosClient`de `Database`, `Container` en de objecten. Voeg vervolgens code toe aan `GetStartedDemoAsync` de hoofdmethode die de methode aanroept waarbij u Azure Cosmos-accountbronnen beheert. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Database maken 

Definieer `CreateDatabaseAsync` de methode `program.cs` binnen de klasse. Met deze `FamilyDatabase` methode wordt de als deze nog niet bestaat.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Een container maken

Definieer `CreateContainerAsync` de methode `program.cs` binnen de klasse. Met deze `FamilyContainer` methode wordt de als deze nog niet bestaat. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Een item maken

Maak een gezinsitem `AddItemsToContainerAsync` door de methode met de volgende code toe te voegen. U `CreateItemAsync` de `UpsertItemAsync` of methoden gebruiken om een item te maken:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
    };

    try
    {
        // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen".
        ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));
        // Note that after creating the item, we can access the body of the item with the Resource property of the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
        Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
    }
    catch (CosmosException ex) when (ex.StatusCode == HttpStatusCode.Conflict)
    {
        Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamily.Id);                
    }
}

```

### <a name="query-the-items"></a>De items opvragen

Nadat u een item hebt ingevoegd, u een query uitvoeren om de details van de familie Andersen op te halen. In de volgende code ziet u hoe u de query rechtstreeks uitvoert met de SQL-query. De SQL-query om de "Anderson" `SELECT * FROM c WHERE c.LastName = 'Andersen'`familie details te krijgen is: . Definieer `QueryItemsAsync` de methode `program.cs` binnen de klasse en voeg er de volgende code aan toe:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>De database verwijderen 

Ten slotte u `DeleteDatabaseAndCleanupAsync` de database verwijderen die de methode met de volgende code toevoegt:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>De CRUD-bewerkingen uitvoeren

Nadat u alle vereiste methoden hebt gedefinieerd, voert `GetStartedDemoAsync` u deze uit met de methode. De `DeleteDatabaseAndCleanupAsync` methode die in deze code is opgenomen, omdat u geen bronnen ziet als die methode wordt uitgevoerd. U de opmerking ongedaan maken nadat u hebt gevaliderd dat uw Azure Cosmos DB-resources zijn gemaakt in de Azure-portal. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUrl, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
}
```

Nadat u alle vereiste methoden hebt `Program.cs` toegevoegd, slaat u het bestand op. 

## <a name="run-the-code"></a>De code uitvoeren

Vervolgens de toepassing bouwen en uitvoeren om de Azure Cosmos DB-resources te maken. Als u een nieuw opdrachtpromptvenster opent, gebruikt u niet dezelfde instantie die u hebt gebruikt om de omgevingsvariabelen in te stellen. Omdat de omgevingsvariabelen niet zijn ingesteld in het huidige geopende venster. U moet een nieuwe opdrachtprompt openen om de updates te bekijken. 

```console
dotnet build
```

```console
dotnet run
```

De volgende uitvoer wordt gegenereerd wanneer u de toepassing uitvoert. U zich ook aanmelden bij de Azure-portal en valideren dat de resources zijn gemaakt:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

U valideren dat de gegevens worden gemaakt door u aan te melden bij de Azure-portal en de vereiste items in uw Azure Cosmos-account te bekijken. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u azure CLI of Azure PowerShell gebruiken om het Azure Cosmos-account en de bijbehorende brongroep te verwijderen. In de volgende opdracht ziet u hoe u de brongroep verwijdert met de Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos-account maakt, een database en een container maakt met behulp van een .NET Core-app. U nu extra gegevens importeren naar uw Azure Cosmos-account met de instructies in het volgende artikel. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)

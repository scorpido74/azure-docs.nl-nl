---
title: 'Quick Start: een .NET-console-app bouwen om Azure Cosmos DB SQL-API-resources te beheren'
description: Meer informatie over hoe u een .NET-console-App kunt maken voor het beheren van Azure Cosmos DB SQL-API-account resources in deze Quick Start.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 0981ed30c6bcd9d4246ce1eb047aa66168e3884a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707922"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Quick Start: een .NET-console-app bouwen om Azure Cosmos DB SQL-API-resources te beheren

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ga aan de slag met de Azure Cosmos DB SQL API-client bibliotheek voor .NET. Volg de stappen in dit document om het .NET-pakket te installeren, een app te bouwen en de voorbeeld code voor basis ruwe bewerkingen uit te proberen voor de gegevens die zijn opgeslagen in Azure Cosmos DB. 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt Azure Cosmos DB gebruiken om snel sleutel-, document-en grafiek databases te maken en op te vragen. Gebruik de Azure Cosmos DB SQL API-client bibliotheek voor .NET voor het volgende:

* Een Azure Cosmos-data base en een container maken
* Voorbeeld gegevens toevoegen aan de container
* Query’s uitvoeren voor de gegevens 
* De database verwijderen

[API-referentie documentatie](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | - [bibliotheek bron code](https://github.com/Azure/azure-cosmos-dotnet-v3) | [pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/) of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, gratis en toezeg gingen. 
* De [.net Core 2,1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een Azure Cosmos-account maakt en een project instelt dat gebruikmaakt van Azure Cosmos DB SQL API-client bibliotheek voor .NET om resources te beheren. De voorbeeld code die in dit artikel wordt beschreven, maakt een `FamilyDatabase` data base en familie leden (elk familielid is een item) binnen die data base. Elk familielid heeft eigenschappen als `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. De eigenschap `LastName` wordt gebruikt als de partitie sleutel voor de container. 

### <a id="create-account"></a>Een Azure Cosmos-account maken

Als u de optie [probeer Azure Cosmos DB gratis voor](https://azure.microsoft.com/try/cosmosdb/) het maken van een Azure Cosmos-account gebruikt, moet u een Azure Cosmos DB account van het type **SQL API**maken. Er is al een Azure Cosmos DB test account voor u gemaakt. U hoeft het account niet expliciet te maken, dus u kunt deze sectie overs Laan en door gaan naar de volgende sectie.

Als u uw eigen Azure-abonnement hebt of een gratis abonnement hebt gemaakt, moet u een Azure Cosmos-account expliciet maken. Met de volgende code wordt een Azure Cosmos-account gemaakt met consistentie van de sessie. Het account wordt gerepliceerd in `South Central US` en `North Central US`.  

U kunt Azure Cloud Shell gebruiken om het Azure Cosmos-account te maken. Azure Cloud Shell is een interactieve, geverifieerde shell die toegankelijk is voor browsers voor het beheer van Azure-resources. Het biedt de flexibiliteit om de shell-ervaring te kiezen die het beste past bij de manier waarop u werkt, hetzij bash of Power shell. Kies voor deze Quick Start de modus **bash** . Azure Cloud Shell u ook een opslag account nodig hebt, kunt u er een maken wanneer u hierom wordt gevraagd.

Selecteer de knop **try it** naast de volgende code, kies **bash** -modus Selecteer **een opslag account maken** en aanmelden bij Cloud shell. Kopieer vervolgens de volgende code en plak deze in azure Cloud shell en voer deze uit. De naam van het Azure Cosmos-account moet globaal uniek zijn. Zorg ervoor dat u de `mysqlapicosmosdb` waarde bijwerkt voordat u de opdracht uitvoert.

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

Het maken van het Azure Cosmos-account neemt enige tijd in beslag, zodra de bewerking is voltooid, kunt u de uitvoer van de bevestiging bekijken. Nadat de opdracht is voltooid, meldt u zich aan bij de [Azure Portal](https://portal.azure.com/) en controleert u of het Azure Cosmos-account met de opgegeven naam bestaat. U kunt het Azure Cloud Shell venster sluiten nadat de resource is gemaakt. 

### <a id="create-dotnet-core-app"></a>Een nieuwe .NET-app maken

Maak een nieuwe .NET-toepassing in uw voorkeurs editor of IDE. Open de Windows-opdracht prompt of een Terminal venster op de lokale computer. Alle opdrachten in de volgende secties worden uitgevoerd vanaf de opdracht prompt of Terminal.  Voer de volgende DotNet-nieuwe opdracht uit om een nieuwe app te maken met de naam `todo`. Met de para meter--langVersion stelt u de eigenschap LangVersion in het gemaakte project bestand in.

```console
dotnet new console --langVersion 7.1 -n todo
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
cd todo
dotnet build
```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Het Azure Cosmos DB-pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Cosmos DB-client bibliotheek voor .NET core met behulp van de DotNet opdracht add package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopieer de referenties van uw Azure Cosmos-account uit de Azure Portal

De voorbeeld toepassing moet worden geverifieerd bij uw Azure Cosmos-account. Als u zich wilt verifiëren, moet u de referenties van het Azure Cosmos-account door geven aan de toepassing. U kunt de referenties van uw Azure Cosmos-account ophalen door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos-account.

1. Open het deel venster **sleutels** en kopieer de **URI** en de **primaire sleutel** van uw account. In de volgende stap voegt u de waarden voor de URI en sleutels toe aan een omgevings variabele.

### <a name="set-the-environment-variables"></a>De omgevings variabelen instellen

Nadat u de **URI** en **primaire sleutel** van uw account hebt gekopieerd, slaat u ze op in een nieuwe omgevings variabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevings variabele wilt instellen, opent u een console venster en voert u de volgende opdracht uit. Zorg ervoor dat `<Your_Azure_Cosmos_account_URI>` en `<Your_Azure_Cosmos_account_PRIMARY_KEY>` waarden worden vervangen.

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

**MacOS**

```bash
export EndpointUrl = "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Object model

Voordat u begint met het bouwen van de toepassing, gaan we kijken naar de hiërarchie van resources in Azure Cosmos DB en het object model dat wordt gebruikt voor het maken en openen van deze resources. De Azure Cosmos DB maakt resources in de volgende volg orde:

* Azure Cosmos-account 
* Databases 
* Containers 
* Items

Zie [werken met data bases, containers en items in azure Cosmos DB](databases-containers-items.md) artikel voor meer informatie over de hiërarchie van verschillende entiteiten. U gebruikt de volgende .NET-klassen om te communiceren met deze resources:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) : deze klasse biedt een logische weer gave aan de client zijde voor de Azure Cosmos DB-service. Het client object wordt gebruikt voor het configureren en uitvoeren van aanvragen voor de service.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) : deze methode maakt (indien aanwezig) of haalt (als deze al bestaat) een database resource als een asynchrone bewerking. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet): deze methode maakt (als deze niet bestaat) of haalt (als deze al bestaat) een container als een asynchrone bewerking. U kunt de status code van het antwoord controleren om te bepalen of de container nieuw is gemaakt (201) of dat een bestaande container is geretourneerd (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) : met deze methode maakt u een item in de container. 

* [UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) : met deze methode maakt u een item in de container als deze nog niet bestaat of vervangt u het item als dit al bestaat. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) : met deze methode maakt u een query voor items onder een container in een Azure Cosmos-data base met behulp van een SQL-instructie met geparametriseerde waarden. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) : Hiermee verwijdert u de opgegeven Data Base uit uw Azure Cosmos-account. met `DeleteAsync` methode wordt alleen de data base verwijderd. Het afstoten van het `Cosmosclient`-exemplaar moet afzonderlijk plaatsvinden (dit gebeurt in de methode DeleteDatabaseAndCleanupAsync. 

 ## <a id="code-examples"></a>Code voorbeelden

Met de voorbeeld code die in dit artikel wordt beschreven, wordt een Family-data base gemaakt in Azure Cosmos DB. De familie database bevat familie gegevens, zoals naam, adres, locatie, de gekoppelde ouders, kinderen en huis dieren. Voordat u de gegevens in uw Azure Cosmos-account vult, definieert u de eigenschappen van een familie-item. Maak een nieuwe klasse met de naam `Family.cs` op het hoofd niveau van uw voorbeeld toepassing en voeg de volgende code toe:

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

### <a name="add-the-using-directives--define-the-client-object"></a>Voeg de using-instructies toe om het client object te definiëren &

Open in de projectmap het `Program.cs`-bestand in de editor en voeg de volgende instructies toe aan het begin van de toepassing:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Voeg aan het **Program.cs** -bestand code toe om de omgevings variabelen te lezen die u in de vorige stap hebt ingesteld. Definieer de `CosmosClient`, de `Database`en de `Container`-objecten. Voeg vervolgens code toe aan de methode Main waarmee de `GetStartedDemoAsync` methode wordt aangeroepen waar u resources van het Azure Cosmos-account beheert. 

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

### <a name="create-a-database"></a>Een database maken 

Definieer de `CreateDatabaseAsync` methode binnen de `program.cs`-klasse. Met deze methode maakt u de `FamilyDatabase` als deze nog niet bestaat.

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Een container maken

Definieer de `CreateContainerAsync` methode binnen de `program.cs`-klasse. Met deze methode maakt u de `FamilyContainer` als deze nog niet bestaat. 

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

Maak een familie-item door de methode `AddItemsToContainerAsync` toe te voegen met de volgende code. U kunt de methoden `CreateItemAsync` of `UpsertItemAsync` gebruiken om een item te maken:

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

### <a name="query-the-items"></a>Query's uitvoeren op de items

Nadat u een item hebt ingevoegd, kunt u een query uitvoeren om de details van de "Splinter" familie te verkrijgen. De volgende code laat zien hoe u de query rechtstreeks kunt uitvoeren met behulp van de SQL-query. De SQL-query voor het ophalen van de details van de familie van Anderson is: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definieer de `QueryItemsAsync` methode binnen de `program.cs` klasse en voeg de volgende code toe:


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

Ten slotte kunt u de data base verwijderen die de `DeleteDatabaseAndCleanupAsync` methode toevoegt met de volgende code:

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

### <a name="execute-the-crud-operations"></a>De ruwe bewerkingen uitvoeren

Nadat u alle vereiste methoden hebt gedefinieerd, voert u deze uit met in de `GetStartedDemoAsync` methode. De `DeleteDatabaseAndCleanupAsync` methode heeft een opmerking in deze code opgenomen omdat er geen resources worden weer geven als deze methode wordt uitgevoerd. U kunt de opmerking opheffen nadat u hebt gecontroleerd of uw Azure Cosmos DB resources zijn gemaakt in de Azure Portal. 

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

Nadat u alle vereiste methoden hebt toegevoegd, slaat u het `Program.cs` bestand op. 

## <a name="run-the-code"></a>De code uitvoeren

Vervolgens bouwt u de toepassing en voert u deze uit om de Azure Cosmos DB-resources te maken. Zorg ervoor dat u een nieuw opdracht prompt venster opent en gebruik niet hetzelfde exemplaar dat u hebt gebruikt om de omgevings variabelen in te stellen. De omgevings variabelen zijn niet ingesteld in het huidige geopende venster. U moet een nieuwe opdracht prompt openen om de updates te bekijken. 

```console
dotnet build
```

```console
dotnet run
```

De volgende uitvoer wordt gegenereerd wanneer u de toepassing uitvoert. U kunt zich ook aanmelden bij de Azure Portal en controleren of de resources zijn gemaakt:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

U kunt controleren of de gegevens zijn gemaakt door u aan te melden bij de Azure Portal en de vereiste items te zien in uw Azure Cosmos-account. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de Azure CLI of Azure PowerShell gebruiken om het Azure Cosmos-account en de bijbehorende resource groep te verwijderen. De volgende opdracht laat zien hoe u de resource groep verwijdert met behulp van de Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account maakt, een Data Base en een container maakt met behulp van een .NET core-app. U kunt nu aanvullende gegevens importeren in uw Azure Cosmos-account met de instructies int het volgende artikel. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)

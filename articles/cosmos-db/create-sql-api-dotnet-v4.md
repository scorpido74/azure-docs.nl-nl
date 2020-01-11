---
title: Azure Cosmos DB SQL-API-resources beheren met behulp van de .net v4-SDK
description: Quick Start om een console-app te bouwen met behulp van de .net v4 SDK voor het beheren van Azure Cosmos DB SQL-API-account resources.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867265"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Quick Start: een console-app bouwen met de .net v4 SDK voor het beheren van Azure Cosmos DB SQL-API-account resources.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ga aan de slag met de Azure Cosmos DB SQL API-client bibliotheek voor .NET. Volg de stappen in dit document om het .NET v4-pakket (Azure. Cosmos) te installeren, een app te bouwen en de voorbeeld code voor basis ruwe bewerkingen uit te voeren op de gegevens die zijn opgeslagen in Azure Cosmos DB. 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt Azure Cosmos DB gebruiken om snel sleutel-, document-en grafiek databases te maken en op te vragen. Gebruik de Azure Cosmos DB SQL API-client bibliotheek voor .NET voor het volgende:

* Een Azure Cosmos-data base en een container maken
* Voorbeeld gegevens toevoegen aan de container
* Query’s uitvoeren voor de gegevens 
* De database verwijderen

 | pakket voor de [bron code](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) van de bibliotheek [(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/) of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, gratis en toezeg gingen. 
* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). U kunt controleren welke versie beschikbaar is in uw omgeving door `dotnet --version`uit te voeren.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een Azure Cosmos-account maakt en een project instelt dat gebruikmaakt van Azure Cosmos DB SQL API-client bibliotheek voor .NET om resources te beheren. De voorbeeld code die in dit artikel wordt beschreven, maakt een `FamilyDatabase` data base en familie leden (elk familielid is een item) binnen die data base. Elk familielid heeft eigenschappen als `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. De eigenschap `LastName` wordt gebruikt als de partitie sleutel voor de container. 

### <a id="create-account"></a>Een Azure Cosmos-account maken

Als u de optie [probeer Azure Cosmos DB gratis voor](https://azure.microsoft.com/try/cosmosdb/) het maken van een Azure Cosmos-account gebruikt, moet u een Azure Cosmos DB account van het type **SQL API**maken. Er is al een Azure Cosmos DB test account voor u gemaakt. U hoeft het account niet expliciet te maken, dus u kunt deze sectie overs Laan en door gaan naar de volgende sectie.

Als u uw eigen Azure-abonnement hebt of een gratis abonnement hebt gemaakt, moet u een Azure Cosmos-account expliciet maken. Met de volgende code wordt een Azure Cosmos-account gemaakt met consistentie van de sessie. Het account wordt gerepliceerd in `South Central US` en `North Central US`.  

U kunt Azure Cloud Shell gebruiken om het Azure Cosmos-account te maken. Azure Cloud Shell is een interactieve, geverifieerde shell die toegankelijk is voor browsers voor het beheer van Azure-resources. Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken: Bash of PowerShell. Kies voor deze Quick Start de modus **bash** . Azure Cloud Shell u ook een opslag account nodig hebt, kunt u er een maken wanneer u hierom wordt gevraagd.

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

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

   ```bash
   cd todo
   dotnet build
   ```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Het Azure Cosmos DB-pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Cosmos DB-client bibliotheek voor .NET core met behulp van de DotNet opdracht add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Kopieer de referenties van uw Azure Cosmos-account uit de Azure Portal

De voorbeeld toepassing moet worden geverifieerd bij uw Azure Cosmos-account. Als u zich wilt verifiëren, moet u de referenties van het Azure Cosmos-account door geven aan de toepassing. U kunt de referenties van uw Azure Cosmos-account ophalen door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos-account.

1. Open het deel venster **sleutels** en kopieer de **URI** en de **primaire sleutel** van uw account. In de volgende stap voegt u de waarden voor de URI en sleutels toe aan een omgevings variabele.

## <a id="object-model"></a>Object model

Voordat u begint met het bouwen van de toepassing, gaan we kijken naar de hiërarchie van resources in Azure Cosmos DB en het object model dat wordt gebruikt voor het maken en openen van deze resources. De Azure Cosmos DB maakt resources in de volgende volg orde:

* Azure Cosmos-account 
* Databases 
* Containers 
* Items

Zie [werken met data bases, containers en items in azure Cosmos DB](databases-containers-items.md) artikel voor meer informatie over de hiërarchie van verschillende entiteiten. U gebruikt de volgende .NET-klassen om te communiceren met deze resources:

* CosmosClient: deze klasse biedt een logische weer gave aan de client zijde voor de Azure Cosmos DB-service. Het client object wordt gebruikt voor het configureren en uitvoeren van aanvragen voor de service.
* CreateDatabaseIfNotExistsAsync: deze methode maakt (indien aanwezig) of haalt (als deze al bestaat) een database resource als een asynchrone bewerking. 
* CreateContainerIfNotExistsAsync: deze methode maakt (als deze niet bestaat) of haalt (als deze al bestaat) een container als een asynchrone bewerking. U kunt de status code van het antwoord controleren om te bepalen of de container nieuw is gemaakt (201) of dat een bestaande container is geretourneerd (200). 
* CreateItemAsync: met deze methode maakt u een item in de container.
* UpsertItemAsync: met deze methode maakt u een item in de container als deze nog niet bestaat of vervangt u het item als dit al bestaat. 
* GetItemQueryIterator: met deze methode maakt u een query voor items onder een container in een Azure Cosmos-data base met behulp van een SQL-instructie met geparametriseerde waarden. 
* DeleteAsync: Hiermee verwijdert u de opgegeven Data Base uit uw Azure Cosmos-account. met `DeleteAsync` methode wordt alleen de data base verwijderd.

 ## <a id="code-examples"></a>Code voorbeelden

Met de voorbeeld code die in dit artikel wordt beschreven, wordt een Family-data base gemaakt in Azure Cosmos DB. De familie database bevat familie gegevens, zoals naam, adres, locatie, de gekoppelde ouders, kinderen en huis dieren. Voordat u de gegevens in uw Azure Cosmos-account vult, definieert u de eigenschappen van een familie-item. Maak een nieuwe klasse met de naam `Family.cs` op het hoofd niveau van uw voorbeeld toepassing en voeg de volgende code toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Voeg de using-instructies toe om het client object te definiëren &

Open in de projectmap het `Program.cs`-bestand in de editor en voeg de volgende instructies toe aan het begin van de toepassing:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Voeg de volgende globale variabelen toe aan uw `Program`-klasse. Dit zijn onder andere de eind punt-en autorisatie sleutels, de naam van de data base en de container die u gaat maken. Zorg ervoor dat u de waarden voor het eind punt en de autorisatie sleutels vervangt door uw omgeving. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Vervang ten slotte de `Main` methode:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Database maken 

Definieer de `CreateDatabaseAsync` methode binnen de `program.cs`-klasse. Met deze methode maakt u de `FamilyDatabase` als deze nog niet bestaat.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Een container maken

Definieer de `CreateContainerAsync` methode binnen de `Program`-klasse. Met deze methode maakt u de `FamilyContainer` als deze nog niet bestaat. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Een item maken

Maak een familie-item door de methode `AddItemsToContainerAsync` toe te voegen met de volgende code. U kunt de methoden `CreateItemAsync` of `UpsertItemAsync` gebruiken om een item te maken:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Query's uitvoeren op de items

Nadat u een item hebt ingevoegd, kunt u een query uitvoeren om de details van de "Splinter" familie te verkrijgen. De volgende code laat zien hoe u de query rechtstreeks kunt uitvoeren met behulp van de SQL-query. De SQL-query voor het ophalen van de details van de familie van Anderson is: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definieer de `QueryItemsAsync` methode binnen de `Program` klasse en voeg de volgende code toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Een item vervangen 

Lees een familie-item en werk het bij door de `ReplaceFamilyItemAsync`-methode toe te voegen met de volgende code.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Een item verwijderen 

Verwijder een familie-item door de methode `DeleteFamilyItemAsync` toe te voegen met de volgende code.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>De database verwijderen 

Ten slotte kunt u de data base verwijderen die de `DeleteDatabaseAndCleanupAsync` methode toevoegt met de volgende code:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Nadat u alle vereiste methoden hebt toegevoegd, slaat u het `Program` bestand op. 

## <a name="run-the-code"></a>De code uitvoeren

Vervolgens bouwt u de toepassing en voert u deze uit om de Azure Cosmos DB-resources te maken.

   ```bash
   dotnet run
   ```

De volgende uitvoer wordt gegenereerd wanneer u de toepassing uitvoert. U kunt zich ook aanmelden bij de Azure Portal en controleren of de resources zijn gemaakt:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
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

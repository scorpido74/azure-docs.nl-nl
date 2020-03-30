---
title: Azure Cosmos DB SQL API-resources beheren met .Net V4 SDK
description: Snel beginnen met het bouwen van een console-app met .Net V4 SDK om Azure Cosmos DB SQL API-accountbronnen te beheren.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77585931"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Snelstart: maak een console-app met de .Net V4 SDK om Azure Cosmos DB SQL API-accountbronnen te beheren.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ga aan de slag met de Azure Cosmos DB SQL API-clientbibliotheek voor .NET. Volg de stappen in dit document om het .NET V4-pakket (Azure.Cosmos) te installeren, een app te bouwen en de voorbeeldcode voor basis-CRUD-bewerkingen uit te proberen voor de gegevens die zijn opgeslagen in Azure Cosmos DB. 

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. U Azure Cosmos DB gebruiken om snel toets-/waarde-, document- en grafiekdatabases te maken en op te vragen. Gebruik de Azure Cosmos DB SQL API-clientbibliotheek voor .NET om:

* Een Azure Cosmos-database en een container maken
* Voorbeeldgegevens toevoegen aan de container
* Query’s uitvoeren voor de gegevens 
* De database verwijderen

[Bibliotheekbroncodepakket](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/) of u Azure Cosmos DB gratis [proberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement, gratis en verplichtingen. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). U controleren welke versie beschikbaar `dotnet --version`is in uw omgeving door.

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

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

   ```bash
   cd todo
   dotnet build
   ```

De verwachte output van de build moet er ongeveer zo uitzien:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Het Azure Cosmos DB-pakket installeren

Installeer de Azure Cosmos DB-clientbibliotheek voor .NET Core met behulp van de opdracht Dotnet add package terwijl u zich nog in de toepassingsmap bevindt.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Uw Azure Cosmos-accountreferenties kopiëren vanuit de Azure-portal

De voorbeeldtoepassing moet worden geverifieerd naar uw Azure Cosmos-account. Als u wilt verifiëren, moet u de azure cosmos-accountreferenties doorgeven aan de toepassing. Haal uw Azure Cosmos-accountreferenties op door de volgende stappen te volgen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Navigeer naar uw Azure Cosmos-account.

1. Open het deelvenster **Sleutels** en kopieer de **URI-** en **primaire sleutel** van uw account. In de volgende stap voegt u de uri- en sleutelswaarden toe aan een omgevingsvariabele.

## <a name="object-model"></a><a id="object-model"></a>Objectmodel

Voordat u begint met het bouwen van de toepassing, kijken we naar de hiërarchie van resources in Azure Cosmos DB en het objectmodel dat wordt gebruikt om deze bronnen te maken en te openen. De Azure Cosmos DB maakt resources in de volgende volgorde:

* Azure Cosmos-account 
* Databases 
* Containers 
* Items

Zie het [werken met databases, containers en items in](databases-containers-items.md) het artikel Azure Cosmos DB voor meer informatie over de hiërarchie van verschillende entiteiten. U gebruikt de volgende .NET-klassen om met deze bronnen te communiceren:

* CosmosClient - Deze klasse biedt een logische weergave aan de clientzijde voor de Azure Cosmos DB-service. Het clientobject wordt gebruikt om aanvragen tegen de service te configureren en uit te voeren.
* CreateDatabaseIfNotExistsAsync - Deze methode maakt (als deze niet bestaat) of krijgt (als er al bestaat) een databasebron als een asynchrone bewerking. 
* CreateContainerIfNotExistsAsync - Deze methode maakt (als deze niet bestaat) of krijgt (als deze al bestaat) een container als een asynchrone bewerking. U de statuscode uit het antwoord controleren om te bepalen of de container is gemaakt (201) of dat een bestaande container is geretourneerd (200). 
* CreateItemAsync - Met deze methode wordt een item in de container gemaakt.
* UpsertItemAsync - Met deze methode wordt een item in de container gemaakt als het item nog niet bestaat of als het item al bestaat. 
* GetItemQueryIterator - Met deze methode wordt een query gemaakt voor items onder een container in een Azure Cosmos-database met behulp van een SQL-instructie met geparameteriseerde waarden. 
* DeleteAsync - Verwijdert de opgegeven database uit uw Azure Cosmos-account. `DeleteAsync`methode verwijdert alleen de database.

 ## <a name="code-examples"></a><a id="code-examples"></a>Codevoorbeelden

De voorbeeldcode die in dit artikel wordt beschreven, maakt een familiedatabase in Azure Cosmos DB. De familiedatabase bevat familiegegevens zoals naam, adres, locatie, de bijbehorende ouders, kinderen en huisdieren. Voordat u de gegevens aanuw Azure Cosmos-account beschrijft, definieert u de eigenschappen van een gezinsitem. Maak een nieuwe `Family.cs` klasse met de naam op basisniveau van uw voorbeeldtoepassing en voeg er de volgende code aan toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>De gebruiksrichtlijnen toevoegen & het clientobject definiëren

Open in de projectmap het `Program.cs` bestand in uw editor en voeg de volgende gebruiksrichtlijnen boven aan uw toepassing toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Voeg de volgende globale `Program` variabelen toe in uw klas. Deze omvatten het eindpunt en autorisatiesleutels, de naam van de database en de container die u maakt. Zorg ervoor dat u de waarden van het eindpunt en de autorisatiesleutels vervangt op basis van uw omgeving. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Ten slotte `Main` vervangt u de methode:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Database maken 

Definieer `CreateDatabaseAsync` de methode `program.cs` binnen de klasse. Met deze `FamilyDatabase` methode wordt de als deze nog niet bestaat.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Een container maken

Definieer `CreateContainerAsync` de methode `Program` binnen de klasse. Met deze `FamilyContainer` methode wordt de als deze nog niet bestaat. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Een item maken

Maak een gezinsitem `AddItemsToContainerAsync` door de methode met de volgende code toe te voegen. U `CreateItemAsync` de `UpsertItemAsync` of methoden gebruiken om een item te maken:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>De items opvragen

Nadat u een item hebt ingevoegd, u een query uitvoeren om de details van de familie Andersen op te halen. In de volgende code ziet u hoe u de query rechtstreeks uitvoert met de SQL-query. De SQL-query om de "Anderson" `SELECT * FROM c WHERE c.LastName = 'Andersen'`familie details te krijgen is: . Definieer `QueryItemsAsync` de methode `Program` binnen de klasse en voeg er de volgende code aan toe:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Een item vervangen 

Lees een gezinsitem en werk `ReplaceFamilyItemAsync` het bij door de methode met de volgende code toe te voegen.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Een item verwijderen 

Een gezinsitem verwijderen `DeleteFamilyItemAsync` door de methode met de volgende code toe te voegen.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>De database verwijderen 

Ten slotte u `DeleteDatabaseAndCleanupAsync` de database verwijderen die de methode met de volgende code toevoegt:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Nadat u alle vereiste methoden hebt `Program` toegevoegd, slaat u het bestand op. 

## <a name="run-the-code"></a>De code uitvoeren

Vervolgens de toepassing bouwen en uitvoeren om de Azure Cosmos DB-resources te maken.

   ```bash
   dotnet run
   ```

De volgende uitvoer wordt gegenereerd wanneer u de toepassing uitvoert. U zich ook aanmelden bij de Azure-portal en valideren dat de resources zijn gemaakt:

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

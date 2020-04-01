---
title: Bulkimportgegevens naar Azure Cosmos DB SQL API-account met behulp van de .Net SDK
description: Meer informatie over het importeren of innemen van gegevens naar Azure Cosmos DB door een .NET-consoletoepassing te bouwen die de ingerichte doorvoer (RU/s) optimaliseert die nodig is voor het importeren van gegevens
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77587563"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Bulkimportgegevens naar Azure Cosmos DB SQL API-account met behulp van de .NET SDK

In deze zelfstudie ziet u hoe u een .NET-consoletoepassing maken die de ingerichte doorvoer (RU/s) optimaliseert die nodig is om gegevens naar Azure Cosmos DB te importeren. In dit artikel leest u gegevens uit een voorbeeldgegevensbron en importeert u deze in een Azure Cosmos-container.
Deze zelfstudie maakt gebruik van [versie 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) van de Azure Cosmos DB .NET SDK, die kan worden gericht op .NET Framework of .NET Core.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Uw project configureren
> * Verbinding maken met een Azure Cosmos-account met ingeschakelde bulkondersteuning
> * Een gegevensimport uitvoeren via bewerkingen voor gelijktijdige maken

## <a name="prerequisites"></a>Vereisten

Controleer voordat u de instructies in dit artikel volgt, dat u de volgende bronnen hebt:

* Een actief Azure-account. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). U controleren welke versie beschikbaar `dotnet --version`is in uw omgeving door.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: een Azure Cosmos DB-account maken

[Maak een Azure Cosmos DB SQL API-account](create-cosmosdb-resources-portal.md) vanaf de Azure-portal of u het account maken met behulp van de [Azure Cosmos DB Emulator.](local-emulator.md)

## <a name="step-2-set-up-your-net-project"></a>Stap 2: Uw .NET-project instellen

Open de opdrachtprompt van Windows of een Terminal-venster vanaf uw lokale computer. U voert alle opdrachten in de volgende secties uit vanaf de opdrachtprompt of terminal. Voer de volgende dotnet nieuwe opdracht uit om een nieuwe app te maken met de naam *bulk-import-demo*. De `--langVersion` parameter stelt de eigenschap *LangVersion* in in het gemaakte projectbestand.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

De verwachte output van de build moet er ongeveer zo uitzien:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Stap 3: Het Azure Cosmos DB-pakket toevoegen

Installeer de Azure Cosmos DB-clientbibliotheek voor .NET Core met behulp van de opdracht Dotnet add package terwijl u zich nog in de toepassingsmap bevindt.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Stap 4: Uw Azure Cosmos-accountreferenties ophalen

De voorbeeldtoepassing moet worden geverifieerd naar uw Azure Cosmos-account. Als u wilt verifiëren, moet u de azure cosmos-accountreferenties doorgeven aan de toepassing. Haal uw Azure Cosmos-accountreferenties op door de volgende stappen te volgen:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/).
1.  Navigeer naar uw Azure Cosmos-account.
1.  Open het deelvenster **Sleutels** en kopieer de **URI-** en **primaire sleutel** van uw account.

Als u de Azure Cosmos DB Emulator gebruikt, u de [emulatorreferenties uit dit artikel](local-emulator.md#authenticating-requests)verkrijgen.

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Stap 5: Het Object CosmosClient initialiseren met ondersteuning voor bulkuitvoering

Open het `Program.cs` gegenereerde bestand in een codeeditor. U maakt een nieuw exemplaar van CosmosClient met bulkuitvoering ingeschakeld en gebruikt deze om bewerkingen uit te voeren tegen Azure Cosmos DB. 

Laten we beginnen met het `Main` overschrijven van de standaardmethode en het definiëren van de globale variabelen. Deze globale variabelen omvatten het eindpunt en de autorisatiesleutels, de naam van de database, de container die u maakt en het aantal items dat u in bulk invoegt. Zorg ervoor dat u de eindpunt- en autorisatiesleutelwaarden vervangt op basis van uw omgeving. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

Voeg `Main` in de methode de volgende code toe om het object CosmosClient te initialiseren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Nadat de bulkuitvoering is ingeschakeld, groepeert de CosmosClient gelijktijdige bewerkingen intern in afzonderlijke serviceoproepen. Op deze manier optimaliseert het doorvoergebruik door serviceoproepen over partities te distribueren en uiteindelijk individuele resultaten toe te wijzen aan de oorspronkelijke bellers.

U dan een container maken om al onze artikelen op te slaan.  Definieer `/pk` als de partitiesleutel, 50000 RU/s als ingerichte doorvoer en een aangepast indexeringsbeleid dat alle velden uitsluit om de schrijfdoorvoer te optimaliseren. Voeg de volgende code toe na de initialisatie-instructie CosmosClient:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Stap 6: Een lijst met gelijktijdige taken invullen

Als u wilt profiteren van de ondersteuning voor bulkuitvoering, maakt u een lijst met asynchrone taken op basis van de bron van gegevens en de bewerkingen die u wilt uitvoeren, en gebruikt u `Task.WhenAll` deze om deze gelijktijdig uit te voeren.
Laten we beginnen met het gebruik van "Bogus" gegevens om een lijst van items uit ons gegevensmodel te genereren. In een real-world applicatie, zouden de items afkomstig zijn van uw gewenste gegevensbron.

Voeg eerst het Bogus-pakket toe aan de oplossing met behulp van de opdracht dotnet add package.

   ```bash
   dotnet add package Bogus
   ```

Definieer de definitie van de items die u wilt opslaan. U moet de `Item` klasse `Program.cs` in het bestand definiëren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Maak vervolgens een helperfunctie `Program` in de klasse. Deze helperfunctie krijgt het aantal items dat u hebt gedefinieerd om willekeurige gegevens in te voegen en genereert:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Lees de items en serialiseren ze in `System.Text.Json` stream exemplaren met behulp van de klasse. Vanwege de aard van de automatisch gegenereerde gegevens, bent u serialiseren van de gegevens als stromen. U de item-instantie ook rechtstreeks gebruiken, maar door deze te converteren naar streams, u gebruikmaken van de prestaties van stream-API's in de CosmosClient. Meestal u de gegevens direct gebruiken zolang u de partitiesleutel kent. 


Als u de gegevens wilt `Main` converteren naar het streamen van instanties, voegt u binnen de methode de volgende code toe direct na het maken van de container:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Vervolgens gebruikt u de gegevensstromen om gelijktijdige taken te maken en de takenlijst in te vullen om de items in de container in te voegen. Als u deze bewerking wilt uitvoeren, voegt u de volgende code toe aan de `Program` klasse:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Al deze gelijktijdige puntbewerkingen worden samen uitgevoerd (dat is in bulk) zoals beschreven in de inleidingssectie.

## <a name="step-7-run-the-sample"></a>Stap 7: Het voorbeeld uitvoeren

Om het voorbeeld uit te voeren, kunt `dotnet` u het eenvoudig doen met de opdracht:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Download het volledige voorbeeld

Als je geen tijd hebt om de stappen in deze zelfstudie te voltooien, of gewoon de codevoorbeelden wilt downloaden, kun je deze krijgen van [GitHub.](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer)

Zorg ervoor dat u na het klonen van het project de gewenste referenties binnen [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25)bijwerkt.

Het voorbeeld kan worden uitgevoerd door te `dotnet`wijzigen in de repository directory en met behulp van:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Uw project configureren
> * Verbinding maken met een Azure Cosmos-account met ingeschakelde bulkondersteuning
> * Een gegevensimport uitvoeren via bewerkingen voor gelijktijdige maken

U nu verder gaan naar de volgende zelfstudie:

> [!div class="nextstepaction"]
>[Query's uitvoeren in Azure Cosmos DB met behulp van de SQL-API](tutorial-query-sql-api.md)
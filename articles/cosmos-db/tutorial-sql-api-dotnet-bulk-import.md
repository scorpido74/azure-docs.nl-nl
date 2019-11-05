---
title: De door Voer optimaliseren bij het bulksgewijs importeren van gegevens naar Azure Cosmos DB SQL-API-account
description: Meer informatie over het bouwen van een .NET-console toepassing die de ingerichte door Voer (RU/s) optimaliseert die is vereist om gegevens te importeren in Azure Cosmos DB.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 3e8f9f6f839f4effa039335e6326c163b18d3dcc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519343"
---
# <a name="optimize-throughput-when-bulk-importing-data-to-azure-cosmos-db-sql-api-account"></a>De door Voer optimaliseren bij het bulksgewijs importeren van gegevens naar Azure Cosmos DB SQL-API-account

Deze zelf studie laat zien hoe u een .NET-console toepassing bouwt die de ingerichte door Voer (RU/s) optimaliseert die is vereist om gegevens te importeren in Azure Cosmos DB. In dit artikel leest u gegevens uit een voorbeeld gegevens bron en importeert u deze in een Azure Cosmos-container.
In deze zelf studie wordt [versie 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) van de Azure Cosmos db .NET SDK gebruikt, die kan worden gericht op .NET Framework of .net core.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Uw project configureren
> * Verbinding maken met een Azure Cosmos-account waarvoor bulk ondersteuning is ingeschakeld
> * Een gegevens import uitvoeren met gelijktijdige bewerkingen voor maken

## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel volgt, moet u ervoor zorgen dat u over de volgende resources beschikt:

* Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). U kunt controleren welke versie beschikbaar is in uw omgeving door `dotnet --version`uit te voeren.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: een Azure Cosmos DB-account maken

[Maak een Azure Cosmos DB SQL-API-account](create-cosmosdb-resources-portal.md) van de Azure portal of u kunt het account maken met behulp van de [Azure Cosmos DB emulator](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Stap 2: uw .NET-project instellen

Open de Windows-opdracht prompt of een Terminal venster op de lokale computer. Alle opdrachten in de volgende secties worden uitgevoerd vanaf de opdracht prompt of Terminal. Voer de volgende DotNet-nieuwe opdracht uit om een nieuwe app te maken met de naam *bulksgewijs importeren-demo*. De para meter `--langVersion` stelt de eigenschap *LangVersion* in het gemaakte project bestand in.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Stap 3: het Azure Cosmos DB-pakket toevoegen

Terwijl u nog steeds in de toepassingsmap, installeert u de Azure Cosmos DB-client bibliotheek voor .NET core met behulp van de DotNet opdracht add package.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Stap 4: de referenties van uw Azure Cosmos-account ophalen

De voorbeeld toepassing moet worden geverifieerd bij uw Azure Cosmos-account. Als u zich wilt verifiëren, moet u de referenties van het Azure Cosmos-account door geven aan de toepassing. U kunt de referenties van uw Azure Cosmos-account ophalen door de volgende stappen uit te voeren:

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1.  Navigeer naar uw Azure Cosmos-account.
1.  Open het deel venster **sleutels** en kopieer de **URI** en de **primaire sleutel** van uw account.

Als u de Azure Cosmos DB-emulator gebruikt, haalt u de [emulator-referenties op uit dit artikel](local-emulator.md#authenticating-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Stap 5: Initialiseer het CosmosClient-object met ondersteuning voor bulk uitvoering

Open het gegenereerde `Program.cs`-bestand in een code-editor. U maakt een nieuw exemplaar van CosmosClient waarvoor bulk uitvoering is ingeschakeld en die het gebruikt om bewerkingen uit te voeren op basis van Azure Cosmos DB. 

Laten we beginnen met het overschrijven van de standaard `Main` methode en het definiëren van de globale variabelen. Deze globale variabelen bevatten de eind punt-en autorisatie sleutels, de naam van de data base, de container die u gaat maken en het aantal items dat u in bulk wilt invoegen. Zorg ervoor dat u de endpointURL-en autorisatie sleutel waarden vervangt volgens uw omgeving. 


   ```csharp
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

Voeg in de methode `Main` de volgende code toe om het object CosmosClient te initialiseren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

Nadat de bulk uitvoering is ingeschakeld, worden in de CosmosClient intern gelijktijdige bewerkingen in enkele service aanroepen gegroepeerd. Op deze manier optimaliseert het het gebruik van de door Voer om service aanroepen over partities te verdelen en uiteindelijk afzonderlijke resultaten toe te wijzen aan de oorspronkelijke bellers.

U kunt vervolgens een container maken om al onze items op te slaan.  Definieer `/pk` als de partitie sleutel, 50000 RU/s als ingerichte door Voer en een aangepast indexerings beleid waarmee alle velden worden uitgesloten voor het optimaliseren van de schrijf doorvoer. Voeg de volgende code toe na de CosmosClient-initialisatie-instructie:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Stap 6: een lijst met gelijktijdige taken vullen

Als u de ondersteuning voor bulk uitvoering wilt benutten, maakt u een lijst met asynchrone taken op basis van de gegevens bron en de bewerkingen die u wilt uitvoeren, en gebruikt u `Task.WhenAll` om deze gelijktijdig uit te voeren.
Laten we beginnen met ' loze ' gegevens om een lijst met items van ons gegevens model te genereren. In een echte toepassing zijn de items afkomstig uit de gewenste gegevens bron.

Voeg eerst het loze pakket toe aan de oplossing met behulp van de DotNet opdracht add package.

   ```bash
   dotnet add package Bogus
   ```

Definieer de definitie van de items die u wilt opslaan. U moet de klasse `Item` in het `Program.cs` bestand definiëren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Maak vervolgens een hulp functie binnen de klasse `Program`. Met deze hulp functie wordt het aantal items opgehaald dat u hebt gedefinieerd om wille keurige gegevens te plaatsen en te genereren:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Lees de items en serialisatie deze naar stream-instanties met behulp van de `System.Text.Json`-klasse. Vanwege de aard van de automatisch gegenereerde gegevens, moet u de gegevens serialiseren als stromen. U kunt het item exemplaar ook rechtstreeks gebruiken, maar door ze te converteren naar stromen, kunt u gebruikmaken van de prestaties van stream-Api's in de CosmosClient. Normaal gesp roken kunt u de gegevens rechtstreeks gebruiken zolang u de partitie sleutel kent. 


Als u de gegevens wilt converteren naar stream-instanties, voegt u de volgende code toe in de `Main`-methode, nadat u de container hebt gemaakt:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Gebruik vervolgens de gegevens stromen om gelijktijdige taken te maken en de taken lijst in te vullen om de items in de container in te voegen. Als u deze bewerking wilt uitvoeren, voegt u de volgende code toe aan de klasse `Program`:

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Al deze gelijktijdige punt bewerkingen worden samen uitgevoerd (dat is in bulk), zoals beschreven in de sectie Inleiding.

## <a name="step-7-run-the-sample"></a>Stap 7: het voor beeld uitvoeren

Als u het voor beeld wilt uitvoeren, kunt u dit gewoon doen door de `dotnet` opdracht:

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Het volledige voor beeld ophalen

Als u geen tijd hebt gehad om de stappen in deze zelfstudie uit te voeren of als u alleen de codevoorbeelden wilt downloaden, kunt u deze ophalen van [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

Nadat u het project hebt gekloond, moet u ervoor zorgen dat u de gewenste referenties in [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25)bijwerkt.

Het voor beeld kan worden uitgevoerd door naar de map opslag plaats te overschakelen en met behulp van `dotnet`:

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Uw project configureren
> * Verbinding maken met een Azure Cosmos-account waarvoor bulk ondersteuning is ingeschakeld
> * Een gegevens import uitvoeren met gelijktijdige bewerkingen voor maken

U kunt nu door gaan met de volgende zelf studie:

> [!div class="nextstepaction"]
>[Query's uitvoeren op Azure Cosmos DB met behulp van de SQL-API](tutorial-query-sql-api.md)
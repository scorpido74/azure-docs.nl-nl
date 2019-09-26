---
title: Een .NET-console-app maken voor het beheren van gegevens in Azure Cosmos DB SQL-API-account
description: Meer informatie over het maken van Azure Cosmos DB SQL-API C# -resources met behulp van een console toepassing.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: kirankk
ms.openlocfilehash: 35e92ff1591bc5f0427dabbf68e697d9c3c32b48
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299290"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Een .NET-console-app maken voor het beheren van gegevens in Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Welkom bij de zelf studie de Azure Cosmos DB SQL API aan de slag. Wanneer u deze zelfstudie hebt voltooid, beschikt u over een consoletoepassing waarmee u Azure Cosmos DB-resources kunt maken en er query's op kunt uitvoeren.

In deze zelf studie wordt versie 3,0 of hoger van de [Azure Cosmos db .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)gebruikt. U kunt werken met [.NET Framework of .net core](https://dotnet.microsoft.com/download).

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
>
> * Een Azure Cosmos-account maken en verbinden
> * Uw project configureren in Visual Studio
> * Een database en een container maken
> * Items toevoegen aan de container
> * Een query uitvoeren op de container
> * Maken, lezen, bijwerken en verwijderen (ruwe) bewerkingen op het item uitvoeren
> * De database verwijderen

Hebt u geen tijd? Geen probleem. De volledige oplossing is beschikbaar via [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Ga naar de sectie [De volledige zelfstudieoplossing ophalen](#GetSolution) voor beknopte instructies.

Tijd om aan de slag te gaan.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: Maak een Azure Cosmos DB-account

Begin met het maken van een Azure Cosmos DB-account. Als u al een account hebt dat u wilt gebruiken, kunt u deze sectie overs Laan. Als u de Azure Cosmos DB-emulator wilt gebruiken, volgt u de stappen op [Azure Cosmos DB-emulator](local-emulator.md) om de emulator in te stellen. Ga vervolgens verder met [stap 2: Stel uw Visual Studio-project](#SetupVS)in.

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Stap 2: Uw Visual Studio-project instellen

1. Open Visual Studio en selecteer **een nieuw project maken**.
1. Kies in **een nieuw project maken de**optie **console-app (.NET Framework)** voor C#en selecteer **volgende**.
1. Geef uw project de naam *CosmosGettingStartedTutorial*en selecteer vervolgens **maken**.

    ![Het project configureren](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. Klik in de **Solution Explorer**met de rechter muisknop op uw nieuwe console toepassing. Deze bevindt zich onder uw Visual Studio-oplossing en selecteer **NuGet-pakketten beheren**.
1. Selecteer in de **NuGet-pakket manager** **Bladeren** en zoek naar *micro soft. Azure. Cosmos*. Kies **micro soft. Azure. Cosmos** en selecteer **installeren**.

   ![NuGet voor Azure Cosmos DB-client-SDK installeren](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   De pakket-id voor de Azure Cosmos DB SQL-API-clientbibliotheek is [Microsoft Azure Cosmos DB-clientbibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Goed gedaan. De configuratie is voltooid en u kunt nu aan de slag met het schrijven van code. Zie [een .net-console-App ontwikkelen met Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)voor het voltooide project van deze zelf studie.

## <a id="Connect"></a>Stap 3: Verbinding maken met een Azure Cosmos DB-account

1. Vervang de verwijzingen aan het begin van uw C# toepassing in het bestand *Program.cs* door de volgende verwijzingen:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Voeg deze constanten en variabelen toe aan `Program` uw klasse.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Als u bekend bent met de vorige versie van de .NET SDK, kunt u bekend zijn met de *verzameling* en het *document*van de voor waarden. Omdat Azure Cosmos DB meerdere API-modellen ondersteunt, gebruikt versie 3,0 van de .NET SDK de algemene voor waarden *container* en *item*. Een *container* kan een verzameling, grafiek of tabel zijn. Een *item* kan een document, Edge/vertex of rij zijn en is de inhoud in een container. Zie [werken met data bases, containers en items in azure Cosmos DB](databases-containers-items.md)voor meer informatie.

1. Open de [Azure Portal](https://portal.azure.com). Zoek uw Azure Cosmos DB-account en selecteer vervolgens **sleutels**.

   ![Azure Cosmos DB sleutels van Azure Portal ophalen](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. Vervang `<your endpoint URL>` in Program.cs door de waarde van **URI**. Vervang `<your primary key>` door de waarde van de **primaire sleutel**.

1. Voeg onder de methode **Main** een nieuwe asynchrone taak toe met de naam **GetStartedDemoAsync**, waarmee een nieuw `CosmosClient`exemplaar wordt gemaakt.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    We gebruiken **GetStartedDemoAsync** als het invoer punt dat methoden aanroept die op Azure Cosmos DB resources werken.

1. Voeg de volgende code toe om de asynchrone taak **GetStartedDemoAsync** uit te voeren vanuit de methode **Main**. Uitzonderingen worden door de methode **Main** onderschept en naar de console geschreven.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecteer F5 om de toepassing uit te voeren.

    Het bericht wordt weer gegeven in de console: **Einde van de demo, druk op een wille keurige toets om af te sluiten.** Dit bericht bevestigt dat uw toepassing een verbinding heeft gemaakt met Azure Cosmos DB. U kunt vervolgens het consolevenster sluiten.

Gefeliciteerd! U hebt verbinding gemaakt met een Azure Cosmos DB-account.

## <a name="step-4-create-a-database"></a>Stap 4: Een database maken

Een database is de logische container voor items die zijn gepartitioneerd in containers. U kunt `CreateDatabaseIfNotExistsAsync` een `CreateDatabaseAsync` data base maken met de or-methode van de [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) -klasse.

1. Kopieer en plak de `CreateDatabaseAsync` methode onder uw `GetStartedDemoAsync` methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`Hiermee maakt u een nieuwe Data `FamilyDatabase` Base met id als deze nog niet bestaat, met de id die `databaseId` is opgegeven in het veld.

1. Kopieer en plak de onderstaande code, waarbij u de CosmosClient maakt om de **CreateDatabaseAsync** -methode aan te roepen die u zojuist hebt toegevoegd.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Uw *Program.cs* moet er nu als volgt uitzien, waarbij uw eind punt en de primaire sleutel zijn ingevuld.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

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
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-data base gemaakt.  

## <a id="CreateColl"></a>Stap 5: Een container maken

> [!WARNING]
> De- `CreateContainerIfNotExistsAsync` methode maakt een nieuwe container met prijs implicaties. Zie onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.
>
>

Een container kan worden gemaakt met behulp van de [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) -of [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) - `CosmosDatabase` methode in de-klasse. Een container bestaat uit items (JSON-documenten als SQL-API) en de bijbehorende toepassings logica aan de server zijde in Java script, bijvoorbeeld opgeslagen procedures, door de gebruiker gedefinieerde functies en triggers.

1. Kopieer en plak de `CreateContainerAsync` methode onder uw `CreateDatabaseAsync` methode. `CreateContainerAsync`Hiermee wordt een nieuwe container met de `FamilyContainer` id gemaakt als deze nog niet bestaat, met behulp van de `containerId` id die is opgegeven `LastName` in het veld gepartitioneerd door van de eigenschap.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Kopieer de onderstaande code en plak deze waar u de CosmosClient hebt geïnstantieerd om de methode **CreateContainer** aan te roepen die u zojuist hebt toegevoegd.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-container gemaakt.  

## <a id="CreateDoc"></a>Stap 6: Items toevoegen aan de container

De methode [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) van de `CosmosContainer` klasse kan een item maken. Bij gebruik van de SQL-API worden items geprojecteerd als documenten, die door de gebruiker gedefinieerde wille keurige JSON-inhoud zijn. U kunt nu een item invoegen in de Azure Cosmos-container.

Eerst gaan we een `Family` klasse maken die objecten vertegenwoordigt die zijn opgeslagen in azure Cosmos db in dit voor beeld. `Parent`We maken `Child`ook subklassen die in`Family`worden gebruikt. `Pet` `Address` Het item moet een `Id` eigenschap hebben die is geserialiseerd als `id` in JSON.

1. Selecteer CTRL + SHIFT + A om **Nieuw item toevoegen**te openen. Voeg een nieuwe klasse `Family.cs` toe aan uw project.

    ![Scherm opname van het toevoegen van een nieuwe Family.cs-klasse aan het project](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Kopieer en plak de `Family` `Parent` `Family.cs`, `Child`,, en`Address`-klassein. `Pet`

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Voeg na `AddItemsToContainerAsync` de`CreateContainerAsync` methode terug in Program.cs de methode toe.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

    Met de code wordt gecontroleerd of er al een item met dezelfde ID bestaat. Er worden twee items ingevoegd, één voor de *familie* en de *Wakefield-serie*.

1. Voeg een aanroep naar `AddItemsToContainerAsync` toe in de methode `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt twee Azure Cosmos-items gemaakt.  

## <a id="Query"></a>Stap 7: Query's uitvoeren op Azure Cosmos DB-resources

Azure Cosmos DB biedt ondersteuning voor uitgebreide query's voor de JSON-documenten die in elke container zijn opgeslagen. Zie aan de slag [met SQL-query's](sql-api-sql-query.md)voor meer informatie. In de volgende voorbeeldcode ziet u hoe u een query uitvoert voor de items die u in de vorige stap hebt ingevoegd.

1. Kopieer en plak de `QueryItemsAsync` -methode na `AddItemsToContainerAsync` uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Voeg een aanroep naar ``QueryItemsAsync`` toe in de methode ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een query uitgevoerd op een Azure Cosmos-container.

## <a id="ReplaceItem"></a>Stap 8: Een JSON-item vervangen

Nu gaan we een item bijwerken in Azure Cosmos DB. We wijzigen de `IsRegistered` eigenschap van de `Family` en `Grade` van de onderliggende items.

1. Kopieer en plak de `ReplaceFamilyItemAsync` -methode na `QueryItemsAsync` uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Voeg een aanroep naar `ReplaceFamilyItemAsync` toe in de methode `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-item vervangen.

## <a id="DeleteDocument"></a>Stap 9: Item verwijderen

Nu gaan we een item verwijderen uit Azure Cosmos DB.

1. Kopieer en plak de `DeleteFamilyItemAsync` -methode na `ReplaceFamilyItemAsync` uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Voeg een aanroep naar `DeleteFamilyItemAsync` toe in de methode `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-item verwijderd.

## <a id="DeleteDatabase"></a>Stap 10: De database verwijderen

Nu gaan we onze data base verwijderen. Als u de gemaakte data base verwijdert, worden de data base en alle onderliggende resources verwijderd. De resources omvatten containers, items en eventuele opgeslagen procedures, door de gebruiker gedefinieerde functies en triggers. We verwijderen ook het `CosmosClient` exemplaar.

1. Kopieer en plak de `DeleteDatabaseAndCleanupAsync` -methode na `DeleteFamilyItemAsync` uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Voeg een aanroep naar ``DeleteDatabaseAndCleanupAsync`` toe in de methode ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecteer F5 om de toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-data base verwijderd.

## <a id="Run"></a>Stap 11: Uw C#-consoletoepassing volledig uitvoeren

Druk in Visual Studio op F5 en compileer en voer de toepassing uit in de foutopsporingsmodus.

Als het goed is, wordt de uitvoer van de volledige app in een consolevenster weergegeven. In de uitvoer ziet u de resultaten van de query's die we hebben toegevoegd. Dit moet overeenkomen met de onderstaande voorbeeld tekst.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Gefeliciteerd! U hebt de zelfstudie voltooid en beschikt nu over een werkende C#-consoletoepassing.

## <a id="GetSolution"></a> De volledige zelfstudieoplossing ophalen

Als u geen tijd hebt gehad om de stappen in deze zelf studie uit te voeren of als u alleen de code voorbeelden wilt downloaden, kunt u deze downloaden.

U hebt de `GetStarted` volgende vereisten nodig om de oplossing te bouwen:

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een [Azure Cosmos DB-account][cosmos-db-create-account].
* De [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)-oplossing die beschikbaar is via GitHub.

Als u de verwijzingen naar de Azure Cosmos DB .NET SDK in Visual Studio wilt herstellen, klikt u met de rechter muisknop op de oplossing in **Solution Explorer**en selecteert u vervolgens **NuGet-pakketten herstellen**. Werk vervolgens in het bestand *app. config* de `EndPointUri` waarden en `PrimaryKey` , zoals beschreven in [stap 3: Verbinding maken met een Azure Cosmos DB](#Connect)-account.

Dat is alles, bouw nu de oplossing. Succes!

## <a name="next-steps"></a>Volgende stappen

* Wilt u een complexere ASP.NET MVC-zelfstudie? Zie [zelf studie: Ontwikkel een ASP.NET Core MVC-webtoepassing met Azure Cosmos DB met behulp](sql-api-dotnet-application.md)van .NET SDK.
* Wilt u de schaal en prestaties testen met Azure Cosmos DB? Bekijk [prestaties en schaal testen met Azure Cosmos DB](performance-testing.md).
* Zie voor meer informatie over het bewaken van Azure Cosmos DB aanvragen, het gebruik en de opslag de [metrische gegevens voor prestaties en opslag bewaken in azure Cosmos DB](monitor-accounts.md).
* Als u query's wilt uitvoeren op basis van de voor beeld-gegevensset, raadpleegt u de [query Playground](https://www.documentdb.com/sql/demo).
* Zie [Welkom bij Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) voor meer informatie over Azure Cosmos DB.

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account

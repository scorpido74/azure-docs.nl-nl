---
title: 'Zelfstudie: Een .NET-console-app maken om gegevens te beheren in Azure Cosmos DB SQL API-account'
description: 'Zelfstudie: Meer informatie over het maken van Azure Cosmos DB SQL API-resources met behulp van een C#-consoletoepassing.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.openlocfilehash: 2681b2199f321f695bc621ed5580319a5e907b34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274018"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Zelfstudie: Een .NET-console-app maken om gegevens te beheren in Azure Cosmos DB SQL API-account

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Welkom bij de zelfstudie Azure Cosmos DB SQL API. Wanneer u deze zelfstudie hebt voltooid, beschikt u over een consoletoepassing waarmee u Azure Cosmos DB-resources kunt maken en er query's op kunt uitvoeren.

In deze zelfstudie wordt versie 3.0 of hoger van de [Azure Cosmos DB .NET SDK gebruikt.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) U werken met [.NET Framework of .NET Core](https://dotnet.microsoft.com/download).

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
>
> * Een Azure Cosmos-account maken en verbinden
> * Uw project configureren in Visual Studio
> * Een database en een container maken
> * Items toevoegen aan de container
> * Een query uitvoeren op de container
> * Bewerkingen voor maken, lezen, bijwerken en verwijderen (CRUD) uitvoeren op het item
> * De database verwijderen

Hebt u geen tijd? Geen probleem. De volledige oplossing is beschikbaar via [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Ga naar de sectie [De volledige zelfstudieoplossing ophalen](#GetSolution) voor beknopte instructies.

Tijd om aan de slag te gaan.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: een Azure Cosmos DB-account maken

Begin met het maken van een Azure Cosmos DB-account. Als je al een account hebt dat je wilt gebruiken, sla je deze sectie over. Als u de Azure Cosmos DB Emulator wilt gebruiken, voert u de stappen uit bij [Azure Cosmos DB Emulator](local-emulator.md) om de emulator in te stellen. Ga dan verder naar [stap 2: Stel je Visual Studio-project in.](#SetupVS)

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Stap 2: Uw Visual Studio-project instellen

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in **Een nieuw project maken**de optie Console App **(.NET Framework)** voor C#en selecteer **Volgende**.
1. Geef uw project *CosmosGettingStartTutorial*een naam en selecteer **Vervolgens Maken**.

    ![Het project configureren](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. Klik in de **Solution Explorer**met de rechtermuisknop op uw nieuwe consoletoepassing, die onder uw Visual Studio-oplossing valt, en selecteer **NuGet-pakketten beheren.**
1. **Selecteer** bladeren en zoeken naar *Microsoft.Azure.Cosmos*in **NuGet Package Manager**. Kies **Microsoft.Azure.Cosmos** en selecteer **Installeren**.

   ![NuGet installeren voor Azure Cosmos DB Client SDK](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   De pakket-id voor de Azure Cosmos DB SQL-API-clientbibliotheek is [Microsoft Azure Cosmos DB-clientbibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Goed gedaan. De configuratie is voltooid en u kunt nu aan de slag met het schrijven van code. Zie [Een .NET-console-app ontwikkelen met Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)voor het voltooide project van deze zelfstudie.

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Stap 3: verbinding maken met een Azure Cosmos DB-account

1. Vervang de verwijzingen aan het begin van uw C#-toepassing in het *Program.cs* bestand door de volgende verwijzingen:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Voeg deze constanten en `Program` variabelen toe aan uw klas.

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
   > Als u bekend bent met de vorige versie van de .NET SDK, bent u mogelijk bekend met de *termenverzameling* en *het document*. Omdat Azure Cosmos DB meerdere API-modellen ondersteunt, gebruikt versie 3.0 van de .NET SDK de algemene termen *container* en *item*. Een *container* kan een verzameling, grafiek of tabel zijn. Een *item* kan een document, rand/hoekpunt of rij zijn en is de inhoud in een container. Zie [Werken met databases, containers en items in Azure Cosmos DB](databases-containers-items.md)voor meer informatie.

1. Open de [Azure Portal](https://portal.azure.com). Zoek uw Azure Cosmos DB-account en selecteer **Sleutels**.

   ![Azure Cosmos DB-sleutels ophalen via Azure-portal](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. In *Program.cs* `<your endpoint URL>` , vervangen door de waarde van **URI**. Vervang `<your primary key>` door de waarde van **PRIMAIRE SLEUTEL**.

1. Voeg onder de **hoofdmethode** een nieuwe asynchrone taak toe genaamd **GetStartedDemoAsync,** die onze nieuwe instantiates. `CosmosClient`

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

    We gebruiken **GetStartedDemoAsync** als het toegangspunt dat methoden aanroept die werken op Azure Cosmos DB-resources.

1. Voeg de volgende code toe om de asynchrone taak **GetStartedDemoAsync** uit te voeren vanuit de methode **Main**. Uitzonderingen worden door de methode **Main** onderschept en naar de console geschreven.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecteer F5 om uw toepassing uit te voeren.

    De console geeft het bericht weer: **Einde van de demo, druk op een toets om af te sluiten.** Dit bericht bevestigt dat uw toepassing een verbinding heeft gemaakt met Azure Cosmos DB. U kunt vervolgens het consolevenster sluiten.

Gefeliciteerd! U hebt met succes verbinding gemaakt met een Azure Cosmos DB-account.

## <a name="step-4-create-a-database"></a>Stap 4: een database maken

Een database is de logische container voor items die zijn gepartitioneerd in containers. De `CreateDatabaseIfNotExistsAsync` klasse `CreateDatabaseAsync` [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) kan een database maken.

1. Kopieer en `CreateDatabaseAsync` plak de `GetStartedDemoAsync` methode onder uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync`hiermee wordt een `FamilyDatabase` nieuwe database met id gemaakt als deze nog `databaseId` niet bestaat, waarbij de id is opgegeven vanuit het veld.

1. Kopieer en plak de onderstaande code waar u de CosmosClient wilt instantiëren om de **CreateDatabaseAsync-methode** aan te roepen die u zojuist hebt toegevoegd.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Uw *Program.cs* moet er nu zo uitzien, waarbij uw eindpunt en primaire sleutel zijn ingevuld.

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

1. Selecteer F5 om uw toepassing uit te voeren.

   > [!NOTE]
   > Als u een fout van de "503-service niet beschikbaar uitzondering" krijgt, is het mogelijk dat de vereiste [poorten](performance-tips.md#networking) voor directe connectiviteitsmodus worden geblokkeerd door een firewall. Als u dit probleem wilt oplossen, opent u de vereiste poorten of gebruikt u de gatewaymoduszoals in de volgende code wordt weergegeven:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Gefeliciteerd! U hebt een Azure Cosmos-database gemaakt.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Stap 5: een container maken

> [!WARNING]
> De `CreateContainerIfNotExistsAsync` methode creëert een nieuwe container, die gevolgen heeft voor de prijsstelling. Ga voor meer informatie naar onze [prijspagina.](https://azure.microsoft.com/pricing/details/cosmos-db/)
>
>

Een container kan worden gemaakt met de methode [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) `CosmosDatabase` of [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) in de klasse. Een container bestaat uit items (JSON-documenten als SQL API) en bijbehorende server-side application logic in JavaScript, bijvoorbeeld opgeslagen procedures, door de gebruiker gedefinieerde functies en triggers.

1. Kopieer en `CreateContainerAsync` plak de `CreateDatabaseAsync` methode onder uw methode. `CreateContainerAsync`hiermee wordt een nieuwe `FamilyContainer` container met de id gemaakt als deze `containerId` nog niet `LastName` bestaat, met behulp van de id die is opgegeven vanuit het veld dat wordt verdeeld door eigenschap.

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

1. Selecteer F5 om uw toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-container gemaakt.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Stap 6: Items toevoegen aan de container

De [**methode CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) van de `CosmosContainer` klasse kan een item maken. Bij het gebruik van de SQL API worden items geprojecteerd als documenten, die door de gebruiker gedefinieerde willekeurige JSON-inhoud zijn. U nu een item invoegen in uw Azure Cosmos-container.

Laten we eerst een `Family` klasse maken die objecten vertegenwoordigt die zijn opgeslagen in Azure Cosmos DB in dit voorbeeld. `Parent`We maken ook `Child`, `Pet` `Address` , subklassen die `Family`worden gebruikt binnen . Het item moet `Id` een eigenschap `id` hebben die is geserialiseerd zoals in JSON.

1. Selecteer Ctrl+Shift+A om **Nieuw item toevoegen**te openen . Voeg een `Family.cs` nieuwe klasse toe aan uw project.

    ![Schermafbeelding van het toevoegen van een nieuwe Family.cs klasse aan het project](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Kopieer en `Family`plak `Parent` `Child`de `Pet`klasse `Address` , `Family.cs`, en de klasse in .

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Terug in *Program.cs,* voeg `AddItemsToContainerAsync` `CreateContainerAsync` de methode na uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    De code controleert of een item met dezelfde id nog niet bestaat. We voegen twee items in, één voor de *Andersen Familie* en de *Wakefield Familie.*

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

1. Selecteer F5 om uw toepassing uit te voeren.

Gefeliciteerd! U hebt twee Azure Cosmos-items gemaakt.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Stap 7: query's uitvoeren op Azure Cosmos DB-resources

Azure Cosmos DB biedt ondersteuning voor uitgebreide query's voor de JSON-documenten die in elke container zijn opgeslagen. Zie [Aan de slag met SQL-query's](sql-api-sql-query.md)voor meer informatie. In de volgende voorbeeldcode ziet u hoe u een query uitvoert voor de items die u in de vorige stap hebt ingevoegd.

1. Kopieer en `QueryItemsAsync` plak de `AddItemsToContainerAsync` methode naar uw methode.

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

1. Selecteer F5 om uw toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-container opgevraagd.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Stap 8: een JSON-item vervangen

Nu werken we een item bij in Azure Cosmos DB. We veranderen het `IsRegistered` eigendom `Family` van `Grade` de en de van een van de kinderen.

1. Kopieer en `ReplaceFamilyItemAsync` plak de `QueryItemsAsync` methode naar uw methode.

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

1. Selecteer F5 om uw toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-item vervangen.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Stap 9: Object verwijderen

Nu verwijderen we een item in Azure Cosmos DB.

1. Kopieer en `DeleteFamilyItemAsync` plak de `ReplaceFamilyItemAsync` methode naar uw methode.

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

1. Selecteer F5 om uw toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-item verwijderd.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Stap 10: de database verwijderen

Nu verwijderen we onze database. Als u de gemaakte database verwijdert, worden de database en alle bronnen voor kinderen verwijderd. De resources omvatten containers, items en opgeslagen procedures, door de gebruiker gedefinieerde functies en triggers. We beschikken ook `CosmosClient` over de instantie.

1. Kopieer en `DeleteDatabaseAndCleanupAsync` plak de `DeleteFamilyItemAsync` methode naar uw methode.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Voeg een aanroep naar ``DeleteDatabaseAndCleanupAsync`` toe in de methode ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecteer F5 om uw toepassing uit te voeren.

Gefeliciteerd! U hebt een Azure Cosmos-database verwijderd.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Stap 11: uw C#-consoletoepassing volledig uitvoeren

Druk in Visual Studio op F5 en compileer en voer de toepassing uit in de foutopsporingsmodus.

Als het goed is, wordt de uitvoer van de volledige app in een consolevenster weergegeven. De uitvoer toont de resultaten van de query's die we hebben toegevoegd. Het moet overeenkomen met de voorbeeldtekst hieronder.

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

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> De volledige zelfstudieoplossing ophalen

Als u geen tijd hebt om de stappen in deze zelfstudie te voltooien of gewoon de codevoorbeelden wilt downloaden, u deze downloaden.

Om de `GetStarted` oplossing te bouwen, hebt u de volgende vereisten nodig:

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een [Azure Cosmos DB-account][cosmos-db-create-account].
* De [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)-oplossing die beschikbaar is via GitHub.

Als u de verwijzingen naar de Azure Cosmos DB .NET SDK in Visual Studio wilt herstellen, klikt u met de rechtermuisknop op de oplossing in **Solution Explorer**en selecteert u **NuGet-pakketten herstellen**. Werk vervolgens in het *bestand App.config* de `EndPointUri` waarden en `PrimaryKey` waarden bij zoals beschreven in stap [3: Verbinding maken met een Azure Cosmos DB-account](#Connect).

Dat is alles, bouw nu de oplossing. Succes!

## <a name="next-steps"></a>Volgende stappen

* Wilt u een complexere ASP.NET MVC-zelfstudie? Zie [Zelfstudie: Ontwikkel een ASP.NET Core MVC-webtoepassing met Azure Cosmos DB met .NET SDK](sql-api-dotnet-application.md).
* Wilt u schaal- en prestatietests uitvoeren met Azure Cosmos DB? Zie [Prestatie- en schaaltests met Azure Cosmos DB](performance-testing.md).
* Zie [Prestatie- en opslagstatistieken controleren in Azure Cosmos DB](monitor-accounts.md)voor meer informatie over het controleren van Azure Cosmos DB-aanvragen, -gebruik en -opslag.
* Zie de [queryspeelplaats](https://www.documentdb.com/sql/demo)om query's uit te voeren op basis van onze voorbeeldgegevensset.
* Zie [Welkom bij Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) voor meer informatie over Azure Cosmos DB.

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account

---
title: 'Azure Cosmos DB: een taken-app ontwikkelen met Xamarin'
description: Biedt een Xamarin-voorbeeldcode die u kunt gebruiken om verbinding te maken met en query's uit te voeren op Azure Cosmos DB
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/30/2018
ms.author: masoucou
ms.openlocfilehash: 125cc2516fbd694bbe62545e85d4e14225fb2a6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756431"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Snelstartgids: een TODO-app bouwen met Xamarin met behulp van Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafen en hier query’s op uitvoeren. Deze databases genieten allemaal het voordeel van de wereldwijde distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB.

> [!NOTE]
> Voorbeeldcode voor een hele canonieke voorbeeldapp in Xamarin, met meerdere Azure-producten, waaronder CosmosDB, zijn te vinden [hier](https://github.com/xamarinhq/app-geocontacts) op GitHub. Deze app demonstreert de weergave van geografisch verspreide contactpersonen en de mogelijkheid om toe te staan dat deze contactpersonen hun locatie wijzigen.

Deze snelstart laat zien hoe u een SQL API-account van Azure Cosmos DB, een documentdatabase en een container kunt maken met behulp van de Azure-portal. Vervolgens bouwt en implementeert u een mobiele app voor taken lijsten die is gebouwd op de [SQL .net API](sql-api-sdk-dotnet.md) en [Xamarin](https://docs.microsoft.com/xamarin/) het gebruik van [Xamarin. Forms](https://docs.microsoft.com/xamarin/) en het [MVVM-architectuur patroon](https://docs.microsoft.com/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

![Taken-app in Xamarin uitgevoerd op iOS](./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png)

## <a name="prerequisites"></a>Vereisten

Als u in Windows ontwikkelt en Visual Studio 2019 nog niet hebt geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** en **Mobile Development met .NET**-workloads inschakelt tijdens de installatie van Visual Studio.

Als u een Mac gebruikt, kunt u **gratis** [Visual Studio voor Mac](https://www.visualstudio.com/vs/mac/) downloaden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaat u de SQL API-app voor Xamarin klonen vanuit GitHub, de code controleren, de API-sleutels ophalen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Open vervolgens vanuit de map samples/xamarin/ToDoItems in Visual Studio het bestand ToDoItems.sln.

## <a name="obtain-your-api-keys"></a>Uw API-sleutels ophalen

Ga terug naar Azure Portal om de API-sleutelgegevens op te halen en deze in de app te kopiëren.

1. Klik in [Azure Portal](https://portal.azure.com/), in uw SQL API-account voor Azure Cosmos DB, in het linker navigatiegedeelte op **Sleutels** en klik vervolgens op **Sleutels voor lezen/schrijven**. In de volgende stap gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de URI en Primaire sleutel in het bestand APIKeys.cs te kopiëren.

    ![Een toegangssleutel bekijken en kopiëren in Azure Portal, blade Sleutels](./media/create-sql-api-xamarin-dotnet/keys.png)

2. Open in Visual Studio 2019 of Visual Studio voor Mac het bestand APIKeys.cs in de map Azure-Cosmos-DB-SQL-xamarin-Getting-Started/src/ToDoItems. Core/Helpers.

3. Kopieer uw URI-waarde vanuit de portal (met de kopieerknop) en geef deze op als waarde voor de variabele `CosmosEndpointUrl` in APIKeys.cs.

    `public static readonly string CosmosEndpointUrl = "";`

4. Kopieer vervolgens de waarde van uw PRIMAIRE SLEUTEL vanuit de portal en geef deze op als waarde van `Cosmos Auth Key` in APIKeys.cs.

    `public static readonly string CosmosAuthKey = "";`

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>De code bekijken

Deze oplossing laat zien hoe u een taken-app kunt maken met behulp van de SQL API van Azure Cosmos DB en Xamarin.Forms. De app bevat twee tabbladen. Het eerste tabblad bevat een lijst met taken die nog niet zijn voltooid. Het tweede tabblad geeft een lijst met voltooide taken weer. U kunt niet alleen niet-voltooide taken in het eerste tabblad bekijken, maar ook nieuwe taken toevoegen, bestaande taken bewerken en taken als voltooid markeren.

![JSON-gegevens kopiëren en op Opslaan klikken in Data Explorer in Azure Portal](./media/create-sql-api-xamarin-dotnet/android-todo-screen.png)

De code in de takenoplossing bevat:

* ToDoItems.Core: dit is een standaard .NET-project met een Xamarin.Forms-project en een logische code voor gedeelde toepassingen die taken in Azure Cosmos DB bijhoudt.
* ToDoItems.Android: dit project bevat de Android-app.
* ToDoItems.iOS: dit project bevat de iOS-app.

Laten we nog eens kort bekijken hoe de app met Azure Cosmos DB communiceert.

* Het NuGet-pakket [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) moet aan alle projecten worden toegevoegd.
* De `ToDoItem` -klasse in de map map Azure-documentdb-DotNet/samples/Xamarin/ToDoItems/ToDoItems. core/model modeleert de documenten in de container **items** die hierboven zijn gemaakt. Houd er rekening mee dat de namen van eigenschappen hoofdlettergevoelig zijn.
* De klasse `CosmosDBService` in de map azure-documentdb-dotnet/samples/xamarin/ToDoItems/ToDoItems.Core/Services bevat de communicatie met Azure Cosmos DB.
* Binnen de klasse `CosmosDBService` bevindt zich een variabele van het type `DocumentClient`. De `DocumentClient` wordt gebruikt voor het configureren en uitvoeren van aanvragen voor het Azure Cosmos DB-account en wordt geïnstantieerd:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Bij het uitvoeren van een query op een container `DocumentClient.CreateDocumentQuery<T>` voor documenten wordt de methode gebruikt, zoals hier `CosmosDBService.GetToDoItems` wordt weer gegeven in de functie:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    De `CreateDocumentQuery<T>` neemt een URI die verwijst naar de container die in de vorige sectie is gemaakt. U kunt ook LINQ-operators opgeven, zoals een `Where`-component. In dit geval worden alleen taken die niet zijn voltooid, geretourneerd.

    De functie `CreateDocumentQuery<T>` wordt synchroon uitgevoerd en retourneert een `IQueryable<T>`. De methode `AsDocumentQuery` zet de `IQueryable<T>` echter om naar een `IDocumentQuery<T>`-object dat asynchroon kan worden uitgevoerd. Zo wordt de gebruikersinterface-thread voor mobiele toepassingen niet geblokkeerd.

    De functie `IDocumentQuery<T>.ExecuteNextAsync<T>` haalt de pagina met resultaten op van Azure Cosmos DB en `HasMoreResults` controleert of er nog extra resultaten moeten worden geretourneerd.

> [!TIP]
> Verschillende functies die worden uitgevoerd op Azure Cosmos-containers en-documenten, maken gebruik van een URI als para meter waarmee het adres van de container of het document wordt opgegeven. Deze URI is opgesteld met de klasse `URIFactory`. Uri's voor data bases, containers en documenten kunnen allemaal met deze klasse worden gemaakt.

* De `ComsmosDBService.InsertToDoItem` functie laat zien hoe u een nieuw document kunt invoegen:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    De URI van het item is opgegeven, evenals het item dat moet worden ingevoegd.

* De `CosmosDBService.UpdateToDoItem` functie laat zien hoe u een bestaand document vervangt door een nieuw bestand:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Hier is een nieuwe URI vereist om het te vervangen document uniek te identificeren en dit te verkrijgen door `UriFactory.CreateDocumentUri` de data base en de namen van de containers en de id van het document door te geven.

    De `DocumentClient.ReplaceDocumentAsync` vervangt het document dat door de URI is geïdentificeerd door het document dat als parameter is opgegeven.

* Het verwijderen van een item wordt getoond `CosmosDBService.DeleteToDoItem` met de functie:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Let op de unieke document-URI die wordt gemaakt en doorgegeven aan de functie `DocumentClient.DeleteDocumentAsync`.

## <a name="run-the-app"></a>De app uitvoeren

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB.

Met de volgende stappen wordt gedemonstreerd hoe u de app uitvoert met behulp van het Visual Studio voor Mac-foutopsporingsprogramma.

> [!NOTE]
> Het gebruik van de Android-app is precies hetzelfde; eventuele verschillen worden in de onderstaande stappen aangegeven. Als u fouten wilt opsporen met Visual Studio in Windows, bekijkt u de documentatie voor [iOS](https://docs.microsoft.com/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) of [Android](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/).

1. Selecteer eerst het doelplatform door op de gemarkeerde vervolgkeuzelijst te klikken en ToDoItems.iOS te selecteren voor iOS of ToDoItems.Android te selecteren voor Android.

    ![Een platform kiezen voor foutopsporing in Visual Studio voor Mac](./media/create-sql-api-xamarin-dotnet/ide-select-platform.png)

2. Druk op cmd + Enter of klik op de afspeelknop om de foutopsporing te starten in de app.

    ![Foutopsporing starten in Visual Studio voor Mac](./media/create-sql-api-xamarin-dotnet/ide-start-debug.png)

3. Als de iOS-simulator of Android-emulator klaar is met opstarten, geeft de app twee tabbladen weer, onderaan het scherm voor iOS en bovenaan het scherm voor Android. Het eerste geeft taken weer die niet zijn voltooid, het tweede taken die wel zijn voltooid.

    ![Startscherm van taken-app](./media/create-sql-api-xamarin-dotnet/ios-droid-started.png)

4. Als u een taak op iOS wilt voltooien, schuift u deze naar links en tikt u op de knop **Voltooien**. Als u een taak op Android wilt voltooien, houdt u het item lang ingedrukt en tikt u op de knop Voltooien.

    ![Een taak voltooien](./media/create-sql-api-xamarin-dotnet/simulator-complete.png)

5. Als u een taak wilt bewerken, tikt u op het item. Er wordt een nieuw scherm weergegeven waarop u nieuwe waarden kunt invoeren. Door op de knop Opslaan te tikken,worden de wijzigingen in Azure Cosmos DB opgeslagen.

    ![Taak bewerken](./media/create-sql-api-xamarin-dotnet/simulator-edit.png)

6. Als u een taak wilt toevoegen, tikt u in de rechterbovenhoek van het startscherm op de knop **Toevoegen**. Er wordt een nieuwe, lege pagina voor bewerken weergegeven.

    ![Taak toevoegen](./media/create-sql-api-xamarin-dotnet/simulator-add.png)

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account maakt, een container maakt met behulp van de Data Explorer en een Xamarin-app bouwt en implementeert. U kunt nu aanvullende gegevens importeren in uw Azure Cosmos-account.

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)

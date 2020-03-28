---
title: ASP.NET Core MVC-webapp-zelfstudie met Azure Cosmos DB
description: ASP.NET Core MVC-zelfstudie om een MVC-webtoepassing te maken met Azure Cosmos DB. U slaat JSON op en opent stap voor stap gegevens van een todo-app die wordt gehost op Azure App Service - ASP NET Core MVC-zelfstudie.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274077"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Zelfstudie: Een ASP.NET Core MVC-webtoepassing ontwikkelen met Azure Cosmos DB met behulp van .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

In deze zelfstudie wordt uitgelegd hoe u Azure Cosmos DB gebruikt om gegevens uit een ASP.NET MVC-toepassing die wordt gehost op Azure op te slaan en te openen. In deze zelfstudie gebruikt u de .NET SDK V3. In de volgende afbeelding ziet u de webpagina die u maakt met behulp van het voorbeeld in dit artikel:

![Schermafbeelding van de dodolijst MVC-webtoepassing die is gemaakt door deze zelfstudie - ASP NET Core MVC-zelfstudie stap voor stap](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Als u geen tijd hebt om de zelfstudie te voltooien, kunt u het volledige voorbeeldproject uit [GitHub][GitHub] downloaden.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
>
> * Een Azure Cosmos-account maken
> * Een ASP.NET Core MVC-app maken
> * De app met Azure Cosmos DB verbinden
> * Uitvoeren van bewerkingen voor maken, lezen, bijwerken en verwijderen (CRUD) op de gegevens

> [!TIP]
> In deze zelfstudie wordt ervan uitgegaan dat u eerder ervaring hebt met ASP.NET Core MVC en Azure App Service. Als u nieuw bent bij ASP.NET Core of de [vereiste tools,](#prerequisites)raden we u aan om het volledige voorbeeldproject van [GitHub][GitHub]te downloaden, de vereiste NuGet-pakketten toe te voegen en uit te voeren. Zodra u het project gaat maken, kunt u dit artikel lezen voor meer informatie over de code in de context van het project.

## <a name="prerequisites"></a><a name="prerequisites"></a>Vereisten

Controleer voordat u de instructies in dit artikel volgt, dat u de volgende bronnen hebt:

* Een actief Azure-account. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alle screenshots in dit artikel zijn van Microsoft Visual Studio Community 2019. Als u een andere versie gebruikt, komen uw schermen en opties mogelijk niet volledig overeen. De oplossing moet werken als u aan de voorwaarden voldoet.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Stap 1: een Azure Cosmos-account maken

Begin met het maken van een Azure Cosmos-account. Als u al een Azure Cosmos DB SQL API-account hebt of als u de Azure Cosmos DB-emulator gebruikt, gaat u naar [Stap 2: Een nieuwe ASP.NET MVC-toepassing maken.](#create-a-new-mvc-application)

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

In het volgende gedeelte maakt u een nieuwe ASP.NET Core MVC-toepassing.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Stap 2: Een nieuwe ASP.NET Core MVC-toepassing maken

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Zoek en selecteer in **Een nieuw project maken**ASP.NET **Kernwebtoepassing** voor C#. Selecteer **Volgende** om door te gaan.

   ![Nieuw ASP.NET Core-webtoepassingsproject maken](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Geef in **Uw nieuwe project configureren**de naam van het project *todo* en selecteer **Maken**.

1. Kies **webtoepassing (Model-View-Controller)** in **Een nieuwe ASP.NET Kernwebtoepassing**maken. Selecteer **Maken** om door te gaan.

   Visual Studio maakt een lege MVC-toepassing.

1. Selecteer Foutopsporing van **foutopsporing** > of F5**debuggen van defoutopsporing** of F5 selecteren om uw ASP.NET-toepassing lokaal uit te voeren.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Stap 3: Azure Cosmos DB NuGet-pakket aan het project toevoegen

Nu we de meeste ASP.NET Core MVC-frameworkcode hebben die we nodig hebben voor deze oplossing, voegen we de NuGet-pakketten toe die nodig zijn om verbinding te maken met Azure Cosmos DB.

1. Klik in **Solution Explorer**met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren.**

1. Zoek naar En selecteer **Microsoft.Azure.Cosmos**in **NuGet Package Manager**. Selecteer **Installeren**.

   ![NuGet-pakket installeren](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio downloadt en installeert het Azure Cosmos DB-pakket en de afhankelijkheden ervan.

   U ook **Package Manager Console** gebruiken om het NuGet-pakket te installeren. Selecteer hiervoor **Extra NuGet** > **Package Manager** > **Package Manager Console**. Typ achter de prompt de volgende opdracht:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Stap 4: De ASP.NET Core MVC-toepassing instellen

Laten we nu de modellen, de weergaven en de controllers toevoegen aan deze MVC-toepassing.

### <a name="add-a-model"></a><a name="add-a-model"></a> Een model toevoegen

1. Klik in **Solution Explorer**met de rechtermuisknop op de map **Modellen** en selecteer**Klasse** **toevoegen** > .

1. Geef **in Nieuw item toevoegen**een naam aan uw nieuwe klasse *Item.cs* en selecteer **Toevoegen**.

1. Vervang de inhoud van *Item.cs* klasse door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB gebruikt JSON om gegevens te verplaatsen en op te slaan. U `JsonProperty` het kenmerk gebruiken om te bepalen hoe JSON objecten serialiseert en deserialiseert. De `Item` klasse toont `JsonProperty` het attribuut. Deze code bepaalt de indeling van de eigenschapsnaam die in JSON gaat. Het hernoemt ook de `Completed`eigenschap .NET .

### <a name="add-views"></a><a name="add-views"></a>Weergaven toevoegen

Laten we vervolgens de volgende drie weergaven maken.

* Een weergave voor een lijst met items toevoegen
* Een nieuwe objectweergave toevoegen
* Een weergave voor het bewerken van items toevoegen

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>Een weergave voor een lijst met items toevoegen

1. Klik in **Solution Explorer**met de rechtermuisknop op de map **Weergaven** en selecteer**Nieuwe map** **toevoegen** > . Naam van het *mapitem*.

1. Klik met de rechtermuisknop op de lege **itemmap** en selecteer**Weergave** **toevoegen** > .

1. Geef **in De MVC-weergave**toevoegen de volgende waarden op:

   * Voer in **Weergavenaam** *Index*in .
   * Selecteer **Lijst**in **Sjabloon**.
   * Selecteer **Model class** **item (todo. Modellen)**.
   * Selecteer **Een indelingspagina gebruiken** en typ *~/Weergaven/Gedeeld/_Layout.cshtml*.

   ![Schermafbeelding van het dialoogvenster MVC-weergave toevoegen](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Zodra u deze waarden hebt toegevoegd, klikt u op **Toevoegen** en wordt er een nieuwe sjabloonweergave in Visual Studio gemaakt.

Eenmaal klaar, Visual Studio opent de *cshtml-bestand* dat het maakt. U dat bestand sluiten in Visual Studio. We komen er later op terug.

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>Een nieuwe objectweergave toevoegen

Maak een nieuwe weergave om items te maken, vergelijkbaar met hoe u een weergave hebt gemaakt voor het weergeven van items, door de volgende stappen te volgen:

1. Klik in **Solution Explorer**opnieuw op de map **Item** en selecteer**Weergave** **toevoegen** > .

1. Breng in **De MVC-weergave toevoegen**de volgende wijzigingen aan:

   * Voer in **Weergavenaam**De optie *Maken in*.
   * Selecteer **in Sjabloon**de optie **Maken**.
   * Selecteer **Model class** **item (todo. Modellen)**.
   * Selecteer **Een indelingspagina gebruiken** en typ *~/Weergaven/Gedeeld/_Layout.cshtml*.
   * Selecteer **Toevoegen**.

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Een weergave voor het bewerken van items toevoegen

En tot slot voegt u een weergave voor het bewerken van items toe met de volgende stappen:

1. Klik in de **Solution Explorer**nogmaals op de map **Item** en selecteer **Weergave** > **toevoegen**.

1. Breng in **De MVC-weergave toevoegen**de volgende wijzigingen aan:

   * Typ in het vak **Weergavenaam***Bewerken*.
   * Selecteer in het vak **Sjabloon** de optie **Bewerken**.
   * Selecteer in het vak **Modelklasse** de optie **Item (todo.Models)**.
   * Selecteer **Een indelingspagina gebruiken** en typ *~/Weergaven/Gedeeld/_Layout.cshtml*.
   * Selecteer **Toevoegen**.

Zodra u deze stappen hebt voltooid, sluit u alle *cshtml-documenten* in Visual Studio wanneer u later terugkeert naar deze weergaven.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Services declareren en initialiseren

Eerst voegen we een klasse toe die de logica bevat om verbinding te maken met En Azure Cosmos DB te gebruiken. Voor deze zelfstudie kapselen we deze `CosmosDBService` logica in `ICosmosDBService`in een klasse genaamd en een interface genaamd . Deze service doet de CRUD-bewerkingen. Het leest ook feedbewerkingen, zoals het aanbieden van onvolledige items, het maken, bewerken en verwijderen van de items.

1. Klik in **Solution Explorer**met de rechtermuisknop op uw project en selecteer Nieuwe**map** **toevoegen** > . Geef de *mapServices*een naam .

1. Klik met de rechtermuisknop op de map **Services** en selecteer**Klasse** **toevoegen** > . Geef de nieuwe klasse *CosmosDBService* een naam en selecteer **Toevoegen**.

1. Vervang de inhoud van *CosmosDBService.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Klik met de rechtermuisknop op de map **Services** en selecteer**Klasse** **toevoegen** > . Geef de nieuwe klasse *ICosmosDBService* een naam en selecteer **Toevoegen**.

1. Voeg de volgende code toe aan de klasse *ICosmosDBService:*

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Open het *Startup.cs-bestand* in `ConfigureServices` uw oplossing en vervang de methode door:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    De code in deze stap initialiseert de client op basis van de configuratie als een singleton-instantie die moet worden geïnjecteerd via [afhankelijkheidsinjectie in ASP.NET Core.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)

1. Voeg binnen hetzelfde bestand de volgende methode **InitializeCosmosClientInstanceAsync**toe , die de configuratie leest en de client initialiseert.

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Definieer de configuratie in het *appsettings.json-bestand* van het project zoals weergegeven in het volgende fragment:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Een controller toevoegen

1. Klik in **Solution Explorer**met de rechtermuisknop op de map **Controllers** en selecteer**Controller** **toevoegen** > .

1. Selecteer **MVC-controller** toevoegen in **Scaffold**toevoegen - Leeg en selecteer **Toevoegen**.

   ![MVC-controller selecteren - Leeg in Steiger toevoegen](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Geef uw nieuwe controller *ItemController een*naam .

1. Vervang de inhoud van *ItemController.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Het kenmerk **ValidateAntiForgeryToken** wordt hier gebruikt om deze toepassing te beschermen tegen aanvallen via aanvraagvervalsing op meerdere sites. Uw standpunten moeten werken met deze anti-vervalsing token ook. Zie [Aanvallen op cross-site aanvraagvervalsing (CSRF) voorkomen in ASP.NET MVC-toepassing][Preventing Cross-Site Request Forgery]voor meer informatie en voorbeelden. De broncode op [GitHub][GitHub] beschikt over de volledige implementatie.

We gebruiken ook het kenmerk **Bind** voor de methodeparameter om u te beschermen tegen over-postingaanvallen. Zie [Zelfstudie: Crud-functionaliteit implementeren met het Entiteitskader in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC]voor meer informatie.

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Stap 5: De toepassing lokaal uitvoeren

Als u de toepassing op uw lokale computer wilt testen, gebruikt u de volgende stappen:

1. Druk in Visual Studio op F5 om de toepassing in de foutopsporingsmodus op te bouwen. De toepassing wordt opgebouwd en wordt er een browser gestart met het lege rasterpagina dat we eerder hebben gezien:

   ![Schermafbeelding van de webapplicatie voor de todolijst die is gemaakt door deze zelfstudie](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Als de toepassing in plaats daarvan `/Item` wordt geopend naar de startpagina, wordt u toegevoegd aan de url.

1. Selecteer de koppeling **Nieuwe maken** en voeg waarden toe aan de velden **Naam** en **beschrijving.** Laat het selectievakje **Voltooid** niet ingeschakeld. Als u het selecteert, voegt de app het nieuwe item in een voltooide status toe. Het item wordt niet meer weergegeven in de oorspronkelijke lijst.

1. Selecteer **Maken**. De app stuurt u terug naar de **indexweergave** en uw item wordt weergegeven in de lijst. Je nog een paar items toevoegen aan je **to-do-lijst.**

    ![Schermafbeelding van de indexweergave](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Selecteer **Bewerken** naast een **item** in de lijst. De app opent de **bewerkingsweergave** waar u elke eigenschap van uw object bijwerken, inclusief de **vlag Voltooid.** Als u **Voltooid** selecteert en **Opslaan**selecteert, wordt in de app het **item** weergegeven zoals voltooid in de lijst.

   ![Schermafbeelding van de indexweergave met het selectievakje Voltooid ingeschakeld](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Controleer de status van de gegevens in de Azure Cosmos DB-service met [Cosmos Explorer](https://cosmos.azure.com) of de Data Explorer van Azure Cosmos DB Emulator.

1. Nadat u de app hebt getest, selecteert u Ctrl+F5 om te stoppen met het debuggen van de app. U kunt de app nu implementeren.

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Stap 6: De toepassing implementeren

Nu de volledige toepassing correct werkt met Azure Cosmos DB, kunt u de web-app implementeren in Azure App Service.  

1. Als u deze toepassing wilt publiceren, klikt u met de rechtermuisknop op het project in **Solution Explorer** en selecteert u **Publiceren**.

1. Selecteer **App-service**in **Een publicatiedoel kiezen**.

1. Als u een bestaand App-serviceprofiel wilt gebruiken, kiest **u Bestaand selecteren**en selecteert u **Publiceren**.

1. Selecteer in **App-service**een **abonnement**. Sorteer met de filter **Weergave** op resourcegroep of resourcetype.

1. Zoek je profiel en selecteer **OK**. Zoek vervolgens de vereiste Azure App Service en selecteer **OK**.

   ![Dialoogvenster App Service in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Een andere optie is het maken van een nieuw profiel:

1. Net als in de vorige procedure klikt u met de rechtermuisknop op het project in **Solution Explorer** en selecteert u **Publiceren**.
  
1. Selecteer **App-service**in **Een publicatiedoel kiezen**.

1. Selecteer in **Een publicatiedoel kiezen**de optie **Nieuw maken** en selecteer **Publiceren**.

1. Voer in **App-service**de naam van uw web-app en het juiste abonnement, de resourcegroep en het hostingplan in en selecteer **Vervolgens Maken**.

   ![Dialoogvenster App-service maken in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

In een paar seconden publiceert Visual Studio uw webtoepassing en start een browser waar u uw project zien draaien in Azure!

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een ASP.NET Core MVC-webtoepassing bouwen. Uw toepassing heeft toegang tot gegevens die zijn opgeslagen in Azure Cosmos DB. U nu doorgaan met de volgende bronnen:

* [Partitionering in Azure Cosmos DB](./partitioning-overview.md)
* [Aan de slag met SQL-query's](./how-to-sql-query.md)
* [Meer informatie over het modelleren en partitioneren van gegevens in Azure Cosmos DB aan de hand van een praktijkvoorbeeld](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app

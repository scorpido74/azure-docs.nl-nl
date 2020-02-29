---
title: Zelf studie over het ASP.NET Core MVC-Web-app met Azure Cosmos DB
description: ASP.NET Core MVC-zelf studie voor het maken van een MVC-webtoepassing met behulp van Azure Cosmos DB. U slaat JSON-en Access-gegevens op uit een TODO-app die wordt gehost op Azure App Service-ASP NET core MVC-zelf studie stap voor stap.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 5403725a57c68a45621d6cc509c57d864b2e0633
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164913"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Zelf studie: een ASP.NET Core MVC-webtoepassing ontwikkelen met Azure Cosmos DB met behulp van .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

In deze zelfstudie wordt uitgelegd hoe u Azure Cosmos DB gebruikt om gegevens uit een ASP.NET MVC-toepassing die wordt gehost op Azure op te slaan en te openen. In deze zelf studie gebruikt u de .NET SDK v3. In de volgende afbeelding ziet u de webpagina die u maakt met behulp van het voor beeld in dit artikel:

![Scherm afbeelding van de taken lijst MVC-webtoepassing die door deze zelf studie is gemaakt: ASP NET core MVC-zelf studie stap voor stap](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Als u geen tijd hebt om de zelf studie te volt ooien, kunt u het volledige voorbeeld project downloaden van [github][GitHub].

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
>
> * Een Azure Cosmos-account maken
> * Een ASP.NET Core MVC-app maken
> * De app met Azure Cosmos DB verbinden
> * Maken, lezen, bijwerken en verwijderen (ruwe) bewerkingen uitvoeren op de gegevens

> [!TIP]
> In deze zelf studie wordt ervan uitgegaan dat u een eerdere ervaring hebt met het gebruik van ASP.NET Core MVC en Azure App Service. Als u geen ervaring hebt met ASP.NET Core of de [vereiste hulpprogram ma's](#prerequisites), raden we u aan het volledige voorbeeld project van [github][GitHub]te downloaden, de vereiste NuGet-pakketten toe te voegen en deze uit te voeren. Zodra u het project gaat maken, kunt u dit artikel lezen voor meer informatie over de code in de context van het project.

## <a name="prerequisites"></a>Vereisten

Voordat u de instructies in dit artikel volgt, moet u ervoor zorgen dat u over de volgende resources beschikt:

* Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alle scherm opnamen in dit artikel zijn afkomstig uit micro soft Visual Studio Community 2019. Als u een andere versie gebruikt, komen uw schermen en opties mogelijk niet geheel overeen. De oplossing zou moeten werken als u aan de vereisten voldoet.

## <a name="create-an-azure-cosmos-account"></a>Stap 1: een Azure Cosmos-account maken

Begin met het maken van een Azure Cosmos-account. Als u al een Azure Cosmos DB SQL-API-account hebt of als u de Azure Cosmos DB-emulator gebruikt, gaat u verder met [stap 2: een nieuwe ASP.NET MVC-toepassing maken](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

In de volgende sectie maakt u een nieuwe ASP.NET Core MVC-toepassing.

## <a name="create-a-new-mvc-application"></a>Stap 2: een nieuwe ASP.NET Core MVC-toepassing maken

1. Open Visual Studio en selecteer **een nieuw project maken**.

1. In **een nieuw project maken**, zoekt en selecteert u **ASP.net core webtoepassing** voor C#. Selecteer **Volgende** om door te gaan.

   ![Nieuw project voor ASP.NET Core-webtoepassing maken](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. Geef in **uw nieuwe project configureren**de naam project *taken* op en selecteer **maken**.

1. Kies in **een nieuwe ASP.net core-webtoepassing maken de**optie **Webtoepassing (model-view-controller)** . Selecteer **maken** om door te gaan.

   Visual Studio maakt een lege MVC-toepassing.

1. Selecteer **fout opsporing** > **fout opsporing starten** of op F5 om uw ASP.NET-toepassing lokaal uit te voeren.

## <a name="add-nuget-packages"></a>Stap 3: Azure Cosmos DB NuGet-pakket aan het project toevoegen

Nu we de meeste van de ASP.NET Core MVC-Framework code hebben die we nodig hebben voor deze oplossing, voegen we de NuGet-pakketten toe die nodig zijn om verbinding te maken met Azure Cosmos DB.

1. Klik in **Solution Explorer**met de rechter muisknop op uw project en selecteer **NuGet-pakketten beheren**.

1. Zoek in de **NuGet-pakket manager**naar **micro soft. Azure. Cosmos**en selecteer deze. Selecteer **Installeren**.

   ![NuGet-pakket installeren](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio downloadt en installeert het Azure Cosmos DB-pakket en de bijbehorende afhankelijkheden.

   U kunt ook de **Package Manager-console** gebruiken om het NuGet-pakket te installeren. Hiertoe selecteert u **Tools** > **NuGet package manager** > **Package Manager console**. Typ achter de prompt de volgende opdracht:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="set-up-the-mvc-application"></a>Stap 4: de ASP.NET Core MVC-toepassing instellen

Nu gaan we de modellen, de weer gaven en de controllers toevoegen aan deze MVC-toepassing.

### <a name="add-a-model"></a> Een model toevoegen

1. In **Solution Explorer**klikt u met de rechter muisknop op de map **modellen** en selecteert u > **klasse** **toevoegen** .

1. Geef in **Nieuw item toevoegen**een naam op voor de nieuwe klasse *item.cs* en selecteer **toevoegen**.

1. Vervang de inhoud van de klasse *item.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB gebruikt JSON om gegevens te verplaatsen en op te slaan. U kunt het kenmerk `JsonProperty` gebruiken om te bepalen hoe JSON-serialisatie en objecten worden gedeserialiseerd. De klasse `Item` demonstreert het kenmerk `JsonProperty`. Met deze code bepaalt u de indeling van de naam van de eigenschap die naar JSON gaat. De naam van de .NET-eigenschap `Completed`wordt ook gewijzigd.

### <a name="add-views"></a>Weergaven toevoegen

Vervolgens gaan we de volgende drie weer gaven maken.

* Een weer gave voor een lijst item toevoegen
* Een nieuwe item weergave toevoegen
* Een weer gave voor het bewerken van items toevoegen

#### <a name="AddItemIndexView"></a>Een weergave voor een lijst met items toevoegen

1. Klik in **Solution Explorer**met de rechter muisknop op de map **Views** en selecteer > **nieuwe map** **toevoegen** . Geef *de map een*naam.

1. Klik met de rechter muisknop op de map met het lege **item** en selecteer vervolgens > **weer gave** **toevoegen** .

1. Geef in de **weer gave MVC toevoegen**de volgende waarden op:

   * Voer *index*in **weergave naam**in.
   * Selecteer in **sjabloon** **lijst**.
   * Selecteer in **model klasse** **item (TODO. Modellen)** .
   * Selecteer **een indelings pagina gebruiken** en voer *~/views/Shared/_Layout. cshtml*in.

   ![Scherm afbeelding van het dialoog venster MVC-weer gave toevoegen](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. Zodra u deze waarden hebt toegevoegd, klikt u op **Toevoegen** en wordt er een nieuwe sjabloonweergave in Visual Studio gemaakt.

Als u klaar bent, opent Visual Studio het *cshtml* -bestand dat wordt gemaakt. U kunt dat bestand sluiten in Visual Studio. We worden later teruggebeld.

#### <a name="AddNewIndexView"></a>Een weergave voor nieuwe items toevoegen

Maak een nieuwe weergave om items te maken, vergelijkbaar met hoe u een weergave hebt gemaakt voor het weergeven van items, door de volgende stappen te volgen:

1. In **Solution Explorer**klikt u nogmaals met de rechter muisknop op de map **item** en selecteert u > **weer gave** **toevoegen** .

1. Breng de volgende wijzigingen aan in de **weer gave MVC toevoegen**:

   * Voer in **weergave naam** *maken*in.
   * Selecteer in **sjabloon** **maken**.
   * Selecteer in **model klasse** **item (TODO. Modellen)** .
   * Selecteer **een indelings pagina gebruiken** en voer *~/views/Shared/_Layout. cshtml*in.
   * Selecteer **Toevoegen**.

#### <a name="AddEditIndexView"></a>Een weergave voor het bewerken van items toevoegen

En tot slot voegt u een weergave voor het bewerken van items toe met de volgende stappen:

1. Klik in het **Solution Explorer**met de rechter muisknop op de map **item** en selecteer **toevoegen** > **weer geven**.

1. Breng de volgende wijzigingen aan in de **weer gave MVC toevoegen**:

   * Typ in het vak **Weergavenaam***Bewerken*.
   * Selecteer in het vak **Sjabloon** de optie **Bewerken**.
   * Selecteer in het vak **Modelklasse** de optie **Item (todo.Models)** .
   * Selecteer **een indelings pagina gebruiken** en voer *~/views/Shared/_Layout. cshtml*in.
   * Selecteer **Toevoegen**.

Nadat u deze stappen hebt uitgevoerd, sluit u alle *cshtml* -documenten in Visual Studio terwijl u deze weer gaven later weer weergeeft.

### <a name="initialize-services"></a>Services declareren en initialiseren

Eerst voegen we een klasse toe die de logica bevat voor het maken van verbinding met en het gebruik van Azure Cosmos DB. Voor deze zelf studie wordt deze logica ingekapseld in een klasse met de naam `CosmosDBService` en een interface met de naam `ICosmosDBService`. Deze service voert de ruwe bewerkingen uit. Ook worden de bewerkingen voor Feeding gelezen, zoals het weer geven van onvolledige items, het maken, bewerken en verwijderen van de items.

1. Klik in **Solution Explorer**met de rechter muisknop op uw project en selecteer > **nieuwe map** **toevoegen** . Geef de map *Services*een naam.

1. Klik met de rechter muisknop op de map **Services** en selecteer > **klasse** **toevoegen** . Noem de nieuwe klasse *CosmosDBService* en selecteer **toevoegen**.

1. Vervang de inhoud van *CosmosDBService.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Klik met de rechter muisknop op de map **Services** en selecteer > **klasse** **toevoegen** . Noem de nieuwe klasse *ICosmosDBService* en selecteer **toevoegen**.

1. Voeg de volgende code toe aan de klasse *ICosmosDBService* :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Open het *Startup.cs* -bestand in uw oplossing en vervang de `ConfigureServices` methode door:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    De code in deze stap initialiseert de client op basis van de configuratie als een singleton-exemplaar dat moet worden geïnjecteerd door middel [van afhankelijkheids injectie in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. Voeg binnen hetzelfde bestand de volgende methode toe **InitializeCosmosClientInstanceAsync**, die de configuratie leest en de-client initialiseert.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync":::

1. Definieer de configuratie in het bestand *appSettings. json* van het project, zoals wordt weer gegeven in het volgende code fragment:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a>Een controller toevoegen

1. Klik in **Solution Explorer**met de rechter muisknop op de map **Controllers** en selecteer > **controller** **toevoegen** .

1. Selecteer in **steigers toevoegen**de optie **MVC-controller-leeg** en selecteer **toevoegen**.

   ![MVC-controller selecteren-leeg in steigers toevoegen](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Geef een naam op voor de nieuwe *item controller*van de controller.

1. Vervang de inhoud van *ItemController.cs* door de volgende code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Het kenmerk **ValidateAntiForgeryToken** wordt hier gebruikt om deze toepassing te beschermen tegen aanvallen via aanvraagvervalsing op meerdere sites. Uw weer gaven werken ook met dit anti-vervalsing token. Zie voor [komen van CSRF-aanvallen (cross-site request vervalsing) in ASP.NET MVC-toepassing][Preventing Cross-Site Request Forgery]voor meer informatie en voor beelden. De broncode op [GitHub][GitHub] beschikt over de volledige implementatie.

We gebruiken ook het kenmerk **Bind** voor de methodeparameter om u te beschermen tegen over-postingaanvallen. Zie [zelf studie: een ruwe functionaliteit implementeren met de Entity Framework in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC]voor meer informatie.

## <a name="run-the-application"></a>Stap 5: de toepassing lokaal uitvoeren

Als u de toepassing wilt testen op uw lokale computer, gebruikt u de volgende stappen:

1. Druk in Visual Studio op F5 om de toepassing in de foutopsporingsmodus op te bouwen. De toepassing wordt opgebouwd en wordt er een browser gestart met het lege rasterpagina dat we eerder hebben gezien:

   ![Scherm afbeelding van de webtoepassing taken lijst die door deze zelf studie is gemaakt](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Als de toepassing in plaats daarvan op de start pagina wordt geopend, voegt u `/Item` toe aan de URL.

1. Selecteer de **nieuwe koppeling maken** en voeg waarden toe aan de velden **naam** en **Beschrijving** . Zorg ervoor dat het selectie vakje **voltooid is** uitgeschakeld. Als u deze optie selecteert, voegt de app het nieuwe item toe met de status voltooid. Het item wordt niet meer weer gegeven in de eerste lijst.

1. Selecteer **Maken**. De app stuurt u terug naar de **index** weergave en uw item wordt weer gegeven in de lijst. U kunt nog enkele items toevoegen aan uw **taken** lijst.

    ![Scherm afbeelding van de index weergave](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Selecteer **bewerken** naast een **item** in de lijst. De app opent de **bewerkings** weergave, waar u elke eigenschap van uw object kunt bijwerken, met inbegrip van de **voltooide** vlag. Als u **voltooid** selecteert en vervolgens **Opslaan**selecteert, wordt het **item** in de lijst weer gegeven als voltooid.

   ![Scherm opname van de weer gave index met het selectie vakje voltooid](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Controleer de status van de gegevens in de Azure Cosmos DB-service met behulp van [Cosmos Explorer](https://cosmos.azure.com) of de Data Explorer van de Azure Cosmos DB emulator.

1. Nadat u de app hebt getest, selecteert u CTRL + F5 om de fout opsporing van de app te stoppen. U kunt de app nu implementeren.

## <a name="deploy-the-application-to-azure"></a>Stap 6: de toepassing implementeren

Nu de volledige toepassing correct werkt met Azure Cosmos DB, kunt u de web-app implementeren in Azure App Service.  

1. Als u deze toepassing wilt publiceren, klikt u met de rechter muisknop op het project in **Solution Explorer** en selecteert u **publiceren**.

1. Selecteer in **Kies een publicatie doel** **app service**.

1. Als u een bestaand App Service profiel wilt gebruiken, kiest u **bestaande selecteren**en selecteert u vervolgens **publiceren**.

1. Selecteer een **abonnement**in **app service**. Sorteer met de filter **Weergave** op resourcegroep of resourcetype.

1. Zoek uw profiel en selecteer **OK**. Zoek vervolgens de vereiste Azure App Service en selecteer **OK**.

   ![Dialoogvenster App Service in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Een andere optie is het maken van een nieuw profiel:

1. Net als in de vorige procedure, klikt u met de rechter muisknop op het project in **Solution Explorer** en selecteert u **publiceren**.
  
1. Selecteer in **Kies een publicatie doel** **app service**.

1. Selecteer in **een publicatie doel kiezen de**optie **nieuwe maken** en selecteer **publiceren**.

1. Voer in **app service**de naam van uw web-app en het juiste abonnement, de resource groep en het hosting plan in en selecteer vervolgens **maken**.

   ![Dialoogvenster App-service maken in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

In een paar seconden wordt uw webtoepassing door Visual Studio gepubliceerd en wordt een browser geopend waarin u kunt zien hoe uw project wordt uitgevoerd in Azure.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een ASP.NET Core MVC-webtoepassing bouwt. Uw toepassing kan toegang krijgen tot gegevens die zijn opgeslagen in Azure Cosmos DB. U kunt nu door gaan met deze resources:

* [Partitionering in Azure Cosmos DB](./partitioning-overview.md)
* [Aan de slag met SQL-query's](./how-to-sql-query.md)
* [Meer informatie over het modelleren en partitioneren van gegevens in Azure Cosmos DB aan de hand van een praktijkvoorbeeld](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app

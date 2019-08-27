---
title: 'ASP.NET Core MVC-zelf studie voor Azure Cosmos DB: Webtoepassingsontwikkeling'
description: ASP.NET Core MVC-zelf studie voor het maken van een MVC-webtoepassing met behulp van Azure Cosmos DB. U slaat JSON-en Access-gegevens op uit een TODO-app die wordt gehost op Azure App Service-ASP NET core MVC-zelf studie stap voor stap.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 9824e1468604763834e37abe94290d68d81077ab
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020116"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Zelfstudie: Een ASP.NET Core MVC-webtoepassing met Azure Cosmos DB ontwikkelen met behulp van .NET SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


In deze zelfstudie wordt uitgelegd hoe u Azure Cosmos DB gebruikt om gegevens uit een ASP.NET MVC-toepassing die wordt gehost op Azure op te slaan en te openen. In deze zelf studie gebruikt u de .NET SDK v3. De volgende afbeelding toont de webpagina die u gaat bouwen met behulp van het voorbeeld in dit artikel:
 
![Scherm afbeelding van de taken lijst MVC-webtoepassing die door deze zelf studie is gemaakt: ASP NET core MVC-zelf studie stap voor stap](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Als u geen tijd hebt om de zelf studie te volt ooien, kunt u het volledige voorbeeld project downloaden van [github][GitHub].

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken
> * Een ASP.NET Core MVC-app maken
> * De app met Azure Cosmos DB verbinden 
> * CRUD-bewerkingen op de gegevens uitvoeren

> [!TIP]
> In deze zelf studie wordt ervan uitgegaan dat u een eerdere ervaring hebt met het gebruik van ASP.NET Core MVC en Azure App Service. Als u geen ervaring hebt met ASP.NET Core of de [vereiste hulpprogram ma's](#prerequisites), raden we u aan het volledige voorbeeld project van [github][GitHub]te downloaden, de vereiste NuGet-pakketten toe te voegen en deze uit te voeren. Zodra u het project gaat maken, kunt u dit artikel lezen voor meer informatie over de code in de context van het project.

## <a name="prerequisites"></a>Vereisten 

Voordat u de instructies in dit artikel uitvoert, moet u beschikken over de volgende resources:

* **Een actief Azure-account:** Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alle scherm opnamen in dit artikel zijn gemaakt met behulp van micro soft Visual Studio Community 2019. Als uw systeem is geconfigureerd met een andere versie, is het mogelijk dat de schermen en opties niet volledig overeenkomen. Als u echter aan de bovenstaande vereisten voldoet, moet deze oplossing werken.

## <a name="create-an-azure-cosmos-account"></a>Stap 1: Een Azure Cosmos-account maken

Begin met het maken van een Azure Cosmos-account. Als u al een SQL-API-account voor Azure Cosmos DB hebt of de Azure Cosmos DB-emulator gebruikt voor deze zelfstudie, kunt u direct doorgaan naar het gedeelte [Een nieuwe ASP.NET MVS-toepassing maken](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

In de volgende sectie maakt u een nieuwe ASP.NET Core MVC-toepassing. 

## <a name="create-a-new-mvc-application"></a>Stap 2: Een nieuwe ASP.NET Core MVC-toepassing maken

1. Ga in Visual Studio in het menu **Bestand** naar **Nieuw** en selecteer vervolgens **Project**. Het dialoogvenster **Nieuw project** wordt weergegeven.

2. In het venster **Nieuw project** gebruikt u het vak **zoeken naar sjablonen** om te zoeken naar ' Web ' en selecteert u vervolgens **ASP.net core webtoepassing**. 

   ![Nieuw project voor ASP.NET Core-webtoepassing maken](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Typ in het vak **Naam** de naam van het project. In deze zelfstudie wordt de naam 'todo' gebruikt. Als u ervoor kiest om iets anders dan deze naam te gebruiken, moet u, wanneer deze zelf studie over de naam ruimte TODO gaat, de opgegeven code voorbeelden aanpassen om te gebruiken wat u de naam van uw toepassing hebt. 

4. Selecteer **Bladeren** om naar de map te navigeren waar u het project wilt maken. Selecteer **Maken**. 

5. Het dialoog venster **een nieuwe ASP.net core webtoepassing maken** wordt weer gegeven. Selecteer in de lijst sjablonen de optie **Webtoepassing (model-view-controller)** .

6. Selecteer **maken** en laat Visual Studio de steiger rond de lege ASP.net core MVC-sjabloon. 

7. Zodra Visual Studio de standaard MVC-toepassing heeft gemaakt, beschikt u over een lege ASP.NET-toepassing die u lokaal kunt uitvoeren.

## <a name="add-nuget-packages"></a>Stap 3: Azure Cosmos DB NuGet-pakket aan het project toevoegen

Nu we de meeste van de ASP.NET Core MVC-Framework code hebben die we nodig hebben voor deze oplossing, voegen we de NuGet-pakketten toe die nodig zijn om verbinding te maken met Azure Cosmos DB.

1. De Azure Cosmos DB .NET SDK wordt verpakt en gedistribueerd als een NuGet-pakket. Als u het NuGet-pakket aan Visual Studio wilt toevoegen, gebruikt u NuGet-pakketbeheer in Visual Studio door in **Solution Explorer** met de rechtermuisknop op het project te klikken en vervolgens **NuGet-pakketten beheren** te selecteren.
   
   ![Scherm afbeelding van de opties met de rechter muisknop voor het webtoepassings project in Solution Explorer, waarbij NuGet-pakketten worden gemarkeerd.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Het dialoogvenster **NuGet-pakketten beheren** wordt weergegeven. Typ in het NuGet-vak **Bladeren** **Microsoft.Azure.Cosmos**. Installeer het pakket **micro soft. Azure. Cosmos** uit de resultaten. Het Azure Cosmos DB-pakket en de bijbehorende afhankelijkheden worden gedownload en geïnstalleerd. Selecteer **Ik ga akkoord** in het venster **licentie acceptatie** om de installatie te volt ooien.
   
   U kunt eventueel ook de console voor Pakketbeheer gebruiken om het NuGet-pakket te installeren. Hiervoor selecteert u in het menu **Extra** **NuGet Package Manager** (NuGet-pakketbeheer) en vervolgens **Package Manager Console** (Pakketbeheer-console). Typ achter de prompt de volgende opdracht:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. Nadat het pakket is geïnstalleerd, moet uw Visual Studio-project de bibliotheek verwijzing naar micro soft. Azure. Cosmos bevatten.
  
## <a name="set-up-the-mvc-application"></a>Stap 4: De ASP.NET Core MVC-toepassing instellen

U kunt nu de modellen, weergaven en controllers toevoegen aan deze MVC-toepassing:

* [Een model toevoegen](#add-a-model).
* [Een controller toevoegen](#add-a-controller).
* [Weergaven toevoegen](#add-views).

### <a name="add-a-model"></a> Een model toevoegen

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Modellen** en selecteer achtereenvolgens **Toevoegen** en **Klasse**. Het dialoogvenster **Nieuw item toevoegen** wordt weergegeven.

1. Noem de nieuwe klasse **item.cs** en selecteer **toevoegen**. 

1. Vervang vervolgens de code in klasse ' Item.cs ' door de volgende code:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   De gegevens die in Azure Cosmos DB zijn opgeslagen, worden doorgegeven via de kabel en opgeslagen als JSON. Als u wilt bepalen hoe uw objecten worden geserialiseerd/gedeserialiseerd door JSON.NET, kunt u het kenmerk **JsonProperty** gebruiken zoals wordt gedemonstreerd in de **item** klasse die u hebt gemaakt. U kunt niet alleen de indeling bepalen van de naam van de eigenschap die naar JSON gaat, maar u kunt ook de naam van uw .NET-eigenschappen wijzigen, net als bij de eigenschap **voltooid** . 

### <a name="add-views"></a>Weergaven toevoegen

Vervolgens gaat u de volgende drie weergaven maken: 

* [Een weergave voor een lijst met items toevoegen](#AddItemIndexView).
* [Een weergave voor nieuwe items toevoegen](#AddNewIndexView).
* [Een weergave voor het bewerken van items toevoegen](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Een weergave voor een lijst met items toevoegen

1. Vouw in **Solution Explorer** de map **Weergaven** uit en klik met de rechtermuisknop op de lege map **Item** die Visual Studio voor u heeft gemaakt toen u **ItemController** hebt toegevoegd. Klik vervolgens op **Toevoegen** en **Weergave**.
   
   ![Scherm afbeelding van Solution Explorer waarin de map item wordt weer gegeven die Visual Studio heeft gemaakt met de opdrachten weer gave toevoegen gemarkeerd](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. Werk in het dialoogvenster **Weergave toevoegen** de volgende waarden bij:
   
   * In het vak **Weergavenaam** typt u ***Index***.
   * Selecteer in het vak **Sjabloon** de optie ***Lijst***.
   * Selecteer in het vak **Modelklasse** de optie ***Item (todo.Models)***.
   * Typ in het veld voor de indelingspagina ***~/Views/Shared/_Layout.cshtml***.
     
   ![Scherm afbeelding van het dialoog venster weer gave toevoegen](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. Zodra u deze waarden hebt toegevoegd, klikt u op **Toevoegen** en wordt er een nieuwe sjabloonweergave in Visual Studio gemaakt. Zodra dit klaar is, wordt het nieuwe cshtml-bestand geopend. U kunt dit bestand in Visual Studio voorlopig sluiten, aangezien dit pas later aan bod komt.

#### <a name="AddNewIndexView"></a>Een weergave voor nieuwe items toevoegen

Maak een nieuwe weergave om items te maken, vergelijkbaar met hoe u een weergave hebt gemaakt voor het weergeven van items, door de volgende stappen te volgen:

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Item** en selecteer achtereenvolgens **Toevoegen** en **Weergave**.

1. Werk in het dialoogvenster **Weergave toevoegen** de volgende waarden bij:
   
   * Typ in het vak **Weergavenaam** ***Maken***.
   * Selecteer in het vak **Sjabloon** de optie ***Maken***.
   * Selecteer in het vak **Modelklasse** de optie ***Item (todo.Models)***.
   * Typ in het veld voor de indelingspagina ***~/Views/Shared/_Layout.cshtml***.
   * Selecteer **Toevoegen**.
   
#### <a name="AddEditIndexView"></a>Een weergave voor het bewerken van items toevoegen

En tot slot voegt u een weergave voor het bewerken van items toe met de volgende stappen:

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Item** en selecteer achtereenvolgens **Toevoegen** en **Weergave**.

1. Voer in het dialoogvenster **Weergave toevoegen** de volgende handelingen uit:
   
   * Typ in het vak **Weergavenaam** ***Bewerken***.
   * Selecteer in het vak **Sjabloon** de optie ***Bewerken***.
   * Selecteer in het vak **Modelklasse** de optie ***Item (todo.Models)***.
   * Typ in het veld voor de indelingspagina ***~/Views/Shared/_Layout.cshtml***.
   * Selecteer **Toevoegen**.

Zodra dit is gebeurd, sluit u alle cshtml-documenten in Visual Studio. U keert later naar deze weergaven terug.

### <a name="add-a-controller"></a>Een controller toevoegen

1. Klik in **Solution Explorer** met de rechtermuisknop op de map **Controllers** en selecteer achtereenvolgens **Toevoegen** en **Controller**. Het dialoogvenster **Add Scaffold** (Scaffold toevoegen) wordt weergegeven.

1. Selecteer **MVC-controller-leeg** en selecteer **toevoegen**.

   ![Scherm afbeelding van het dialoog venster basis toevoegen met de MVC-controller: de optie Empty is gemarkeerd](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Geef de nieuwe controller een naam, **item controller**en vervang de code in dat bestand door de volgende code:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   Het kenmerk **ValidateAntiForgeryToken** wordt hier gebruikt om deze toepassing te beschermen tegen aanvallen via aanvraagvervalsing op meerdere sites. Het volstaat echter niet om dit kenmerk alleen toe te voegen. Uw weergaven moeten ook samenwerken met dit anti-vervalsingstoken. Zie voor [komen van aanvraag vervalsing op meerdere sites][Preventing Cross-Site Request Forgery]voor meer informatie over het onderwerp en voor beelden van een juiste implementatie. De broncode op [GitHub][GitHub] beschikt over de volledige implementatie.

   We gebruiken ook het kenmerk **Bind** voor de methodeparameter om u te beschermen tegen over-postingaanvallen. Zie voor meer informatie [basis ruwe bewerkingen in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="connect-to-cosmosdb"></a>Stap 5: Verbinding maken met Azure Cosmos DB 

Nu de standaardwerkzaamheden voor MVC zijn voltooid, kunt u de code voor het verbinding maken met Azure Cosmos DB toevoegen en CRUD-bewerkingen uitvoeren. 

### <a name="perform-crud-operations"></a> CRUD-bewerkingen op de gegevens uitvoeren

Allereerst moet u een klasse toevoegen die de logica bevat voor de verbinding met en het gebruik van Azure Cosmos DB. Voor deze zelf studie wordt deze logica ingekapseld in een klasse `CosmosDBService` met de naam en een interface met de naam. `ICosmosDBService` Met deze service worden de ruwe en lees bewerkingen uitgevoerd, zoals het weer geven van onvolledige items, het maken, bewerken en verwijderen van de items. 

1. Maak in **Solution Explorer** een nieuwe map onder uw project met de naam **Services**.

1. Klik met de rechtermuisknop op de map **Services** en selecteer **Toevoegen** en **Klasse**. Noem de nieuwe klasse **CosmosDBService** en selecteer **toevoegen**.

1. Voeg de volgende code toe aan de klasse **CosmosDBService** en vervang de code in dat bestand door de volgende code:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Herhaal stap 2-3, maar deze keer, voor een klasse met de naam **ICosmosDBService**en voeg de volgende code toe:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. De vorige code ontvangt een `CosmosClient` als onderdeel van de constructor. Na ASP.NET Core pijp lijn moet u naar de **Startup.cs** van het project gaan en de client initialiseren op basis van de configuratie als een singleton-exemplaar dat moet worden geïnjecteerd via [afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). In de **ConfigureServices** -handler definieert u het volgende:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. In hetzelfde bestand definiëren we de **InitializeCosmosClientInstanceAsync**, waarmee de configuratie wordt gelezen en de client wordt geïnitialiseerd.

    [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)] 

1. De configuratie wordt gedefinieerd in het bestand **appSettings. json** van het project. Open het en voeg een sectie toe met de naam **CosmosDb**:

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Als u de toepassing nu uitvoert, wordt **CosmosDbService** door de pijp lijn van ASP.net core gemaakt en wordt er één exemplaar onderhouden als Singleton. Wanneer **item controller** wordt gebruikt voor het verwerken van aanvragen aan de client zijde, ontvangt deze één exemplaar en kunt u het gebruiken om ruwe bewerkingen uit te voeren.

Als u dit project nu bouwt en uitvoert, ziet u nu iets dat er ongeveer als volgt uitziet:

![Scherm afbeelding van de webtoepassing TODO list gemaakt door deze data base-zelf studie](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Stap 6: De toepassing lokaal uitvoeren

Volg deze stappen als u de toepassing wilt testen op een lokale machine:

1. Druk in Visual Studio op F5 om de toepassing in de foutopsporingsmodus op te bouwen. De toepassing wordt opgebouwd en wordt er een browser gestart met het lege rasterpagina dat we eerder hebben gezien:
   
   ![Scherm afbeelding van de webtoepassing taken lijst die door deze zelf studie is gemaakt](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Klik op de koppeling **Nieuw maken** en voeg waarden toe aan de velden **Naam** en **Beschrijving**. Schakel het selectievakje **Voltooid** niet in, anders wordt het nieuwe item toegevoegd met een onvoltooide status en wordt het niet weergegeven in de aanvankelijke lijst.
   
3. Als u op **Maken** klikt, keert u terug naar de weergave **Index** en wordt uw item in de lijst weergegeven. U kunt eventueel nog enkele items aan uw takenlijst toevoegen.

    ![Scherm afbeelding van de index weergave](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Klik op **Bewerken** naast een **Item** in de lijst, zodat u wordt omgeleid naar de weergave **Bewerken**. Hier kunt u de eigenschappen van uw object bijwerken, inclusief de vlag **Voltooid**. Als u de vlag **complete** markeert en op **Opslaan**klikt, wordt het **item** in de lijst weer gegeven als voltooid.
   
   ![Scherm opname van de weer gave index met het selectie vakje voltooid](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. U kunt op elk moment de status van de gegevens in de Azure Cosmos DB-service controleren met behulp van [Cosmos Explorer](https://cosmos.azure.com) of de Data Explorer van de Azure Cosmos DB emulator.

6. Zodra u de app hebt getest, drukt u op Ctrl + F5 om de foutopsporing voor de app te stoppen. U kunt de app nu implementeren.

## <a name="deploy-the-application-to-azure"></a>Stap 7: De toepassing implementeren 
Nu de volledige toepassing correct werkt met Azure Cosmos DB, kunt u de web-app implementeren in Azure App Service.  

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren** om de toepassing te publiceren.
   
2. Selecteer in het dialoog venster **publiceren** de optie **app service**en selecteer vervolgens **nieuwe maken** om een app service profiel te maken, of kies **bestaande selecteren** om een bestaand profiel te gebruiken.

3. Als u al een Azure App Service-profiel hebt, selecteert u **Abonnement** in de vervolgkeuzelijst. Sorteer met de filter **Weergave** op resourcegroep of resourcetype. Zoek vervolgens de vereiste Azure App Service en selecteer **OK**.
   
   ![Dialoogvenster App Service in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Klik op **Nieuw profiel maken** in het dialoogvenster **Publiceren** om een nieuw Azure App Service-profiel te maken. Voer in het dialoogvenster **App Service maken** de naam van uw webtoepassing en het abonnement, de resourcegroep en het App Service-abonnement in en selecteer vervolgens **Maken**.

   ![Dialoogvenster App-service maken in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

In een paar seconden wordt uw webtoepassing door Visual Studio gepubliceerd en wordt een browser geopend waarin u kunt zien hoe uw project wordt uitgevoerd in Azure.

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u een ASP.NET Core MVC-webtoepassing kunt maken die toegang heeft tot gegevens die zijn opgeslagen in Azure Cosmos DB. U kunt nu verdergaan met het volgende artikel:

* [Meer informatie over het partitioneren van uw gegevens in Azure Cosmos DB](./partitioning-overview.md)
* [Meer informatie over het uitvoeren van geavanceerdere query's in Azure Cosmos DB](./how-to-sql-query.md)
* [Meer informatie over het model leren van uw gegevens in een geavanceerdere scenario](./how-to-model-partition-example.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app

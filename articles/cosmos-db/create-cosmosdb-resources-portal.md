---
title: Snelstart - Azure Cosmos DB-resources maken vanuit de Azure-portal
description: In deze snelstart ziet u hoe u een Azure Cosmos-database, container en items maakt met behulp van de Azure-portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521077"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Snelstart: een Azure Cosmos-account, database, container en items maken vanuit de Azure-portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U Azure Cosmos DB gebruiken om snel sleutel-/waardedatabases, documentdatabases en grafiekdatabases te maken en op te vragen, die allemaal profiteren van de wereldwijde distributie- en horizontale schaalmogelijkheden in de kern van Azure Cosmos DB. 

Deze quickstart laat zien hoe u de Azure-portal gebruiken om een Azure Cosmos DB [SQL API-account](sql-api-introduction.md) te maken, een documentdatabase en -container te maken en gegevens aan de container toe te voegen. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement of een gratis Proefversie van Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Ga naar de [Azure-portal](https://portal.azure.com/) om een Azure Cosmos DB-account te maken. Zoek en selecteer **Azure Cosmos DB**.

   ![Het deelvenster Databases in Azure Portal](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Selecteer **Toevoegen**.
1. Voer op de pagina **Azure Cosmos DB-account maken** de basisinstellingen in voor het nieuwe Azure Cosmos-account. 

    |Instelling|Waarde|Beschrijving |
    |---|---|---|
    |Abonnement|Abonnementsnaam|Selecteer het Azure-abonnement dat u wilt gebruiken voor dit Azure Cosmos-account. |
    |Resourcegroep|Naam van de resourcegroep|Selecteer een resourcegroep of selecteer **Nieuw maken**en voer vervolgens een unieke naam in voor de nieuwe resourcegroep. |
    |Accountnaam|Een unieke naam|Voer een naam in om uw Azure Cosmos-account te identificeren. Omdat *documents.azure.com* is toegevoegd aan de naam die u verstrekt om uw URI te maken, gebruikt u een unieke naam.<br><br>De naam kan alleen kleine letters, cijfers en het koppelteken (-) bevatten. Het moet tussen de 3-31 tekens in lengte.|
    |API|Het type account dat u wilt maken|Selecteer **Core (SQL)** om een documentdatabase en query te maken met SQL-syntaxis. <br><br>De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf API's: Core (SQL) en MongoDB voor documentgegevens, Gremlin voor grafiekgegevens, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>[Meer informatie over de SQL-API](introduction.md).|
    |Gratis laagkorting toepassen|Solliciteren of niet toepassen|Met azure cosmos DB gratis laag, krijgt u de eerste 400 RU / s en 5 GB opslag gratis in een account. Meer informatie over [gratis laag](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die het dichtst bij uw gebruikers staat om hen de snelste toegang tot de gegevens te geven.|
    |Accounttype|Productie of niet-productie|Selecteer **Productie** als het account wordt gebruikt voor een productiewerkbelasting. Selecteer **Niet-productie** als het account wordt gebruikt voor niet-productie, bijvoorbeeld ontwikkeling, testen, QA of fasering. Dit is een Azure-brontaginstelling die de Portal-ervaring afstemt, maar geen invloed heeft op het onderliggende Azure Cosmos DB-account. U deze waarde op elk gewenst moment wijzigen.|


> [!NOTE]
> U maximaal één gratis Azure Cosmos DB-account per Azure-abonnement hebben en u moet zich aanmelden bij het maken van het account. Als u de optie om de gratis laagkorting toe te passen niet ziet, betekent dit dat een ander account in het abonnement al is ingeschakeld met een gratis laag.
   
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Selecteer **Controleren + maken**. U de secties **Netwerk** en **tags** overslaan.

1. Controleer de accountinstellingen en selecteer **Vervolgens Maken**. Het maken van het account duurt enkele minuten. Wacht tot de portalpagina wordt weergegeven **Uw implementatie is voltooid.** 

    ![Het deelvenster Meldingen in Azure Portal](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Selecteer **Ga naar resource om** naar de azure cosmos DB-accountpagina te gaan. 

    ![De pagina Azure Cosmos DB-account](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Een database en een container toevoegen 

U de Gegevensverkenner in de Azure-portal gebruiken om een database en container te maken. 

1.  Selecteer **Gegevensverkenner** op de linkernavigatie op uw Azure Cosmos DB-accountpagina en selecteer **Vervolgens Nieuwe container**. 
    
    Mogelijk moet u naar rechts schuiven om het venster **Container toevoegen** te zien.
    
    ![Azure Portal Data Explorer, deelvenster Container toevoegen](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  Voer in het **containervenster Toevoegen** de instellingen voor de nieuwe container in.
    
    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Takenlijst|Voer *ToDoList* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Controleer de optie **Voorhet overslaginrichten** van de database, hiermee u de overslag die is ingericht met de database delen over alle containers in de database. Deze optie helpt ook bij kostenbesparingen. |
    |**Doorvoer**|400|Laat de doorvoer op 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *Items in* als de naam voor uw nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| In het voorbeeld dat in dit artikel wordt beschreven, wordt */categorie* als partitiesleutel gebruikt.|

    
    Voeg geen **unieke toetsen** toe voor dit voorbeeld. Met unieke sleutels u een laag gegevensintegriteit aan de database toevoegen door ervoor te zorgen dat een of meer waarden per partitiesleutel uniek zijn. Zie [Unieke sleutels in Azure Cosmos DB](unique-keys.md)voor meer informatie.
    
1.  Selecteer **OK**. In de gegevensverkenner worden de nieuwe database en de container weergegeven die u hebt gemaakt.

## <a name="add-data-to-your-database"></a>Gegevens toevoegen aan uw database

Voeg gegevens toe aan uw nieuwe database met Behulp van Data Explorer.

1. Vouw in **Gegevensverkenner**de **ToDoList-database** uit en vouw de container **Items** uit. Selecteer vervolgens **Objecten**en selecteer Vervolgens **Nieuw object**. 
   
   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Voeg de volgende structuur toe aan het document aan de rechterkant van het deelvenster **Documenten:**

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Selecteer **Opslaan**.
   
   ![Kopiëren in json-gegevens en selecteer Opslaan in Gegevensverkenner in de Azure-portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Selecteer **opnieuw Nieuw document** en maak en `id`sla een ander document op met een unieke en alle andere eigenschappen en waarden die u wilt. Uw documenten kunnen elke structuur hebben, omdat Azure Cosmos DB geen schema oplegt aan uw gegevens.

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Als u alleen de database wilt verwijderen en in de toekomst het Azure Cosmos-account wilt gebruiken, u de database met de volgende stappen verwijderen:

* Naar uw Azure Cosmos-account.
* Open **Gegevensverkenner**, klik met de rechtermuisknop op de database die u wilt verwijderen en selecteer **Database verwijderen**.
* Voer de naam Database-id/database in om de verwijderingsbewerking te bevestigen. 

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een database en container maakt met behulp van de Data Explorer. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)

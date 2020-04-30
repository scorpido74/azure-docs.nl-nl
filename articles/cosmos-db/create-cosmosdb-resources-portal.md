---
title: 'Quick Start: Azure Cosmos DB resources maken op basis van de Azure Portal'
description: Deze Quick Start laat zien hoe u een Azure Cosmos-data base,-container en-items kunt maken met behulp van de Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80521077"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Snelstartgids: een Azure Cosmos-account,-Data Base,-container en-items maken op basis van de Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt Azure Cosmos DB gebruiken om snel key/value-data bases, document databases en Graph-data bases te maken en op te vragen, die allemaal profiteren van de mogelijkheden van globale distributie en horizontale schaal op basis van Azure Cosmos DB. 

In deze Quick start ziet u hoe u de Azure Portal kunt gebruiken om een Azure Cosmos DB [SQL-API](sql-api-introduction.md) -account te maken, een document database en-container te maken en gegevens toe te voegen aan de container. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement of gratis Azure Cosmos DB proef account
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Ga naar de [Azure Portal](https://portal.azure.com/) om een Azure Cosmos DB-account te maken. Zoek en selecteer **Azure Cosmos DB**.

   ![Het deelvenster Databases in Azure Portal](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Selecteer **Toevoegen**.
1. Voer op de pagina **Azure Cosmos DB account maken** de basis instellingen in voor het nieuwe Azure Cosmos-account. 

    |Instelling|Waarde|Beschrijving |
    |---|---|---|
    |Abonnement|Abonnementsnaam|Selecteer het Azure-abonnement dat u wilt gebruiken voor dit Azure Cosmos-account. |
    |Resourcegroep|Naam van de resourcegroep|Selecteer een resource groep of selecteer **nieuwe maken**en voer vervolgens een unieke naam in voor de nieuwe resource groep. |
    |Accountnaam|Een unieke naam|Voer een naam in om uw Azure Cosmos-account aan te duiden. Omdat *Documents.Azure.com* wordt toegevoegd aan de naam die u opgeeft om uw URI te maken, gebruikt u een unieke naam.<br><br>De naam mag alleen kleine letters, cijfers en het koppel teken (-) bevatten. De waarde moet tussen de 3-31 tekens lang zijn.|
    |API|Het type account dat moet worden gemaakt|Selecteer **kern (SQL)** om een document database en-query te maken met behulp van de SQL-syntaxis. <br><br>De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf Api's: core (SQL) en MongoDB voor document gegevens, Gremlin voor grafiek gegevens, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>[Meer informatie over de SQL-API](introduction.md).|
    |Gratis laag korting Toep assen|Toep assen of niet Toep assen|Met Azure Cosmos DB gratis laag ontvangt u de eerste 400 RU/s en 5 GB aan opslag ruimte gratis in een account. Meer informatie over de [gratis laag](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt om hen de snelste toegang tot de gegevens te geven.|
    |Account type|Productie of niet-productie|Selecteer **productie** als het account wordt gebruikt voor een productiewerk belasting. Selecteer **niet-productie** als het account wordt gebruikt voor niet-productie, zoals ontwikkeling, testen, QA of fase ring. Dit is een Azure-resource code-instelling die de portal-ervaring afstemt, maar geen invloed heeft op het onderliggende Azure Cosmos DB-account. U kunt deze waarde op elk gewenst moment wijzigen.|


> [!NOTE]
> U kunt Maxi maal één gratis laag Azure Cosmos DB account per Azure-abonnement hebben en u moet zich aanmelden wanneer u het account maakt. Als u de optie voor het Toep assen van de gratis laag korting niet ziet, betekent dit dat er al een ander account in het abonnement is ingeschakeld met de laag gratis.
   
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Selecteer **controleren + maken**. U kunt de secties **netwerk** en **Tags** overs Laan.

1. Controleer de account instellingen en selecteer vervolgens **maken**. Het duurt enkele minuten om het account te maken. Wacht tot de portal pagina **uw implementatie is voltooid**. 

    ![Het deelvenster Meldingen in Azure Portal](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Selecteer **Ga naar resource** om naar de pagina Azure Cosmos DB-account te gaan. 

    ![De pagina Azure Cosmos DB-account](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Een Data Base en een container toevoegen 

U kunt de Data Explorer in de Azure Portal gebruiken om een Data Base en container te maken. 

1.  Selecteer **Data Explorer** in de linkernavigatiebalk op de pagina Azure Cosmos DB-account en selecteer vervolgens **nieuwe container**. 
    
    Mogelijk moet u naar rechts schuiven om het venster **container toevoegen** weer te geven.
    
    ![Azure Portal Data Explorer, deelvenster Container toevoegen](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  Voer in het deel venster **container toevoegen** de instellingen voor de nieuwe container in.
    
    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Takenlijst|Voer *ToDoList* in als de naam voor de nieuwe data base. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **doorvoer database inrichten** in, zodat u de door Voer die is ingericht voor de data base, kunt delen in alle containers in de data base. Deze optie helpt ook bij het besparen van kosten. |
    |**Doorvoer**|400|De door Voer bij 400 aanvraag eenheden per seconde (RU/s) behouden. U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *items* in als de naam voor de nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.|
    |**Partitiesleutel**| /category| Het voor beeld dat in dit artikel wordt beschreven, maakt gebruik van *Category* als de partitie sleutel.|

    
    Voeg geen **unieke sleutels** toe voor dit voor beeld. Met unieke sleutels kunt u een laag van gegevens integriteit toevoegen aan de data base door de uniekheid van een of meer waarden per partitie sleutel te garanderen. Zie [unieke sleutels in azure Cosmos DB](unique-keys.md)voor meer informatie.
    
1.  Selecteer **OK**. In de Data Explorer worden de nieuwe data base en de door u gemaakte container weer gegeven.

## <a name="add-data-to-your-database"></a>Gegevens toevoegen aan uw data base

Gegevens toevoegen aan uw nieuwe Data Base met behulp van Data Explorer.

1. In **Data Explorer**vouwt u de **ToDoList** -data base uit en vouwt u de container **items** uit. Selecteer vervolgens **items**en selecteer vervolgens **Nieuw item**. 
   
   ![Nieuwe documenten maken in Data Explorer in Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Voeg de volgende structuur toe aan het document aan de rechter kant van het deel venster **documenten** :

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
   
   ![Kopieer in JSON-gegevens en selecteer Opslaan in Data Explorer in het Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Selecteer **Nieuw document** opnieuw en maak en sla een ander document op met een `id`unieke en andere eigenschappen en waarden die u wilt. Uw documenten kunnen een structuur hebben, omdat Azure Cosmos DB geen schema voor uw gegevens oplegt.

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Als u alleen de Data Base wilt verwijderen en het Azure Cosmos-account in de toekomst wilt gebruiken, kunt u de data base verwijderen door de volgende stappen uit te voeren:

* U hebt een Azure Cosmos-account ontvangen.
* Open **Data Explorer**, klik met de rechter muisknop op de data base die u wilt verwijderen en selecteer **Data Base verwijderen**.
* Voer de data base-ID/database naam in om de Verwijder bewerking te bevestigen. 

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB account maakt, hoe u een Data Base en container maakt met behulp van de Data Explorer. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)

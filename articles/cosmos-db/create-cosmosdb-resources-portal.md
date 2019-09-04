---
title: Maak een Azure Cosmos-account,-container en-items met de Azure Portal.
description: Maak een Azure Cosmos-account,-container en-items met de Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/01/2019
ms.openlocfilehash: e0a9f4fa6ca5ff7447d2ffaef3eab2f3c54fdeae
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241265"
---
# <a name="quickstart-create-an-azure-cosmos-account-container-and-items-with-the-azure-portal"></a>Quickstart: Een Azure Cosmos-account,-container en-items maken met de Azure Portal

> [!div class="op_single_selector"]
> * [Azure-portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. U kunt Azure Cosmos DB gebruiken om snel key/value-data bases, document databases en Graph-data bases te maken en op te vragen, die allemaal profiteren van de mogelijkheden van globale distributie en horizontale schaal op basis van Azure Cosmos DB. 

In deze Quick start ziet u hoe u de Azure Portal kunt gebruiken om een Azure Cosmos DB [SQL-API](sql-api-introduction.md) -account te maken, een document database en-container te maken en gegevens toe te voegen aan de container. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement of gratis Azure Cosmos DB proef account
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Een Data Base en een container toevoegen 

U kunt de Data Explorer in de Azure Portal gebruiken om een Data Base en container te maken. 

1.  Selecteer **Data Explorer** in de linkernavigatiebalk op de pagina Azure Cosmos DB-account en selecteer vervolgens **nieuwe container**. 
    
    Mogelijk moet u naar rechts schuiven om het venster **container toevoegen** weer te geven.
    
    ![Het deel venster Azure Portal Data Explorer, container toevoegen](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  Voer in het deel venster **container toevoegen** de instellingen voor de nieuwe container in.
    
    |Instelling|Voorgestelde waarde|Description
    |---|---|---|
    |**Database-id**|Takenlijst|Voer *ToDoList* in als de naam voor de nieuwe data base. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **doorvoer database inrichten** in, zodat u de door Voer die is ingericht voor de data base, kunt delen in alle containers in de data base. Deze optie helpt ook bij het besparen van kosten. |
    |**Doorvoer**|400|De door Voer bij 400 aanvraag eenheden per seconde (RU/s) behouden. U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *items* in als de naam voor de nieuwe container. Container-Id's hebben dezelfde teken vereisten als database namen.|
    |**Partitie sleutel**| /category| Het voor beeld dat in dit artikel wordt beschreven, maakt gebruik van *Category* als de partitie sleutel.|

    
    Voeg geen **unieke sleutels** toe voor dit voor beeld. Met unieke sleutels kunt u een laag van gegevens integriteit toevoegen aan de data base door de uniekheid van een of meer waarden per partitie sleutel te garanderen. Zie [unieke sleutels in azure Cosmos DB](unique-keys.md)voor meer informatie.
    
1.  Selecteer **OK**. In de Data Explorer worden de nieuwe data base en de door u gemaakte container weer gegeven.

## <a name="add-data-to-your-database"></a>Gegevens toevoegen aan uw data base

Gegevens toevoegen aan uw nieuwe Data Base met behulp van Data Explorer.

1. In **Data Explorer**vouwt u de **ToDoList** -data base uit en vouwt u de container **items** uit. Selecteer vervolgens **items**en selecteer vervolgens **Nieuw item**. 
   
   ![Nieuwe documenten maken in Data Explorer in de Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
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

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB account maakt, hoe u een Data Base en container maakt met behulp van de Data Explorer. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
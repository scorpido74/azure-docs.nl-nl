---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ecf45a692f83689ed56c03bec13f291781508474
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061697"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links de optie **een resource maken**.
   
   ![Een resource maken in de Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Selecteer op de pagina **Nieuw** de optie **data bases** > **Azure Cosmos DB**.
   
   ![Het deelvenster Databases in Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Voer op de pagina **Azure Cosmos DB account maken** de instellingen voor het nieuwe Azure Cosmos DB-account in. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|Nieuwe maken<br><br>Voer dezelfde naam in als de account naam|Selecteer **Nieuw maken**. Voer vervolgens een nieuwe naam voor de resource groep voor uw account in. Gebruik de naam van uw Azure Cosmos DB-account voor eenvoud. 
    Accountnaam|Voer een unieke naam in|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren. Uw account-URI wordt *Mongo.Cosmos.Azure.com* toegevoegd aan uw unieke account naam.<br><br>De account naam mag alleen kleine letters, cijfers en afbreek streepjes (-) gebruiken en moet tussen de 3 en 31 tekens lang zijn.
    API|Azure Cosmos DB voor Mongo DB API|De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf Api's: core (SQL) voor document databases, Gremlin voor Graph-data bases, Azure Cosmos DB voor Mongo DB API voor document databases, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>Selecteer **Azure Cosmos DB voor MONGO DB API** . in deze Snelstartgids maakt u een verzameling die werkt met MongoDb.<br><br>Meer [informatie over Azure Cosmos DB voor MongoDb-API](../articles/cosmos-db/mongodb-introduction.md).|
    Locatie|Selecteer de regio het dichtst in de buurt van uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.|

    Selecteer **Beoordelen en maken**. U kunt de secties **Netwerk** en **Tags** overslaan. 

    ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Het duurt enkele minuten om het account te maken. Wacht tot de Portal de **Gefeliciteerd weergeeft. Uw Azure Cosmos DB voor de Mongo DB API-account is gereed** .

    ![Het deelvenster Meldingen in Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)

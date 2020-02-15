---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 2001d0621a340cbdb04c0bb5eea1166ce8b88eab
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212744"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links de optie **een resource maken**.
   
   ![Een resource maken in de Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Selecteer op de pagina **Nieuw** de optie **data bases** > **Azure Cosmos DB**.
   
   ![Het deelvenster Databases in Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Voer op de pagina **Azure Cosmos DB account maken** de instellingen voor het nieuwe Azure Cosmos DB-account in. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|**Nieuwe maken**en vervolgens account naam|Selecteer **Nieuw maken**. Voer vervolgens een nieuwe naam voor de resource groep voor uw account in. Gebruik de naam van uw Azure Cosmos DB-account voor eenvoud. 
    Accountnaam|Een unieke naam|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren.<br><br>De account naam mag alleen kleine letters, cijfers en afbreek streepjes (-) gebruiken en moet tussen de 3 en 31 tekens lang zijn.
    API|Tabel|De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf Api's: core (SQL) voor document databases, Gremlin voor Graph-data bases, MongoDB voor document databases, Azure Table en Cassandra. U moet voor elke API een afzonderlijk account maken. <br><br>Selecteer **Azure Table**, omdat u in deze Snelstartgids een tabel maakt die werkt met de Table-API. <br><br>Meer [informatie over de Table-API](../articles/cosmos-db/table-introduction.md).|
    Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.

    U kunt de schrijf opties voor **geo-redundantie** en **meerdere regio's** bij **uitschakelen** laten staan om extra kosten te voor komen en de secties **netwerk** en **Tags** over te slaan.

5. Selecteer **Beoordelen en maken**. Nadat de validatie is voltooid, selecteert u **maken** om het account te maken. 
 
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Het duurt enkele minuten om het account te maken. Er wordt een bericht weer gegeven waarin staat dat **uw implementatie zich bevindt**. Wacht tot de implementatie is voltooid en selecteer vervolgens **Ga naar resource**.

    ![Het deel venster Azure Portal meldingen](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)


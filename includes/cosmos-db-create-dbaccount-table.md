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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212744"
---
1. Meld u in een nieuw browservenster aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer in het linkermenu De optie **Een resource maken**.
   
   ![Een resource maken in de Azure-portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png)
   
3. Selecteer op de pagina **Nieuw** **de** > optie**Databases Azure Cosmos DB**.
   
   ![Het deelvenster Databases in Azure Portal](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)
   
3. Voer op de pagina **Azure Cosmos DB-account** maken de instellingen in voor het nieuwe Azure Cosmos DB-account. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|**Nieuwe,** dan accountnaam maken|Selecteer **Nieuw maken**. Voer vervolgens een nieuwe naam van de resourcegroep in voor uw account. Gebruik voor de eenvoud dezelfde naam als de naam van uw Azure Cosmos DB-account. 
    Accountnaam|Een unieke naam|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren.<br><br>De accountnaam mag alleen kleine letters, cijfers en koppeltekens (-) gebruiken en moet tussen de 3 en 31 tekens lang zijn.
    API|Tabel|De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf API's: Core (SQL) voor documentdatabases, Gremlin voor grafiekdatabases, MongoDB voor documentdatabases, Azure Table en Cassandra. U moet voor elke API een apart account aanmaken. <br><br>Selecteer **Azure Table,** omdat u in deze snelstart een tabel maakt die werkt met de tabel-API. <br><br>[Meer informatie over de tabel-API](../articles/cosmos-db/table-introduction.md).|
    Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.

    U de opties **Voor georedundantie** en schrijfbewerking in **meerdere regio's** bij **Uitschakelen** verlaten om extra kosten te voorkomen en de secties **Netwerk** en **tags** overslaan.

5. Selecteer **Beoordelen en maken**. Nadat de validatie is voltooid, selecteert **u Maken** om het account te maken. 
 
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. Het maken van het account duurt enkele minuten. U ziet een bericht waarin staat dat **uw implementatie aan de gang is.** Wacht tot de implementatie is voltooid en selecteer **Ga naar resource**.

    ![Het deelvenster Azure-portalmeldingen](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)


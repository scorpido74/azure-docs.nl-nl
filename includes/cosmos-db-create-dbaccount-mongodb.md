---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.custom: include file
ms.openlocfilehash: 0acc989f558c5de4e8f52ba2c77f2842f8d5d70c
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75751755"
---
1. Meld u in een nieuw venster aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in het menu links de optie **een resource maken**, selecteer **data bases**en selecteer vervolgens onder **Azure Cosmos DB** **maken**.
   
   ![Scherm afbeelding van het Azure Portal, markeren van meer services en Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Voer op de pagina **Azure Cosmos DB-account maken** de instellingen in voor het nieuwe Azure Cosmos DB-account. 
 
    Instelling|Waarde|Beschrijving
    ---|---|---
    Abonnement|Uw abonnement|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos DB-account wilt gebruiken. 
    Resourcegroep|Nieuwe maken<br><br>Voer de unieke naam in die u in ID hebt opgegeven|Selecteer **Nieuw maken**. Voer daarna een nieuwe resourcegroepnaam in voor het account. Gebruik voor het gemak dezelfde naam als uw id. 
    Accountnaam|Voer een unieke naam in|Voer een unieke naam in om uw Azure Cosmos DB-account te identificeren. Gebruik een unieke id omdat *documents.azure.com* is toegevoegd aan de id die u hebt opgegeven om uw URI te maken.<br><br>De id mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De id moet tussen de 3 en 31 tekens lang zijn.
    API|Azure Cosmos DB-API voor MongoDB|De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf Api's: core (SQL) voor document databases, Gremlin voor Graph-data bases, Azure Cosmos DB API MongoDB voor document databases, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>Selecteer **MongoDB** omdat u in deze quickstart een tabel maakt die geschikt is voor de MongoDB.|
    Locatie|Selecteer de regio het dichtst in de buurt van uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.

    Selecteer **Beoordelen en maken**. U kunt de secties **Netwerk** en **Tags** overslaan. 

    ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Het duurt enkele minuten om het account te maken. Wacht tot de Portal de **Gefeliciteerd weergeeft. Uw Cosmos-account met wire-protocol compatibiliteit voor de pagina MongoDB is gereed** .

    ![Het deelvenster Meldingen in Azure Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)

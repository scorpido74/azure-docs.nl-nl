---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: bfce10b0c9c55012c3ba2fb25121b28157f203b8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672093"
---
1. Ga naar de [Azure-portal](https://portal.azure.com/) om een Azure Cosmos DB-account aan te maken. Zoek en selecteer **Azure Cosmos DB**.

   ![Het deelvenster Databases in Azure Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Selecteer **Toevoegen**.
1. Voer op de pagina **Azure Cosmos DB-account maken** de basisinstellingen in voor het nieuwe Azure Cosmos-account. 

    |Instelling|Waarde|Beschrijving |
    |---|---|---|
    |Abonnement|Abonnementsnaam|Selecteer het Azure-abonnement dat u voor dit Azure Cosmos-account wilt gebruiken. |
    |Resourcegroep|Naam van de resourcegroep|Selecteer een resourcegroep of selecteer **Nieuwe maken** en voer vervolgens een unieke naam in voor de nieuwe resourcegroep. |
    |Accountnaam|Een unieke naam|Voer een naam in om uw Azure Cosmos-account te identificeren. Gebruik een unieke naam omdat *documents.azure.com* is toegevoegd aan de naam die u hebt opgegeven om uw URI te maken.<br><br>De naam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet tussen de 3 en 31 tekens lang zijn.|
    |API|Het type account dat moet worden gemaakt|Selecteer **Core(SQL)** om een documentdatabase en query's aan te maken met SQL-syntaxis. <br><br>De API bepaalt het type te maken account. Azure Cosmos DB heeft vijf API's: Core (SQL) en MongoDB voor documentgegevens, Gremlin voor grafiekgegevens, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>[Meer informatie over de SQL-API](../articles/cosmos-db/documentdb-introduction.md).|
    |Korting voor gratis lagen toepassen|Toepassen of niet toepassen|Met de gratis laag van Azure Cosmos DB ontvangt u de eerste 400 RU/s en 5 GB aan opslagruimte gratis in een account. Meer informatie over de [gratis laag](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt, zodat ze de snelst mogelijke toegang tot de gegevens hebben.|
    |Accounttype|Productie of niet-productie|Selecteer **Productie** als het account wordt gebruikt voor een productie-werkbelasting. Selecteer **Niet-productie** als het account wordt gebruikt voor niet-productie, zoals ontwikkeling, testing, QA of fasering. Dit is een resourcetag-instelling in Azure die de portal-ervaring afstemt, maar geen invloed heeft op het onderliggende Azure Cosmos DB-account. U kunt deze waarde op elk gewenst moment wijzigen.|


> [!NOTE]
> U kunt per Azure-abonnement maximaal één gratis laag voor het Azure Cosmos DB-account hebben, en u moet zich aanmelden wanneer u het account maakt. Als u de optie voor het toepassen van de korting voor gratis lagen niet ziet, betekent dit dat er al een ander account in het abonnement is ingeschakeld met een gratis laag.
   
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Selecteer **Controleren + maken**. U kunt de secties **Netwerk** en **Tags** overslaan.

1. Controleer de accountinstellingen en selecteer vervolgens **Maken**. Het duurt een paar minuten om het account te maken. Wacht tot de portal-pagina **Uw implementatie is voltooid** weergeeft. 

    ![Het deelvenster Meldingen in Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selecteer **Ga naar resource** om naar de Azure Cosmos DB-accountpagina te gaan. 

    ![De Azure Cosmos DB-accountpagina](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)

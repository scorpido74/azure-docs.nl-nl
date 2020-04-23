---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/10/2020
ms.custom: include file
ms.openlocfilehash: 4c6e45eaa816499c7e5969e302a036368bea0937
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81422088"
---
1. Ga naar de [Azure Portal](https://portal.azure.com/) om een Azure Cosmos DB-account te maken. Kies op uw start pagina **een resource maken** in het deel venster **Azure-Services** .

   ![Een resource maken](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-resource-1.png)

1. Zoek en selecteer **Azure Cosmos DB**.

   ![De vervolg keuzelijst Azure Portal resources](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-updated-1.png)

1. Selecteer **Maken**.

   ![Azure Cosmos DB resource maken](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account-1.png)

1. Voer op de pagina **Azure Cosmos DB account maken** de basis instellingen in voor het nieuwe Azure Cosmos-account. 

    |Instelling|Waarde|Beschrijving |
    |---|---|---|
    |Abonnement|Abonnementsnaam|Selecteer het Azure-abonnement dat u wilt gebruiken voor dit Azure Cosmos-account. |
    |Resourcegroep|Naam van de resourcegroep|Selecteer een resource groep of selecteer **nieuwe maken**en voer vervolgens een unieke naam in voor de nieuwe resource groep. |
    |Accountnaam|Een unieke naam|Voer een naam in om uw Azure Cosmos-account aan te duiden. Omdat *Documents.Azure.com* wordt toegevoegd aan de naam die u opgeeft om uw URI te maken, gebruikt u een unieke naam.<br><br>De naam mag alleen kleine letters, cijfers en het koppel teken (-) bevatten. De waarde moet tussen de 3-31 tekens lang zijn.|
    |API|Het type account dat moet worden gemaakt|Selecteer **kern (SQL)** om een document database en-query te maken met behulp van de SQL-syntaxis. <br><br>De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf Api's: core (SQL) en MongoDB voor document gegevens, Gremlin voor grafiek gegevens, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>[Meer informatie over de SQL-API](../articles/cosmos-db/documentdb-introduction.md).|
    |Gratis laag korting Toep assen|Toep assen of niet Toep assen|Met Azure Cosmos DB gratis laag ontvangt u de eerste 400 RU/s en 5 GB aan opslag ruimte gratis in een account. Meer informatie over de [gratis laag](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die zich het dichtst bij uw gebruikers bevindt om hen de snelste toegang tot de gegevens te geven.|
    |Account type|Productie of niet-productie|Selecteer **productie** als het account wordt gebruikt voor een productiewerk belasting. Selecteer **niet-productie** als het account wordt gebruikt voor niet-productie, zoals ontwikkeling, testen, QA of fase ring. Dit is een Azure-resource code-instelling die de portal-ervaring afstemt, maar geen invloed heeft op het onderliggende Azure Cosmos DB-account. U kunt deze waarde op elk gewenst moment wijzigen.|


    > [!NOTE]
    > U kunt Maxi maal één gratis laag Azure Cosmos DB account per Azure-abonnement hebben en u moet zich aanmelden wanneer u het account maakt. Als u de optie voor het Toep assen van de gratis laag korting niet ziet, betekent dit dat er al een ander account in het abonnement is ingeschakeld met de laag gratis.
   
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Selecteer **controleren + maken**. U kunt de secties **netwerk** en **Tags** overs Laan.

1. Controleer de account instellingen en selecteer vervolgens **maken**. Het duurt enkele minuten om het account te maken. Wacht tot de portal pagina **uw implementatie is voltooid**. 

    ![Het deelvenster Meldingen in Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selecteer **Ga naar resource** om naar de pagina Azure Cosmos DB-account te gaan. 

    ![De pagina Azure Cosmos DB-account](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)

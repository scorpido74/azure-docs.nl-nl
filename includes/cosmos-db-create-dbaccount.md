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
1. Ga naar de [Azure-portal](https://portal.azure.com/) om een Azure Cosmos DB-account te maken. Kies op uw startpagina **Een resource maken** in het **azure-servicespaneel.**

   ![Een resource maken](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-resource-1.png)

1. Zoek en selecteer **Azure Cosmos DB**.

   ![De vervolgkeuzelijst Azure-portalbronnen](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-updated-1.png)

1. Selecteer **Maken**.

   ![Azure Cosmos DB-bron maken](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account-1.png)

1. Voer op de pagina **Azure Cosmos DB-account maken** de basisinstellingen in voor het nieuwe Azure Cosmos-account. 

    |Instelling|Waarde|Beschrijving |
    |---|---|---|
    |Abonnement|Abonnementsnaam|Selecteer het Azure-abonnement dat u wilt gebruiken voor dit Azure Cosmos-account. |
    |Resourcegroep|Naam van de resourcegroep|Selecteer een resourcegroep of selecteer **Nieuw maken**en voer vervolgens een unieke naam in voor de nieuwe resourcegroep. |
    |Accountnaam|Een unieke naam|Voer een naam in om uw Azure Cosmos-account te identificeren. Omdat *documents.azure.com* is toegevoegd aan de naam die u verstrekt om uw URI te maken, gebruikt u een unieke naam.<br><br>De naam kan alleen kleine letters, cijfers en het koppelteken (-) bevatten. Het moet tussen de 3-31 tekens in lengte.|
    |API|Het type account dat u wilt maken|Selecteer **Core (SQL)** om een documentdatabase en query te maken met SQL-syntaxis. <br><br>De API bepaalt het type te maken account. Azure Cosmos DB biedt vijf API's: Core (SQL) en MongoDB voor documentgegevens, Gremlin voor grafiekgegevens, Azure Table en Cassandra. Op dit moment moet u voor elke API een afzonderlijk account maken. <br><br>[Meer informatie over de SQL-API](../articles/cosmos-db/documentdb-introduction.md).|
    |Gratis laagkorting toepassen|Solliciteren of niet toepassen|Met azure cosmos DB gratis laag, krijgt u de eerste 400 RU / s en 5 GB opslag gratis in een account. Meer informatie over [gratis laag](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Locatie|De regio het dichtst bij uw gebruikers|Selecteer een geografische locatie waar u het Azure Cosmos DB-account wilt hosten. Gebruik de locatie die het dichtst bij uw gebruikers staat om hen de snelste toegang tot de gegevens te geven.|
    |Accounttype|Productie of niet-productie|Selecteer **Productie** als het account wordt gebruikt voor een productiewerkbelasting. Selecteer **Niet-productie** als het account wordt gebruikt voor niet-productie, bijvoorbeeld ontwikkeling, testen, QA of fasering. Dit is een Azure-brontaginstelling die de Portal-ervaring afstemt, maar geen invloed heeft op het onderliggende Azure Cosmos DB-account. U deze waarde op elk gewenst moment wijzigen.|


    > [!NOTE]
    > U maximaal één gratis Azure Cosmos DB-account per Azure-abonnement hebben en u moet zich aanmelden bij het maken van het account. Als u de optie om de gratis laagkorting toe te passen niet ziet, betekent dit dat een ander account in het abonnement al is ingeschakeld met een gratis laag.
   
   ![De pagina Nieuw account voor Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png)

1. Selecteer **Controleren + maken**. U de secties **Netwerk** en **tags** overslaan.

1. Controleer de accountinstellingen en selecteer **Vervolgens Maken**. Het maken van het account duurt enkele minuten. Wacht tot de portalpagina wordt weergegeven **Uw implementatie is voltooid.** 

    ![Het deelvenster Meldingen in Azure Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Selecteer **Ga naar resource om** naar de azure cosmos DB-accountpagina te gaan. 

    ![De pagina Azure Cosmos DB-account](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)

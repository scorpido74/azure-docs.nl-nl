---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 408fb2c40e645d9a8b10f1e04d282e134c5489a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80501957"
---
U kunt query's in Data Explorer gebruiken om uw gegevens op te halen en te filteren.

1. Controleer de standaard query `SELECT * FROM c`boven aan het tabblad **items** in Data Explorer. Met deze query worden alle documenten opgehaald en weer gegeven uit de container die is gesorteerd op ID. 
   
   ![De standaardquery in Data Explorer is 'SELECT * FROM c'](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Als u de query wilt wijzigen, selecteert u **filter bewerken**, vervangt u `ORDER BY c._ts DESC`de standaard query door en selecteert u vervolgens **filter Toep assen**.
   
   ![Wijzig de standaardquery door ORDER BY c._ts DESC toe te voegen en te klikken op Filter toepassen](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Met de gewijzigde query worden de documenten in aflopende volg orde weer gegeven op basis van hun tijds tempel. nu wordt uw tweede document als eerste weer gegeven. 
   
   ![De query is gewijzigd in ORDER BY c. _ts DESC en klik op filter Toep assen](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Als u bekend bent met SQL-syntaxis, kunt u alle ondersteunde [SQL-query's](../articles/cosmos-db/sql-api-sql-query.md) invoeren in het vak query predicaat. U kunt Data Explorer ook gebruiken voor het maken van opgeslagen procedures, Udf's en triggers voor bedrijfs logica aan de server zijde. 

Data Explorer biedt eenvoudige Azure Portal toegang tot alle ingebouwde programmatische functies voor gegevens toegang die beschikbaar zijn in de Api's. U kunt de portal ook gebruiken om de door voer te schalen, sleutels en verbindings reeksen op te halen en metrische gegevens en Sla's voor uw Azure Cosmos DB-account te controleren. 


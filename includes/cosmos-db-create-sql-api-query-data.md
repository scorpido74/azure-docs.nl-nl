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
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927381"
---
U query's in Gegevensverkenner gebruiken om uw gegevens op te halen en te filteren.

1. Controleer boven aan het tabblad **Items** in Gegevensverkenner de standaardquery `SELECT * FROM c`. Met deze query worden alle documenten in de verzameling opgehaald en weergegeven in id-volgorde. 
   
   ![De standaardquery in Data Explorer is 'SELECT * FROM c'](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Als u de query wilt wijzigen, selecteert `ORDER BY c._ts DESC`u Filter **bewerken,** vervangt u de standaardquery door en selecteert u Filter **toepassen**.
   
   ![Wijzig de standaardquery door ORDER BY c._ts DESC toe te voegen en te klikken op Filter toepassen](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   De gewijzigde query geeft de documenten in aflopende volgorde weer op basis van hun tijdstempel, dus nu wordt uw tweede document eerst weergegeven. 
   
   ![Query gewijzigd in ORDER BY c._ts DESC en op Filter toepassen](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Als u bekend bent met sql-syntaxis, u alle ondersteunde [SQL-query's](../articles/cosmos-db/sql-api-sql-query.md) invoeren in het querypredicaatvak. U Gegevensverkenner ook gebruiken om opgeslagen procedures, UDF's en triggers te maken voor bedrijfslogica aan de serverzijde. 

Data Explorer biedt eenvoudige Azure-portaltoegang tot alle ingebouwde programmatische gegevenstoegangsfuncties die beschikbaar zijn in de API's. U gebruikt de portal ook om de doorvoer te schalen, sleutels en verbindingstekenreeksen op te halen en statistieken en SLA's voor uw Azure Cosmos DB-account te bekijken. 


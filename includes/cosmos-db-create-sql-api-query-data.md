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
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115201"
---
U kunt query's in Data Explorer gebruiken om uw gegevens op te halen en te filteren.

1. Controleer bovenaan het tabblad **Items** in Data Explorer de standaardquery `SELECT * FROM c`. Met deze query worden alle documenten opgehaald uit de container en weergegeven op volgorde van id. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Standaardquery in Data Explorer is SELECT * FROM c":::
   
1. Als u de query wilt wijzigen, selecteert u **Filter bewerken**, vervangt u de standaardquery door `ORDER BY c._ts DESC` en selecteert u **Filter toepassen**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Standaardquery in Data Explorer is SELECT * FROM c":::

   De gewijzigde query sorteert de documenten in aflopende volgorde op basis van hun tijdstempel. Uw tweede document wordt nu dus als eerste weergegeven. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Standaardquery in Data Explorer is SELECT * FROM c":::

Als u bekend bent met SQL-syntaxis, kunt u een van de ondersteunde [SQL-query's](../articles/cosmos-db/sql-api-sql-query.md) in het vak Querypredicaat invoeren. U kunt Data Explorer ook gebruiken voor het maken van opgeslagen procedures, UDF's en triggers om bedrijfslogica aan de serverzijde uit te voeren. 

Data Explorer biedt eenvoudige toegang via Azure Portal tot alle ingebouwde programmatische datatoegangsfuncties die in de API's beschikbaar zijn. U gebruikt de portal ook om de doorvoer te schalen, sleutels en verbindingsreeksen op te halen en metrische gegevens en SLA's voor uw Azure Cosmos DB-account te bekijken. 


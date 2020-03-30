---
title: Azure Cosmos DB Explorer gebruiken om uw gegevens te beheren
description: Azure Cosmos DB explorer is een zelfstandige webinterface waarmee u de gegevens die zijn opgeslagen in Azure Cosmos DB bekijken en beheren.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096816"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Werken met gegevens in Azure Cosmos Explorer 

Azure Cosmos DB explorer is een zelfstandige webinterface waarmee u de gegevens die zijn opgeslagen in Azure Cosmos DB bekijken en beheren. Azure Cosmos DB explorer is gelijk aan het bestaande tabblad **Data Explorer** dat beschikbaar is in Azure-portal wanneer u een Azure Cosmos DB-account maakt. De belangrijkste voordelen van Azure Cosmos DB explorer ten opzichte van de bestaande Data explorer zijn:

* U hebt een volledig scherm onroerend goed om uw gegevens te bekijken, query's uit te voeren, opgeslagen procedures, triggers, en bekijk hun resultaten.  

* U tijdelijke of permanente lees- of lees-schrijftoegang bieden tot uw databaseaccount en de bijbehorende verzamelingen aan andere gebruikers die geen toegang hebben tot de Azure-portal of -abonnement.  

* U de queryresultaten delen met andere gebruikers die geen toegang hebben tot de Azure-portal of -abonnement.  

## <a name="access-azure-cosmos-db-explorer"></a>Toegang tot Azure Cosmos DB explorer

1. Meld u aan bij [Azure Portal](https://portal.azure.com/). 

2. Zoek en navigeer vanuit **alle bronnen**naar uw Azure Cosmos DB-account, selecteer Sleutels en kopieer de tekenreeks **primaire verbinding**.  

3. Ga https://cosmos.azure.com/naar, plak de verbindingstekenreeks en selecteer **Verbinden**. Door de verbindingstekenreeks te gebruiken, hebt u zonder tijdslimieten toegang tot de Azure Cosmos DB-verkenner.  

   Als u andere gebruikers tijdelijk toegang wilt geven tot uw Azure Cosmos DB-account, u dit doen met behulp van de URL's voor lees- en leestoegang. 

4. Open het **blade van Data Explorer** en selecteer Volledig scherm **openen**. In het pop-updialoogvenster u twee toegangs-URL's bekijken : **Lezen** en **lezen**. Met deze URL's u uw Azure Cosmos DB-account tijdelijk delen met andere gebruikers. De toegang tot het account verloopt binnen 24 uur waarna u opnieuw verbinding maken via een nieuwe toegangs-URL of de verbindingstekenreeks. 

   **Lezen-schrijven** - Wanneer u de URL voor lezen schrijven deelt met andere gebruikers, kunnen ze de databases, verzamelingen, query's en andere bronnen die aan dat specifieke account zijn gekoppeld, bekijken en wijzigen.

   **Lees** - Wanneer u de alleen-lezen URL deelt met andere gebruikers, kunnen ze de databases, verzamelingen, query's en andere bronnen bekijken die aan dat specifieke account zijn gekoppeld. Als u bijvoorbeeld de resultaten van een query wilt delen met uw teamleden die geen toegang hebben tot de Azure-portal of uw Azure Cosmos DB-account, u deze URL opgeven.

   Kies het type toegang waarmee u het account wilt openen en klik op **Openen**. Nadat u de verkenner hebt geopend, is de ervaring hetzelfde als met het tabblad Gegevensverkenner in Azure-portal.   

   ![Azure Cosmos DB explorer openen](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Bekende problemen

Momenteel wordt de **Open Full Screen-ervaring** waarmee u tijdelijke lees- of leestoegang delen, nog niet ondersteund voor Azure Cosmos DB Gremlin- en Table API-accounts. U uw Gremlin- en Table API-accounts nog steeds bekijken door de verbindingstekenreeks door te geven aan Azure Cosmos DB Explorer. 

Momenteel wordt het bekijken van documenten die een UUID bevatten, momenteel niet ondersteund in Data Explorer. Dit heeft geen invloed op het laden van verzamelingen, alleen het bekijken van afzonderlijke documenten of query's die deze documenten bevatten. Om deze documenten te bekijken en te beheren, moeten gebruikers het hulpprogramma blijven gebruiken dat oorspronkelijk werd gebruikt om deze documenten te maken.

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u aan de slag met Azure Cosmos DB explorer om uw gegevens te beheren, u vervolgens:

* Begin met het definiëren van [query's](sql-api-query-reference.md) met SQL-syntaxis en voer [de programmering aan de serverzijde](stored-procedures-triggers-udfs.md) uit met behulp van opgeslagen procedures, UDF's, triggers. 

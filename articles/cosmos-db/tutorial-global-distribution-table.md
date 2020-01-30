---
title: Zelfstudie voor het instellen van wereldwijde distributie met Azure Cosmos DB met behulp van de Table-API
description: Meer informatie over de werking van globale distributie in Azure Cosmos DB Table-API accounts en het configureren van de voorkeurs lijst met regio's
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: 148e17edbb8be566db611216f444fedad514e638
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76770584"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Wereldwijde distributie met Azure Cosmos DB instellen met behulp van de Table-API

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie configureren met behulp van de [Table-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Verbinding maken met een voorkeursregio met behulp van de Table-API

Om te profiteren van [wereldwijde distributie](distribute-data-globally.md), kunnen clienttoepassingen de geordende voorkeurslijst met regio's opgeven die moet worden gebruikt voor het uitvoeren van documentbewerkingen. Dit kan worden gedaan door het instellen van de eigenschap [TableConnectionPolicy.PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet). De Azure Cosmos DB Table-API-SDK kiest het beste eindpunt om mee te communiceren op basis van de accountconfiguratie, de huidige regionale beschikbaarheid en de opgegeven voorkeurslijst.

De PreferredLocations moeten een door komma's gescheiden lijst bevatten met voorkeurslocaties (multihoming) voor leesbewerkingen. Elke clientinstantie kan een subset van deze regio's opgeven in de gewenste volgorde voor leesbewerkingen met een lage latentie. De naam van de regio's moet overeenkomen met hun [weergavenaam](https://msdn.microsoft.com/library/azure/gg441293.aspx), bijvoorbeeld `West US`.

Alle leesbewerkingen worden verzonden naar de eerst beschikbare regio in de lijst PreferredLocations. Als de aanvraag mislukt, gaat de client naar de volgende regio in de lijst, enzovoort.

De SDK probeert de regio's te lezen die zijn opgegeven in PreferredLocations. Dus als bijvoorbeeld het databaseaccount in drie regio's beschikbaar is, maar de client alleen twee regio's waarnaar niet kan worden geschreven opgeeft als PreferredLocations, worden er geen leesbewerkingen verwerkt vanuit de schrijfregio, zelfs in geval van een failover.

De SDK verzendt alle schrijfbewerkingen automatisch naar de huidige schrijfregio.

Als de eigenschap PreferredLocations niet is ingesteld, worden alle aanvragen verwerkt vanuit de huidige schrijfregio.

En daarmee is deze zelfstudie voltooid. Informatie over het beheren van de consistentie van uw wereldwijd gerepliceerde account kunt u vinden in [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md). En voor meer informatie over hoe wereldwijde databasereplicatie werkt in Azure Cosmos DB, gaat u naar [Gegevens wereldwijd distribueren met Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie met Azure Cosmos DB instellen met behulp van de Table-API’s


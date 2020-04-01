---
title: Zelfstudie voor het instellen van wereldwijde distributie met Azure Cosmos DB met behulp van de Table-API
description: Meer informatie over hoe globale distributie werkt in Azure Cosmos DB Table API-accounts en hoe u de voorkeurslijst met regio's configureert
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900176"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Wereldwijde distributie met Azure Cosmos DB instellen met behulp van de Table-API

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie configureren met behulp van de [Table-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Verbinding maken met een voorkeursregio met behulp van de Table-API

Om te profiteren van de [globale distributie,](distribute-data-globally.md)moeten clienttoepassingen de huidige locatie opgeven waar hun toepassing wordt uitgevoerd. Dit wordt gedaan `CosmosExecutorConfiguration.CurrentRegion` door het instellen van de eigenschap. De `CurrentRegion` eigenschap moet één locatie bevatten. Elke clientinstantie kan zijn eigen regio opgeven voor lezen met een lage latentie. De regio moet worden benoemd met behulp van hun [weergavenamen](https://msdn.microsoft.com/library/azure/gg441293.aspx) zoals "West US". 

De Azure Cosmos DB Table API SDK SDK kiest automatisch het beste eindpunt om mee te communiceren op basis van de accountconfiguratie en de huidige regionale beschikbaarheid. Het geeft prioriteit aan de dichtstbijzijnde regio om klanten een betere latentie te bieden. Nadat u de `CurrentRegion` huidige eigenschap hebt ingesteld, worden lees- en schrijfverzoeken als volgt gericht:

* **Lees verzoeken:** Alle leesverzoeken worden verzonden `CurrentRegion`naar de geconfigureerde . Op basis van de nabijheid selecteert de SDK automatisch een fallback geo-gerepliceerde regio voor hoge beschikbaarheid.

* **Schrijf aanvragen:** De SDK stuurt automatisch alle schrijfaanvragen naar het huidige schrijfgebied. In een multi master-account zal de huidige regio de schrijfverzoeken ook dienen. Op basis van de nabijheid selecteert de SDK automatisch een fallback geo-gerepliceerde regio voor hoge beschikbaarheid.

Als u de `CurrentRegion` eigenschap niet opgeeft, gebruikt de SDK het huidige schrijfgebied voor alle bewerkingen.

Als een Azure Cosmos-account zich bijvoorbeeld in de regio's West-VS en 'Oost-VS' bevindt. Als "West US" is het schrijven regio en de aanvraag is aanwezig in "Oost-VS". Als de eigenschap CurrentRegion niet is geconfigureerd, worden alle lees- en schrijfverzoeken altijd gericht naar de regio 'West-VS'. Als de eigenschap CurrentRegion is geconfigureerd, worden alle leesaanvragen weergegeven vanuit de regio 'Oost-VS'.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie met Azure Cosmos DB instellen met behulp van de Table-API’s


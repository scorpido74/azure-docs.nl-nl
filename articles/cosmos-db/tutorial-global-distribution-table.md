---
title: Zelfstudie voor het instellen van wereldwijde distributie met Azure Cosmos DB met behulp van de Table-API
description: Meer informatie over de werking van globale distributie in Azure Cosmos DB Table-API accounts en het configureren van de voorkeurs lijst met regio's
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900176"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Wereldwijde distributie met Azure Cosmos DB instellen met behulp van de Table-API

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie configureren met behulp van de [Table-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Verbinding maken met een voorkeursregio met behulp van de Table-API

Om gebruik te kunnen maken van de [wereld wijde distributie](distribute-data-globally.md), moeten client toepassingen de huidige locatie opgeven waar de toepassing wordt uitgevoerd. Dit doet u door de eigenschap `CosmosExecutorConfiguration.CurrentRegion` in te stellen. De eigenschap `CurrentRegion` moet één locatie bevatten. Elk client exemplaar kan hun eigen regio opgeven voor lees bewerkingen met lage latentie. De naam van de regio moet worden opgegeven met behulp van hun [weergave namen](https://msdn.microsoft.com/library/azure/gg441293.aspx) , zoals ' vs-West '. 

De Azure Cosmos DB Table-API SDK kiest automatisch het beste eind punt om mee te communiceren op basis van de account configuratie en huidige regionale Beschik baarheid. Dit geeft een prioriteit aan de dichtstbijzijnde regio voor betere latentie aan clients. Nadat u de huidige eigenschap `CurrentRegion` hebt ingesteld, worden lees-en schrijf aanvragen als volgt omgeleid:

* **Lees aanvragen:** Alle Lees aanvragen worden verzonden naar de geconfigureerde `CurrentRegion`. Op basis van de nabijheid selecteert de SDK automatisch een geo-gerepliceerde terugval regio voor hoge Beschik baarheid.

* **Schrijf aanvragen:** De SDK verzendt alle schrijf aanvragen automatisch naar de huidige schrijf regio. In een multi master-account zullen de huidige regio ook de schrijf aanvragen verwerken. Op basis van de nabijheid selecteert de SDK automatisch een geo-gerepliceerde terugval regio voor hoge Beschik baarheid.

Als u de eigenschap `CurrentRegion` niet opgeeft, gebruikt de SDK de huidige schrijf regio voor alle bewerkingen.

Bijvoorbeeld, als een Azure Cosmos-account zich in regio's ' vs-West ' en ' vs-Oost ' bevindt. Als ' vs-West ' de schrijf regio is en de toepassing aanwezig is in ' vs-Oost '. Als de eigenschap CurrentRegion niet is geconfigureerd, worden alle Lees-en schrijf aanvragen altijd doorgestuurd naar de regio vs-West. Als de eigenschap CurrentRegion is geconfigureerd, worden alle Lees aanvragen verzonden vanuit de regio VS-Oost.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie met Azure Cosmos DB instellen met behulp van de Table-API’s


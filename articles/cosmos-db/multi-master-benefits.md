---
title: Azure Cosmos DB voor delen van meerdere masters
description: Krijg inzicht in de voor delen van multi-master in Azure Cosmos DB, vergelijking van latentie-en SLA-vereisten in één en meerdere schrijf locaties.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872006"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Leer de voor delen van meerdere masters in Azure Cosmos DB

Cosmos DB account operators moeten de juiste globale distributie configuratie kiezen om ervoor te zorgen dat de latentie, Beschik baarheid en RTO-vereisten voor hun toepassingen. Azure Cosmos-accounts die zijn geconfigureerd met meerdere schrijf locaties, bieden aanzienlijke voor delen ten opzichte van accounts met één schrijf locatie, waaronder, 99,999% SLA voor schrijf beschikbaarheid, < 10 MS-SLA voor schrijf latentie in het 99e percentiel en RTO = 0 in een regionale nood geval.

## <a name="comparison-of-features"></a>Vergelijking van functies

|Toepassings vereiste|Meerdere schrijf locaties|Enkele schrijf locatie|Opmerking|
|---|---|---|---|
|SLA voor schrijf latentie van < 10 MS op P99|**Ja**|Nee|Accounts met één schrijf locatie hebben extra netwerk latentie in meerdere regio's voor elke schrijf bewerking.|
|SLA voor de lees latentie van < 10 MS op P99|**Ja**|Ja| |
|SLA van 99,999% schrijven|**Ja**|Nee|Accounts met één schrijf locatie garantie voor de SLA van 99,99%|
|RTO = 0|**Ja**|Nee|Geen tijd meer voor schrijf bewerkingen in het geval van regionale rampen. Accounts met één schrijf locatie hebben RTO van 15 minuten.|

## <a name="next-steps"></a>Volgende stappen

Als u EnableMultipleWriteLocations in uw Azure Cosmos-account wilt uitschakelen, kunt u [een ondersteunings ticket openen](https://azure.microsoft.com/support/create-ticket/).

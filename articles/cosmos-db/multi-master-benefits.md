---
title: Azure Cosmos DB multi-master voordelen
description: Inzicht in de voordelen van multi-master in Azure Cosmos DB, vergelijking van latentie en SLA-vereisten in enkele en meerdere schrijflocaties.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872006"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Meerwetende voordelen in Azure Cosmos DB

Cosmos DB-accountbeheerders moeten de juiste globale distributieconfiguratie kiezen om de latentie-, beschikbaarheids- en RTO-vereisten voor hun toepassingen te garanderen. Azure Cosmos-accounts die zijn geconfigureerd met meerdere schrijflocaties bieden aanzienlijke voordelen ten opzichte van accounts met één schrijflocatie, waaronder SLA, 99,999%, schrijfbeschikbaarheid SLA, <10 ms schrijflatentie SLA bij het 99e percentiel en RTO = 0 in een regionale ramp.

## <a name="comparison-of-features"></a>Vergelijking van functies

|Toepassingsvereiste|Meerdere schrijflocaties|Locatie voor één schrijflocatie|Opmerking|
|---|---|---|---|
|Schrijf latentie SLA van <10ms op P99|**Ja**|Nee|Accounts met één schrijflocatie hebben extra netwerklatentie voor beide regio's voor elke schrijflocatie.|
|Lees latentie SLA van <10ms op P99|**Ja**|Ja| |
|Schrijf SLA van 99,999%|**Ja**|Nee|Accounts met single write locatie garantie SLA van 99,99%|
|RTO = 0|**Ja**|Nee|Nul down time voor schrijft in het geval van regionale rampen. Accounts met enkele schrijflocatie hebben RTO van 15 min.|

## <a name="next-steps"></a>Volgende stappen

Als u enablemultiplewritelocaties nog steeds wilt uitschakelen in uw Azure Cosmos-account, opent u [een ondersteuningsticket](https://azure.microsoft.com/support/create-ticket/).

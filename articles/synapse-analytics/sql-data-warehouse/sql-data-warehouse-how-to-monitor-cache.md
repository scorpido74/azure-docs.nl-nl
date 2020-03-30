---
title: Uw Gen2-cache optimaliseren
description: Meer informatie over het bewaken van uw Gen2-cache met behulp van de Azure-portal.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350411"
---
# <a name="how-to-monitor-the-gen2-cache"></a>De Gen2-cache controleren

In dit artikel wordt beschreven hoe u trage queryprestaties controleren en oplossen door te bepalen of uw werkbelasting optimaal gebruik maakt van de Gen2-cache.

De Gen2-opslagarchitectuur laagt automatisch uw meest opgevraagde kolomarchiefsegmenten in een cache die zich bevindt op NVMe-gebaseerde SSD's die zijn ontworpen voor Gen2-gegevensmagazijnen. Betere prestaties worden gerealiseerd wanneer uw query's segmenten ophalen die zich in de cache bevinden.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met de Azure-portal

U Azure Monitor gebruiken om Gen2-cachestatistieken weer te geven om problemen met query's op te lossen. Ga eerst naar de Azure-portal en klik op **Monitor,** **Statistieken** en **+ Selecteer een bereik:**

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Gebruik de zoek- en vervolgkeuzebalken om uw gegevensmagazijn te vinden. Selecteer vervolgens toepassen.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

De belangrijkste statistieken voor het oplossen van problemen met de Gen2-cache zijn **cachehitpercentage** en **cachepercentage.** Selecteer **Het percentage cachedruk** en gebruik de **knop Metrische toevoegen** om het gebruikte **percentage cache**toe te voegen. 

![Cachestatistieken](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Cachehit- en gebruikt percentage

De onderstaande matrix beschrijft scenario's op basis van de waarden van de cachestatistieken:

|                                | **Hoog slagpercentage met cache** | **Laag slagingspercentage cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Gebruikt hoog cachepercentage** |          Scenario 1           |          Scenario 2          |
| **Gebruikt percentage met lage cache**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** U gebruikt uw cache optimaal. [Problemen oplossen](sql-data-warehouse-manage-monitor.md) met andere gebieden die uw query's mogelijk vertragen.

**Scenario 2:** Uw huidige werkgegevensset past niet in de cache, wat een laag slagingspercentage in de cache veroorzaakt als gevolg van fysieke reads. Overweeg uw prestatieniveau op te schalen en uw werkbelasting opnieuw uit te voeren om de cache te vullen.

**Scenario 3:** Het is waarschijnlijk dat uw query traag wordt uitgevoerd vanwege redenen die niets met de cache te maken hebben. [Problemen oplossen](sql-data-warehouse-manage-monitor.md) met andere gebieden die uw query's mogelijk vertragen. U ook overwegen [uw instantie te verkleinen](sql-data-warehouse-manage-monitor.md) om de cachegrootte te verkleinen om kosten te besparen. 

**Scenario 4:** Je had een koude cache die de reden zou kunnen zijn waarom uw query traag was. Overweeg uw query opnieuw uit te voeren omdat uw werkgegevensset nu in de cache moet staan. 

> [!IMPORTANT]
> Als het percentage cachehit of het gebruikte percentage cache niet wordt bijgewerkt nadat uw werkbelasting opnieuw is uitgevoerd, kan uw werkset al in het geheugen staan. Alleen geclusterde kolomarchieftabellen worden in de cache opgeslagen.

## <a name="next-steps"></a>Volgende stappen
Zie [Queryuitvoering controleren](sql-data-warehouse-manage-monitor.md#monitor-query-execution)voor meer informatie over algemene queryprestaties.

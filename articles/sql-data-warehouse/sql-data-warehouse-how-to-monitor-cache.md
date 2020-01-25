---
title: Optimaliseer uw Gen2-cache
description: Meer informatie over het bewaken van uw Gen2-cache met behulp van de Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721214"
---
# <a name="how-to-monitor-the-gen2-cache"></a>De Gen2-cache bewaken
De Gen2-opslag architectuur bevat automatisch uw meest query's in de opgeslagen column Store-segmenten in een cache op NVMe gebaseerd Ssd's ontworpen voor Gen2 data warehouses. Betere prestaties worden gerealiseerd wanneer uw query's segmenten ophalen die zich in de cache bevinden. In dit artikel wordt beschreven hoe u langzame query prestaties bewaakt en oplost door te bepalen of uw werk belasting optimaal gebruikmaakt van de Gen2-cache.  
## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met behulp van de Azure Portal
U kunt Azure Monitor gebruiken om de Gen2-cache gegevens weer te geven om de prestaties van query's op te lossen. Ga eerst naar de Azure Portal en klik op monitor:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Selecteer de knop metrische gegevens en vul het **abonnement**, de **resource** **groep**, het **resource type**en de **resource naam** van uw data warehouse in.

De belangrijkste metrische gegevens voor het oplossen van problemen met de Gen2-cache zijn het **percentage cache treffers** en het **percentage cache gebruik**. Configureer de Azure metrische grafiek om deze twee metrische gegevens weer te geven.

![Metrische cache gegevens](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Aantal cache treffers en gebruikte percentages
In de volgende matrix worden scenario's beschreven op basis van de waarden van de cache-metrische gegevens:

|                                | **Percentage treffers in hoge cache** | **Percentage treffers voor lage cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Percentage gebruikt hoog cache geheugen** |          Scenario 1           |          Scenario 2          |
| **Percentage beperkt cache gebruik**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** U kunt uw cache optimaal gebruiken. [Los](sql-data-warehouse-manage-monitor.md) andere gebieden op die uw query's mogelijk vertragen.

**Scenario 2:** De huidige werkset voor werk gegevens past niet in de cache, waardoor er een percentage van lage cache treffers wordt veroorzaakt door fysieke Lees bewerkingen. U kunt het prestatie niveau omhoog schalen en de werk belasting opnieuw uitvoeren om de cache te vullen.

**Scenario 3:** Waarschijnlijk wordt uw query traag als gevolg van redenen die niet aan de cache zijn gerelateerd. [Los](sql-data-warehouse-manage-monitor.md) andere gebieden op die uw query's mogelijk vertragen. U kunt ook overwegen [uw exemplaar omlaag te schalen](sql-data-warehouse-manage-monitor.md) om uw cache grootte te beperken om kosten te besparen. 

**Scenario 4:** U had een koude cache. Dit kan de reden zijn waarom uw query traag is. Overweeg de query opnieuw uit te voeren, omdat uw werk gegevensset nu in de cache moet worden opgeslagen. 

> [!IMPORTANT]
> Als het percentage van de cache treffer of het cache gebruik niet wordt bijgewerkt na het opnieuw uitvoeren van de workload, kan de werkset al in het geheugen worden geplaatst. Alleen geclusterde column Store-tabellen worden opgeslagen in de cache.

## <a name="next-steps"></a>Volgende stappen
Zie de [uitvoering van query's bewaken](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution)voor meer informatie over het afstemmen van algemene query prestaties.

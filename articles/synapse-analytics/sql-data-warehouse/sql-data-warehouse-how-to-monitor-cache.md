---
title: Optimaliseer uw Gen2-cache
description: Meer informatie over het bewaken van uw Gen2-cache met behulp van de Azure Portal.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 4d66a1174b1b4adc94b24c6aecd55b2b8679f2f7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85211881"
---
# <a name="how-to-monitor-the-gen2-cache"></a>De Gen2-cache bewaken

In dit artikel wordt beschreven hoe u langzame query prestaties bewaakt en oplost door te bepalen of uw werk belasting optimaal gebruikmaakt van de Gen2-cache.

De Gen2-opslag architectuur bevat automatisch uw meest query's in de opgeslagen column Store-segmenten in een cache op NVMe gebaseerd Ssd's ontworpen voor Gen2 data warehouses. Betere prestaties worden gerealiseerd wanneer uw query's segmenten ophalen die zich in de cache bevinden.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met behulp van de Azure Portal

U kunt Azure Monitor gebruiken om de Gen2-cache gegevens weer te geven om de prestaties van query's op te lossen. Ga eerst naar de Azure Portal en klik op **monitor**, **metrische gegevens** en **+ een bereik selecteren**:

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Gebruik de balken zoeken en vervolg keuzelijst om uw data warehouse te zoeken. Selecteer vervolgens Toep assen.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

De belangrijkste metrische gegevens voor het oplossen van problemen met de Gen2-cache zijn het **percentage cache treffers** en het **percentage cache gebruik**. Selecteer **percentage cache treffers** en gebruik vervolgens de knop **metriek toevoegen** om **percentage gebruikt geheugen**toe te voegen. 

![Metrische cache gegevens](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

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
Zie de [uitvoering van query's bewaken](sql-data-warehouse-manage-monitor.md#monitor-query-execution)voor meer informatie over het afstemmen van algemene query prestaties.

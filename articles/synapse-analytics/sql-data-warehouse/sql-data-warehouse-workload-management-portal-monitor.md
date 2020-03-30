---
title: Bewaking van workloadbeheerportalen
description: Richtlijnen voor het bewaken van workloadmanagementportalen in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f904619b1cea97849e631310cf303ed07194a01e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349920"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – Workload Management Portal Monitoring (Preview)
In dit artikel wordt uitgelegd hoe u het gebruik van resourceen en queryactiviteit van [de werkbelastinggroep](sql-data-warehouse-workload-isolation.md#workload-groups) controleren. Zie het artikel Aan de slag [met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md) voor meer informatie over het configureren van de Azure Metrics Explorer.  Zie de sectie [Resourcegebruik](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) in azure Synapse Analytics Monitoring-documentatie voor meer informatie over het controleren van het verbruik van systeembronnen.
Er zijn twee verschillende categorieën workloadgroepstatistieken beschikbaar voor het bewaken van het beheer van de werkbelasting: toewijzing van resources en queryactiviteit.  Deze statistieken kunnen worden gesplitst en gefilterd op werkbelastinggroep.  De statistieken kunnen worden gesplitst en gefilterd op basis van het feit dat ze systeemgedefinieerd zijn (resourceklasseworkloadgroepen) of door de gebruiker gedefinieerd (gemaakt door de gebruiker met [syntaxis VAN DE WERKBELASTINGMAKEN).](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Metrische definities voor werkbelastingbeheer

|Metrische naam                    |Beschrijving  |Aggregatietype |
|-------------------------------|-------------|-----------------|
|Percentage effectieve cap-resources | *Effectief plafondresourcepercentage* is een harde limiet voor het percentage resources dat toegankelijk is voor de werkbelastinggroep, rekening houdend met *effectief minresourcepercentage* dat is toegewezen aan andere werkworkloadgroepen. De statistiek *Effectief capdresourcepercentage* `CAP_PERCENTAGE_RESOURCE` is geconfigureerd met de parameter in de syntaxis VAN DE [WERKBELASTINGGROEP MAKEN.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  De effectieve waarde wordt hier beschreven.<br><br>Als er bijvoorbeeld `DataLoads` een werkbelastinggroep wordt gemaakt met `CAP_PERCENTAGE_RESOURCE` = 100 en er een andere werkbelastinggroep wordt gemaakt met een effectief minresourcepercentage van 25%, is het percentage effectieve *limietresources* voor de `DataLoads` werkbelastinggroep 75%.<br><br>De *Effective cap resource procent* bepaalt de bovengrens van gelijktijdigheid (en dus potentiële doorvoer) die een workloadgroep kan bereiken.  Als extra doorvoer nodig is dan wat momenteel wordt gerapporteerd door `CAP_PERCENTAGE_RESOURCE`de `MIN_PERCENTAGE_RESOURCE` statistiek *Effectief capresourcepercentage,* verhoogt u de van andere werkbelastinggroepen, of schaalt u de instantie op om meer resources toe te voegen.  Het `REQUEST_MIN_RESOURCE_GRANT_PERCENT` verlagen van de gelijktijdigheid kan verhogen, maar kan de totale doorvoer niet verhogen.| Min, Avg, Max |
|Effectief min resource percentage |*Effectief minresourcepercentage* is het minimale percentage resources dat is gereserveerd en geïsoleerd voor de werkbelastinggroep, rekening houdend met het minimumvan het serviceniveau.  De statistiek Effectief minresourcepercentage wordt `MIN_PERCENTAGE_RESOURCE` geconfigureerd met de parameter in de syntaxis VAN DE [WERKBELASTINGGROEP MAKEN.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  De effectieve waarde wordt [hier](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)beschreven .<br><br>Gebruik het aggregatietype Som wanneer deze statistiek ongefilterd en ongedeeld is om de totale werkbelastingisolatie te controleren die op het systeem is geconfigureerd.<br><br>De *effectieve min resource procent* bepaalt de ondergrens van gegarandeerde gelijktijdigheid (en dus gegarandeerde doorvoer) een workload groep kan bereiken.  Als er meer gegarandeerde resources nodig zijn dan wat momenteel wordt gerapporteerd `MIN_PERCENTAGE_RESOURCE` door de statistiek *Effectieve minresourcepercenten,* verhoogt u de parameter die is geconfigureerd voor de werkbelastinggroep.  Het `REQUEST_MIN_RESOURCE_GRANT_PERCENT` verlagen van de gelijktijdigheid kan verhogen, maar kan de totale doorvoer niet verhogen. |Min, Avg, Max|
|Actieve query's voor workloadgroep  |Deze statistiek rapporteert de actieve query's binnen de werkbelastinggroep.  Met deze ongefilterde en ongesplitste statistiek worden alle actieve query's weergegeven die op het systeem worden uitgevoerd.|Sum         |
|Toewijzing workloadgroep op basis van maximaal resourcepercentage |Met deze statistiek wordt het percentage toewijzing van resources ten opzichte van het *percentage effectieve limietresources* per werkbelastinggroep weergegeven.  Deze statistiek biedt het effectieve gebruik van de werkbelastinggroep.<br><br>Overweeg een `DataLoads` workloadgroep met een Effective cap resource `REQUEST_MIN_RESOURCE_GRANT_PERCENT` *percentage* van 75% en een geconfigureerd op 25%.  De *toewijzing van de workloadgroep op basis van de maximaal gefilterde waarde van het resourcepercentage* `DataLoads` is 33% (25% / 75%) als er één query wordt uitgevoerd in deze werkbelastinggroep.<br><br>Gebruik deze statistiek om het gebruik van een werkbelastinggroep te identificeren.  Een waarde van bijna 100% geeft aan dat alle resources die beschikbaar zijn voor de werkbelastinggroep worden gebruikt.  Bovendien geeft de *groep Query's in* de wachtrij voor dezelfde werkbelastinggroep met een waarde groter dan nul, als de werkbelastinggroep extra resources zou gebruiken als deze wordt toegewezen.  Als deze statistiek daarentegen consistent laag is en de *actieve query's van* de groep Workload laag is, wordt de werkbelastinggroep niet gebruikt.  Deze situatie is vooral problematisch als *het percentage effectieve cap-resources* groter is dan nul, omdat dit zou duiden op [onderbenutte workloadisolatie](#underutilized-workload-isolation).|Min, Avg, Max |
|Toewijzing workloadgroep per systeempercentage | Deze statistiek geeft het percentage toewijzing van resources ten opzichte van het hele systeem weer.<br><br>Overweeg een `DataLoads` werkbelastinggroep met een `REQUEST_MIN_RESOURCE_GRANT_PERCENT` geconfigureerde op 25%.  *Workload groep toewijzing door* systeem `DataLoads` procent waarde gefilterd op zou 25% (25% / 100%) als er één query wordt uitgevoerd in deze werkbelastinggroep.|Min, Avg, Max |
|Time-outs van werkgroepquery's |Query's voor de werkbelastinggroep die een time-out heeft.  Query-time-outs die door deze statistiek worden gerapporteerd, zijn pas nadat de query is gestart met uitvoeren (er is geen wachttijd opgenomen vanwege vergrendeling of resourcewachttijden).<br><br>Querytime-out is geconfigureerd `QUERY_EXECUTION_TIMEOUT_SEC` met behulp van de parameter in de syntaxis [VAN DE WERKGROEP MAKEN.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Als u de waarde verhoogt, kan het aantal time-outs voor query's worden verminderd.<br><br>Overweeg de `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parameter voor de werkbelastinggroep te verhogen om het aantal time-outs te verminderen en meer resources per query toe te wijzen.  Let op, het verhogen `REQUEST_MIN_RESOURCE_GRANT_PERCENT` vermindert de hoeveelheid gelijktijdigheid voor de werkbelastinggroep. |Sum |
|Query's in de groep Werkbelasting in de wachtrij | Query's voor de werkbelastinggroep die momenteel in de wachtrij staat te wachten om de uitvoering te starten.  Query's kunnen in de wachtrij staan omdat ze wachten op resources of vergrendelingen.<br><br>Vragen kunnen wachten om tal van redenen.  Als het systeem overbelast is en de vraag naar gelijktijdigheid groter is dan wat er beschikbaar is, worden query's in de wachtrij geplaatst.<br><br>Overweeg meer resources toe te voegen `CAP_PERCENTAGE_RESOURCE` aan de werkbelastinggroep door de parameter in de groep [werkbelasting maken te](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) verhogen.  Als `CAP_PERCENTAGE_RESOURCE` de statistiek Effectieve capresourcepercent groter is dan de statistiek *Effectieve limietresourcepercenten,* heeft de geconfigureerde werkbelastingisolatie voor andere werkbelastinggroep invloed op de resources die aan deze werkbelastinggroep zijn toegewezen.  Overweeg `MIN_PERCENTAGE_RESOURCE` om andere werkbelastinggroepen te verlagen of de instantie op te schalen om meer resources toe te voegen. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>Scenario's en acties bewaken
Hieronder vindt u een reeks grafiekconfiguraties om het metrische gebruik van workloadbeheer voor probleemoplossing te markeren, samen met bijbehorende acties om het probleem op te lossen.

### <a name="underutilized-workload-isolation"></a>Onderbenutte werkbelastingisolatie
Denk aan de volgende werkbelastinggroep en classificatieconfiguratie `wgPriority` waarbij een benoemde workloadgroep wordt `wcCEOPriority` gemaakt en *TheCEO* `membername` eraan wordt toegewezen met behulp van de classificatie werkbelasting.  De `wgPriority` werkbelastinggroep heeft er 25%`MIN_PERCENTAGE_RESOURCE` workload isolation voor geconfigureerd ( = 25).  Elke vraag die door *TheCEO* wordt ingediend, krijgt 5% van de systeembronnen (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
De onderstaande grafiek is als volgt geconfigureerd:<br>
Metric 1: *Effectief minresourcepercentage* `blue line`(Avg-aggregatie, )<br>
Metric 2: *Toewijzing workloadgroep per systeempercentage* (Avg-aggregatie, `purple line`)<br>
Filter: [Werkbelastinggroep] =`wgPriority`<br>
![onderbenut-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) Uit de grafiek blijkt dat bij 25% workload isolation gemiddeld slechts 10% wordt gebruikt.  In dit geval `MIN_PERCENTAGE_RESOURCE` kan de parameterwaarde worden verlaagd tot tussen 10 of 15 en andere workloads op het systeem de resources kunnen verbruiken.

### <a name="workload-group-bottleneck"></a>Knelpunt workloadgroep
Denk aan de volgende werkbelastinggroep en classificatieconfiguratie `wgDataAnalyst` waarbij een benoemde workloadgroep wordt gemaakt `wcDataAnalyst` en de *DataAnalyst* `membername` eraan wordt toegewezen met behulp van de classificatie werkbelasting.  De `wgDataAnalyst` werkbelastinggroep heeft 6% workload`MIN_PERCENTAGE_RESOURCE` isolation geconfigureerd voor het ( =`CAP_PERCENTAGE_RESOURCE` 6) en een resource limiet van 9% ( = 9).  Elke query die door de *DataAnalyst* wordt`REQUEST_MIN_RESOURCE_GRANT_PERCENT` ingediend, krijgt 3% van de systeembronnen ( = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
De onderstaande grafiek is als volgt geconfigureerd:<br>
Metric 1: *Effective cap resource* `blue line`percentage (Avg aggregatie, )<br>
Metric 2: *Toewijzing workloadgroep op basis van maximaal resourcepercentage* (Avg-aggregatie, `purple line`)<br>
Metric 3: *Query's in de groep Werkbelasting in de wachtrij* (Aggregatie som, `turquoise line`)<br>
Filter: [Werkbelastinggroep] =`wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) De grafiek laat zien dat met een limiet van 9% op resources, de workload groep is 90%+ gebruikt (van de *Workload groep toewijzing door max resource procent metrische).*  Er is een gestage wachtrij van query's zoals weergegeven in de *groep Werkbelasting query's metrische*.  In dit geval `CAP_PERCENTAGE_RESOURCE` zal het verhogen van de waarde tot een waarde hoger dan 9% meer query's tegelijk kunnen uitvoeren.  Het `CAP_PERCENTAGE_RESOURCE` verhogen van de veronderstelt dat er voldoende middelen beschikbaar zijn en niet geïsoleerd door andere workload groepen.  Controleer of de limiet is verhoogd door de *statistiek Effectief cap-resourcepercentage te*controleren .  Als er meer doorvoer gewenst `REQUEST_MIN_RESOURCE_GRANT_PERCENT` is, overweeg dan ook om de waarde te verhogen tot een waarde groter dan 3.  Als `REQUEST_MIN_RESOURCE_GRANT_PERCENT` u de query's verhoogt, kunnen query's sneller worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
[Snelstart: workloadisolatie configureren met T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[WORKLOADGROEP MAKEN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[WORKLOADKLASSEIFIER MAKEN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Het gebruik van resources controleren](sql-data-warehouse-concept-resource-utilization-query-activity.md)


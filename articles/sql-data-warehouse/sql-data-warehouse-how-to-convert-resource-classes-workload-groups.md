---
title: Resource klasse converteren naar een werkbelasting groep
description: Meer informatie over het maken van een werkbelasting groep die vergelijkbaar is met een resource klasse in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685707"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Resource klassen omzetten in werkbelasting groepen
Werkbelasting groepen bieden een mechanisme om systeem bronnen te isoleren en te bevatten.  Daarnaast kunt u met werkbelasting groepen uitvoerings regels instellen voor de aanvragen die hierop worden uitgevoerd.  Een regel voor het uitvoeren van een query time-out kan overmatige query's annuleren zonder tussen komst van de gebruiker.  In dit artikel wordt uitgelegd hoe u een bestaande resource klasse maakt en een werkbelasting groep met een vergelijk bare configuratie kunt maken.  Daarnaast wordt een regel voor een optionele time-out voor query's toegevoegd.

## <a name="understanding-the-existing-resource-class-configuration"></a>Informatie over de configuratie van de bestaande resource klasse
Voor werkbelasting groepen is een para meter met de naam `REQUEST_MIN_RESOURCE_GRANT_PERCENT` vereist die het percentage van de totale systeem resources opgeeft dat per aanvraag is toegewezen.  Resource toewijzing wordt uitgevoerd voor [resource klassen](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) door gelijktijdigheids sleuven toe te wijzen.  Als u wilt bepalen welke waarde moet worden opgegeven voor `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, gebruikt u de sys. DM _workload_management_workload_groups_stats <link tbd> DMV.  De onderstaande query query retourneert bijvoorbeeld een waarde die kan worden gebruikt voor de para meter `REQUEST_MIN_RESOURCE_GRANT_PERCENT` om een werkbelasting groep te maken die lijkt op staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Werkbelasting groepen worden uitgevoerd op basis van het percentage van de algemene systeem resources.  

Omdat werkbelasting groepen worden uitgevoerd op basis van het percentage van de algemene systeem bronnen, worden de resources die zijn toegewezen aan statische resource klassen ten opzichte van de algemene systeem resources gewijzigd, wanneer u omhoog of omlaag schaalt.  Staticrc40 op DW1000c wijst bijvoorbeeld 9,6% van de algemene systeem bronnen toe.  Op DW2000c wordt 19,2% toegewezen.  Dit model is vergelijkbaar als u wilt opschalen voor gelijktijdigheid en meer resources per aanvraag wilt toewijzen.   

## <a name="create-workload-group"></a>Werkbelasting groep maken
Met de bekende `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, kunt u de werk belasting groep maken <link> gebruiken om de werkbelasting groep te maken.  U kunt desgewenst een `MIN_PERCENTAGE_RESOURCE` opgeven dat groter is dan nul om resources voor de werkbelasting groep te isoleren.  U kunt ook `CAP_PERCENTAGE_RESOURCE` kleiner dan 100 opgeven om de hoeveelheid resources te beperken die de werkbelasting groep mag verbruiken.  

In het onderstaande voor beeld wordt de `MIN_PERCENTAGE_RESOURCE` ingesteld om 9,6% van de systeem resources toe te wijzen aan `wgDataLoads` en garandeert u dat een query de tijden kan uitvoeren.  Daarnaast is `CAP_PERCENTAGE_RESOURCE` ingesteld op 38,4% en wordt deze werkbelasting groep beperkt tot vier gelijktijdige aanvragen.  Door de para meter `QUERY_EXECUTION_TIMEOUT_SEC` in te stellen op 3600, wordt elke query die gedurende meer dan 1 uur wordt uitgevoerd, automatisch geannuleerd.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>De classificatie maken
Voorheen werd de toewijzing van query's aan resource klassen uitgevoerd met [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Als u dezelfde functionaliteit wilt gebruiken en aanvragen wilt toewijzen aan werkbelasting groepen, gebruikt u de [classificatie syntaxis werk belasting maken](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Als u sp_addrolemember gebruikt, hebt u alleen de mogelijkheid om resources toe te wijzen aan een aanvraag op basis van een aanmelding.  Een classificatie biedt extra opties naast aanmelding, zoals:
    - label
    - session
    - tijd in het onderstaande voor beeld worden query's toegewezen uit de `AdfLogin`-aanmelding waarvoor ook het [optie label](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) is ingesteld op `factloads` aan de werkbelasting groep `wgDataLoads` die hierboven is gemaakt.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Testen met een voorbeeld query
Hieronder vindt u een voorbeeld query en een DMV-query om te controleren of de werkbelasting groep en de classificatie correct zijn geconfigureerd.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Volgende stappen

Werkbelasting isolatie: koppeling nog te bepalen hoe een werkbelasting groep maken-nog te koppelen koppelingen
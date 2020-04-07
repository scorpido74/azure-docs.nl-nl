---
title: Resourceklasse converteren naar een werkbelastinggroep
description: Meer informatie over het maken van een werkbelastinggroep die vergelijkbaar is met een resourceklasse in Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8cee874106598c7d81b923d7dd32ba91902d9326
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745189"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Resourceklassen converteren naar werkbelastinggroepen

Werkbelastinggroepen bieden een mechanisme om systeembronnen te isoleren en te bevatten.  Bovendien u met werkbelastinggroepen uitvoeringsregels instellen voor de aanvragen die in deze aanvragen worden uitgevoerd.  Met een uitvoeringsregel voor het uitvoeren van een time-out voor query's kunnen op hol geslagen queryquery's worden geannuleerd zonder tussenkomst van de gebruiker.  In dit artikel wordt uitgelegd hoe u een bestaande resourceklasse gebruiken en een werkbelastinggroep met een vergelijkbare configuratie maken.  Daarnaast wordt een optionele time-outregel voor query's toegevoegd.

## <a name="understanding-the-existing-resource-class-configuration"></a>Inzicht in de bestaande configuratie van resourceklasse

Workloadgroepen vereisen een `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parameter die het percentage van de totale systeemresources per aanvraag opgeeft.  Toewijzing van resources gebeurt voor resourceklassen door [gelijktijdigheidsruimten](resource-classes-for-workload-management.md#what-are-resource-classes) toe te wijzen.  Gebruik de sys.dm_workload_management_workload_groups_stats `REQUEST_MIN_RESOURCE_GRANT_PERCENT` <link tbd> DMV om de te specificeren waarde te bepalen.  De onderstaande queryquery retourneert bijvoorbeeld een `REQUEST_MIN_RESOURCE_GRANT_PERCENT` waarde die kan worden gebruikt voor de parameter om een werkbelastinggroep te maken die vergelijkbaar is met staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Workloadgroepen werken op basis van het percentage van de totale systeemresources.  

Omdat werkworkloadgroepen werken op basis van het percentage van de totale systeemresources, terwijl u op schaal t/m opschaalt, verandert het percentage resources dat is toegewezen aan statische resourceklassen ten opzichte van de algemene systeemresources.  Staticrc40 bij DW1000c wijst bijvoorbeeld 9,6% van de totale systeembronnen toe.  Bij DW2000c wordt 19,2% toegewezen.  Dit model is vergelijkbaar als u wilt opschalen voor gelijktijdigheid versus meer resources per aanvraag wilt toewijzen.

## <a name="create-workload-group"></a>Workloadgroep maken

Met de `REQUEST_MIN_RESOURCE_GRANT_PERCENT`bekende u de <link> syntaxis VAN DE WERKBELASTINGGROEP MAKEN gebruiken om de werkbelastinggroep te maken.  U optioneel `MIN_PERCENTAGE_RESOURCE` een optie opgeven die groter is dan nul om resources voor de werkbelastinggroep te isoleren.  U ook optioneel `CAP_PERCENTAGE_RESOURCE` minder dan 100 opgeven om de hoeveelheid resources te beperken die de werkbelastinggroep kan verbruiken.  

In het onderstaande voorbeeld wordt ingesteld `MIN_PERCENTAGE_RESOURCE` dat 9,6% van de systeembronnen wordt toegewezen aan `wgDataLoads` en garandeert dat één query alle keren kan worden uitgevoerd.  Bovendien `CAP_PERCENTAGE_RESOURCE` is ingesteld op 38,4% en beperkt deze werkbelastinggroep tot vier gelijktijdige aanvragen.  Door de `QUERY_EXECUTION_TIMEOUT_SEC` parameter in te stellen op 3600, wordt elke query die langer dan 1 uur duurt automatisch geannuleerd.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>De classificatie maken

Voorheen werd het toewijzen van query's naar resourceklassen gedaan met [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Als u dezelfde functionaliteit en toewijzingsaanvragen wilt bereiken voor werkbelastinggroepen, gebruikt u de syntaxis VAN DE [WERKBELASTINGKLASSEALSER MAKEN.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Met behulp van sp_addrolemember alleen toegestaan om middelen in kaart te brengen naar een aanvraag op basis van een login.  Een classificatie biedt extra opties naast inloggen, zoals:
    - label
    - Sessie
    - tijd In het onderstaande voorbeeld `AdfLogin` worden query's uit `factloads` de aanmelding `wgDataLoads` geplaatst waarop ook het [OPTION-label](sql-data-warehouse-develop-label.md) is ingesteld op de bovenstaande werkbelastinggroep.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testen met een voorbeeldquery

Hieronder vindt u een voorbeeldquery en een DMV-query om ervoor te zorgen dat de werkbelastinggroep en classificatie correct zijn geconfigureerd.

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

- [Werkbelastingisolatie](sql-data-warehouse-workload-isolation.md)
- [Een groep-koppeling van workloadgroep maken](quickstart-configure-workload-isolation-tsql.md)

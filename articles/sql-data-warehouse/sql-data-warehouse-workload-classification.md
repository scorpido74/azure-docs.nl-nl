---
title: Workloadclassificatie
description: Richt lijnen voor het gebruik van classificatie voor het beheren van gelijktijdigheids-, urgentie-en reken resources voor query's in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: ab7c8ba64057b4f27e00a2928a65de8eadc78c4b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768835"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Classificatie van Azure SQL Data Warehouse werk belasting

In dit artikel wordt uitgelegd wat het SQL Data Warehouse werk belasting classificatie proces is van het toewijzen van een resource klasse en belang rijk voor inkomende aanvragen.

## <a name="classification"></a>Classificatie

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Met de classificatie van werkbelasting beheer kunnen werkbelasting beleid worden toegepast op aanvragen via het toewijzen van [resource klassen](resource-classes-for-workload-management.md#what-are-resource-classes) en het [belang](sql-data-warehouse-workload-importance.md).

Hoewel er veel manieren zijn om werk belastingen voor gegevens opslag te classificeren, wordt de eenvoudigste en meest voorkomende classificatie geladen en query. U laadt gegevens met de instructies INSERT, update en DELETE.  U kunt een query uitvoeren op de gegevens met behulp van selecteren. Een oplossing voor gegevens opslag heeft vaak een werkbelasting beleid voor het laden van activiteiten, zoals het toewijzen van een hogere resource klasse met meer resources. Een ander werkbelasting beleid kan van toepassing zijn op query's, zoals lagere urgentie ten opzichte van laad activiteiten.

U kunt ook de werk belasting voor laden en query's onderverdelen. Subclassificatie geeft u meer controle over uw workloads. Query werkbelastingen kunnen bijvoorbeeld bestaan uit kubus vernieuwingen, dashboard query's of ad-hoc query's. U kunt elk van deze query-workloads classificeren met verschillende bron klassen of belang rijke instellingen. Loading kan ook profiteren van subclassificatie. Grote trans formaties kunnen worden toegewezen aan grotere resource klassen. Een hogere urgentie kan worden gebruikt om ervoor te zorgen dat de gegevens van de belangrijkste omzet loader zijn vóór weer gegevens of een sociale gegevensfeed.

Niet alle instructies worden geclassificeerd omdat ze geen bronnen nodig hebben of belang rijk moeten zijn om de uitvoering te beïnvloeden.  DBCC-opdrachten, BEGIN-, doorvoer-en ROLLBACK-transactie overzichten worden niet geclassificeerd.

## <a name="classification-process"></a>Classificatie proces

Classificatie in SQL Data Warehouse wordt vandaag bereikt door gebruikers toe te wijzen aan een rol waaraan een bijbehorende resource klasse is toegewezen met behulp van [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). De mogelijkheid om aanvragen te kenmerken buiten een aanmelding bij een resource klasse is beperkt met deze mogelijkheid. Een rijkere methode voor classificatie is nu beschikbaar met de [classificatie syntaxis CREATE WORKLOAD](/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Met deze syntaxis SQL Data Warehouse gebruikers prioriteit toewijzen en hoeveel systeem bronnen worden toegewezen aan een aanvraag via de `workload_group`-para meter. 

> [!NOTE]
> De classificatie wordt per aanvraag geëvalueerd. Meerdere aanvragen in één sessie kunnen op verschillende manieren worden geclassificeerd.

## <a name="classification-weighting"></a>Classificatie weging

Als onderdeel van het classificatie proces is weging van kracht om te bepalen welke werkbelasting groep is toegewezen.  De weging gaat als volgt:

|Classifier-para meter |Gewicht   |
|---------------------|---------|
|LIDNAAM: GEBRUIKER      |64       |
|LIDNAAM: ROL      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

De para meter `membername` is verplicht.  Als de opgegeven lidnaam echter een database gebruiker in plaats van een databaserol is, is het gewicht van de gebruiker hoger en dus de classificatie gekozen.

Als een gebruiker lid is van meerdere rollen met verschillende resource klassen die zijn toegewezen aan of overeenkomen met meerdere classificaties, krijgt de gebruiker de hoogste toewijzing van resource klassen.  Dit gedrag is consistent met het bestaande gedrag van de toewijzing van resource klassen.

## <a name="system-classifiers"></a>Systeem classificaties

Classificatie van werk belasting heeft classificaties van systeemwerk belasting. De classificaties van het systeem wijzen bestaande lidmaatschappen van resource klassen toe aan resource toewijzingen van resource klassen met normale urgentie. Systeem classificaties kunnen niet worden verwijderd. Als u systeem classificaties wilt weer geven, kunt u de onderstaande query uitvoeren:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Resource klassen toewijzingen combi neren met classificaties

Systeem classificaties die namens u zijn gemaakt, bieden een eenvoudig pad om te migreren naar de classificatie van werk belastingen. Met de toewijzings prioriteit van resource klassen kunt u leiden tot een misclassificatie wanneer u begint met het maken van nieuwe classificaties die belang rijk zijn.

Denkt u zich het volgende scenario eens in:

- Een bestaand Data Warehouse heeft een database gebruiker DBAUser toegewezen aan de resource klasse largerc. De toewijzing van de resource klasse is uitgevoerd met sp_addrolemember.
- Het Data Warehouse wordt nu bijgewerkt met werkbelasting beheer.
- Als u de nieuwe classificatie syntaxis wilt testen, heeft de databaserol DBARole (waarvan DBAUser lid is), een classificatie gemaakt waarmee ze worden toegewezen aan mediumrc en hoge urgentie.
- Wanneer DBAUser zich aanmeldt en een query uitvoert, wordt de query toegewezen aan largerc. Omdat een gebruiker voor rang heeft op een rollidmaatschap.

Om het oplossen van problemen met een fout te vereenvoudigen, raden we u aan om resource klasse-roltoewijzingen te verwijderen tijdens het maken van de werk belasting classificaties.  De onderstaande code retourneert bestaande lidmaatschappen van resource klassen.  Voer [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) uit voor elke lidnaam die wordt geretourneerd door de bijbehorende resource klasse.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [classificatie werk belasting maken (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)voor meer informatie over het maken van een classificatie.  
- Zie de Snelstartgids voor het maken van een classificatie van werk belastingen [een classificatie van werk belasting maken](quickstart-create-a-workload-classifier-tsql.md).
- Zie de artikelen met procedures voor het [configureren van de urgentie van werk belastingen](sql-data-warehouse-how-to-configure-workload-importance.md) en het [beheren en bewaken van workload Management](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Zie [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) om query's en de prioriteit weer te geven.
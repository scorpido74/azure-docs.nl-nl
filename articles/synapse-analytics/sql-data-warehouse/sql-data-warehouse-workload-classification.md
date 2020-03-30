---
title: Workloadclassificatie
description: Richtlijnen voor het gebruik van classificatie voor het beheren van gelijktijdigheid, belang en rekenresources voor query's in Azure Synapse Analytics.
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
ms.openlocfilehash: 7661981f07799592f9fdfcab3fb402336d48b4d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349980"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Azure Synapse Analytics-workloadclassificatie

In dit artikel wordt het proces van workloadclassificatie uitgelegd van het toewijzen van een workloadgroep en het belang voor binnenkomende aanvragen met SQL Analytics in Azure Synapse.

## <a name="classification"></a>Classificatie

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Met workloadmanagementclassificatie kan workloadbeleid worden toegepast op aanvragen door resourceklassen en -belang toe te [wijsen.](resource-classes-for-workload-management.md#what-are-resource-classes) [importance](sql-data-warehouse-workload-importance.md)

Hoewel er veel manieren zijn om workloads voor gegevensopslag te classificeren, is de eenvoudigste en meest voorkomende classificatie laden en query. U laadt gegevens met het invoegen, bijwerken en verwijderen van instructies.  U bevraagt de gegevens met behulp van selecties. Een oplossing voor gegevensopslag heeft vaak een werkbelastingsbeleid voor belastingsactiviteit, zoals het toewijzen van een hogere resourceklasse met meer resources. Er kan een ander werkbelastingbeleid worden toegepast op query's, zoals minder belang in vergelijking met laadactiviteiten.

U ook uw belastings- en queryworkloads onderclassificeren. Subclassificatie geeft u meer controle over uw workloads. Queryworkloads kunnen bijvoorbeeld bestaan uit kubusvernieuwingen, dashboardquery's of ad-hocquery's. U elk van deze queryworkloads classificeren met verschillende resourceklassen of belangrijkheidsinstellingen. Load kan ook profiteren van subclassificatie. Grote transformaties kunnen worden toegewezen aan grotere resourceklassen. Een groter belang kan worden gebruikt om ervoor te zorgen dat belangrijke verkoopgegevens worden opgeladen vóór weergegevens of een sociale gegevensfeed.

Niet alle verklaringen zijn geclassificeerd omdat ze geen middelen vereisen of belang nodig hebben om de uitvoering te beïnvloeden.  DBCC-opdrachten, BEGIN-, COMMIT- en ROLLBACK-transactieoverzichten worden niet geclassificeerd.

## <a name="classification-process"></a>Classificatieproces

Classificatie voor SQL Analytics in Azure Synapse wordt vandaag bereikt door gebruikers toe te wijzen aan een rol waarvoor een overeenkomstige resourceklasse is toegewezen met behulp van [sp_addrolemember.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) De mogelijkheid om aanvragen te karakteriseren buiten een login op een resourceklasse is beperkt met deze mogelijkheid. Er is nu een rijkere classificatiemethode beschikbaar met de syntaxis VAN DE [KLASSEKLASSE 'WERKBELASTING MAKEN'.](/sql/t-sql/statements/create-workload-classifier-transact-sql)  Met deze syntaxis kunnen SQL Analytics-gebruikers belang toewijzen en hoeveel `workload_group` systeembronnen via de parameter aan een aanvraag worden toegewezen. 

> [!NOTE]
> De classificatie wordt per aanvraag geëvalueerd. Meerdere aanvragen in één sessie kunnen anders worden geclassificeerd.

## <a name="classification-weighting"></a>Indelingsweging

Als onderdeel van het classificatieproces is de weging aanwezig om te bepalen welke werkbelastinggroep is toegewezen.  De weging gaat als volgt:

|Classifier Parameter |Gewicht   |
|---------------------|---------|
|LIDNAAM:GEBRUIKER      |64       |
|NAAM VAN HET LID:ROL      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

De `membername` parameter is verplicht.  Als de opgegeven lidnaam echter een databasegebruiker is in plaats van een databaserol, is de weging voor de gebruiker hoger en wordt die classificatie dus gekozen.

Als een gebruiker lid is van meerdere rollen met verschillende resourceklassen die zijn toegewezen of gematcht in meerdere classificaties, krijgt de gebruiker de hoogste toewijzing van de resourceklasse.  Dit gedrag komt overeen met het bestaande toewijzingsgedrag van resourceklassen.

## <a name="system-classifiers"></a>Systeemclassificaties

Workloadclassificatie heeft classificatie van systeemworkloadsclassificaties. De classificaties van de systeemclassificaties brengen bestaande lidmaatschappen van resourceklassen in kaart met resourceklassetoewijzingen met normaal belang. Systeemclassificaties kunnen niet worden afgezet. Als u systeemclassificaties wilt weergeven, u de onderstaande query uitvoeren:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Toewijzingen van resourceklassen mengen met classificaties

Systeemclassificaties die namens u zijn gemaakt, bieden een eenvoudig pad om te migreren naar workloadclassificatie. Als u toewijzingen van resourceklassen met classificatieprioriteit gebruikt, kan dit leiden tot een verkeerde classificatie wanneer u nieuwe classificaties met belang begint te maken.

Denkt u zich het volgende scenario eens in:

- In een bestaand gegevensmagazijn is een DBAUser van de databasegebruiker toegewezen aan de rol van de grotere resourceklasse. De toewijzing van resourceklasse is gedaan met sp_addrolemember.
- Het datawarehouse wordt nu bijgewerkt met workloadmanagement.
- Om de nieuwe classificatiesyntaxis te testen, heeft de databaserol DBARole (waarvan DBAUser lid is), een classificatie gemaakt om ze in kaart te brengen naar mediumrc en hoog belang.
- Wanneer DBAUser zich aanmeldt en een query uitvoert, wordt de query toegewezen aan biggerc. Omdat een gebruiker voorrang heeft op een rollidmaatschap.

Om foutikale problemen te vereenvoudigen, raden we u aan toewijzingen voor resourceklassen te verwijderen terwijl u workloadclassificaties maakt.  De onderstaande code retourneert bestaande lidmaatschappen van resourceklassen.  Voer [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) uit voor elke lidnaam die is geretourneerd uit de bijbehorende resourceklasse.

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

- Zie de [KLASSER (Transact-SQL) maken voor](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)meer informatie over het maken van een classificatie.  
- Zie de Quickstart voor het maken van een classificatie voor werkbelasting [Maak een classificatie voor werkbelasting](quickstart-create-a-workload-classifier-tsql.md).
- Bekijk de how-to-artikelen om [het belang van workloads](sql-data-warehouse-how-to-configure-workload-importance.md) te configureren en hoe [u Workload Management beheren en bewaken.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
- Zie [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) om query's en het toegewezen belang weer te geven.
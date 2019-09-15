---
title: Opmerkingen bij de release van Azure SQL Database | Microsoft Docs
description: Meer informatie over de nieuwe functies en verbeteringen in de Azure SQL Database-Service en in de Azure SQL Database documentatie
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001514"
---
# <a name="sql-database-release-notes"></a>Opmerkingen bij de release SQL Database

Dit artikel bevat een overzicht van SQL Database functies die momenteel beschikbaar zijn in de open bare preview. Zie [SQL database service-updates](https://azure.microsoft.com/updates/?product=sql-database)voor SQL database updates en verbeteringen. Zie [service-updates](https://azure.microsoft.com/updates)voor updates en verbeteringen voor andere Azure-Services.

## <a name="features-in-public-preview"></a>Functies in open bare preview

### <a name="single-databasetabsingle-database"></a>[Individuele database](#tab/single-database)

| Functie | Details |
| ---| --- |
| Versneld database herstel met afzonderlijke data bases en elastische Pools | Zie [versneld database herstel](sql-database-accelerated-database-recovery.md)voor meer informatie.|
|Aantal benadering|Zie [geschatte aantallen DISTINCT](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)voor meer informatie.|
|Batch-modus op Rowstore (onder compatibiliteits niveau 150)|Zie [batch-modus op Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)voor meer informatie.|
| Gegevensdetectie en -classificatie  |Zie [Azure SQL database en SQL Data Warehouse gegevens detectie & classificatie](sql-database-data-discovery-and-classification.md)voor meer informatie.|
| Taken voor Elastic Database | Zie [elastische taken maken, configureren en beheren](elastic-jobs-overview.md)voor meer informatie. |
| Elastische query’s | Zie [overzicht van elastische query's](sql-database-elastic-query-overview.md)voor meer informatie. |
| Elastische transacties | [Gedistribueerde trans acties over Cloud databases](sql-database-elastic-transactions-overview.md). |
|Feedback over geheugen toekenning (rij-modus) (onder compatibiliteits niveau 150)|Zie [feedback over geheugen toekenning (rij-modus)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)voor meer informatie.|
| Query-Editor in het Azure Portal |Zie [de SQL-query editor van het Azure Portal gebruiken om verbinding te maken en gegevens op te vragen](sql-database-connect-query-portal.md)voor meer informatie.|
| R Services/machine learning met afzonderlijke data bases en elastische Pools |Zie [Machine Learning Services in Azure SQL database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)voor meer informatie.|
| Serverloze compute-laag | Zie [SQL database serverloze (preview-versie)](sql-database-serverless.md)voor meer informatie.|
|SQL-analyse|Zie [Azure SQL-analyse](../azure-monitor/insights/azure-sql.md)voor meer informatie.|
|Uitgestelde compilatie van tabel variabele (onder compatibiliteits niveau 150)|Zie voor meer informatie [tabel variabele uitgestelde compilatie](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

| Functie | Details |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Instantie groepen</a> | Een handige en rendabele manier om kleinere SQL-instanties naar de cloud te migreren. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparent Data Encryption (TDE) met Bring Your Own Key (BYOK)</a> |Zie [Azure SQL transparent Data Encryption met door de klant beheerde sleutels in azure Key Vault voor meer informatie: Ondersteuning](transparent-data-encryption-byok-azure-sql.md)voor Bring your own Key.|
| <a href="https://aka.ms/managed-instance-failover-groups">Groepen met geo-gedistribueerde failover</a> | Bewaar een kopie van het exemplaar in een andere regio en zorg ervoor dat uw gegevens ook beschikbaar zijn in het scenario voor het regionale nood geval. |
| <a href="https://aka.ms/managed-instance-aadlogins">Azure AD server-principals op exemplaar niveau (aanmeldingen)</a> | Aanmeldingen op server niveau maken met behulp van de instructie voor het <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">maken van een aanmelding vanuit een externe provider</a> . |
| [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md) | Repliceer de wijzigingen van uw tabellen in andere data bases die zijn geplaatst in beheerde exemplaren, afzonderlijke data bases of SQL Server exemplaren, of werk uw tabellen bij wanneer sommige rijen worden gewijzigd in andere beheerde exemplaren of SQL Server exemplaar. Zie [Configure Replication in a Azure SQL database Managed instance data base](replication-with-sql-database-managed-instance.md)(Engelstalig) voor meer informatie. |
| Detectie van bedreigingen |Zie [detectie van dreigingen configureren in Azure SQL database Managed instance](sql-database-managed-instance-threat-detection.md)voor meer informatie.|
| Verwijderde data bases opnieuw maken met beheerde exemplaren |Zie [Verwijderde data bases opnieuw maken in Azure SQL Managed instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)voor meer informatie.|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>Opgeloste bekende problemen

- **Aug 2019** -Inge sloten data bases worden volledig ondersteund in een beheerd exemplaar.

## <a name="updates"></a>Updates

Zie [SQL database service-updates](https://azure.microsoft.com/updates/?product=sql-database)voor een lijst met updates en verbeteringen van SQL database.

Zie [service-updates](https://azure.microsoft.com/updates)voor updates en verbeteringen voor alle Azure-Services.

## <a name="contribute-to-content"></a>Bijdragen aan inhoud

Raadpleeg de [hand leiding voor docs-inzenders](https://docs.microsoft.com/contribute/)om een bijdrage te leveren aan de Azure SQL database documentatie.

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
ms.openlocfilehash: 90be253a4763aebd31b663aa0e765c187c8bff92
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266419"
---
# <a name="sql-database-release-notes"></a>Opmerkingen bij de release SQL Database

Dit artikel bevat een overzicht van SQL Database functies die momenteel beschikbaar zijn in de open bare preview. Zie [SQL database service-updates](https://azure.microsoft.com/updates/?product=sql-database)voor SQL database updates en verbeteringen. Zie [service-updates](https://azure.microsoft.com/updates)voor updates en verbeteringen voor andere Azure-Services.

## <a name="features-in-public-preview"></a>Functies in open bare preview

### <a name="single-databasetabsingle-database"></a>[Individuele database](#tab/single-database)

| Functie | Details |
| ---| --- |
| [Persoonlijke Azure-koppeling](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Private Link vereenvoudigt de netwerkarchitectuur en beveiligt de verbinding tussen eindpunten in Azure doordat gegevens op het Azure-netwerk blijven en niet worden blootgesteld op het openbare internet. U kunt met Private Link ook uw eigen services maken en weergeven in Azure. |
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
| <a href="https://aka.ms/managed-instance-aadlogins">Azure AD server-principals op exemplaar niveau (aanmeldingen)</a> | Aanmeldingen op server niveau maken met behulp van de instructie voor het <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">maken van een aanmelding vanuit een externe provider</a> . |
| [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md) | Repliceer de wijzigingen van uw tabellen in andere data bases die zijn geplaatst in beheerde exemplaren, afzonderlijke data bases of SQL Server exemplaren, of werk uw tabellen bij wanneer sommige rijen worden gewijzigd in andere beheerde exemplaren of SQL Server exemplaar. Zie [Configure Replication in a Azure SQL database Managed instance data base](replication-with-sql-database-managed-instance.md)(Engelstalig) voor meer informatie. |
| Detectie van bedreigingen |Zie [detectie van dreigingen configureren in Azure SQL database Managed instance](sql-database-managed-instance-threat-detection.md)voor meer informatie.|
| Verwijderde data bases opnieuw maken met beheerde exemplaren |Zie [Verwijderde data bases opnieuw maken in Azure SQL Managed instance](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)voor meer informatie.|
| &nbsp; |

---

## <a name="new-features"></a>Nieuwe functies

### <a name="managed-instance-h2-2019-updates"></a>Managed instance H2 2019-updates

- Met [groepen voor automatische failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) kunt u alle data bases van het primaire exemplaar repliceren naar een secundair exemplaar in een andere regio.
- Configureer het gedrag van het beheerde exemplaar met [globale traceer vlaggen](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Beheerde exemplaren van H1 2019

De volgende functies zijn ingeschakeld in het implementatie model voor beheerde instanties in H1 2019:
  - Ondersteuning voor abonnementen met een <a href="https://aka.ms/sql-mi-visual-studio-subscribers">maandelijks Azure-tegoed voor Visual Studio-abonnees</a> en verhoogde [regionale limieten](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Ondersteuning voor <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 en SharePoint 2019 </a> en <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Maak instanties met <a href="https://aka.ms/managed-instance-collation">sorteringen op server niveau</a> en <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">tijd zone</a> van uw keuze.
  - Beheerde exemplaren zijn nu beveiligd met <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">ingebouwde firewall</a>.
  - Configureer instanties voor het gebruik van [open bare eind punten](sql-database-managed-instance-public-endpoint-configure.md), een [proxy onderdrukkings](sql-database-connectivity-architecture.md#connection-policy) verbinding om betere netwerk prestaties te krijgen, <a href="https://aka.ms/four-cores-sql-mi-update">4 vCores op GEN5 hardware genereren</a> of <a href="https://aka.ms/managed-instance-configurable-backup-retention">het bewaren van back-ups te configureren tot 35 dagen voor herstel naar een</a> bepaald tijdstip. Lange termijn retentie van back-ups (Maxi maal 10 jaar) is nog niet ingeschakeld, zodat u <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">alleen back-ups met alleen-kopiëren</a> als alternatief kunt gebruiken.
  - Dankzij nieuwe functies kunt u <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">uw data base met behulp van Power shell naar een ander Data Center herstellen, de</a> [naam van de data base wijzigen](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), het [virtuele cluster verwijderen](sql-database-managed-instance-delete-virtual-cluster.md).
  - Met de nieuwe [rol Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) voor ingebouwde instanties kunt u SoD-naleving met beveiligings principes en naleving van de bedrijfs normen.
  - Het beheerde exemplaar is beschikbaar in de volgende Azure Government regio's tot GA (US Gov-Texas, US Gov-Arizona), evenals in China-noord 2 en China-oost 2. Het is ook beschikbaar in de volgende open bare regio's: Australië-centraal, Australië-centraal 2, Brazilië-zuid, Frankrijk-zuid, UAE-centraal, UAE-noord, Zuid-Afrika-noord, Zuid-Afrika-west.

## <a name="fixed-known-issues"></a>Opgeloste bekende problemen

- **Aug 2019** -Inge sloten data bases worden volledig ondersteund in een beheerd exemplaar.

## <a name="updates"></a>Updates

Zie [SQL database service-updates](https://azure.microsoft.com/updates/?product=sql-database)voor een lijst met updates en verbeteringen van SQL database.

Zie [service-updates](https://azure.microsoft.com/updates)voor updates en verbeteringen voor alle Azure-Services.

## <a name="contribute-to-content"></a>Bijdragen aan inhoud

Raadpleeg de [hand leiding voor docs-inzenders](https://docs.microsoft.com/contribute/)om een bijdrage te leveren aan de Azure SQL database documentatie.

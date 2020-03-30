---
title: Problemen met het verbinden van brondatabases
titleSuffix: Azure Database Migration Service
description: Meer informatie over het oplossen van bekende problemen/fouten die zijn gekoppeld aan het verbinden van Azure Database Migration Service met brondatabases.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297101"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>DMS-fouten oplossen bij het verbinden van de brondatabases

In het volgende artikel vindt u informatie over het oplossen van mogelijke problemen die u ondervinden bij het koppelen van de Azure Database Migration Service (DMS) aan uw brondatabase. Elke sectie hieronder heeft betrekking op een specifiek type brondatabase, waarin de fout wordt vermeld die u samen met details tegenkomen en koppelingen naar informatie over hoe u de verbinding oplossen.

## <a name="sql-server"></a>SQL Server

Mogelijke problemen in verband met het maken van verbinding met een SQL Server-brondatabase en hoe deze te verpakken, worden in de volgende tabel weergegeven.

| Fout         | Details voor oorzaak en probleemoplossing |
| ------------- | ------------- |
| SQL-verbinding is mislukt. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk. Controleer of de instantienaam juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan.<br> | Deze fout treedt op als de service de bronserver niet kan vinden. Zie het artikel Fout [bij het maken van een dynamische poort of benoemde instantie om](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)het probleem op te lossen. |
| **Fout 53** - SQL-verbinding is mislukt. (Ook voor foutcodes 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Deze fout treedt op als de service geen verbinding kan maken met de bronserver. Als u het probleem wilt oplossen, raadpleegt u de volgende bronnen en probeert u het opnieuw. <br><br>  [Interactieve gebruikershandleiding om het verbindingsprobleem op te lossen](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Vereisten voor het migreren van SQL Server naar Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Vereisten voor het migreren van SQL Server naar een beheerde Azure SQL Database-instantie](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Fout 18456** - Inloggen is mislukt.<br> | Deze fout treedt op als de service geen verbinding kan maken met de brondatabase met behulp van de meegeleverde T-SQL-referenties. Als u het probleem wilt verhelpen, controleert u de ingevoerde referenties. U ook verwijzen naar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) of naar de probleemoplossingsdocumenten in de notitie onder deze tabel en het vervolgens opnieuw proberen. |
| Ongevormde AccountName-waarde '{0}' verstrekt. Verwachte notatie voor AccountName is DomainName\UserName<br> | Deze fout treedt op als de gebruiker Windows-verificatie selecteert, maar de gebruikersnaam in een ongeldige indeling opgeeft. Als u het probleem wilt oplossen, geeft u de gebruikersnaam op in de juiste indeling voor Windows-verificatie of selecteert u **SQL-verificatie.** |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Mogelijke problemen in verband met het maken van verbinding met een bron AWS RDS MySQL-database en hoe deze aan te pakken zijn opgenomen in de volgende tabel.

| Fout         | Details voor oorzaak en probleemoplossing |
| ------------- | ------------- |
| **Fout [2003]**[HY000] - verbinding is mislukt. FOUT [HY000] [MySQL][ODBC x.x(w)-stuurprogramma] Kan geen verbinding maken met MySQL-server op {server}' (10060) | Deze fout treedt op als het MySQL ODBC-stuurprogramma geen verbinding kan maken met de bronserver. Als u het probleem wilt oplossen, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw.<br> |
| **Fout [2005]**[HY000] - verbinding is mislukt. FOUT [HY000] [MySQL][ODBC x.x(w) driver] Onbekende MySQL serverhost '{server}' | Deze fout treedt op als de service de bronhost op RDS niet kan vinden. Het probleem kan zijn omdat de vermelde bron niet bestaat of er een probleem is met de RDS-infrastructuur. Als u het probleem wilt oplossen, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw.<br> |
| **Fout [1045]**[HY000] - verbinding is mislukt. FOUT [HY000] [MySQL][ODBC x.x(w) driver] Toegang geweigerd voor gebruiker '{user}'@'{server}' (met wachtwoord: JA) | Deze fout treedt op als het MySQL ODBC-stuurprogramma geen verbinding kan maken met de bronserver vanwege ongeldige referenties. Controleer de referenties die u hebt ingevoerd. Als het probleem zich voortzet, controleert u of de broncomputer de juiste referenties heeft. Mogelijk moet u het wachtwoord in de console opnieuw instellen. Als u het probleem nog steeds tegenkomt, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw.<br> |
| **Fout [9002]**[HY000] - verbinding is mislukt. FOUT [HY000] [MySQL][ODBC x.x(w)-stuurprogramma] De verbindingstekenreeks is mogelijk niet geschikt. Bezoek portal voor referenties.| Deze fout treedt op als de verbinding mislukt als gevolg van een probleem met de verbindingstekenreeks. Controleer of de opgegeven verbindingstekenreeks geldig is. Als u het probleem wilt oplossen, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw.<br> |
| **Fout in binaire logboekregistratie. Variabele binlog_format heeft waarde {value}'. Verander het in 'rij'.** | Deze fout treedt op als er een fout optreedt in binaire logboekregistratie; de variabele binlog_format de verkeerde waarde heeft. Als u het probleem wilt oplossen, wijzigt u de binlog_format in parametergroep in 'RIJ' en start u de instantie opnieuw op. Zie voor meer informatie [binaire logboekopties en variabelen](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) of documentatie van AWS [RDS MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Zie de volgende bronnen voor meer informatie over probleemoplossingsproblemen in verband met het maken van verbinding met een bron AWS RDS MySQL-database:
> * [Problemen met problemen met Amazon RDS-connectiviteit oplossen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hoe los ik problemen op die verbinding maken met mijn Amazon RDS-database-exemplaar?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Mogelijke problemen in verband met het maken van verbinding met een bron AWS RDS PostgreSQL-database en hoe deze aan te pakken zijn opgenomen in de volgende tabel.

| Fout         | Details voor oorzaak en probleemoplossing |
| ------------- | ------------- |
| **Fout [101]**[08001] - verbinding is mislukt. FOUT [08001] time-out verlopen. | Deze fout treedt op als het Postgres-stuurprogramma geen verbinding kan maken met de bronserver. Als u het probleem wilt oplossen, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw. |
| **Fout: Parameter wal_level heeft waarde {value}'. Wijzig het in 'logisch' om replicatie toe te staan.** | Deze fout treedt op als de parameter wal_level de verkeerde waarde heeft. Als u het probleem wilt verhelpen, wijzigt u de rds.logical_replication in parametergroep in 1 en start u de instantie opnieuw op. Zie voor meer informatie [de vereisten voor het migreren naar Azure PostgreSQL met Behulp van DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) of [PostgreSQL op Amazon RDS.](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html) |

> [!NOTE]
> Zie de volgende bronnen voor meer informatie over probleemoplossingsproblemen in verband met het maken van verbinding met een bron-AWS RDS PostgreSQL-database:
> * [Problemen met problemen met Amazon RDS-connectiviteit oplossen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Hoe los ik problemen op die verbinding maken met mijn Amazon RDS-database-exemplaar?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Mogelijke problemen in verband met het maken van verbinding met een BRON-AWS RDS SQL Server-database en hoe deze te verpakken, worden in de volgende tabel weergegeven.

| Fout         | Details voor oorzaak en probleemoplossing |
| ------------- | ------------- |
| **Fout 53** - SQL-verbinding is mislukt. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server is niet gevonden of niet toegankelijk. Controleer of de instantienaam juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan. (provider: Named Pipes Provider, fout: 40 - Kon geen verbinding openen met SQL Server | Deze fout treedt op als de service geen verbinding kan maken met de bronserver. Als u het probleem wilt oplossen, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw. |
| **Fout 18456** - Inloggen is mislukt. Inloggen is mislukt voor gebruiker {user}' | Deze fout treedt op als de service geen verbinding kan maken met de brondatabase met de opgegeven T-SQL-referenties. Als u het probleem wilt verhelpen, controleert u de ingevoerde referenties. U ook verwijzen naar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) of naar de documenten voor het oplossen van problemen in de notitie onder deze tabel en probeer het opnieuw. |
| **Fout 87** - Verbindingstekenreeks is niet geldig. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk. Controleer of de instantienaam juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan. (provider: SQL Network Interfaces, fout: 25 - Verbindingstekenreeks is niet geldig) | Deze fout treedt op als de service geen verbinding kan maken met de bronserver vanwege een ongeldige verbindingstekenreeks. Als u het probleem wilt oplossen, controleert u de opgegeven verbindingstekenreeks. Als het probleem blijft bestaan, raadpleegt u de probleemoplossingsdocumenten in de notitie onder deze tabel en probeert u het opnieuw. |
| **Fout - Servercertificaat niet vertrouwd.** Er is een verbinding met de server tot stand gebracht, maar er is vervolgens een fout opgetreden tijdens het aanmeldingsproces. (provider: SSL Provider, fout: 0 - De certificaatketen is uitgegeven door een autoriteit die niet wordt vertrouwd.) | Deze fout treedt op als het gebruikte certificaat niet wordt vertrouwd. Als u het probleem wilt oplossen, moet u een certificaat vinden dat kan worden vertrouwd en vervolgens inschakelen op de server. U ook de optie Vertrouwenscertificaat selecteren tijdens het maken van verbinding. Doe deze actie alleen als u bekend bent met het gebruikte certificaat en u vertrouwt deze. <br> TLS-verbindingen die worden versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging - ze zijn gevoelig voor man-in-the-middle-aanvallen. Vertrouw niet op TLS die zelfondertekende certificaten gebruikt in een productieomgeving of op servers die zijn verbonden met internet. <br> Zie voor meer informatie [SSL gebruiken met een Microsoft SQL Server DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) of [Tutorial: Rds SQL Server migreren naar Azure met DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Fout 300** - De gebruiker heeft geen vereiste machtigingen. VIEW SERVER STATE-statustoestemming is geweigerd op object {server}', database {database}' | Deze fout treedt op als de gebruiker geen toestemming heeft om de migratie uit te voeren. Als u het probleem wilt verhelpen, raadpleegt u [GRANT Server Permissions - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) of [Tutorial: Migreer RDS SQL Server naar Azure met DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) voor meer informatie. |

> [!NOTE]
> Zie de volgende bronnen voor meer informatie over probleemoplossingsproblemen in verband met het maken van verbinding met een bron AWS RDS SQL Server:
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (Verbindingsproblemen met SQL Server oplossen)
> * [Hoe los ik problemen op die verbinding maken met mijn Amazon RDS-database-exemplaar?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Bekende problemen

* [Bekende problemen/migratiebeperkingen met online migraties naar Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Bekende problemen/migratiebeperkingen met online migraties naar Azure Database voor MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Bekende problemen/migratiebeperkingen met online migraties naar Azure Database voor PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Volgende stappen

* Bekijk het artikel [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Bekijk het artikel [Serverparameters configureren in Azure Database voor MySQL met behulp van de Azure-portal.](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* Bekijk het [artikelOverzicht van vereisten voor het gebruik van Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Zie de [veelgestelde vragen over het gebruik van Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).

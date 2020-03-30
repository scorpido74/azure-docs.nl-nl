---
title: Veelvoorkomende verbindingsproblemen met SQL-database oplossen
description: Biedt stappen om problemen met azure SQL-database-verbindingsproblemen op te lossen en andere specifieke SQL-database-problemen op te lossen
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208786"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Verbindingsproblemen en andere fouten oplossen met Microsoft Azure SQL Database

U ontvangt foutberichten wanneer het verbinden met Azure SQL Database mislukt. Deze verbindingsproblemen kunnen worden veroorzaakt door de herconfiguratie van Azure SQL Database, firewall-instellingen, een time-out van de verbinding, onjuiste inloggegevens of het niet toepassen van aanbevolen procedures en ontwerprichtlijnen tijdens het [ontwerpproces van](sql-database-develop-overview.md) de toepassing. Als de maximale limiet voor sommige Azure SQL Database-resources is bereikt, u bovendien geen verbinding maken met Azure SQL Database.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Tijdelijke foutfouten (40197, 40613 en anderen)

De Azure-infrastructuur biedt de mogelijkheid om servers dynamisch opnieuw te configureren wanneer zware workloads optreden in de SQL Database-service.  Dit dynamische gedrag kan ervoor zorgen dat uw clientprogramma de verbinding met SQL Database verliest. Dit soort foutvoorwaarde wordt een *tijdelijke fout*genoemd. Gebeurtenissen voor databaseconfiguratie vinden plaats vanwege een geplande gebeurtenis (bijvoorbeeld een software-upgrade) of een ongeplande gebeurtenis (bijvoorbeeld een procescrash of taakverdeling). De meeste herconfiguratiegebeurtenissen zijn over het algemeen van korte duur en moeten in ten hoogste 60 seconden worden voltooid. Deze gebeurtenissen kunnen echter soms langer duren, bijvoorbeeld wanneer een grote transactie een langdurig herstel veroorzaakt. In de volgende tabel worden verschillende tijdelijke fouten weergegeven die toepassingen kunnen ontvangen wanneer ze verbinding maken met SQL Database

### <a name="list-of-transient-fault-error-codes"></a>Lijst met tijdelijke foutfoutcodes


| Foutcode | Severity | Beschrijving |
| ---:| ---:|:--- |
| 4060 |16 |Kan geen database "%.&#x2a;ls" openen die door de login wordt gevraagd. De login is mislukt. Zie [Fouten 4000 tot en met 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999) voor meer informatie|
| 40197 |17 |Er is een fout opgetreden bij het verwerken van uw aanvraag door de service. Probeer het opnieuw. Foutcode %d.<br/><br/>U ontvangt deze fout wanneer de service is uitgeschakeld als gevolg van software- of hardware-upgrades, hardwarefouten of andere failoverproblemen. De foutcode (%d) die is ingesloten in het bericht van fout 40197] bevat aanvullende informatie over het soort fout of failover dat is opgetreden. Enkele voorbeelden van de foutcodes zijn ingesloten in het bericht van fout 40197 zijn 40020, 40143, 40166 en 40540.<br/><br/>Als u opnieuw verbinding maakt met uw SQL Database-server, wordt u automatisch verbonden met een gezonde kopie van uw database. Uw toepassing moet fout 40197 opvangen, de ingesloten foutcode (%d) registreren in het bericht voor het oplossen van problemen en proberen opnieuw verbinding te maken met SQL Database totdat de bronnen beschikbaar zijn en uw verbinding opnieuw tot stand is gebracht. Zie [Tijdelijke fouten voor](sql-database-connectivity-issues.md#transient-errors-transient-faults)meer informatie .|
| 40501 |20 |De service is momenteel te druk. Probeer het verzoek na 10 seconden opnieuw. Incident ID: %ls. Code: %d. Zie voor meer informatie: <br/>&bull;&nbsp; [Beperkingen van databaseserverbronnen](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-limieten voor afzonderlijke databases](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Database '%.&#x2a;ls' op server '%.&#x2a;ls' is momenteel niet beschikbaar. Probeer de verbinding later opnieuw. Als het probleem blijft bestaan, neemt u contact op met de klantenservice en geeft u hen de sessietracerings-ID van '&#x2a;ls'.<br/><br/> Deze fout kan optreden als er al een bestaande dedicated administrator-verbinding (DAC) is ingesteld in de database. Zie [Tijdelijke fouten voor](sql-database-connectivity-issues.md#transient-errors-transient-faults)meer informatie .|
| 49918 |16 |Kan aanvraag niet verwerken. Niet genoeg resources om aanvragen te verwerken.<br/><br/>De service is momenteel te druk. Probeer het verzoek later opnieuw. Zie voor meer informatie: <br/>&bull;&nbsp; [Beperkingen van databaseserverbronnen](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-limieten voor afzonderlijke databases](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Kan het aanvragen voor maken of bijwerken niet verwerken. Te veel bewerkingen maken of bijwerken in uitvoering voor abonnement "%ld".<br/><br/>De service is bezig met het verwerken van meerdere aanvragen voor het maken of bijwerken van aanvragen voor uw abonnement of server. Aanvragen zijn momenteel geblokkeerd voor resourceoptimalisatie. [Querysys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) voor bewerkingen in behandeling. Wacht totdat aanvragen voor maken of bijwerken in behandeling zijn voltooid of verwijder een van uw in behandeling zijnde aanvragen en probeer uw aanvraag later opnieuw. Zie voor meer informatie: <br/>&bull;&nbsp; [Beperkingen van databaseserverbronnen](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-limieten voor afzonderlijke databases](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Kan aanvraag niet verwerken. Te veel bewerkingen in uitvoering voor abonnement "%ld".<br/><br/>De service is bezig met het verwerken van meerdere aanvragen voor dit abonnement. Aanvragen zijn momenteel geblokkeerd voor resourceoptimalisatie. Query [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) voor de bedrijfsstatus. Wacht tot in behandeling zijnde aanvragen zijn voltooid of verwijder een van uw in behandeling zijnde aanvragen en probeer uw aanvraag later opnieuw. Zie voor meer informatie: <br/>&bull;&nbsp; [Beperkingen van databaseserverbronnen](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-limieten voor afzonderlijke databases](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Inloggen op read-secondary mislukt als gevolg van lange wachttijd op 'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'. De replica is niet beschikbaar voor aanmelding omdat rijversies ontbreken voor transacties die tijdens de vlucht waren toen de replica werd gerecycled. Het probleem kan worden opgelost door de actieve transacties op de primaire replica terug te draaien of te plegen. Het voorkomen van deze voorwaarde kan worden geminimaliseerd door lange schrijftransacties op de primaire te vermijden. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen om tijdelijke verbindingsproblemen op te lossen

1. Controleer het [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) op bekende storingen die zich hebben voorgedaan tijdens de periode waarin de fouten door de toepassing zijn gerapporteerd.
2. Toepassingen die verbinding maken met een cloudservice zoals Azure SQL Database, moeten periodieke herconfiguratiegebeurtenissen verwachten en een nieuwe configuratie implementeren om deze fouten te verwerken in plaats van deze als toepassingsfouten voor gebruikers te vernemen. 
3. Als een database de resourcelimieten nadert, kan deze een probleem zijn met tijdelijke connectiviteit. Zie [Resourcelimieten](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Als er verbindingsproblemen blijven optreden of als de duur waarvoor uw toepassing de fout tegenkomt meer dan 60 seconden bedraagt of als u meerdere exemplaren van de fout op een bepaalde dag ziet, dient u een Azure-ondersteuningsaanvraag in door **Ondersteuning op** de [Azure Support-site](https://azure.microsoft.com/support/options) te selecteren.

#### <a name="implementing-retry-logic"></a>Logica voor opnieuw proberen implementeren
Het wordt sterk aanbevolen dat uw clientprogramma logica opnieuw probeert, zodat het een verbinding opnieuw kan herstellen nadat het de tijdelijke fouttijd heeft gegeven om zichzelf te corrigeren.  We raden u aan 5 seconden te wachten voordat u het eerst opnieuw probeert. Opnieuw proberen na een vertraging korter dan 5 seconden dreigt de cloudservice te overweldigen. Voor elke volgende poging moet de vertraging exponentieel groeien, tot een maximum van 60 seconden.

Zie voor codevoorbeelden van logica opnieuw proberen:
- [Flexibel verbinding maken met SQL via ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Flexibel verbinding maken met SQL via PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Voor meer informatie over het afhandelen van tijdelijke fouten in uw toepassingscontrole
* [Tijdelijke verbindingsfouten oplossen in SQL-database](sql-database-connectivity-issues.md)

Een discussie over de *blokkeringsperiode* voor clients die ADO.NET gebruiken, is beschikbaar in [SQL Server Connection Pooling (ADO.NET).](https://msdn.microsoft.com/library/8xx3tyca.aspx)

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Er is een netwerkgerelateerde of instantiespecifieke fout opgetreden bij het tot stand brengen van een verbinding met SQL Database-server

Het probleem treedt op als de toepassing geen verbinding kan maken met de server.

Als u dit probleem wilt oplossen, probeert u de stappen (in de weergegeven volgorde) in de [sectie Stappen om veelvoorkomende verbindingsproblemen op te lossen.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>De server/instantie is niet gevonden of niet toegankelijk geweest (fouten 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Fout 26: Foutzoekende server opgegeven

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Fout 40: kan geen verbinding met de server openen

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fout 10053: er is een fout op transportniveau opgetreden bij het ontvangen van resultaten van de server

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Deze problemen doen zich voor als de toepassing geen verbinding kan maken met de server.

Als u deze problemen wilt oplossen, probeert u de stappen (in de weergegeven volgorde) in de [sectie Stappen om veelvoorkomende verbindingsproblemen op te lossen.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Kan geen verbinding maken met de server vanwege firewallproblemen

### <a name="error-40615-cannot-connect-to--servername-"></a>Fout 40615: kan geen verbinding maken met < servernaam >

Als u dit probleem wilt oplossen, [configureert u firewall-instellingen in SQL Database via de Azure-portal.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Fout 5: Kan geen verbinding maken met < servernaam >

Als u dit probleem wilt oplossen, moet u ervoor zorgen dat poort 1433 open staat voor uitgaande verbindingen op alle firewalls tussen de client en het internet.

Zie [De Windows Firewall configureren om SQL Server-toegang toe te staan](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)voor meer informatie.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Kan niet inloggen op de server (fouten 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Inloggen is mislukt voor gebruiker '< Gebruikersnaam >'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Neem contact op met de servicebeheerder om u een geldige SQL Server-gebruikersnaam en -wachtwoord te geven om dit probleem op te lossen.

Doorgaans kan de servicebeheerder de volgende stappen gebruiken om de inloggegevens toe te voegen:

1. Meld u aan bij de server met SQL Server Management Studio (SSMS).
2. Voer de volgende SQL-query uit om te controleren of de aanmeldingsnaam is uitgeschakeld:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Als de bijbehorende naam is uitgeschakeld, schakelt u deze in met behulp van de volgende instructie:

   ```sql
   Alter login <User name> enable
   ```

4. Als de SQL-inloggebruikersnaam niet bestaat, maakt u deze door de volgende stappen te volgen:

   1. Dubbelklik in SSMS op **Beveiliging** om deze uit te breiden.
   2. Klik met de rechtermuisknop op **Aanmeldingen**, en selecteer vervolgens **Nieuwe aanmelding**.
   3. Bewerk en voer in het gegenereerde script met tijdelijke aanduidingen de volgende SQL-query uit:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Dubbelklik op **database**.
6. Selecteer de database waaraan u de gebruiker toestemming wilt geven.
7. Dubbelklik op **Beveiliging**.
8. Klik met de rechtermuisknop op **Gebruikers**, en selecteer vervolgens **Nieuwe gebruiker**.
9. Bewerk en voer in het gegenereerde script met tijdelijke aanduidingen de volgende SQL-query uit:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > U `sp_addrolemember` ook specifieke gebruikers toewijzen aan specifieke databaserollen.

Zie [Databases en aanmeldingen beheren in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)voor meer informatie.

## <a name="connection-timeout-expired-errors"></a>Verlopen time-out van verbinding

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Time-out van verbinding verlopen

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Time-out verlopen

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: De onderliggende provider is mislukt op Openen

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Kan geen verbinding maken met < servernaam >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Deze uitzonderingen kunnen optreden als gevolg van verbindings- of queryproblemen. Zie Bevestigen of een fout wordt veroorzaakt door een verbindingsprobleem om te controleren of deze fout wordt veroorzaakt [door verbindingsproblemen.](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)

Er treden verbindingstime-outs op omdat de toepassing geen verbinding kan maken met de server. Als u dit probleem wilt oplossen, probeert u de stappen (in de weergegeven volgorde) in de [sectie Stappen om veelvoorkomende verbindingsproblemen op te lossen.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Fouten in resourcebeheer

### <a name="error-10928-resource-id-d"></a>Fout 10928: resource-id: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Als u dit probleem wilt oplossen, probeert u een van de volgende methoden:

* Controleer of er langlopende query's zijn.

  > [!NOTE]
  > Dit is een minimalistische aanpak die het probleem mogelijk niet oplost.

1. Voer de volgende SQL-query uit om de [sys.dm_exec_requests-weergave](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) te controleren om blokkeringsverzoeken te zien:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Bepaal de **invoerbuffer** voor de hoofdblokker.
3. Stem de hoofdblokkerquery af.

   Zie [Wordt mijn query in de cloud prima uitgevoerd voor](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)een diepgaande probleemoplossingsprocedure.

Als de database consequent zijn limiet bereikt ondanks het aanpakken van blokkeringen en langlopende query's, u overwegen te upgraden naar een editie met meer resources [Editions](https://azure.microsoft.com/pricing/details/sql-database/)).

Zie [Systeemdynamische beheerweergaven](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)voor meer informatie over dynamische beheerweergaven.

Zie [SQL Database-bronlimieten voor Azure SQL Database-server voor](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)meer informatie over databaselimieten.

### <a name="error-10929-resource-id-1"></a>Fout 10929: Resource-id: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Fout 40501: De service is momenteel bezet

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Dit is een fout in de beperking van de motor, een indicatie dat resourcelimieten worden overschreden.

Zie [Databaseserverbronlimieten](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)voor meer informatie over resourcelimieten.

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fout 40544: De database heeft het groottequotum bereikt

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Deze fout treedt op wanneer de database het groottequotum heeft bereikt.

De volgende stappen kunnen u helpen het probleem te omzeilen of u extra opties bieden:

1. Controleer de huidige grootte van de database met behulp van het dashboard in de Azure-portal.

   > [!NOTE]
   > Voer de volgende SQL-query uit om te bepalen welke tabellen de meeste ruimte verbruiken en daarom potentiële kandidaten zijn voor opschoning:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Als de huidige grootte de maximale grootte die voor uw editie wordt ondersteund, niet overschrijdt, u ALTER DATABASE gebruiken om de MAXSIZE-instelling te verhogen.
3. Als de database al voorbij de maximale ondersteunde grootte voor uw editie is, probeert u een of meer van de volgende stappen:

   * Normale databaseopruimactiviteiten uitvoeren. Ruim bijvoorbeeld de ongewenste gegevens op met het gebruik van truncate/delete of verplaats gegevens met SQL Server Integration Services (SSIS) of het hulpprogramma voor het bulkcopyprogramma (bcp).
   * Gegevens partitioneren of verwijderen, indexen laten vallen of de documentatie raadplegen voor mogelijke resoluties.
   * Zie [Afzonderlijke databaseresources schalen](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) en [Elastische poolbronnen schalen](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)voor databaseschalen.

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fout 40549: sessie wordt beëindigd omdat u een langlopende transactie hebt

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Als u deze fout herhaaldelijk ondervindt, probeert u het probleem op te lossen door de volgende stappen te volgen:

1. Controleer de sys.dm_exec_requests weergave om alle geopende sessies te zien die een hoge waarde hebben voor de total_elapsed_time kolom. Voer deze controle uit door het volgende SQL-script uit te voeren:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Bepaal de invoerbuffer voor de langlopende query.
3. Stem de query af.

Overweeg ook om uw query's te batcheren. Zie [Batching gebruiken om de prestaties van sql database-toepassingen te verbeteren voor](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)informatie over batching.

Zie [Wordt mijn query in de cloud prima uitgevoerd voor](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)een diepgaande probleemoplossingsprocedure.

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fout 40551: De sessie is beëindigd vanwege overmatig TEMPDB-gebruik

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Voer de volgende stappen uit om dit probleem te omzeilen:

1. Wijzig de query's om het gebruik van tijdelijke tabelruimte te verminderen.
2. Laat tijdelijke objecten vallen nadat ze niet meer nodig zijn.
3. Tabellen afkappen of ongebruikte tabellen verwijderen.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fout 40552: De sessie is beëindigd vanwege overmatig gebruik van transactielogboekruimte

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Probeer de volgende methoden om dit op te lossen:

* Het probleem kan optreden als gevolg van invoegbewerkingen, bijwerken of verwijderen.
Probeer het aantal rijen te verminderen dat onmiddellijk wordt uitgevoerd door batching of splitsing in meerdere kleinere transacties te implementeren.
* Het probleem kan optreden als gevolg van indexreconstructiebewerkingen. Als u dit probleem wilt omzeilen, moet u ervoor zorgen dat het aantal rijen dat wordt beïnvloed in de tabel * (gemiddelde grootte van het veld dat is bijgewerkt in bytes + 80) < 2 gigabyte (GB) moet worden beïnvloed.

  > [!NOTE]
  > Voor een indexreconstructie moet de gemiddelde grootte van het veld dat is bijgewerkt, worden vervangen door de gemiddelde indexgrootte.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fout 40553: De sessie is beëindigd vanwege overmatig geheugengebruik

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Als u dit probleem wilt oplossen, probeert u de query te optimaliseren.

Zie [Wordt mijn query in de cloud prima uitgevoerd voor](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)een diepgaande probleemoplossingsprocedure.

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabel met foutberichten voor resourcegovernance

| Foutcode | Severity | Beschrijving |
| ---:| ---:|:--- |
| 10928 |20 |Resource-id: %d. De %s-limiet voor de database is %d en is bereikt. Zie [SQL Database-bronlimieten voor afzonderlijke en samengevoegde databases voor](sql-database-resource-limits-database-server.md)meer informatie.<br/><br/>De resource-id geeft de resource aan die de limiet heeft bereikt. Voor werknemersthreads wordt de resource-id = 1. Voor sessies is de Resource-ID = 2.<br/><br/>Zie voor meer informatie over deze fout en hoe u deze oplossen: <br/>&bull;&nbsp; [Beperkingen van databaseserverbronnen](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-limieten voor afzonderlijke databases](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Resource-id: %d. De minimumgarantie %d, maximumlimiet is %d en het huidige gebruik voor de database is %d. De server is momenteel echter te druk om aanvragen te ondersteunen die groter zijn dan %d voor deze database. De resource-id geeft de resource aan die de limiet heeft bereikt. Voor werknemersthreads wordt de resource-id = 1. Voor sessies is de Resource-ID = 2. Zie voor meer informatie: <br/>&bull;&nbsp; [Beperkingen van databaseserverbronnen](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-limieten voor afzonderlijke databases](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md). <br/>Anders u het later opnieuw proberen. |
| 40544 |20 |De database heeft het groottequotum bereikt. Gegevens partitioneren of verwijderen, indexen laten vallen of de documentatie raadplegen voor mogelijke resoluties. Zie [Afzonderlijke databaseresources schalen](sql-database-single-database-scale.md) en [Elastische poolbronnen schalen](sql-database-elastic-pool-scale.md)voor databaseschalen.|
| 40549 |16 |Sessie wordt beëindigd omdat u een langlopende transactie hebt. Probeer uw transactie te verkorten. Zie [Batching gebruiken om de prestaties van sql database-toepassingen te verbeteren voor](sql-database-use-batching-to-improve-performance.md)informatie over batching.|
| 40550 |16 |De sessie is beëindigd omdat deze te veel sloten heeft aangeschaft. Probeer minder rijen in één transactie te lezen of te wijzigen. Zie [Batching gebruiken om de prestaties van sql database-toepassingen te verbeteren voor](sql-database-use-batching-to-improve-performance.md)informatie over batching.|
| 40551 |16 |De sessie is beëindigd `TEMPDB` vanwege overmatig gebruik. Probeer uw query te wijzigen om het gebruik van de tijdelijke tabelruimte te verminderen.<br/><br/>Als u tijdelijke objecten gebruikt, `TEMPDB` kunt u ruimte in de database opslaan door tijdelijke objecten te laten vallen nadat ze niet meer nodig zijn voor de sessie. Zie [Tempdb-database in SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor meer informatie over tempdb-gebruik in SQL Database.|
| 40552 |16 |De sessie is beëindigd vanwege overmatig gebruik van transactielogboekruimte. Probeer minder rijen in één transactie te wijzigen. Zie [Batching gebruiken om de prestaties van sql database-toepassingen te verbeteren voor](sql-database-use-batching-to-improve-performance.md)informatie over batching.<br/><br/>Als u bulkinserts `bcp.exe` uitvoert met `System.Data.SqlClient.SqlBulkCopy` behulp van `-b batchsize` het `BatchSize` hulpprogramma of de klasse, probeert u de opties of opties te gebruiken om het aantal rijen dat in elke transactie naar de server is gekopieerd, te beperken. Als u een index `ALTER INDEX` met de instructie `REBUILD WITH ONLINE = ON` opnieuw opbouwt, probeert u de optie te gebruiken. Zie voor informatie over transactielogboekformaten voor het vCore-inkoopmodel: <br/>&bull;&nbsp; [vCore-limieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |De sessie is beëindigd vanwege overmatig geheugengebruik. Probeer uw query te wijzigen om minder rijen te verwerken.<br/><br/>Het verminderen `ORDER BY` van `GROUP BY` het aantal en bewerkingen in uw Transact-SQL-code vermindert de geheugenvereisten van uw query. Zie [Afzonderlijke databaseresources schalen](sql-database-single-database-scale.md) en [Elastische poolbronnen schalen](sql-database-elastic-pool-scale.md)voor databaseschalen.|

## <a name="elastic-pool-errors"></a>Elastische poolfouten

De volgende fouten zijn gerelateerd aan het maken en gebruiken van elastische pools:

| Foutcode | Severity | Beschrijving | Corrigerende maatregelen |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |De elastische pool heeft zijn opslaglimiet bereikt. Het opslaggebruik voor de elastische pool mag niet hoger zijn dan (%d) GB's. Probeer gegevens naar een database te schrijven wanneer de opslaglimiet van de elastische groep is bereikt. Zie voor informatie over resourcelimieten: <br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> |Overweeg om de DTU's van en/of opslag aan de elastische pool zo mogelijk te verhogen om de opslaglimiet te verhogen, de opslag die wordt gebruikt door afzonderlijke databases in de elastische groep te verminderen of databases uit de elastische groep te verwijderen. Zie [Elastische poolresources schalen](sql-database-elastic-pool-scale.md)voor elastische poolschaling.|
| 10929 | 16 |De minimumgarantie %d, maximumlimiet is %d en het huidige gebruik voor de database is %d. De server is momenteel echter te druk om aanvragen te ondersteunen die groter zijn dan %d voor deze database. Zie voor informatie over resourcelimieten: <br/>&bull;&nbsp; [DTU-limieten voor elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-limieten voor elastische pools.](sql-database-vcore-resource-limits-elastic-pools.md) <br/> Anders u het later opnieuw proberen. DTU / vCore min per database; DTU / vCore max per database. Het totale aantal gelijktijdige werknemers (aanvragen) in alle databases in de elastische groep heeft geprobeerd de limiet voor de groep te overschrijden. |Overweeg om indien mogelijk de DTU's of vCores van de elastische groep te verhogen om de werknemerslimiet te verhogen of databases uit de elastische groep te verwijderen. |
| 40844 | 16 |Database '%ls' op Server '%ls' is een '%ls' editie database in een elastische pool en kan geen continue kopieerrelatie hebben.  |N.v.t. |
| 40857 | 16 |Elastische pool niet gevonden voor server: '%ls', elastische poolnaam: '%ls'. Opgegeven elastische pool bestaat niet in de opgegeven server. | Geef een geldige elastische poolnaam op. |
| 40858 | 16 |Elastic pool '%ls' bestaat al in server: '%ls'. Opgegeven elastische groep bestaat al in de opgegeven SQL Database-server. | Geef een nieuwe elastische poolnaam op. |
| 40859 | 16 |Elastic pool biedt geen ondersteuning voor servicelaag '%ls'. Opgegeven servicelaag wordt niet ondersteund voor het inrichten van elastische zwembaden. |Geef de juiste editie op of laat de servicelaag leeg om de standaardservicelaag te gebruiken. |
| 40860 | 16 |De combinatie '%ls' en servicedoelstelling '%ls' is ongeldig. Elastische pool en servicelaag kunnen alleen samen worden opgegeven als resourcetype is opgegeven als 'ElasticPool'. |Geef de juiste combinatie van elastische pool en servicelaag op. |
| 40861 | 16 |De database editie '%. *ls' kan niet anders zijn dan de elastische poolservicelaag die '' is.* ls'. De database-editie is anders dan de elastische poolservicelaag. |Geef geen database-editie op die anders is dan de elastische groepservicelaag.  Houd er rekening mee dat de database-editie niet hoeft te worden opgegeven. |
| 40862 | 16 |De naam van de elastische pool moet worden opgegeven als de doelstelling voor de elastische poolservice is opgegeven. Elastische poolservicedoelstelling identificeert niet op unieke wijze een elastische pool. |Geef de naam van de elastische pool op als u de doelstelling elastische poolservice gebruikt. |
| 40864 | 16 |De DTU's voor de elastische pool moeten ten minste (%d) DTU's zijn voor servicelaag '%.*ls'. In een poging om de DTO's in te stellen voor de elastische pool onder de minimumlimiet. |Probeer de DTO's voor de elastische pool opnieuw in te stellen op ten minste de minimumlimiet. |
| 40865 | 16 |De DTU's voor de elastische pool mogen niet hoger zijn dan (%d) DTU's voor servicelaag '%.*ls'. In een poging om de DTO's in te stellen voor de elastische pool boven de maximale limiet. |Probeer de DTU's voor de elastische pool opnieuw in te stellen op niet groter dan de maximumlimiet. |
| 40867 | 16 |De DTU max per database moet ten minste (%d) zijn voor servicetier '%.*ls'. In een poging om de DTU max per database onder de ondersteunde limiet in te stellen. | Overweeg de elastische poolservicelaag te gebruiken die de gewenste instelling ondersteunt. |
| 40868 | 16 |De DTU max per database mag niet hoger zijn dan (%d) voor servicetier '%.*ls'. Proberen om de DTU max per database in te stellen buiten de ondersteunde limiet. | Overweeg de elastische poolservicelaag te gebruiken die de gewenste instelling ondersteunt. |
| 40870 | 16 |De DTU min per database mag niet hoger zijn dan (%d) voor servicelaag '%.*ls'. Proberen om de DTU min per database in te stellen buiten de ondersteunde limiet. | Overweeg de elastische poolservicelaag te gebruiken die de gewenste instelling ondersteunt. |
| 40873 | 16 |Het aantal databases (%d) en DTU min per database (%d) mag niet hoger zijn dan de DTU's van de elastische groep (%d). Probeer DTU min op te geven voor databases in de elastische groep die de DTU's van de elastische groep overschrijdt. | Overweeg de DTU's van de elastische groep te verhogen of de DTU-min per database te verlagen of het aantal databases in de elastische groep te verlagen. |
| 40877 | 16 |Een elastische groep kan niet worden verwijderd, tenzij deze geen databases bevat. De elastische pool bevat een of meer databases en kan daarom niet worden verwijderd. |Verwijder databases uit de elastische groep om deze te verwijderen. |
| 40881 | 16 |De elastische pool '%.*ls' heeft de limiet voor het aantal gegevens bereikt.  De limiet voor het aantal gegevens voor de elastische groep mag niet hoger zijn dan (%d) voor een elastische pool met (%d) DTU's. Probeer database te maken of toe te voegen aan elastische groep wanneer de limiet voor het aantal gegevens van de elastische groep is bereikt. | Overweeg om indien mogelijk de DTU's van de elastische pool te verhogen om de databaselimiet te verhogen of databases uit de elastische groep te verwijderen. |
| 40889 | 16 |De DTU's of opslaglimiet voor de elastische pool '%.*ls' kunnen niet worden verlaagd, omdat dit onvoldoende opslagruimte voor de databases zou bieden. In een poging om de opslaglimiet van de elastische pool onder het opslaggebruik te verlagen. | Overweeg het opslaggebruik van afzonderlijke databases in de elastische groep te verminderen of databases uit de groep te verwijderen om de DTU's of opslaglimiet te verlagen. |
| 40891 | 16 |De DTU min per database (%d) mag de DTU max per database (%d) niet overschrijden. Proberen om de DTU min per database hoger dan de DTU max per database in te stellen. |Zorg ervoor dat de DTU min per databases de DTU max per database niet overschrijdt. |
| NOG TE BEPALEN | 16 |De opslaggrootte voor een afzonderlijke database in een elastische groep mag niet hoger zijn dan de maximale grootte die is toegestaan door de elastische groep '%.*ls'. De maximale grootte voor de database overschrijdt de maximale grootte die is toegestaan door de elastische poolservicelaag. |Stel de maximale grootte van de database in binnen de grenzen van de maximale grootte die is toegestaan door de elastische poolservicelaag. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Kan niet openen database "master" gevraagd door de login. De aanmelding is mislukt

Dit probleem treedt op omdat het account geen toestemming heeft om toegang te krijgen tot de hoofddatabase. Maar standaard probeert SQL Server Management Studio (SSMS) verbinding te maken met de hoofddatabase.

Volg deze stappen om dit probleem op te lossen:

1. Selecteer op het aanmeldingsscherm van SSMS **Opties**en selecteer **Verbindingseigenschappen**.
2. Voer in het veld **Verbinding maken met de database** de standaarddatabasenaam van de gebruiker in als de standaardinlogdatabase en selecteer Verbinding **maken**.

   ![Verbindingseigenschappen](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Controleren of een fout wordt veroorzaakt door een verbindingsprobleem

Als u wilt controleren of een fout wordt veroorzaakt door een verbindingsprobleem, controleert u de stacktracering voor frames die oproepen weergeven om een verbinding te openen zoals de volgende (let op de verwijzing naar de klasse **SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Wanneer de uitzondering wordt geactiveerd door queryproblemen, ziet u een callstack die vergelijkbaar is met de volgende (let op de verwijzing naar de klasse **SqlCommand).** Stem in deze situatie [uw vragen af.](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx)

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Zie de volgende bronnen voor aanvullende richtlijnen voor het verfijnen van de prestaties:

* [Azure SQL-indexen en -statistieken onderhouden](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Queryprestaties handmatig afstemmen in Azure SQL-database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Prestaties Azure SQL Database bewaken met dynamische beheerweergaven](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [De Query Store bedienen in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Stappen voor het oplossen van algemene verbindingsproblemen

1. Controleer of TCP/IP is ingeschakeld als clientprotocol op de toepassingsserver. Zie [Clientprotocollen configureren](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)voor meer informatie . Controleer op toepassingsservers waar sql server-hulpprogramma's niet zijn geïnstalleerd, of TCP/IP is ingeschakeld door **cliconfg.exe** (SQL Server Client Network utility) uit te voeren.
2. Controleer de verbindingstekenreeks van de toepassing om te controleren of deze correct is geconfigureerd. Zorg er bijvoorbeeld voor dat de verbindingstekenreeks de juiste poort (1433) en de volledig gekwalificeerde servernaam opgeeft.
Zie [SQL Server-verbindingsgegevens opvragen](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Probeer de time-outwaarde van de verbinding te verhogen. We raden u aan een time-out van de verbinding van ten minste 30 seconden te gebruiken.
4. Test de connectiviteit tussen de toepassingsserver en de Azure SQL-database met SQL [Server management Studio (SSMS),](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)een UDL-bestand, ping of telnet. Zie [Problemen met SQL Server-connectiviteit oplossen](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) en Diagnostische problemen voor [connectiviteitsproblemen voor](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)meer informatie.

   > [!NOTE]
   > Als probleemoplossingsstap u de connectiviteit ook testen op een andere clientcomputer.

5. Als een best practice, zorg ervoor dat de retry logica is op zijn plaats. Zie [Tijdelijke fouten en verbindingsfouten in SQL-database oplossen](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)voor meer informatie over logica opnieuw proberen.

Als deze stappen uw probleem niet oplossen, probeert u meer gegevens te verzamelen en neemt u contact op met de ondersteuning. Als uw toepassing een cloudservice is, schakelt u logboekregistratie in. Met deze stap wordt een UTC-tijdstempel van de fout geretourneerd. Bovendien retourneert SQL Azure de tracerings-id. [Microsoft Customer Support Services](https://azure.microsoft.com/support/options/) kan deze informatie gebruiken.

Zie [Logboekregistratie voor diagnostische gegevens inschakelen voor apps in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)voor meer informatie over het inschakelen van logboekregistratie.

## <a name="next-steps"></a>Volgende stappen

* [Azure SQL-connectiviteitsarchitectuur](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Azure SQL Database- en Data Warehouse-netwerktoegangsbesturingselementen](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)

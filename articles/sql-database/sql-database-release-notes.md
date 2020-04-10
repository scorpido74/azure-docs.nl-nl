---
title: Releaseopmerkingen
description: Meer informatie over de nieuwe functies en verbeteringen in de Azure SQL Database-service en in de Azure SQL Database-documentatie
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: sstein
ms.openlocfilehash: b677fd7fe2b14e1c42443478a887ddfa2481dfbf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011443"
---
# <a name="sql-database-release-notes"></a>SQL Database release notes SQL Database release notes SQL Database release notes SQL Database

In dit artikel worden SQL Database-functies weergegeven die momenteel in openbare preview staan. Zie [SQL Database-serviceupdates](https://azure.microsoft.com/updates/?product=sql-database)voor SQL Database-updates. Zie [Service-updates](https://azure.microsoft.com/updates)voor updates en verbeteringen in andere Azure-services.

## <a name="features-in-public-preview"></a>Functies in openbare preview

### <a name="single-database"></a>[Eén database](#tab/single-database)

| Functie | Details |
| ---| --- |
| Nieuwe Fsv2-serie en M-serie hardware generaties| Zie [Hardwaregeneraties voor](sql-database-service-tiers-vcore.md#hardware-generations)meer informatie.|
| Versneld databaseherstel met afzonderlijke databases en elastische pools | Zie Versneld [databaseherstel](sql-database-accelerated-database-recovery.md)voor meer informatie .|
|Geschatte telling onderscheiden|Zie Geschatte [telling distinct voor](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)informatie.|
|Batchmodus op Rowstore (onder compatibiliteitsniveau 150)|Zie [Batchmodus in Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)voor meer informatie.|
| Gegevensdetectie en -classificatie  |Zie Azure [SQL Database en SQL Data Warehouse-gegevensdetectie & classificatie](sql-database-data-discovery-and-classification.md)voor informatie.|
| Taken voor Elastic Database | Zie [Elastische taken maken, configureren en beheren](elastic-jobs-overview.md)voor informatie. |
| Elastische query’s | Zie Overzicht [van elastische query's voor](sql-database-elastic-query-overview.md)informatie. |
| Elastische transacties | [Gedistribueerde transacties over clouddatabases](sql-database-elastic-transactions-overview.md). |
|Feedback voor geheugensubsidie (rijmodus) (onder compatibiliteitsniveau 150)|Zie Feedback [voor geheugenverlening (rijmodus)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)voor meer informatie.|
| Queryeditor in de Azure-portal |Zie De [SQL-queryeditor van de Azure-portal gebruiken om verbinding te maken en querygegevens op te vragen.](sql-database-connect-query-portal.md)|
| R-services / machine learning met afzonderlijke databases en elastische pools |Zie Machine [Learning Services in Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)voor meer informatie.|
|SQL Analytics|Zie Azure [SQL Analytics](../azure-monitor/insights/azure-sql.md)voor informatie.|
|Tabel variabele uitgestelde compilatie (onder compatibiliteitsniveau 150)|Zie [Tabelvariabele uitgestelde compilatie voor](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)informatie .|
| &nbsp; |

### <a name="managed-instance"></a>[Beheerde instantie](#tab/managed-instance)

| Functie | Details |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Exemplaargroepen</a> | Een handige en kostenefficiënte manier om kleinere SQL-exemplaren naar de cloud te migreren. |
| <a href="https://aka.ms/managed-instance-aadlogins">Azure AD-serverprincipals op instantieniveau (aanmeldingen)</a> | Aanmeldingen op serverniveau maken met <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">de instructie LOGIN MAKEN van externe provider.</a> |
| [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md) | Repliceer de wijzigingen van uw tabellen in andere databases die zijn geplaatst in beheerde exemplaren, afzonderlijke databases of SQL Server-instanties of werk uw tabellen bij wanneer sommige rijen worden gewijzigd in andere beheerde instanties of SQL Server-exemplaren. Zie Replicatie [configureren in een azure SQL Database managed instance database](replication-with-sql-database-managed-instance.md)voor informatie. |
| Detectie van bedreigingen |Zie [Bedreigingsdetectie configureren in azure SQL Database managed instance](sql-database-managed-instance-threat-detection.md)voor informatie.|

---

## <a name="managed-instance---new-features-and-known-issues"></a>Beheerde instantie - nieuwe functies en bekende problemen

### <a name="managed-instance-h2-2019-updates"></a>Beheerde exemplaar H2 2019-updates

- [Subnetconfiguratie met servicehulp](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) Een veilige en handige manier om subnetconfiguratie te beheren waar u het dataverkeer beheert terwijl de beheerde instantie zorgt voor een ononderbroken stroom van beheerverkeer
- [Transparent data encryption (TDE) met Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) maakt bring-your-own-key (BYOK) scenario voor gegevensbescherming in rust mogelijk en stelt organisaties in staat om beheertaken voor sleutels en gegevens te scheiden.
- [Met groepen voor automatisch mislukken](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) u alle databases van de primaire instantie repliceren naar een secundaire instantie in een andere regio.
- Configureer uw gedrag van beheerde instantie met [globale traceervlaggen](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Beheerde exemplaar H1 2019-updates

De volgende functies zijn ingeschakeld in het implementatiemodel voor beheerde instantie in H1 2019:
  - Ondersteuning voor abonnementen met <a href="https://aka.ms/sql-mi-visual-studio-subscribers">maandelijks tegoed van Azure voor Visual Studio-abonnees</a> en verhoogde [regionale limieten](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Ondersteuning voor <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 en SharePoint 2019 </a> en <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Maak instanties met <a href="https://aka.ms/managed-instance-collation">een band op serverniveau</a> en <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">tijdzone</a> naar keuze.
  - Beheerde exemplaren zijn nu beveiligd met <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">ingebouwde firewall.</a>
  - Configureer instanties om [openbare eindpunten](sql-database-managed-instance-public-endpoint-configure.md)te gebruiken , [Proxy override-verbinding](sql-database-connectivity-architecture.md#connection-policy) om betere netwerkprestaties te krijgen, <a href="https://aka.ms/four-cores-sql-mi-update">4 vCores op Gen5-hardwaregeneratie</a> of <a href="https://aka.ms/managed-instance-configurable-backup-retention">Back-upretentie configureren tot 35 dagen</a> voor point-in-time herstel. Lange termijn back-upretentie (tot 10 jaar) is nog steeds niet ingeschakeld, zodat u <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">copy-only back-ups</a> als alternatief gebruiken.
  - Met nieuwe functionaliteiten u <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">uw database geo-restoren naar een ander datacenter met PowerShell,</a> [de naam van de database wijzigen,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/) [virtuele cluster verwijderen](sql-database-managed-instance-delete-virtual-cluster.md).
  - De nieuwe ingebouwde [rol van instance contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) maakt de naleving van beveiligingsprincipes en naleving van bedrijfsstandaarden mogelijk door de SoD (SoD).
  - Beheerde instantie is beschikbaar in de volgende Azure Government-regio's naar GA (US Gov Texas, US Gov Arizona) evenals in China North 2 en China East 2. Het is ook beschikbaar in de volgende openbare regio's: Australia Central, Australia Central 2, Brazilië Zuid, Frankrijk Zuid, VAE Centraal, VAE Noord, Zuid-Afrika Noord, Zuid-Afrika West.

### <a name="known-issues"></a>Bekende problemen

|Probleem  |Datum ontdekt  |Status  |Datum opgelost  |
|---------|---------|---------|---------|
|[Machtigingen voor resourcegroep die niet zijn toegepast op Beheerde instantie](#permissions-on-resource-group-not-applied-to-managed-instance)|Februari 2020|Heeft tijdelijke oplossing||
|[Beperking van handmatige failover via portal voor failovergroepen](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Heeft tijdelijke oplossing||
|[SQL Agent-rollen hebben expliciete UITVOER-machtigingen nodig voor niet-sysadmin-aanmeldingen](#in-memory-oltp-memory-limits-are-not-applied)|December 2019|Heeft tijdelijke oplossing||
|[SQL Agent-taken kunnen worden onderbroken door opnieuw opstarten van het Agent-proces](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|December 2019|Geen tijdelijke oplossing|Maart 2020|
|[AAD logins en gebruikers worden niet ondersteund in SSDT](#aad-logins-and-users-are-not-supported-in-ssdt)|November 2019|Geen tijdelijke oplossing||
|[In-memory OLTP-geheugenlimieten worden niet toegepast](#in-memory-oltp-memory-limits-are-not-applied)|Okt 2019|Heeft tijdelijke oplossing||
|[Fout fout geretourneerd terwijl u een bestand probeert te verwijderen dat niet leeg is](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Okt 2019|Heeft tijdelijke oplossing||
|[Servicelaag wijzigen en instantiebewerkingen maken, worden geblokkeerd door doorlopend databaseherstel](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Heeft tijdelijke oplossing||
|[Resource Governor op Business Critical service tier kan nodig zijn om opnieuw te worden geconfigureerd na failover](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Heeft tijdelijke oplossing||
|[Dialoogvensters voor servicebroker met meerdere gegevens moeten opnieuw worden geïnitialiseerd na de upgrade van de servicelaag](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|Augustus 2019|Heeft tijdelijke oplossing||
|[Impersonificatie van Azure AD-aanmeldingstypen wordt niet ondersteund](#impersonification-of-azure-ad-login-types-is-not-supported)|Jul 2019|Geen tijdelijke oplossing||
|[@queryparameter die niet wordt ondersteund in sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|April 2019|Geen tijdelijke oplossing||
|[Transactionele replicatie moet opnieuw worden geconfigureerd na geo-failover](#transactional-replication-must-be-reconfigured-after-geo-failover)|Maart 2019|Geen tijdelijke oplossing||
|[Tijdelijke database wordt gebruikt tijdens RESTORE-bewerking](#temporary-database-is-used-during-restore-operation)||Heeft tijdelijke oplossing||
|[TEMPDB-structuur en -inhoud worden opnieuw gemaakt](#tempdb-structure-and-content-is-re-created)||Geen tijdelijke oplossing||
|[Opslagruimte met kleine databasebestanden overschrijden](#exceeding-storage-space-with-small-database-files)||Heeft tijdelijke oplossing||
|[GUID-waarden weergegeven in plaats van databasenamen](#guid-values-shown-instead-of-database-names)||Heeft tijdelijke oplossing||
|[Foutlogboeken worden niet gehandhaafd](#error-logs-arent-persisted)||Geen tijdelijke oplossing||
|[Transactiebereik in twee databases binnen dezelfde instantie wordt niet ondersteund](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Heeft tijdelijke oplossing||
|[CLR-modules en gekoppelde servers kunnen soms niet verwijzen naar een lokaal IP-adres](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Heeft tijdelijke oplossing||
|Databaseconsistentie niet geverifieerd met DBCC CHECKDB na herstel database uit Azure Blob Storage.||Opgelost|November 2019|
|Point-in-time database restore from Business Critical tier to General Purpose tier will not succeed if source database contains in-memory OLTP objects.||Opgelost|Okt 2019|
|DatabaseMail-functie met externe (niet-Azure) e-mailservers met behulp van een beveiligde verbinding||Opgelost|Okt 2019|
|Opgenomen databases die niet worden ondersteund in beheerde instantie||Opgelost|Augustus 2019|

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>Machtigingen voor resourcegroep die niet zijn toegepast op beheerde instantie

RBAC-rol managed instance contributor wanneer deze wordt toegepast op een resourcegroep (RG) wordt niet toegepast op Beheerde instantie en heeft geen effect.

**Tijdelijke oplossing:** rol voor beheerde instantieinzender voor gebruikers op abonnementsniveau.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Beperking van handmatige failover via portal voor failovergroepen

Als failovergroep oververschillende exemplaren in verschillende Azure-abonnementen of resourcegroepen overspant, kan handmatige failover niet worden gestart vanuit de primaire instantie in de failovergroep.

**Tijdelijke oplossing**: Failover starten via portal van de geo-secundaire instantie.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Agent-rollen hebben expliciete UITVOER-machtigingen nodig voor niet-sysadmin-aanmeldingen

Als niet-sysadmin-aanmeldingen worden toegevoegd aan een van [de vaste databaserollen](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)van SQL Agent, bestaat er een probleem waarbij expliciete UITVOER-machtigingen moeten worden verleend aan de hoofdopgeslagen procedures voor deze aanmeldingen om te werken. Als dit probleem wordt aangetroffen, wordt het foutbericht 'De uitvoermachtiging is geweigerd op het object <object_name> (Microsoft SQL Server, Error: 229)" weergegeven.

**Tijdelijke oplossing:** Zodra u aanmeldingen toevoegt aan een van de vaste databaserollen van SQL Agent: SQLAgentUserRole, SQLAgentReaderRole of SQLAgentOperatorRole, voert u voor elk van de aanmeldingen die aan deze rollen zijn toegevoegd het onderstaande T-SQL-script uit om UITVOER-machtigingen expliciet toe te kennen aan de vermelde opgeslagen procedures.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Agent-taken kunnen worden onderbroken door opnieuw opstarten van het Agent-proces

SQL Agent maakt een nieuwe sessie telkens wanneer taak wordt gestart, waardoor het geheugenverbruik geleidelijk toeneemt. Om te voorkomen dat de interne geheugenlimiet wordt bereikt die de uitvoering van geplande taken zou blokkeren, wordt het agentproces opnieuw gestart zodra het geheugenverbruik de drempelwaarde bereikt. Dit kan leiden tot het onderbreken van de uitvoering van taken die worden uitgevoerd op het moment van opnieuw opstarten.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>In-memory OLTP-geheugenlimieten worden niet toegepast

Business Critical service-tier past in sommige gevallen geen [maximale geheugenlimieten toe voor geheugengeoptimaliseerde objecten.](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space) Beheerde instantie kan workload inschakelen om meer geheugen te gebruiken voor OLTP-bewerkingen in het geheugen, wat van invloed kan zijn op de beschikbaarheid en stabiliteit van de instantie. Oltp-query's in het geheugen die de limieten bereiken, mislukken mogelijk niet onmiddellijk. Dit probleem zal binnenkort worden opgelost. De query's die meer IN-memory OLTP-geheugen gebruiken, mislukken eerder als ze de [limieten](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)bereiken.

**Tijdelijke oplossing:** [controleer het OLTP-opslaggebruik in het geheugen](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring) met SQL Server Management [Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) om ervoor te zorgen dat de werkbelasting niet meer gebruikt dan het beschikbare geheugen. Verhoog de geheugenlimieten die afhankelijk zijn van het aantal vCores of optimaliseer uw werkbelasting om minder geheugen te gebruiken.

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Fout fout geretourneerd terwijl u een bestand probeert te verwijderen dat niet leeg is

SQL Server/Managed Instance [staat niet toe dat de gebruiker een bestand laat vallen dat niet leeg is.](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites) Als u een niet-leeg gegevensbestand `ALTER DATABASE REMOVE FILE` probeert te `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` verwijderen met een instructie, wordt de fout niet onmiddellijk geretourneerd. Managed Instance blijft proberen om het bestand te laten vallen `Internal server error`en de bewerking zal mislukken na 30 min met .

**Tijdelijke oplossing:** verwijder de inhoud `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` van het bestand met de opdracht. Als dit het enige bestand in de bestandsgroep is, moet u gegevens uit de tabel of partitie verwijderen die aan deze bestandsgroep zijn gekoppeld voordat u het bestand krimpt en deze gegevens optioneel in een andere tabel/partitie laden.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Servicelaag wijzigen en instantiebewerkingen maken, worden geblokkeerd door doorlopend databaseherstel

Lopende `RESTORE` instructie, het migratieproces van de Gegevensmigratieservice en het ingebouwde inpoint-in-time-herstel blokkeren de updateservicelaag of het formaat van de bestaande instantie wijzigen en nieuwe exemplaren maken totdat het herstelproces is voltooid. Het herstelproces blokkeert deze bewerkingen op de beheerde instanties en instantiegroepen in hetzelfde subnet waar het herstelproces wordt uitgevoerd. De instanties in instantiegroepen worden niet beïnvloed. Bewerkingen van servicelagen maken of wijzigen mislukken niet of een time-out : ze gaan door zodra het herstelproces is voltooid of geannuleerd.

**Tijdelijke oplossing:** wacht tot het herstelproces is voltooid of annuleer het herstelproces als de bewerking servicelaag maken of bijwerken een hogere prioriteit heeft.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor op Business Critical service tier kan nodig zijn om opnieuw te worden geconfigureerd na failover

[Resource Governor-functie](/sql/relational-databases/resource-governor/resource-governor) waarmee u de resources die aan de gebruikersworkload zijn toegewezen, kan een bepaalde gebruikerswerklast onjuist classificeren na een mislukte of door de gebruiker geïnitieerde wijziging van de servicelaag (bijvoorbeeld de wijziging van de maximale vCore- of maximale opslaggrootte van bijvoorbeeld instance).

**Tijdelijke oplossing:** voer `ALTER RESOURCE GOVERNOR RECONFIGURE` periodiek of als onderdeel van SQL Agent Job uit die de SQL-taak uitvoert wanneer de instantie wordt gestart als u [Resource Governor gebruikt.](/sql/relational-databases/resource-governor/resource-governor)

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>Dialoogvensters voor servicebroker met meerdere gegevens moeten opnieuw worden geïnitialiseerd na de upgrade van de servicelaag

Dialogen met servicebroker met meerdere databases stoppen met het leveren van de berichten aan de services in andere databases na wijziging van de servicelaagwerking. De berichten gaan **niet verloren** en zijn te vinden in de afzenderwachtrij. Elke wijziging van vCores of instantieopslaggrootte `service_broke_guid` in Beheerde instantie zorgt ervoor dat de weergave waarde in [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) voor alle databases wordt gewijzigd. Elke `DIALOG` gemaakte met behulp [van BEGIN DIALOG-instructie](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) die verwijst naar Service Brokers in andere database, stopt met het leveren van berichten aan de doelservice.

**Tijdelijke oplossing:** Stop alle activiteiten die dialogen met cross-database Service Broker gebruiken voordat u de servicelaag bijwerkt en deze daarna opnieuw initialiseert. Als er nog berichten zijn die niet worden geleverd na wijziging van de servicelaag, leest u de berichten uit de bronwachtrij en stuurt u deze opnieuw naar de doelwachtrij.

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>Impersonificatie van Azure AD-aanmeldingstypen wordt niet ondersteund

Imitatie met `EXECUTE AS USER` of `EXECUTE AS LOGIN` van het volgen van AAD-principals wordt niet ondersteund:
-   Aad-gebruikers met aliassen. De volgende fout wordt `15517`in dit geval geretourneerd .
- AAD logins en gebruikers op basis van AAD applicaties of service principals. De volgende fouten worden `15517` in `15406`dit geval geretourneerd en .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryparameter die niet wordt ondersteund in sp_send_db_mail

De `@query` parameter in de [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedure werkt niet.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>Transactionele replicatie moet opnieuw worden geconfigureerd na geo-failover

Als Transactionele replicatie is ingeschakeld in een database in een groep voor automatische failover, moet de beheerde instantiebeheerder alle publicaties op het oude primaire bestandgebied opschonen en opnieuw configureren op het nieuwe primaire voorhoofd nadat een failover naar een ander gebied is uitgevoerd. Zie [Replicatie](sql-database-managed-instance-transact-sql-information.md#replication) voor meer informatie.

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>AAD logins en gebruikers worden niet ondersteund in SSDT

SQL Server Data Tools ondersteunen azure active directory logins en gebruikers niet volledig.

### <a name="temporary-database-is-used-during-restore-operation"></a>Tijdelijke database wordt gebruikt tijdens RESTORE-bewerking

Wanneer een database wordt hersteld op Beheerde instantie, maakt de herstelservice eerst een lege database met de gewenste naam om de naam aan de instantie toe te wijzen. Na enige tijd wordt deze database verwijderd en wordt het herstellen van de werkelijke database gestart. De database die zich in *herstelstatus bevindt,* heeft tijdelijk een willekeurige GUID-waarde in plaats van de naam. De tijdelijke naam wordt gewijzigd in `RESTORE` de gewenste naam die in een instructie is opgegeven zodra het herstelproces is voltooid. In de beginfase heeft de gebruiker toegang tot de lege database en kan zelfs tabellen maken of gegevens laden in deze database. Deze tijdelijke database wordt verwijderd wanneer de herstelservice de tweede fase start.

**Tijdelijke oplossing:** heb geen toegang tot de database die u herstelt totdat u ziet dat herstel is voltooid.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-structuur en -inhoud worden opnieuw gemaakt

De `tempdb` database is altijd opgesplitst in 12 gegevensbestanden en de bestandsstructuur kan niet worden gewijzigd. De maximale grootte per bestand kan niet worden gewijzigd `tempdb`en nieuwe bestanden kunnen niet worden toegevoegd aan . `Tempdb`wordt altijd opnieuw gemaakt als een lege database wanneer de instantie `tempdb` wordt gestart of mislukt, en eventuele wijzigingen die zijn aangebracht in niet worden bewaard.

### <a name="exceeding-storage-space-with-small-database-files"></a>Opslagruimte met kleine databasebestanden overschrijden

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`en `RESTORE DATABASE` instructies kunnen mislukken omdat de instantie de Azure Storage-limiet kan bereiken.

Elk algemeen doel beheerde instantie heeft maximaal 35 TB opslagruimte gereserveerd voor Azure Premium Schijfruimte. Elk databasebestand wordt op een afzonderlijke fysieke schijf geplaatst. Schijfformaten kunnen 128 GB, 256 GB, 512 GB, 1 TB of 4 TB bedragen. Ongebruikte ruimte op de schijf wordt niet in rekening gebracht, maar de totale som van Azure Premium-schijfformaten mag niet hoger zijn dan 35 TB. In sommige gevallen kan een beheerde instantie die in totaal geen 8 TB nodig heeft, de Azure-limiet van 35 TB voor de opslaggrootte overschrijden als gevolg van interne fragmentatie.

Een algemeen doelbeheerexemplaar kan bijvoorbeeld één groot bestand hebben dat 1,2 TB groot is op een schijf van 4 TB. Het kan ook 248 bestanden met 1 GB formaat elk die worden geplaatst op afzonderlijke 128-GB schijven. In dit voorbeeld:

- De totale toegewezen schijfopslagis 1 x 4 TB + 248 x 128 GB = 35 TB.
- De totale gereserveerde ruimte voor databases op de instantie is 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Dit voorbeeld illustreert dat onder bepaalde omstandigheden, als gevolg van een specifieke verdeling van bestanden, een beheerde instantie de limiet van 35 TB kan bereiken die is gereserveerd voor een gekoppelde Azure Premium Disk wanneer u dit mogelijk niet verwacht.

In dit voorbeeld blijven bestaande databases werken en kunnen ze zonder problemen groeien zolang er geen nieuwe bestanden worden toegevoegd. Nieuwe databases kunnen niet worden gemaakt of hersteld omdat er niet genoeg ruimte is voor nieuwe schijven, zelfs als de totale grootte van alle databases de limiet voor de instantiegrootte niet bereikt. De fout die in dat geval is geretourneerd, is niet duidelijk.

U [het aantal resterende bestanden identificeren](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) met behulp van systeemweergaven. Als u deze limiet bereikt, probeert u een aantal kleinere bestanden te [legen en te verwijderen met de dbcc SHRINKFILE-instructie](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) of schakelt u over naar de [laag Bedrijfskritiek, die deze limiet niet heeft.](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-waarden weergegeven in plaats van databasenamen

Verschillende systeemweergaven, prestatiemeteritems, foutberichten, XEvents- en foutlogboekvermeldingen geven GUID-database-id's weer in plaats van de werkelijke databasenamen. Vertrouw niet op deze GUID-id's omdat ze in de toekomst worden vervangen door werkelijke databasenamen.

**Tijdelijke oplossing**: De weergave sys.databases gebruiken om de werkelijke databasenaam op te lossen van de fysieke databasenaam, opgegeven in de vorm van GUID-database-id's

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Foutlogboeken worden niet gehandhaafd

Foutlogboeken die beschikbaar zijn in beheerde instantie, blijven niet bestaan en de grootte ervan is niet opgenomen in de maximale opslaglimiet. Foutlogboeken kunnen automatisch worden gewist als er een failover optreedt. Er kunnen hiaten zijn in de foutlogboekgeschiedenis omdat Beheerde instantie meerdere keren is verplaatst op verschillende virtuele machines.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Transactiebereik in twee databases binnen dezelfde instantie wordt niet ondersteund

De `TransactionScope` klasse in .NET werkt niet als twee query's worden verzonden naar twee databases binnen dezelfde instantie onder hetzelfde transactiebereik:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Hoewel deze code werkt met gegevens binnen dezelfde instantie, vereist MSDTC.

**Tijdelijke oplossing:** Gebruik [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) om een andere database in een verbindingscontext te gebruiken in plaats van twee verbindingen te gebruiken.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-modules en gekoppelde servers kunnen soms niet verwijzen naar een lokaal IP-adres

CLR-modules die in een beheerde instantie zijn geplaatst en servers of gedistribueerde query's die verwijzen naar een huidige instantie, kunnen soms het IP-adres van een lokale instantie niet oplossen. Deze fout is een tijdelijk probleem.

**Tijdelijke oplossing:** Gebruik indien mogelijk contextverbindingen in een CLR-module.

## <a name="updates"></a>Updates

Zie [SQL Database-serviceupdates](https://azure.microsoft.com/updates/?product=sql-database)voor een lijst met SQL-database-updates.

Zie [Service-updates](https://azure.microsoft.com/updates)voor updates en verbeteringen voor alle Azure-services.

## <a name="contribute-to-content"></a>Bijdragen aan inhoud

Zie de [handleiding voor documenteninleveren](https://docs.microsoft.com/contribute/)om bij te dragen aan de Azure SQL Database-documentatie.

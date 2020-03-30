---
title: Beheerde instantie T-SQL-verschillen
description: In dit artikel worden de T-SQL-verschillen tussen een beheerde instantie in Azure SQL Database en SQL Server besproken
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365484"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Beheerde instantie T-SQL-verschillen en -beperkingen

In dit artikel worden de verschillen in syntaxis en gedrag tussen azure SQL Database managed instance en on-premises SQL Server Database Engine samengevat en uitgelegd. De beheerde optie voor het implementeren van instance biedt een hoge compatibiliteit met on-premises SQL Server Database Engine. De meeste SQL Server-databaseenginefuncties worden ondersteund in een beheerde instantie.

![Migratie](./media/sql-database-managed-instance/migration.png)

Er zijn enkele PaaS-beperkingen die worden geïntroduceerd in Managed Instance en sommige gedragswijzigingen ten opzichte van SQL Server. De verschillen zijn onderverdeeld in de volgende categorieën:<a name="Differences"></a>

- [Beschikbaarheid](#availability) omvat de verschillen in [Always On Availability Groups](#always-on-availability-groups) en [back-ups.](#backup)
- [Beveiliging](#security) omvat de verschillen in [auditing,](#auditing) [certificaten,](#certificates) [referenties,](#credential) [cryptografische providers,](#cryptographic-providers) [logins en gebruikers](#logins-and-users), en de [servicekey en service master key](#service-key-and-service-master-key).
- [Configuratie](#configuration) omvat de verschillen in [buffer pool extensie,](#buffer-pool-extension) [collation,](#collation) [compatibiliteitsniveaus,](#compatibility-levels) [database mirroring,](#database-mirroring) [database opties,](#database-options) [SQL Server Agent](#sql-server-agent), en [tabel opties](#tables).
- [Functionaliteiten](#functionalities) omvatten [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [gedistribueerde transacties,](#distributed-transactions) [uitgebreide gebeurtenissen](#extended-events), [externe bibliotheken](#external-libraries), [filestream en FileTable](#filestream-and-filetable), [full-text semantic search](#full-text-semantic-search), linked [servers](#linked-servers), [PolyBase](#polybase), [Replication](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [opgeslagen procedures, functies en triggers](#stored-procedures-functions-and-triggers).
- [Omgevinginstellingen](#Environment) zoals VNets en subnetconfiguraties.

De meeste van deze functies zijn architectonische beperkingen en vertegenwoordigen servicefuncties.

Tijdelijke bekende problemen die worden ontdekt in beheerde instantie en die in de toekomst zullen worden opgelost, worden beschreven in [de pagina releasenotes](sql-database-release-notes.md).

## <a name="availability"></a>Beschikbaarheid

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Beschikbaarheidsgroepen altijd op beschikbaarheid

[Hoge beschikbaarheid](sql-database-high-availability.md) is ingebouwd in beheerde instantie en kan niet worden gecontroleerd door gebruikers. De volgende instructies worden niet ondersteund:

- [EINDPUNT MAKEN ... VOOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [Beschikbaarheidsgroep MAKEN](/sql/t-sql/statements/create-availability-group-transact-sql)
- [BESCHIKBAARHEIDSGROEP ALTER](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP BESCHIKBAARHEIDSGROEP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- De [SET HADR-clausule](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) van de ALTER [DATABASE-instructie](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Back-up

Beheerde exemplaren hebben automatische back-ups, zodat gebruikers volledige databaseback-ups `COPY_ONLY` kunnen maken. Back-ups van differentiële, logboek- en bestandsmomentopnamen worden niet ondersteund.

- Met een beheerde instantie u een back-up van een instantiedatabase alleen maken naar een Azure Blob-opslagaccount:
  - Alleen `BACKUP TO URL` wordt ondersteund.
  - `FILE`, `TAPE`en back-upapparaten worden niet ondersteund.
- De meeste `WITH` algemene opties worden ondersteund.
  - `COPY_ONLY`is verplicht.
  - `FILE_SNAPSHOT`wordt niet ondersteund.
  - Tapeopties: `REWIND` `NOREWIND`, `UNLOAD`, `NOUNLOAD` en worden niet ondersteund.
  - Logspecifieke opties: `NORECOVERY` `STANDBY`, `NO_TRUNCATE` en worden niet ondersteund.

Beperkingen: 

- Met een beheerde instantie u een back-up maken van een instantiedatabase naar een back-up met maximaal 32 strepen, wat voldoende is voor databases tot 4 TB als back-upcompressie wordt gebruikt.
- U niet `BACKUP DATABASE ... WITH COPY_ONLY` worden uitgevoerd op een database die is versleuteld met door de service beheerde Transparante Gegevensversleuteling (TDE). Service-managed TDE dwingt back-ups te versleutelen met een interne TDE-toets. De sleutel kan niet worden geëxporteerd, dus u de back-up niet herstellen. Gebruik automatische back-ups en point-in-time restore, of gebruik in plaats daarvan [door de klant beheerde (BYOK) TDE.](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) U ook versleuteling in de database uitschakelen.
- De maximale grootte van `BACKUP` de back-upstreep met behulp van de opdracht in een beheerde instantie is 195 GB, wat de maximale blobgrootte is. Verhoog het aantal strepen in de back-upopdracht om de afzonderlijke streepgrootte te verkleinen en binnen deze limiet te blijven.

    > [!TIP]
    > Als u deze beperking wilt omzeilen, u het doen als u een back-up maakt van een database van SQL Server in een on-premises omgeving of in een virtuele machine:
    >
    > - Back-up `DISK` naar in `URL`plaats van een back-up naar .
    > - Upload de back-upbestanden naar Blob-opslag.
    > - Herstellen in de beheerde instantie.
    >
    > De `Restore` opdracht in een beheerde instantie ondersteunt grotere blobformaten in de back-upbestanden omdat een ander blobtype wordt gebruikt voor de opslag van de geüploade back-upbestanden.

Zie [BACK-UP en back-ups voor](/sql/t-sql/statements/backup-transact-sql)informatie over back-ups met T-SQL.

## <a name="security"></a>Beveiliging

### <a name="auditing"></a>Controleren

De belangrijkste verschillen tussen controle in databases in Azure SQL Database en databases in SQL Server zijn:

- Met de beheerde optie voor het implementeren van instance in Azure SQL Database werkt auditing op serverniveau. De `.xel` logboekbestanden worden opgeslagen in Azure Blob-opslag.
- Met de opties voor één database en elastische poolimplementatie in Azure SQL Database werkt auditing op databaseniveau.
- In sql server on-premises of virtuele machines werkt auditing op serverniveau. Gebeurtenissen worden opgeslagen in bestandssysteem- of Windows-gebeurtenislogboeken.
 
XEvent-controle in beheerde instantie ondersteunt Azure Blob-opslagdoelen. Bestands- en Windows-logboeken worden niet ondersteund.

De belangrijkste verschillen `CREATE AUDIT` in de syntaxis voor controle naar Azure Blob-opslag zijn:

- Er wordt `TO URL` een nieuwe syntaxis opgegeven die u gebruiken `.xel` om de URL op te geven van de Azure Blob-opslagcontainer waar de bestanden worden geplaatst.
- De `TO FILE` syntaxis wordt niet ondersteund omdat een beheerde instantie geen toegang heeft tot Windows-bestandsshares.

Zie voor meer informatie: 

- [SERVERAUDIT MAKEN](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [CONTROLE VAN DE SERVER WIJZIGEN](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controleren](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificaten

Een beheerde instantie heeft geen toegang tot bestandsshares en Windows-mappen, dus de volgende beperkingen zijn van toepassing:

- `CREATE FROM` / Het `BACKUP TO` bestand wordt niet ondersteund voor certificaten.
- Het `CREATE` / / `ASSEMBLY` certificaat `FILE` van wordt niet ondersteund. `BACKUP` Privésleutelbestanden kunnen niet worden gebruikt. 

Zie [CERTIFICAAT MAKEN](/sql/t-sql/statements/create-certificate-transact-sql) en EEN [BACK-UPCERTIFICAAT .](/sql/t-sql/statements/backup-certificate-transact-sql) 
 
**Tijdelijke oplossing:** In plaats van back-up van het certificaat te maken en de back-up te herstellen, [krijgt u de binaire inhoud en privésleutel van het certificaat, slaat u het op als .sql-bestand en maakt u vanuit binaire:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Referentie

Alleen Azure Key `SHARED ACCESS SIGNATURE` Vault en identiteiten worden ondersteund. Windows-gebruikers worden niet ondersteund.

Zie [REFERENTIE MAKEN](/sql/t-sql/statements/create-credential-transact-sql) en REFERENTIE [WIJZIGEN](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Cryptografische providers

Een beheerde instantie heeft geen toegang tot bestanden, dus cryptografische providers kunnen niet worden gemaakt:

- `CREATE CRYPTOGRAPHIC PROVIDER`wordt niet ondersteund. Zie [CRYPTOGRAFISCHE PROVIDER MAKEN](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`wordt niet ondersteund. Zie [CRYPTOGRAFISCHE PROVIDER WIJZIGEN](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Aanmeldingen en gebruikers

- SQL-aanmeldingen die `FROM CERTIFICATE` `FROM ASYMMETRIC KEY`zijn `FROM SID` gemaakt met behulp van , en worden ondersteund. Zie [AANMELDING MAKEN](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) serverprincipals (logins) die zijn gemaakt met de [syntaxis VAN AANMELDING MAKEN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) of de [syntaxis VAN DE GEBRUIKER MAKEN VAN AANMELDING [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) worden ondersteund. Deze aanmeldingen worden gemaakt op serverniveau.

    Beheerde instantie ondersteunt Azure AD-databaseprincipals met de syntaxis `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Deze functie wordt ook wel Azure AD genoemd, databasegebruikers.

- Windows-aanmeldingen die `CREATE LOGIN ... FROM WINDOWS` met de syntaxis zijn gemaakt, worden niet ondersteund. Gebruik Azure Active Directory-aanmeldingen en -gebruikers.
- De Azure AD-gebruiker die de instantie heeft gemaakt, heeft [onbeperkte beheerdersbevoegdheden](sql-database-manage-logins.md).
- Gebruikers op Azure AD-databaseniveau kunnen niet-beheerders worden gemaakt met behulp van de `CREATE USER ... FROM EXTERNAL PROVIDER` syntaxis. Zie [GEBRUIKER MAKEN ... VAN EXTERNE PROVIDER](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Azure AD-serverprincipals (logins) ondersteunen SQL-functies binnen slechts één beheerde instantie. Functies waarvoor interactie tussen meerdere gevallen vereist is, ongeacht of ze zich binnen dezelfde Azure AD-tenant of verschillende tenants bevinden, worden niet ondersteund voor Azure AD-gebruikers. Voorbeelden van dergelijke functies zijn:

  - SQL transactionele replicatie.
  - Koppelingsserver.

- Als u een Azure AD-aanmelding instelt die is toegewezen aan een Azure AD-groep, wordt de database-eigenaar niet ondersteund.
- Imitatie van Azure AD-nodaalprincipes op serverniveau met behulp van andere Azure AD-principals wordt ondersteund, zoals de [COMPONENT UITVOEREN ALS.](/sql/t-sql/statements/execute-as-transact-sql) EXECUTE AS beperkingen zijn:

  - EXECUTE AS USER wordt niet ondersteund voor Azure AD-gebruikers wanneer de naam afwijkt van de inlognaam. Een voorbeeld is wanneer de gebruiker wordt gemaakt via de syntaxis MAAK GEBRUIKER [myAadUser] UIT LOGIN [ ]john@contoso.comen imitatie wordt geprobeerd via EXEC AS USER = _myAadUser_. Wanneer u een **gebruiker maakt vanaf** een Azure AD-serverprincipal (login), geeft u de user_name op als dezelfde login_name van **LOGIN**.
  - Alleen de SQL Server-level principals (logins) `sysadmin` die deel uitmaken van de rol kunnen de volgende bewerkingen uitvoeren die zich richten op Azure AD-principals:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Databaseexporteren/importeren met bacpac-bestanden worden ondersteund voor Azure AD-gebruikers in beheerde instantie met behulp van [SSMS V18.4 of hoger](/sql/ssms/download-sql-server-management-studio-ssms)of [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - De volgende configuraties worden ondersteund met databasebacpac-bestand: 
    - Een database exporteren/importeren tussen verschillende beheerinstanties binnen hetzelfde Azure AD-domein.
    - Exporteer een database vanuit beheerde instantie en importeer naar SQL Database binnen hetzelfde Azure AD-domein. 
    - Exporteer een database vanuit SQL Database en importeer naar beheerde instantie binnen hetzelfde Azure AD-domein.
    - Een database exporteren van beheerde instantie en importeren naar SQL Server (versie 2012 of hoger).
      - In deze configuratie worden alle Azure AD-gebruikers gemaakt als SQL-databaseprincipals (gebruikers) zonder aanmeldingen. Het type gebruikers wordt vermeld als SQL (zichtbaar als SQL_USER in sys.database_principals). Hun machtigingen en rollen blijven in de metagegevens van de SQL Server-database staan en kunnen worden gebruikt voor imitatie. Ze kunnen echter niet worden gebruikt om toegang te krijgen tot en in te loggen op de SQL Server met behulp van hun referenties.

- Alleen de hoofdaanmelding op serverniveau, die wordt gemaakt door het beheerde `securityadmin` instantieinrichtingsproces, leden van de serverrollen, zoals of of `sysadmin`andere aanmeldingen met WIJZIGINGS-INLOG-machtiging op serverniveau, kunnen bijvoorbeeld Azure AD-serverprincipals (aanmeldingen) maken in de beheerde hoofddatabase.
- Als de aanmelding een SQL-principal is, `sysadmin` kunnen alleen aanmeldingen die deel uitmaken van de rol de opdracht Maken gebruiken om aanmeldingen voor een Azure AD-account te maken.
- De Azure AD-aanmelding moet lid zijn van een Azure AD in dezelfde map die wordt gebruikt voor azure SQL Database-beheerde instantie.
- Azure AD-serverprincipals (aanmeldingen) zijn zichtbaar in Object Explorer, te beginnen met SQL Server Management Studio 18.0 preview 5.
- Overlappende Azure AD-serverprincipals (aanmeldingen) met een Azure AD-beheeraccount is toegestaan. Azure AD-serverprincipals (aanmeldingen) hebben voorrang op de Azure AD-beheerder wanneer u de principal oplost en machtigingen toepast op de beheerde instantie.
- Tijdens de verificatie wordt de volgende reeks toegepast om de authenticerende principal op te lossen:

    1. Als het Azure AD-account bestaat als rechtstreeks toegewezen aan de Azure AD-serverprincipal (login), die aanwezig is in sys.server_principals als type 'E', verleent u machtigingen voor de Azure AD-serverprincipal (login) en past u deze toe.
    2. Als het Azure AD-account lid is van een Azure AD-groep die is toegewezen aan de Azure AD-serverprincipal (login), die aanwezig is in sys.server_principals als type 'X', verleent u toegang en past u machtigingen toe voor de aanmelding van de Azure AD-groep.
    3. Als het Azure AD-account een speciale Azure AD-beheerder is voor beheerde instantie, die niet bestaat in beheerde instantiesysteemweergaven, past u speciale vaste machtigingen van de Azure AD-beheerder toe voor beheerde instantie (verouderde modus).
    4. Als het Azure AD-account bestaat als rechtstreeks toegewezen aan een Azure AD-gebruiker in een database, die aanwezig is in sys.database_principals als type 'E', verleent u machtigingen en past u machtigingen toe voor de Azure AD-databasegebruiker.
    5. Als het Azure AD-account lid is van een Azure AD-groep die is toegewezen aan een Azure AD-gebruiker in een database, die aanwezig is in sys.database_principals als type 'X', verleent u toegang en past u machtigingen toe voor de aanmelding van de Azure AD-groep.
    6. Als er een Azure AD-aanmelding is toegewezen aan een Azure AD-gebruikersaccount of een Azure AD-groepsaccount, dat wordt opgelost voor de gebruiker die authenticeert, worden alle machtigingen van deze Azure AD-aanmelding toegepast.

### <a name="service-key-and-service-master-key"></a>Servicesleutel en servicemastersleutel

- [Master key back-up](/sql/t-sql/statements/backup-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL Database service).
- [Hoofdsleutelherstel](/sql/t-sql/statements/restore-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL Database-service).
- [Back-upvan de servicemastersleutel](/sql/t-sql/statements/backup-service-master-key-transact-sql) wordt niet ondersteund (beheerd door sql-databaseservice).
- [Herstel van de servicemastersleutel](/sql/t-sql/statements/restore-service-master-key-transact-sql) wordt niet ondersteund (beheerd door sql-databaseservice).

## <a name="configuration"></a>Configuratie

### <a name="buffer-pool-extension"></a>Uitbreiding bufferpool

- [De uitbreiding van de buffergroep](/sql/database-engine/configure-windows/buffer-pool-extension) wordt niet ondersteund.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`wordt niet ondersteund. Zie [CONFIGURATIE VAN DE SERVER WIJZIGEN](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortering

De standaardinstantiecollatie `SQL_Latin1_General_CP1_CI_AS` is en kan worden opgegeven als een creatieparameter. Zie [Collations](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Compatibiliteitsniveaus

- Ondersteunde compatibiliteitsniveaus zijn 100, 110, 120, 130, 140 en 150.
- Compatibiliteitsniveaus onder de 100 worden niet ondersteund.
- Het standaardcompatibiliteitsniveau voor nieuwe databases is 140. Voor herstelde databases blijft het compatibiliteitsniveau ongewijzigd als het 100 en hoger was.

Zie [COMPATIBILITEITsniveau VAN DE DATABASE WIJZIGEN](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasespiegeling

Database mirroring wordt niet ondersteund.

- `ALTER DATABASE SET PARTNER`en `SET WITNESS` opties worden niet ondersteund.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`wordt niet ondersteund.

Zie [PARTNER ALTER DATABASE SET en WITNESS INSTELLEN](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) en EINDPUNT MAKEN voor meer informatie [... voor DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Databaseopties

- Meerdere logboekbestanden worden niet ondersteund.
- In-memory objecten worden niet ondersteund in de servicelaag Algemeen Doel. 
- Er is een limiet van 280 bestanden per instantie voor algemeen gebruik, wat een maximum van 280 bestanden per database impliceert. Zowel gegevens als logboekbestanden in de laag Algemeen doel worden meegeteld voor deze limiet. [De laag Bedrijfskritiek ondersteunt 32.767 bestanden per database.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)
- De database mag geen bestandsgroepen bevatten die bestandsstroomgegevens bevatten. Herstellen mislukt als `FILESTREAM` .bak gegevens bevat. 
- Elk bestand wordt in Azure Blob-opslag geplaatst. IO en doorvoer per bestand zijn afhankelijk van de grootte van elk afzonderlijk bestand.

#### <a name="create-database-statement"></a>DATABASE-instructie MAKEN

De volgende beperkingen `CREATE DATABASE`zijn van toepassing op :

- Bestanden en bestandsgroepen kunnen niet worden gedefinieerd. 
- De `CONTAINMENT` optie wordt niet ondersteund. 
- `WITH`opties worden niet ondersteund. 
   > [!TIP]
   > Gebruik `ALTER DATABASE` als tijdelijke oplossing `CREATE DATABASE` na het instellen van databaseopties om bestanden toe te voegen of insluiting in te stellen. 

- De `FOR ATTACH` optie wordt niet ondersteund.
- De `AS SNAPSHOT OF` optie wordt niet ondersteund.

Zie [DATABASE MAKEN](/sql/t-sql/statements/create-database-sql-server-transact-sql)voor meer informatie.

#### <a name="alter-database-statement"></a>VERKLARING ALTER DATABASE

Sommige bestandseigenschappen kunnen niet worden ingesteld of gewijzigd:

- Een bestandspad kan niet worden `ALTER DATABASE ADD FILE (FILENAME='path')` opgegeven in de T-SQL-instructie. Verwijder `FILENAME` uit het script omdat een beheerde instantie de bestanden automatisch plaatst. 
- Een bestandsnaam kan niet worden `ALTER DATABASE` gewijzigd met behulp van de instructie.

De volgende opties zijn standaard ingesteld en kunnen niet worden gewijzigd:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

De volgende opties kunnen niet worden gewijzigd:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Zie [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)voor meer informatie.

### <a name="sql-server-agent"></a>SQL Server Agent

- Sql Server Agent in- en uitschakelen wordt momenteel niet ondersteund in beheerde instantie. SQL Agent wordt altijd uitgevoerd.
- Sql Server Agent-instellingen worden alleen gelezen. De `sp_set_agent_properties` procedure wordt niet ondersteund in beheerde instantie. 
- Taken
  - T-SQL-taakstappen worden ondersteund.
  - De volgende replicatietaken worden ondersteund:
    - Transactielogboeklezer
    - Momentopname
    - Distributeur
  - SSIS-taakstappen worden ondersteund.
  - Andere typen taakstappen worden momenteel niet ondersteund:
    - De stap replicatievan samenvoegen wordt niet ondersteund. 
    - Wachtrijlezer wordt niet ondersteund. 
    - De opdrachtshell wordt nog niet ondersteund.
  - Beheerde exemplaren hebben geen toegang tot externe bronnen, bijvoorbeeld netwerkshares via robocopy. 
  - SQL Server Analysis Services worden niet ondersteund.
- Meldingen worden gedeeltelijk ondersteund.
- E-mailmelding wordt ondersteund, hoewel hiervoor een Database Mail-profiel moet worden geconfigureerd. SQL Server Agent kan slechts één Database Mail-profiel gebruiken en moet worden aangeroepen `AzureManagedInstance_dbmail_profile`. 
  - Pager wordt niet ondersteund.
  - NetSend wordt niet ondersteund.
  - Waarschuwingen worden nog niet ondersteund.
  - Volmachten worden niet ondersteund.
- EventLog wordt niet ondersteund.

De volgende SQL Agent-functies worden momenteel niet ondersteund:

- Proxies
- Taken plannen op een niet-actieve CPU
- Een agent in- of uitschakelen
- Waarschuwingen

Zie [SQL Server Agent](/sql/ssms/agent/sql-server-agent) voor meer informatie over SQL Server Agent.

### <a name="tables"></a>Tabellen

De volgende tabeltypen worden niet ondersteund:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [BESTANDSTABEL](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNE TABEL](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (niet alleen ondersteund in de laag Algemeen doel)

Zie [TABEL](/sql/t-sql/statements/create-table-transact-sql) MAKEN en [TABEL WIJZIGEN](/sql/t-sql/statements/alter-table-transact-sql)voor informatie over het maken en wijzigen van tabellen.

## <a name="functionalities"></a>Functionaliteiten

### <a name="bulk-insert--openrowset"></a>Bulkinvoegsel / OPENROWSET

Een beheerde instantie heeft geen toegang tot bestandsshares en Windows-mappen, dus de bestanden moeten worden geïmporteerd uit Azure Blob-opslag:

- `DATASOURCE`is vereist `BULK INSERT` in de opdracht terwijl u bestanden importeert uit Azure Blob-opslag. Zie [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`is vereist `OPENROWSET` in de functie wanneer u de inhoud van een bestand uit Azure Blob-opslag leest. Zie [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`kan worden gebruikt om gegevens uit andere Azure SQL-afzonderlijke databases, beheerde exemplaren of SQL Server-exemplaren te lezen. Andere bronnen, zoals Oracle-databases of Excel-bestanden, worden niet ondersteund.

### <a name="clr"></a>Clr

Een beheerde instantie heeft geen toegang tot bestandsshares en Windows-mappen, dus de volgende beperkingen zijn van toepassing:

- Alleen `CREATE ASSEMBLY FROM BINARY` wordt ondersteund. Zie [ASSEM BLY MAKEN VAN BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`wordt niet ondersteund. Zie [Vergadering maken uit bestand](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`kan geen verwijzing naar bestanden. Zie [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Databasemail (db_mail)
 - `sp_send_dbmail`kan geen bijlagen @file_attachments verzenden met behulp van parameter. Lokaal bestandssysteem en externe shares of Azure Blob Storage zijn niet toegankelijk via deze procedure.
 - Bekijk de bekende `@query` problemen met betrekking tot parameter en verificatie.
 
### <a name="dbcc"></a>Dbcc

DBCC-instructies zonder papieren die zijn ingeschakeld in SQL Server, worden niet ondersteund in beheerde instanties.

- Slechts een beperkt aantal Global Trace-vlaggen wordt ondersteund. Sessieniveau `Trace flags` wordt niet ondersteund. Zie [Trace vlaggen](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) en [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) werken met het beperkte aantal wereldwijde trace-flags.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) met opties REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST en REPAIR_REBUILD kunnen `SINGLE_USER` niet worden gebruikt omdat de database niet in de modus kan worden ingesteld - zie [VERSCHILLEN IN ALTER DATABASE](#alter-database-statement). Potentiële beschadigingen van gegevens worden afgehandeld door azure-ondersteuningsteam. Neem contact op met Azure-ondersteuning als u databasebeschadiging opmerkt die moet worden opgelost.

### <a name="distributed-transactions"></a>Gedistribueerde transacties

MSDTC en [elastische transacties](sql-database-elastic-transactions-overview.md) worden momenteel niet ondersteund in beheerde exemplaren.

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

Sommige Windows-specifieke doelen voor Extended Events (XEvents) worden niet ondersteund:

- Het `etw_classic_sync` doelwit wordt niet ondersteund. Bestanden `.xel` opslaan in Azure Blob-opslag. Zie [etw_classic_sync doel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Het `event_file` doelwit wordt niet ondersteund. Bestanden `.xel` opslaan in Azure Blob-opslag. Zie [event_file doel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externe bibliotheken

In-database R en Python worden externe bibliotheken nog niet ondersteund. Zie [SQL Server Machine Learning Services](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Filestream en Bestandstabel

- Filestream-gegevens worden niet ondersteund.
- De database mag geen bestandsgroepen met `FILESTREAM` gegevens bevatten.
- `FILETABLE`wordt niet ondersteund.
- Tabellen kunnen geen `FILESTREAM` typen hebben.
- De volgende functies worden niet ondersteund:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Zie [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) en [FileTables](/sql/relational-databases/blob/filetables-sql-server)voor meer informatie.

### <a name="full-text-semantic-search"></a>Semantisch zoeken met volledige tekst

[Semantisch zoeken](/sql/relational-databases/search/semantic-search-sql-server) wordt niet ondersteund.

### <a name="linked-servers"></a>Gekoppelde servers

Gekoppelde servers in beheerde exemplaren ondersteunen een beperkt aantal doelen:

- Ondersteunde doelen zijn Beheerde exemplaren, Afzonderlijke databases en SQL Server-exemplaren. 
- Gekoppelde servers ondersteunen geen gedistribueerde schrijfbare transacties (MS DTC).
- Doelen die niet worden ondersteund, zijn bestanden, analysis services en andere RDBMS. Probeer native CSV-import uit Azure `BULK INSERT` `OPENROWSET` Blob Storage te gebruiken met of als alternatief voor het importeren van bestanden.

Bewerkingen

- Schrijftransacties met verschillende gevallen worden niet ondersteund.
- `sp_dropserver`wordt ondersteund voor het laten vallen van een gekoppelde server. Zie [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- De `OPENROWSET` functie kan alleen worden gebruikt om query's uit te voeren op SQL Server-exemplaren. Ze kunnen worden beheerd, on-premises, of in virtuele machines. Zie [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- De `OPENDATASOURCE` functie kan alleen worden gebruikt om query's uit te voeren op SQL Server-exemplaren. Ze kunnen worden beheerd, on-premises, of in virtuele machines. Alleen `SQLNCLI`de `SQLNCLI11`, `SQLOLEDB` en waarden worden ondersteund als een provider. Een voorbeeld is `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zie [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Gekoppelde servers kunnen niet worden gebruikt om bestanden (Excel, CSV) te lezen vanuit de netwerkshares. Probeer [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) of [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) te gebruiken die CSV-bestanden uit Azure Blob Storage leest. Deze aanvragen bijhouden op [het feedbackitem voor beheerde instantie](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Externe tabellen die verwijzen naar de bestanden in HDFS- of Azure Blob-opslag worden niet ondersteund. Zie [PolyBase](/sql/relational-databases/polybase/polybase-guide)voor informatie over PolyBase.

### <a name="replication"></a>Replicatie

- Momentopname- en bidirectionele replicatietypen worden ondersteund. Replicatie, Peer-to-peer-replicatie en updatable abonnementen worden niet ondersteund.
- [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md) is beschikbaar voor een openbare preview van beheerde instantie met enkele beperkingen:
    - Alle typen replicatiedeelnemers (uitgever, distributeur, pull-abonnee en pushabonnee) kunnen worden geplaatst op beheerde instanties, maar de uitgever en de distributeur moeten zich zowel in de cloud als on-premises bevinden.
    - Beheerde exemplaren kunnen communiceren met de recente versies van SQL Server. Zie de [matrix ondersteunde versies](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) voor meer informatie.
    - Transactionele replicatie heeft een aantal [aanvullende netwerkvereisten](sql-database-managed-instance-transactional-replication.md#requirements).

Zie de volgende zelfstudies voor meer informatie over het configureren van transactionele replicatie:
- [Replicatie tussen een MI-uitgever en abonnee](replication-with-sql-database-managed-instance.md)
- [Replicatie tussen een MI-uitgever, MI-distributeur en SQL Server-abonnee](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>HERSTEL-instructie 

- Ondersteunde syntaxis:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Niet-ondersteunde syntaxis:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Bron: 
  - `FROM URL`(Azure Blob-opslag) is de enige ondersteunde optie.
  - `FROM DISK`/`TAPE`/back-upapparaat wordt niet ondersteund.
  - Back-upsets worden niet ondersteund.
- `WITH`opties worden niet ondersteund, zoals `DIFFERENTIAL` `STATS`nee of .
- `ASYNC RESTORE`: Herstellen gaat door, zelfs als de clientverbinding wordt verbroken. Als uw verbinding wordt verbroken, u de `sys.dm_operation_status` weergave controleren op de status van een herstelbewerking en op een CREATE- en DROP-database. Zie [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

De volgende databaseopties zijn ingesteld of overschreven en kunnen later niet worden gewijzigd: 

- `NEW_BROKER`als de broker niet is ingeschakeld in het .bak-bestand. 
- `ENABLE_BROKER`als de broker niet is ingeschakeld in het .bak-bestand. 
- `AUTO_CLOSE=OFF`als een database in het `AUTO_CLOSE=ON`.bak-bestand . 
- `RECOVERY FULL`als een database in het `SIMPLE` `BULK_LOGGED` .bak-bestand of herstelmodus heeft.
- Er wordt een voor geheugen geoptimaliseerde bestandsgroep toegevoegd en XTP genoemd als deze niet in het bronbestand .bak stond. 
- Elke bestaande voor geheugen geoptimaliseerde bestandsgroep wordt omgedoopt tot XTP. 
- `SINGLE_USER`en `RESTRICTED_USER` opties worden `MULTI_USER`geconverteerd naar .

Beperkingen: 

- Back-ups van de beschadigde databases kunnen worden hersteld, afhankelijk van het type van de corruptie, maar geautomatiseerde back-ups worden niet genomen totdat de corruptie is opgelost. Zorg ervoor dat `DBCC CHECKDB` u de broninstantie `WITH CHECKSUM` uitvoert en gebruik back-up om dit probleem te voorkomen.
- Herstel `.BAK` van het bestand van een database die een `FILESTREAM` beperking `FILETABLE` bevat die in dit document is beschreven (bijvoorbeeld of objecten) kan niet worden hersteld op Beheerde instantie.
- `.BAK`bestanden die meerdere back-upsets bevatten, kunnen niet worden hersteld. 
- `.BAK`bestanden die meerdere logboekbestanden bevatten, kunnen niet worden hersteld.
- Back-ups die databases bevatten die groter zijn dan 8 TB, actieve OLTP-objecten in het geheugen of het aantal bestanden dat meer dan 280 bestanden per exemplaar zou bevatten, kunnen niet worden hersteld op een instantie voor algemeen gebruik. 
- Back-ups die databases bevatten die groter zijn dan 4 TB of OLTP-objecten in het geheugen met de totale grootte groter dan de grootte die is beschreven in [resourcelimieten,](sql-database-managed-instance-resource-limits.md) kunnen niet worden hersteld op de instantie Bedrijfskritiek.
Zie [Verklaringen HERSTELLEN voor](/sql/t-sql/statements/restore-statements-transact-sql)informatie over herstelverklaringen .

 > [!IMPORTANT]
 > Dezelfde beperkingen gelden voor de ingebouwde point-in-time herstelbewerking. Als voorbeeld kan de database voor algemeen gebruik van meer dan 4 TB niet worden hersteld op de instantie Bedrijfskritiek. Business Critical-database met OLTP-bestanden in het geheugen of meer dan 280 bestanden kan niet worden hersteld op de instantie Algemeen Doel.

### <a name="service-broker"></a>Servicemakelaar

Servicebroker met verschillende gevallen wordt niet ondersteund:

- `sys.routes`: Als voorwaarde moet u het adres selecteren op sys.routes. Het adres moet lokaal zijn op elke route. Zie [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Je `CREATE ROUTE` niet `ADDRESS` gebruiken `LOCAL`met andere dan. Zie [ROUTE MAKEN](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Je `ALTER ROUTE` niet `ADDRESS` gebruiken `LOCAL`met andere dan. Zie [ROUTE WIJZIGEN](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Opgeslagen procedures, functies en triggers

- `NATIVE_COMPILATION`wordt niet ondersteund in de laag Algemeen Doel.
- De volgende [opties sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) worden niet ondersteund: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`wordt niet ondersteund. Zie [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`wordt niet ondersteund. Zie [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`worden niet ondersteund, waaronder `sp_addextendedproc`   `sp_dropextendedproc`en . Zie [Uitgebreide opgeslagen procedures](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`en `sp_detach_db` worden niet ondersteund. Zie [sp_attach_db,](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql) [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)en [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Systeemfuncties en variabelen

De volgende variabelen, functies en weergaven geven verschillende resultaten weer:

- `SERVERPROPERTY('EngineEdition')`geeft de waarde 8 als resultaat. Deze eigenschap identificeert op unieke wijze een beheerde instantie. Zie [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`retourneert NULL omdat het concept van instantie zoals deze bestaat voor SQL Server niet van toepassing is op een beheerde instantie. Zie [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`retourneert een volledige DNS-naam "connectable", bijvoorbeeld my-managed-instance.wcus17662feb9ce98.database.windows.net. Zie [@SERVERNAME@](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`retourneert een volledige DNS-naam `myinstance.domain.database.windows.net` 'verbindend', zoals voor de eigenschappen 'naam' en 'data_source'. Zie [SYS. SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`retourneert NULL omdat het concept van de service zoals deze bestaat voor SQL Server niet van toepassing is op een beheerde instantie. Zie [@SERVICENAME@](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`wordt ondersteund. Het retourneert NULL als de Azure AD-login niet in sys.syslogins staat. Zie [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`wordt niet ondersteund. De verkeerde gegevens worden geretourneerd, wat een tijdelijk bekend probleem is. Zie [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Milieubeperkingen

### <a name="subnet"></a>Subnet
-  U geen andere resources (bijvoorbeeld virtuele machines) in het subnet plaatsen waar u uw beheerde instantie hebt geïmplementeerd. Implementeer deze resources met een ander subnet.
- Subnet moet over voldoende beschikbare [IP-adressen](sql-database-managed-instance-connectivity-architecture.md#network-requirements)beschikken . Minimum is 16, terwijl de aanbeveling is om ten minste 32 IP-adressen in het subnet.
- [Serviceeindpunten kunnen niet worden gekoppeld aan het subnet van de beheerde instantie](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Zorg ervoor dat de optie serviceeindpunten is uitgeschakeld wanneer u het virtuele netwerk maakt.
- Het aantal vCores en typen exemplaren dat u in een regio implementeren, heeft een aantal [beperkingen en limieten.](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)
- Er zijn een aantal [beveiligingsregels die moeten worden toegepast op het subnet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET (VNET)
- VNet kan worden geïmplementeerd met behulp van Resource Model - Classic Model voor VNet wordt niet ondersteund.
- Nadat een beheerde instantie is gemaakt, wordt het verplaatsen van de beheerde instantie of VNet naar een andere resourcegroep of abonnement niet ondersteund.
- Sommige services, zoals App-serviceomgevingen, Logische-apps en beheerde instanties (gebruikt voor Geo-replicatie, Transactionele replicatie of via gekoppelde servers) hebben geen toegang tot beheerde instanties in verschillende regio's als hun VNets zijn verbonden met behulp van [globale peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). U verbinding maken met deze bronnen via ExpressRoute of VNet-to-VNet via VNet Gateways.

### <a name="tempdb"></a>Tempdb

De maximale `tempdb` bestandsgrootte van mag niet groter zijn dan 24 GB per core op een klasse voor algemeen gebruik. De `tempdb` maximale grootte op een businesskritieke laag wordt beperkt door de opslaggrootte van de instantie. `Tempdb`logbestandsgrootte is beperkt tot 120 GB op de laag Algemeen Doel. Sommige query's kunnen een fout retourneren als ze `tempdb` meer dan 24 GB per core in of als ze produceren meer dan 120 GB aan loggegevens.

### <a name="msdb"></a>Msdb

De volgende MSDB-schema's in beheerde instantie moeten eigendom zijn van hun respectievelijke vooraf gedefinieerde rollen:

- Algemene rollen
  - TargetServersRole
- [Vaste databaserollen](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail-rollen:](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)
  - DatabaseMailUserRole
- [Rollen in integratiediensten:](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Als u de vooraf gedefinieerde rolnamen, schemanamen en schema-eigenaren door klanten wijzigt, wordt de normale werking van de service beïnvloed. Eventuele wijzigingen in deze wijzigingen worden terugverwezen naar de vooraf gedefinieerde waarden zodra deze zijn gedetecteerd, of bij de volgende service-update ten laatste om de normale servicewerking te garanderen.

### <a name="error-logs"></a>Foutenlogboeken

Een beheerde instantie plaatst uitgebreide informatie in foutlogboeken. Er zijn veel interne systeemgebeurtenissen die zijn aangemeld in het foutlogboek. Gebruik een aangepaste procedure om foutlogboeken te lezen die een aantal irrelevante vermeldingen filteren. Zie [beheerde instantie – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) of [beheerde instantie-extensie(preview)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) voor Azure Data Studio voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Wat is een [beheerde instantie voor](sql-database-managed-instance.md) meer informatie over beheerde exemplaren?
- Zie [Azure SQL Database-functievergelijking](sql-database-features.md)voor een lijst met functies en vergelijking van functies .
- Zie [SQL Database release notes](sql-database-release-notes.md) voor release-updates en bekende problemen
- Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een snelle start die u laat zien hoe u een nieuw beheerde instantie maakt.

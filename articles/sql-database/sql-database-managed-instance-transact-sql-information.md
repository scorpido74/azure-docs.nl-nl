---
title: T-SQL-verschillen Azure SQL Database Managed instance | Microsoft Docs
description: In dit artikel worden de T-SQL-verschillen tussen een beheerd exemplaar in Azure SQL Database beschreven en SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5e9972c5fea7aaa2e6b5270aff87343437b1963e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624013"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database van beheerde instanties T-SQL-verschillen van SQL Server

In dit artikel vindt u een overzicht van de verschillen in de syntaxis en het gedrag tussen Azure SQL Database beheerde instantie en de on-premises SQL Server data base-engine. De volgende onderwerpen worden besproken:<a name="Differences"></a>

- [Beschik baarheid](#availability) omvat de verschillen in [altijd](#always-on-availability) en [back-ups](#backup).
- [Beveiliging](#security) omvat de verschillen in [controle](#auditing), [certificaten](#certificates), [referenties](#credential), [cryptografische providers](#cryptographic-providers), aanmeldingen [en gebruikers](#logins-and-users), en de [Service sleutel en service hoofd sleutel](#service-key-and-service-master-key).
- [De configuratie](#configuration) bevat de verschillen [](#buffer-pool-extension)in de buffergroepuitbreiding, [sortering](#collation), [compatibiliteits niveaus](#compatibility-levels), [database spiegeling](#database-mirroring), [database opties](#database-options), [SQL Server Agent](#sql-server-agent)en [tabel opties](#tables).
- De [functies](#functionalities) omvatten [Bulk Insert/](#bulk-insert--openrowset)OPENROWSET [, CLR](#clr), [DBCC](#dbcc), [Distributed trans actions](#distributed-transactions), [Extended Events](#extended-events), [externe bibliotheken](#external-libraries), [FileStream en bestands tabel](#filestream-and-filetable), [volledige tekst Semantisch zoeken](#full-text-semantic-search), [gekoppelde servers](#linked-servers), [poly base](#polybase), [replicatie](#replication), [herstel](#restore-statement), [Service Broker](#service-broker), [opgeslagen procedures, functies en triggers](#stored-procedures-functions-and-triggers).
- [Omgevings instellingen](#Environment) , zoals VNets en subnet-configuraties.
- [Functies met een ander gedrag in beheerde exemplaren](#Changes).
- [Tijdelijke beperkingen en bekende problemen](#Issues).

De implementatie optie Managed instance biedt hoge compatibiliteit met on-premises SQL Server data base-engine. De meeste functies van de SQL Server data base-engine worden ondersteund in een beheerd exemplaar.

![Migratie](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Beschikbaarheid

### <a name="always-on-availability"></a>Altijd aan

[Hoge Beschik baarheid](sql-database-high-availability.md) is ingebouwd in een beheerd exemplaar en kan niet worden beheerd door gebruikers. De volgende instructies worden niet ondersteund:

- [EIND PUNT MAKEN... VOOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [BESCHIKBAARHEIDS GROEP MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [BESCHIKBAARHEIDS GROEP NEERZETTEN](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- De [set HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) -component van de instructie [ALTER data base](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Back-up

Beheerde exemplaren hebben automatische back-ups, zodat gebruikers volledige database `COPY_ONLY` back-ups kunnen maken. Differentiële back-ups, logboek bestanden en moment opnamen van bestands momentopnamen worden niet ondersteund.

- Met een beheerd exemplaar kunt u een back-up maken van een exemplaar database alleen naar een Azure Blob Storage-account:
  - Alleen `BACKUP TO URL` wordt ondersteund.
  - `FILE`, `TAPE`en back-upapparaten worden niet ondersteund.
- De meeste algemene `WITH` opties worden ondersteund.
  - `COPY_ONLY`is verplicht.
  - `FILE_SNAPSHOT`wordt niet ondersteund.
  - Tape opties: `REWIND` `NOREWIND` ,`UNLOAD`, en`NOUNLOAD` worden niet ondersteund.
  - Opties die specifiek zijn voor `NORECOVERY`het `STANDBY`logboek: `NO_TRUNCATE` , en worden niet ondersteund.

Beperkingen: 

- Met een beheerd exemplaar kunt u een back-up maken van een exemplaar database met Maxi maal 32 stroken, die voldoende is voor data bases tot 4 TB als back-upcompressie wordt gebruikt.
- U kunt niet `BACKUP DATABASE ... WITH COPY_ONLY` uitvoeren op een Data Base die is versleuteld met door service beheerde transparent Data Encryption (TDE). Door service beheerde TDE zorgt ervoor dat back-ups worden versleuteld met een interne TDE-sleutel. De sleutel kan niet worden geëxporteerd, dus u kunt de back-up niet herstellen. Gebruik automatische back-ups en herstel naar een bepaald tijdstip, of gebruik in plaats daarvan door de [klant beheerde (BYOK) TDe](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) . U kunt versleuteling ook uitschakelen voor de data base.
- De maximale grootte van de back-upstripe `BACKUP` met behulp van de opdracht in een beheerd exemplaar is 195 GB. Dit is de maximale grootte van de blob. Verhoog het aantal Stripes in de back-upopdracht om de afzonderlijke Stripe-grootte te verminderen en binnen deze limiet te blijven.

    > [!TIP]
    > Als u een back-up van een Data Base maakt vanuit een van SQL Server in een on-premises omgeving of op een virtuele machine, kunt u het volgende doen om deze beperking te omzeilen:
    >
    > - Maak een back `DISK` -up in plaats van een `URL`back-up te maken naar.
    > - Upload de back-upbestanden naar de Blob-opslag.
    > - Herstel naar het beheerde exemplaar.
    >
    > De `Restore` opdracht in een beheerd exemplaar ondersteunt grotere BLOB-grootten in de back-upbestanden omdat een ander type BLOB wordt gebruikt voor het opslaan van de geüploade back-upbestanden.

Zie [back-up](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)voor informatie over back-ups met behulp van T-SQL.

## <a name="security"></a>Beveiliging

### <a name="auditing"></a>Controleren

De belangrijkste verschillen tussen controles in data bases in Azure SQL Database en data bases in SQL Server zijn:

- Met de implementatie optie Managed instance in Azure SQL Database werkt auditing op server niveau. De `.xel` logboek bestanden worden opgeslagen in Azure Blob-opslag.
- Met de implementatie opties voor één data base en elastische pool in Azure SQL Database werkt auditing op het niveau van de data base.
- Bij SQL Server on-premises of virtuele machines werkt auditing op server niveau. Gebeurtenissen worden opgeslagen in gebeurtenis logboeken van het bestands systeem of Windows.
 
XEvent-controle in een beheerd exemplaar ondersteunt Azure Blob-opslag doelen. Bestands-en Windows-logboeken worden niet ondersteund.

De belangrijkste verschillen in de `CREATE AUDIT` syntaxis voor de controle van Azure Blob-opslag zijn:

- Er wordt een `TO URL` nieuwe syntaxis opgegeven die u kunt gebruiken om de URL op te geven van de Azure Blob Storage `.xel` -container waar de bestanden worden geplaatst.
- De syntaxis `TO FILE` wordt niet ondersteund omdat een beheerd exemplaar geen toegang krijgt tot Windows-bestands shares.

Zie voor meer informatie: 

- [SERVER CONTROLE MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Controle](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificaten

Een beheerd exemplaar heeft geen toegang tot bestands shares en Windows-mappen, dus de volgende beperkingen zijn van toepassing:

- Het `CREATE FROM` bestandwordtniet`BACKUP TO` ondersteund voor certificaten. /
- Het `CREATE` certificaat/van wordtniet`FILE` ondersteund. / `BACKUP` `ASSEMBLY` Persoonlijke sleutel bestanden kunnen niet worden gebruikt. 

Zie [certificaat](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) en [back-upcertificaat](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)maken. 
 
**Tijdelijke oplossing**: Script voor het certificaat of de persoonlijke sleutel, opslaan als. SQL-bestand en maken van binair:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Referentie

Alleen Azure Key Vault en `SHARED ACCESS SIGNATURE` identiteiten worden ondersteund. Windows-gebruikers worden niet ondersteund.

Zie [referentie maken](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) en [referenties wijzigen](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Cryptografische providers

Een beheerd exemplaar heeft geen toegang tot bestanden, zodat er geen cryptografische providers kunnen worden gemaakt:

- `CREATE CRYPTOGRAPHIC PROVIDER`wordt niet ondersteund. Zie [Cryptografische provider maken](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER`wordt niet ondersteund. Zie [ALTER CRYPTOGRAPHIC provider](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Aanmeldingen en gebruikers

- SQL-aanmeldingen die `FROM CERTIFICATE`zijn `FROM ASYMMETRIC KEY`gemaakt met `FROM SID` behulp van, en worden ondersteund. Zie [login maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server-principals (aanmeldingen) die zijn gemaakt met de syntaxis voor het maken van een [aanmelding](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) of de syntaxis voor het [maken van een gebruiker op basis van aanmelding [Azure AD login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) worden ondersteund (open bare preview). Deze aanmeldingen worden gemaakt op server niveau.

    Het beheerde exemplaar ondersteunt Azure AD-data base-principals met de syntaxis `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Deze functie is ook bekend als Azure AD, Inge sloten database gebruikers.

- Windows-aanmeldingen die `CREATE LOGIN ... FROM WINDOWS` met de syntaxis worden gemaakt, worden niet ondersteund. Gebruik Azure Active Directory aanmeldingen en gebruikers.
- De Azure AD-gebruiker die het exemplaar heeft gemaakt, heeft [onbeperkte beheerders bevoegdheden](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Gebruikers van Azure AD-data bases die geen beheerder zijn, kunnen worden `CREATE USER ... FROM EXTERNAL PROVIDER` gemaakt met behulp van de syntaxis. Zie [gebruiker maken... VAN EXTERNE PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Azure AD server-principals (aanmeldingen) bieden alleen ondersteuning voor SQL-functies binnen één Managed instance. Functies waarvoor cross-instance interacties zijn vereist, ongeacht of deze zich binnen dezelfde Azure AD-Tenant of andere tenants bevinden, worden niet ondersteund voor Azure AD-gebruikers. Voor beelden van deze functies zijn:

  - SQL transactionele replicatie.
  - Koppelings server.

- Het instellen van een Azure AD-aanmelding die is toegewezen aan een Azure AD-groep, wordt niet ondersteund voor de eigenaar van de data base.
- Imitatie van Azure AD-principals op server niveau met behulp van andere Azure AD-principals wordt ondersteund, zoals de component [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . UITVOEREN als beperkingen zijn:

  - UITVOEREN als gebruiker wordt niet ondersteund voor Azure AD-gebruikers wanneer de naam verschilt van de aanmeldings naam. Een voor beeld hiervan is wanneer de gebruiker wordt gemaakt via de syntaxis create user [myAadUser] vanjohn@contoso.comlogin [] en imitatie wordt geprobeerd via exec as User = _myAadUser_. Wanneer u een **gebruiker** maakt op basis van een Azure ad-server-principal (aanmelden), geeft u de gebruikers naam als dezelfde Login_name van **aanmelding**.
  - Alleen de principals op het SQL server niveau (aanmeldingen) die deel uitmaken van de `sysadmin` rol kunnen de volgende bewerkingen uitvoeren die zijn gericht op Azure AD-principals:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Beperkingen van de open bare Preview voor Azure AD server-principals (aanmeldingen):

  - Beperkingen voor Active Directory beheerder voor het beheerde exemplaar:

    - De Azure AD-beheerder die wordt gebruikt voor het instellen van het beheerde exemplaar kan niet worden gebruikt voor het maken van een Azure AD-server-principal (aanmelden) binnen het beheerde exemplaar. U moet de eerste Azure ad server-principal (login) maken met behulp van een SQL Server account `sysadmin` dat een rol is. Deze tijdelijke beperking wordt verwijderd nadat de Azure AD-server-principals (aanmeldingen) algemeen beschikbaar zijn. Als u probeert een Azure AD-beheerders account te gebruiken om de aanmelding te maken, ziet u de volgende fout:`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Op dit moment moet de eerste Azure AD-aanmelding die in de hoofd database wordt gemaakt, worden gemaakt door de standaard SQL Server account (niet-Azure `sysadmin` AD) dat een rol is met behulp van aanmelding bij externe provider [maken](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) . Na algemene Beschik baarheid wordt deze beperking verwijderd. Vervolgens kunt u een eerste Azure AD-aanmelding maken met behulp van de Active Directory beheerder voor een beheerd exemplaar.
    - DacFx (exporteren/importeren) met SQL Server Management Studio of SqlPackage wordt niet ondersteund voor Azure AD-aanmeldingen. Deze beperking wordt verwijderd nadat de Azure AD-server-principals (aanmeldingen) algemeen beschikbaar zijn.
    - Met behulp van Azure AD server-principals (aanmeldingen) met SQL Server Management Studio:

      - Het uitvoeren van scripts voor Azure AD-aanmeldingen die gebruikmaken van een geverifieerde aanmelding, wordt niet ondersteund.
      - IntelliSense herkent de instructie aanmelden van externe PROVIDER maken niet en toont een rode onderstreping.

- Alleen de principal-aanmelding op server niveau, die wordt gemaakt door het proces van het inrichten van het beheerde exemplaar, leden van de server `securityadmin` functies `sysadmin`, zoals of, of andere aanmeldingen met wijziging van de machtiging Aanmelden op server niveau, kunnen Azure AD maken Server-principals (aanmeldingen) in de hoofd database voor een beheerd exemplaar.
- Als de aanmelding een SQL-principal is, kunnen alleen aanmeldingen die deel `sysadmin` uitmaken van de rol de opdracht Create gebruiken om aanmeldingen voor een Azure ad-account te maken.
- De Azure AD-aanmelding moet lid zijn van een Azure AD in dezelfde map die wordt gebruikt voor Azure SQL Database Managed instance.
- Azure AD server-principals (aanmeldingen) zijn zichtbaar in Objectverkenner vanaf SQL Server Management Studio 18,0 Preview 5.
- Het is niet toegestaan Azure AD-server-principals (aanmeldingen) te overlappen met een Azure AD-beheerders account. Azure AD server-principals (aanmeldingen) hebben voor rang op de Azure AD-beheerder wanneer u de principal oplost en machtigingen toepast op het beheerde exemplaar.
- Tijdens de verificatie wordt de volgende reeks toegepast om de verificatie-principal op te lossen:

    1. Als het Azure AD-account is gekoppeld aan de principal van de Azure AD-server (aanmelden), dat aanwezig is in sys. server_principals als type "E," toegang verlenen en machtigingen Toep assen van de Azure AD server-principal (aanmelden).
    2. Als het Azure AD-account lid is van een Azure AD-groep die is toegewezen aan de Azure AD-server principal (login), die aanwezig is in sys. server_principals als type "X", toegang verlenen en machtigingen Toep assen op de Azure AD-groeps aanmelding.
    3. Als het Azure AD-account een speciaal door de portal geconfigureerde Azure AD-beheerder voor het beheerde exemplaar is, dat niet bestaat in de beheer weergaven van het beheerde exemplaar, moet u speciale vaste machtigingen van de Azure AD-beheerder voor het beheerde exemplaar (legacy-modus) Toep assen.
    4. Als het Azure AD-account is gekoppeld aan een Azure AD-gebruiker in een Data Base die aanwezig is in sys. database_principals als type "E," toegang verlenen en machtigingen Toep assen van de gebruiker van de Azure AD-data base.
    5. Als het Azure AD-account lid is van een Azure AD-groep die is toegewezen aan een Azure AD-gebruiker in een Data Base, die aanwezig is in sys. database_principals als type "X", toegang verlenen en machtigingen Toep assen van de Azure AD-groeps aanmelding.
    6. Als er een Azure AD-aanmelding is toegewezen aan een Azure AD-gebruikers account of een Azure AD-groeps account, die wordt omgezet naar de gebruiker die de verificatie uitvoert, worden alle machtigingen van deze Azure AD-aanmelding toegepast.

### <a name="service-key-and-service-master-key"></a>Service sleutel en service hoofd sleutel

- [Back-up van hoofd sleutel](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL database-service).
- Het terugzetten van de [hoofd sleutel](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) wordt niet ondersteund (beheerd door de SQL database-service).
- De [back-up van de service hoofd sleutel](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) wordt niet ondersteund (beheerd door SQL database-service).
- Het herstellen van de [service hoofd sleutel](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) wordt niet ondersteund (beheerd door de SQL database-service).

## <a name="configuration"></a>Configuratie

### <a name="buffer-pool-extension"></a>Extensie van buffer groep

- De [buffergroepuitbreiding](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) wordt niet ondersteund.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`wordt niet ondersteund. Zie [ALTER Server Configuration](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Serverconfiguratie

De standaard sortering van exemplaren `SQL_Latin1_General_CP1_CI_AS` is en kan worden opgegeven als een aanmaak parameter. Zie [sorteringen](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Compatibiliteitsniveaus

- Ondersteunde compatibiliteits niveaus zijn 100, 110, 120, 130 en 140.
- Compatibiliteits niveaus onder 100 worden niet ondersteund.
- Het standaard compatibiliteits niveau voor nieuwe data bases is 140. Voor herstelde data bases blijft het compatibiliteits niveau ongewijzigd als het 100 en hoger.

Zie [ALTER data base Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Databasespiegeling

Het spie gelen van data bases wordt niet ondersteund.

- `ALTER DATABASE SET PARTNER`en `SET WITNESS` opties worden niet ondersteund.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`wordt niet ondersteund.

Zie [ALTER data base set partner en Witness instellen](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) en ENDPOINT maken voor meer informatie [. VOOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Database opties

- Meerdere logboek bestanden worden niet ondersteund.
- In-Memory-objecten worden niet ondersteund in de servicelaag Algemeen. 
- Er is een limiet van 280 bestanden per Algemeen-exemplaar, wat een maximum van 280 bestanden per data base impliceert. De gegevens en logboek bestanden in de laag Algemeen worden meegeteld bij deze limiet. [De laag bedrijfskritiek ondersteunt 32.767 bestanden per data base](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- De data base mag geen bestands groepen bevatten die FileStream-gegevens bevatten. Herstellen mislukt als. bak gegevens `FILESTREAM` bevat. 
- Elk bestand wordt in Azure Blob-opslag geplaatst. I/o en door Voer per bestand zijn afhankelijk van de grootte van elk afzonderlijk bestand.

#### <a name="create-database-statement"></a>Instructie CREATE data base

De volgende beperkingen zijn van `CREATE DATABASE`toepassing op:

- Bestanden en bestands groepen kunnen niet worden gedefinieerd. 
- De `CONTAINMENT` optie wordt niet ondersteund. 
- `WITH`opties worden niet ondersteund. 
   > [!TIP]
   > Als tijdelijke oplossing gebruikt `ALTER DATABASE` u na `CREATE DATABASE` om database opties in te stellen voor het toevoegen van bestanden of het instellen van containment. 

- De `FOR ATTACH` optie wordt niet ondersteund.
- De `AS SNAPSHOT OF` optie wordt niet ondersteund.

Zie [Create Data Base](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)(Engelstalig) voor meer informatie.

#### <a name="alter-database-statement"></a>Instructie ALTER data base

Sommige bestands eigenschappen kunnen niet worden ingesteld of gewijzigd:

- Er kan geen bestandspad worden opgegeven in de `ALTER DATABASE ADD FILE (FILENAME='path')` t-SQL-instructie. Verwijderen `FILENAME` uit het script omdat een beheerd exemplaar automatisch de bestanden plaatst. 
- Een bestands naam kan niet worden gewijzigd met behulp van de `ALTER DATABASE` -instructie.

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

Zie [ALTER data base](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)(Engelstalig) voor meer informatie.

### <a name="sql-server-agent"></a>SQL Server Agent

- Het in-en uitschakelen van SQL Server Agent wordt momenteel niet ondersteund in een beheerd exemplaar. SQL Agent wordt altijd uitgevoerd.
- SQL Server Agent-instellingen zijn alleen-lezen. De procedure `sp_set_agent_properties` wordt niet ondersteund in een beheerd exemplaar. 
- Taken
  - T-SQL-taak stappen worden ondersteund.
  - De volgende replicatie taken worden ondersteund:
    - Transactie logboek lezer
    - Momentopname
    - Verdeler
  - SSIS-taak stappen worden ondersteund.
  - Andere typen taak stappen worden momenteel niet ondersteund:
    - De taak stap voor samenvoeg replicatie wordt niet ondersteund. 
    - Queue Reader wordt niet ondersteund. 
    - De opdracht shell wordt nog niet ondersteund.
  - Beheerde exemplaren hebben geen toegang tot externe bronnen, bijvoorbeeld netwerk shares via Robocopy. 
  - SQL Server Analysis Services worden niet ondersteund.
- Meldingen worden gedeeltelijk ondersteund.
- E-mail meldingen worden ondersteund, maar hiervoor moet u een Database Mail profiel configureren. SQL Server Agent kunt slechts één Database Mail profiel gebruiken en het moet worden aangeroepen `AzureManagedInstance_dbmail_profile`. 
  - Paginering wordt niet ondersteund.
  - Netsend wordt niet ondersteund.
  - Waarschuwingen worden nog niet ondersteund.
  - Proxy's worden niet ondersteund.
- EventLog wordt niet ondersteund.

De volgende functies van de SQL-Agent worden momenteel niet ondersteund:

- Proxy's
- Taken plannen voor een niet-actieve CPU
- Een agent in-of uitschakelen
- Waarschuwingen

Zie [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) voor meer informatie over SQL Server Agent.

### <a name="tables"></a>Tabellen

De volgende tabel typen worden niet ondersteund:

- [-](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [BESTANDS TABEL](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [externe tabel](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) Poly base
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (niet alleen ondersteund in Algemeen-laag)

Zie [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) en [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)voor meer informatie over het maken en wijzigen van tabellen.

## <a name="functionalities"></a>Functionaliteiten

### <a name="bulk-insert--openrowset"></a>Bulksgewijs invoegen/OPENROWSET

Een beheerd exemplaar heeft geen toegang tot bestands shares en Windows-mappen. Daarom moeten de bestanden vanuit Azure Blob Storage worden geïmporteerd:

- `DATASOURCE`is vereist in de `BULK INSERT` opdracht tijdens het importeren van bestanden vanuit Azure Blob-opslag. Zie [Bulk Insert](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`is vereist in de `OPENROWSET` functie wanneer u de inhoud van een bestand leest uit Azure Blob-opslag. Zie [](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)OPENROWSET.

### <a name="clr"></a>-

Een beheerd exemplaar heeft geen toegang tot bestands shares en Windows-mappen, dus de volgende beperkingen zijn van toepassing:

- Alleen `CREATE ASSEMBLY FROM BINARY` wordt ondersteund. Zie [Assembly maken van binair](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE`wordt niet ondersteund. Zie [Assembly maken op basis van een bestand](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY`kan niet verwijzen naar bestanden. Zie [ALTER assembly](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Niet-gedocumenteerde DBCC-instructies die zijn ingeschakeld in SQL Server, worden niet ondersteund in beheerde exemplaren.

- Slechts een beperkt aantal globale `Trace flags` waarden wordt ondersteund. Sessie niveau `Trace flags` wordt niet ondersteund. Zie [tracerings vlaggen](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) en [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) werken met het beperkte aantal globale traceer vlaggen.
- [DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) met de opties REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST en REPAIR_REBUILD kan niet worden gebruikt, omdat de Data Base `SINGLE_USER` niet kan worden ingesteld in de modus-Zie [ALTER data base verschillen](#alter-database-statement). Mogelijke beschadigingen van de Data Base worden verwerkt door het ondersteunings team van Azure. Neem contact op met de ondersteuning van Azure als u een merkt-database beschadiging hebt die moet worden opgelost.

### <a name="distributed-transactions"></a>Gedistribueerde transacties

MSDTC-en [elastische trans acties](sql-database-elastic-transactions-overview.md) worden momenteel niet ondersteund in beheerde exemplaren.

### <a name="extended-events"></a>Uitgebreide gebeurtenissen

Sommige Windows-specifieke doelen voor Extended Events (XEvents) worden niet ondersteund:

- Het `etw_classic_sync` doel wordt niet ondersteund. Bestanden `.xel` opslaan in Azure Blob-opslag. Zie [etw_classic_sync-doel](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Het `event_file` doel wordt niet ondersteund. Bestanden `.xel` opslaan in Azure Blob-opslag. Zie [event_file-doel](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Externe bibliotheken

In-data base R en python worden externe bibliotheken nog niet ondersteund. Zie [SQL Server machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream en bestands tabel

- FileStream-gegevens worden niet ondersteund.
- De data base mag geen bestands `FILESTREAM` groepen met gegevens bevatten.
- `FILETABLE`wordt niet ondersteund.
- Tabellen kunnen geen `FILESTREAM` typen hebben.
- De volgende functies worden niet ondersteund:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Zie [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) en [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)voor meer informatie.

### <a name="full-text-semantic-search"></a>Zoek opdracht in volledige tekst

[Semantisch zoeken](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) wordt niet ondersteund.

### <a name="linked-servers"></a>Gekoppelde servers

Gekoppelde servers in beheerde instanties ondersteunen een beperkt aantal doelen:

- Ondersteunde doelen zijn beheerde instanties, afzonderlijke data bases en SQL Server exemplaren. 
- Gekoppelde servers ondersteunen geen gedistribueerde Beschrijf bare trans acties (MS DTC).
- Doelen die niet worden ondersteund zijn bestanden, Analysis Services en andere RDBMS. Gebruik of `BULK INSERT` `OPENROWSET` als alternatief voor het importeren van bestanden met behulp van een systeem eigen csv-import bewerking vanuit Azure Blob Storage.

Bewerkingen

- Trans acties voor cross-instances worden niet ondersteund.
- `sp_dropserver`wordt ondersteund voor het verwijderen van een gekoppelde server. Zie [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- De `OPENROWSET` functie kan worden gebruikt om alleen query's uit te voeren op SQL Server exemplaren. Ze kunnen worden beheerd, on-premises of in virtuele machines. Zie [](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)OPENROWSET.
- De `OPENDATASOURCE` functie kan worden gebruikt om alleen query's uit te voeren op SQL Server exemplaren. Ze kunnen worden beheerd, on-premises of in virtuele machines. Alleen de `SQLNCLI`waarden `SQLNCLI11`, en `SQLOLEDB` worden ondersteund als een provider. Een voorbeeld is `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zie [OPENDATA source](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
- Gekoppelde servers kunnen niet worden gebruikt voor het lezen van bestanden (Excel, CSV) van de netwerk shares. Probeer [Bulk Insert](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) of OPENROWSET te gebruiken waarmee CSV-bestanden van Azure Blob Storage worden gelezen. [](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) Deze aanvragen bijhouden voor het feedback-item van het [beheerde exemplaar](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Externe tabellen die verwijzen naar de bestanden in HDFS of Azure Blob Storage, worden niet ondersteund. Zie [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)voor meer informatie over poly base.

### <a name="replication"></a>Replicatie

- De typen moment opname en bidirectionele replicatie worden ondersteund. Samenvoeg replicatie, peer-to-peer-replicatie en bij te werken abonnementen worden niet ondersteund.
- [Transactionele replicatie](sql-database-managed-instance-transactional-replication.md) is beschikbaar voor open bare preview op een beheerd exemplaar met enkele beperkingen:
    - Alle typen replicatie deelnemers (uitgever, distributeur, pull-abonnee en push-abonnee) kunnen worden geplaatst op beheerde exemplaren, maar Publisher en Distributor kunnen niet op verschillende instanties worden geplaatst.
    - Beheerde instanties kunnen communiceren met de recente versies van SQL Server. Zie de ondersteunde versies [hier](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - Transactionele replicatie heeft enkele [extra netwerk vereisten](sql-database-managed-instance-transactional-replication.md#requirements).

Zie de [zelf studie over replicatie](replication-with-sql-database-managed-instance.md)voor meer informatie over het configureren van replicatie.


Als replicatie is ingeschakeld voor een data base in [](sql-database-auto-failover-group.md)een failovergroep, moet de beheerder van het beheerde exemplaar alle publicaties opschonen op de oude primaire en opnieuw configureren op de nieuwe primaire versie nadat een failover is uitgevoerd. In dit scenario zijn de volgende activiteiten nodig:

1. Stop alle replicatie taken die worden uitgevoerd op de data base, indien aanwezig.
2. Voer het volgende script uit op de Publisher-data base om de meta gegevens van uw abonnement uit Publisher te verwijderen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. De meta gegevens van het abonnement op de abonnee weghalen. Voer het volgende script uit in de abonnementen database op het exemplaar van de abonnee:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Haal alle replicatie objecten geforceerd van Publisher door het volgende script uit te voeren in de gepubliceerde Data Base:

   ```sql
   EXEC sp_removedbreplication
   ```

1. De oude Distributor geforceerd verwijderen van de oorspronkelijke primaire instantie (als er een failover wordt uitgevoerd naar een oud primair exemplaar dat is gebruikt voor een distributeur). Voer het volgende script uit op de hoofd database in het oude beheerde exemplaar van de distributie server:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### <a name="restore-statement"></a>Instructie Restore 

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
  - `FROM URL`(Azure Blob-opslag) is de enige optie die wordt ondersteund.
  - `FROM DISK`/`TAPE`/Backup-apparaat wordt niet ondersteund.
  - Back-upsets worden niet ondersteund.
- `WITH`opties worden niet ondersteund, zoals Nee `DIFFERENTIAL` of `STATS`.
- `ASYNC RESTORE`: Herstellen gaat verder, zelfs als de client verbinding is verbroken. Als de verbinding wordt verbroken, kunt u de `sys.dm_operation_status` weer gave voor de status van een herstel bewerking controleren en voor een Data Base maken en verwijderen. Zie [sys. DM _operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

De volgende database opties zijn ingesteld of worden overschreven en kunnen later niet meer worden gewijzigd: 

- `NEW_BROKER`Als de Broker niet is ingeschakeld in het bak-bestand. 
- `ENABLE_BROKER`Als de Broker niet is ingeschakeld in het bak-bestand. 
- `AUTO_CLOSE=OFF`Als een data base in het. bak- `AUTO_CLOSE=ON`bestand is. 
- `RECOVERY FULL`Als een data base in het. bak- `SIMPLE` bestand `BULK_LOGGED` of de herstel modus.
- Een bestands groep die is geoptimaliseerd voor geheugen wordt toegevoegd met de naam XTP als deze niet voor komt in het bron. bak-bestand. 
- De naam van een bestaande bestands groep met geoptimaliseerd geheugen is gewijzigd in XTP. 
- `SINGLE_USER`en `RESTRICTED_USER` opties worden geconverteerd naar `MULTI_USER`.

Beperkingen: 

- Back-ups van de beschadigde data bases kunnen worden hersteld, afhankelijk van het type beschadiging, maar automatische back-ups worden pas uitgevoerd als de beschadiging is opgelost. Zorg ervoor dat u uitvoert `DBCC CHECKDB` op het bron exemplaar en gebruik back `WITH CHECKSUM` -up om dit probleem te voor komen.
- Herstellen van `.BAK` een bestand met een beperking die in dit document is `FILESTREAM` beschreven (bijvoorbeeld of `FILETABLE` objecten) kan niet worden hersteld op een beheerd exemplaar.
- `.BAK`bestanden die meerdere back-upsets bevatten, kunnen niet worden hersteld. 
- `.BAK`bestanden die meerdere logboek bestanden bevatten, kunnen niet worden hersteld.
- Back-ups die data bases bevatten die groter zijn dan 8TB, actieve in-Memory OLTP-objecten of meer dan 280 bestanden kunnen niet worden hersteld op een Algemeen-exemplaar. 
- Back-ups met data bases die groter zijn dan 4 TB of in-Memory OLTP-objecten met de totale grootte die groter is dan de grootte die is beschreven in [resource limieten](sql-database-managed-instance-resource-limits.md) , kunnen niet worden hersteld op bedrijfskritiek exemplaar.
Zie Restore statements ( [instructies herstellen](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)) voor meer informatie over Restore-instructies.

### <a name="service-broker"></a>Service Broker

Service Broker met meerdere exemplaren wordt niet ondersteund:

- `sys.routes`: Als vereiste moet u het adres selecteren uit sys. routes. Het adres moet lokaal op elke route zijn. Zie [sys. routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: U kunt niet `CREATE ROUTE` gebruiken `ADDRESS` met andere `LOCAL`dan. Zie [route maken](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: U kunt niet `ALTER ROUTE` gebruiken `ADDRESS` met andere `LOCAL`dan. Zie [ALTER route](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Opgeslagen procedures, functies en triggers

- `NATIVE_COMPILATION`wordt niet ondersteund in de laag Algemeen.
- De volgende [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) -opties worden niet ondersteund: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`wordt niet ondersteund. See [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell`wordt niet ondersteund. Zie [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`worden niet ondersteund, inclusief `sp_addextendedproc`  en `sp_dropextendedproc`. Zie [uitgebreide opgeslagen procedures](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` en`sp_detach_db` worden niet ondersteund. Zie [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)en [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Environment"></a>Omgevings beperkingen

### <a name="subnet"></a>Subnet
-  U kunt geen andere resources (bijvoorbeeld virtuele machines) plaatsen in het subnet waar u uw beheerde exemplaar hebt geïmplementeerd. Implementeer deze bronnen met behulp van een ander subnet.
- Het subnet moet voldoende beschik bare [IP-adressen](sql-database-managed-instance-connectivity-architecture.md#network-requirements)hebben. Mini maal is 16, terwijl aanbeveling ten minste 32 IP-adressen in het subnet.
- [Service-eind punten kunnen niet worden gekoppeld aan het subnet van het beheerde exemplaar](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Zorg ervoor dat de optie service-eind punten is uitgeschakeld tijdens het maken van het virtuele netwerk.
- Het aantal vCores en typen instanties dat u in een regio kunt implementeren, hebben een aantal [beperkingen en](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)beperkingen.
- Er zijn enkele [beveiligings regels die moeten worden toegepast op het subnet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- VNet kan worden geïmplementeerd met behulp van resource model-Klassiek model voor VNet wordt niet ondersteund.
- Nadat een beheerd exemplaar is gemaakt, wordt het beheerde exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.
- Sommige services, zoals App Service omgevingen, Logic apps en beheerde instanties (gebruikt voor geo-replicatie, transactionele replicatie of via gekoppelde servers), hebben geen toegang tot beheerde instanties in verschillende regio's als hun VNets zijn verbonden met behulp van [Global peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). U kunt via VNet-gateways verbinding maken met deze resources via ExpressRoute of VNet-naar-VNet.

### <a name="tempdb-size"></a>TEMPDB-grootte

De maximale bestands grootte van `tempdb` mag niet groter zijn dan 24 GB per kern op een algemeen laag. De maximale `tempdb` grootte van een bedrijfskritiek laag wordt beperkt door de opslag grootte van het exemplaar. `Tempdb`de grootte van het logboek bestand is beperkt tot 120 GB op Algemeen en Bedrijfskritiek lagen. Sommige query's retour neren mogelijk een fout als deze meer dan 24 GB per kern `tempdb` nodig heeft of als er meer dan 120 GB aan logboek gegevens worden geproduceerd.

## <a name="Changes"></a>Gedrags wijzigingen

De volgende variabelen, functies en weer gaven retour neren verschillende resultaten:

- `SERVERPROPERTY('EngineEdition')`retourneert de waarde 8. Deze eigenschap is een unieke aanduiding voor een beheerd exemplaar. Zie [Server Property](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`retourneert NULL omdat het concept van instance voor SQL Server niet van toepassing is op een beheerd exemplaar. Zie [Server Property (' instanceName ')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`retourneert een volledige DNS-naam (' connectable '), bijvoorbeeld my-managed-instance.wcus17662feb9ce98.database.windows.net. Zie [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`retourneert de volledige DNS-naam ' connectable ', zoals `myinstance.domain.database.windows.net` voor de eigenschappen ' name ' en ' data_source '. Zie [sys. SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`retourneert NULL omdat het concept van de service bestaat voor SQL Server niet van toepassing is op een beheerd exemplaar. Zie [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID`wordt ondersteund. Retourneert NULL als de Azure AD-aanmelding zich niet in sys. syslogins bevindt. Zie [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID`wordt niet ondersteund. De verkeerde gegevens worden geretourneerd. Dit is een tijdelijk bekend probleem. Zie [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a>Bekende problemen en beperkingen

### <a name="cross-database-service-broker-dialogs-dont-work-after-service-tier-upgrade"></a>Service Broker dialoog vensters voor meerdere data bases werken niet na een upgrade van de servicelaag

**Vallen** Aug 2019

Meerdere data base-Service Broker dialogen kunnen de berichten niet leveren nadat de bewerking van de service tier is gewijzigd. Elke wijziging van de vCores of de opslag grootte van een exemplaar in het `service_broke_guid` beheerde exemplaar leidt ertoe dat de waarde van de weer gave [sys. data bases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) voor alle data bases wordt gewijzigd. Elke `DIALOG` gemaakt met de instructie [begin dialog](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql) waarmee naar service-Brokers in de andere data base door de GUID wordt verwezen, kan geen berichten afleveren.

**Enkele** Stop alle activiteiten die gebruikmaken van cross-data base Service Broker dialoog venster gesprekken voordat u de servicelaag bijwerkt en opnieuw initialiseert.

### <a name="some-aad-login-types-cannot-be-impersonated"></a>Sommige AAD-aanmeldings typen kunnen niet worden geïmiteerd

**Vallen** 2019 juli

Imitatie met `EXECUTE AS USER` of `EXECUTE AS LOGIN` van volgende Aad-principals wordt niet ondersteund:
-   Aliased AAD-gebruikers. In dit geval `15517`wordt de volgende fout geretourneerd.
- AAD-aanmeldingen en-gebruikers op basis van AAD-toepassingen of service-principals. De volgende fouten worden in dit geval `15517` en `15406`worden geretourneerd.

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@queryde para meter wordt niet ondersteund in sp_send_db_mail

**Vallen** April 2019

De `@query` para meter in de [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) -procedure werkt niet.

### <a name="aad-logins-and-users-are-not-supported-in-tools"></a>AAD-aanmeldingen en gebruikers worden niet ondersteund in hulpprogram ma's

**Vallen** Jan 2019

SQL Server Management Studio en SQL Server Data Tools niet fuly Azure-Acctive Directory-aanmeldingen en-gebruikers te ondersteunen.
- Het gebruik van Azure AD server-principals (aanmeldingen) en gebruikers (open bare preview) met SQL Server Data Tools wordt momenteel niet ondersteund.
- Scripts voor Azure AD server-principals (aanmeldingen) en gebruikers (open bare preview) worden niet ondersteund in SQL Server Management Studio.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB-structuur en-inhoud worden opnieuw gemaakt

De `tempdb` data base is altijd gesplitst in 12 gegevens bestanden en de bestands structuur kan niet worden gewijzigd. De maximale grootte per bestand kan niet worden gewijzigd en er kunnen geen nieuwe bestanden aan `tempdb`worden toegevoegd. `Tempdb`wordt altijd opnieuw gemaakt als een lege data base wanneer het exemplaar wordt gestart of een failover wordt uitgevoerd `tempdb` , en eventuele wijzigingen worden niet bewaard.

### <a name="exceeding-storage-space-with-small-database-files"></a>Opslag ruimte overschrijden met kleine database bestanden

`CREATE DATABASE`, `ALTER DATABASE ADD FILE` en`RESTORE DATABASE` -instructies kunnen mislukken omdat het exemplaar de Azure Storage limiet kan bereiken.

Elk Algemeen Managed instance heeft tot 35 TB aan opslag ruimte gereserveerd voor Azure Premium. Elk database bestand wordt geplaatst op een afzonderlijke fysieke schijf. Schijf grootten kunnen 128 GB, 256 GB, 512 GB, 1 TB of 4 TB zijn. Voor ongebruikte ruimte op de schijf worden geen kosten in rekening gebracht, maar de totale som van Azure Premium-schijf grootten mag niet groter zijn dan 35 TB. In sommige gevallen kan een beheerd exemplaar dat niet 8 TB in totaal nodig heeft, de Azure-limiet van 35 TB overschrijden bij de opslag grootte vanwege interne fragmentatie.

Een Algemeen beheerde instantie kan bijvoorbeeld één groot bestand hebben dat 1,2 TB groot is voor een schijf van 4 TB. Het bestand kan ook 248 bestanden van 1 GB bestanden bevatten die elk op afzonderlijke 128 GB-schijven worden geplaatst. In dit voorbeeld:

- De totale toegewezen schijf ruimte is 1 x 4 TB + 248 x 128 GB = 35 TB.
- De totale gereserveerde ruimte voor data bases op het exemplaar is 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

In dit voor beeld ziet u dat als gevolg van een specifieke distributie van bestanden onder bepaalde omstandigheden, een beheerd exemplaar de limiet van 35 TB kan bereiken die is gereserveerd voor een gekoppelde Azure Premium-schijf wanneer u dit mogelijk niet verwacht.

In dit voor beeld blijven bestaande data bases werken en kunnen ze zonder enig probleem groeien zolang er geen nieuwe bestanden worden toegevoegd. Nieuwe data bases kunnen niet worden gemaakt of hersteld omdat er onvoldoende ruimte is voor nieuwe schijf stations, zelfs als de totale grootte van alle data bases niet de limiet voor de exemplaar grootte bereikt. De fout die in dat geval wordt geretourneerd, is niet duidelijk.

U kunt [het aantal resterende bestanden identificeren](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) met behulp van systeem weergaven. Als u deze limiet bereikt, kunt u [een aantal kleinere bestanden leegmaken en verwijderen met behulp van de DBCC SHRINKFILE-instructie](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) of overschakelen naar de [bedrijfskritiek-laag, die deze limiet niet overschrijdt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>GUID-waarden die worden weer gegeven in plaats van database namen

In verschillende systeem weergaven, prestatie meter items, fout berichten, XEvents en fouten logboek vermeldingen worden GUID-database-id's weer gegeven in plaats van de werkelijke database namen. Vertrouw niet op deze GUID-id's, omdat deze in de toekomst worden vervangen door feitelijke database namen.

### <a name="error-logs-arent-persisted"></a>Fouten logboeken zijn niet persistent gemaakt

Fouten logboeken die beschikbaar zijn in het beheerde exemplaar, worden niet persistent gemaakt en hun grootte is niet opgenomen in de maximale opslag limiet. Fout logboeken kunnen automatisch worden gewist als er een failover wordt uitgevoerd.

### <a name="error-logs-are-verbose"></a>Fouten logboeken zijn uitgebreid

Een beheerd exemplaar plaatst uitgebreide informatie in fouten logboeken en veel ervan is niet relevant. 

**Enkele** Gebruik een aangepaste procedure om fout logboeken te lezen die een aantal irrelevante vermeldingen filteren. Zie [Managed instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)voor meer informatie.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Het transactie bereik van twee data bases binnen hetzelfde exemplaar wordt niet ondersteund

De `TransactionScope` klasse in .net werkt niet als twee query's worden verzonden naar twee data bases binnen hetzelfde exemplaar onder hetzelfde transactie bereik:

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

Hoewel deze code werkt met gegevens binnen hetzelfde exemplaar, is MSDTC vereist.

**Enkele** Gebruik [SqlConnection. ChangeDatabase (teken reeks)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) als u een andere data base in een verbindings context wilt gebruiken in plaats van twee verbindingen te gebruiken.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR-modules en gekoppelde servers kunnen soms niet verwijzen naar een lokaal IP-adres

CLR-modules die worden geplaatst in een beheerd exemplaar en gekoppelde servers of gedistribueerde query's die verwijzen naar een huidige instantie, kunnen het IP-adres van een lokaal exemplaar soms niet omzetten. Deze fout is een tijdelijk probleem.

**Enkele** Gebruik, indien mogelijk, context verbindingen in een CLR-module.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-database-managed-instance.md) voor meer informatie over beheerde exemplaren.
- Zie [Azure SQL database functie vergelijking](sql-database-features.md)voor een overzicht van functies en vergelijkings lijsten.
- Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor een Snelstartgids waarin wordt uitgelegd hoe u een nieuw beheerd exemplaar maakt.

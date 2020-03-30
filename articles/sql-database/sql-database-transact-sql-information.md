---
title: T-SQL-verschillen-migratie oplossen
description: Transact-SQL-instructies die minder dan volledig worden ondersteund in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: e0870ac9dc818ca07e149421b486136c76dd61a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208815"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Transact-SQL-verschillen oplossen tijdens migratie naar SQL Database

Wanneer [u uw database migreert](sql-database-single-database-migrate.md) van SQL Server naar Azure SQL Server, u ontdekken dat uw database enige re-engineering vereist voordat de SQL Server kan worden gemigreerd. Dit artikel biedt richtlijnen om u te helpen bij zowel het uitvoeren van deze re-engineering en het begrijpen van de onderliggende redenen waarom de re-engineering nodig is. Als u onverenigbaarheden wilt detecteren, gebruikt u de [Gegevensmigratieassistent (DMA).](https://www.microsoft.com/download/details.aspx?id=53595)

## <a name="overview"></a>Overzicht

De meeste Transact-SQL-functies die toepassingen gebruiken, worden volledig ondersteund in zowel Microsoft SQL Server als Azure SQL Database. De belangrijkste SQL-componenten, zoals gegevenstypen, operatoren, tekenreeksen, rekenkundige, logische en cursorfuncties, werken bijvoorbeeld identiek in SQL Server en SQL Database. Er zijn echter een paar T-SQL-verschillen in DDL-elementen (data-definition taal) en DML (datamanipulatietaal) wat resulteert in T-SQL-instructies en query's die slechts gedeeltelijk worden ondersteund (die we later in dit artikel bespreken).

Daarnaast zijn er enkele functies en syntaxis die helemaal niet worden ondersteund omdat Azure SQL Database is ontworpen om functies te isoleren van afhankelijkheden van de hoofddatabase en het besturingssysteem. Als zodanig zijn de meeste activiteiten op serverniveau niet geschikt voor SQL Database. T-SQL-instructies en -opties zijn niet beschikbaar als ze opties op serverniveau, onderdelen van het besturingssysteem configureren of bestandssysteemconfiguratie opgeven. Wanneer dergelijke mogelijkheden vereist zijn, is een geschikt alternatief vaak op een andere manier beschikbaar vanuit SQL Database of vanuit een andere Azure-functie of -service.

Hoge beschikbaarheid is bijvoorbeeld ingebouwd in Azure SQL Database met behulp van technologie die vergelijkbaar is met [Always On Availability Groups.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) T-SQL-instructies met betrekking tot beschikbaarheidsgroepen worden niet ondersteund door SQL Database en de dynamische beheerweergaven met betrekking tot Always On Availability-groepen worden ook niet ondersteund.

Zie [Azure SQL Database-functievergelijking](sql-database-features.md)voor een lijst met functies die worden ondersteund en niet worden ondersteund door SQL Database. De lijst op deze pagina vult dat richtlijnen en functies artikel, en richt zich op Transact-SQL verklaringen.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Syntaxisinstructies transact-SQL met gedeeltelijke verschillen

De kernverklaringen van DDL (gegevensdefinitietaal) zijn beschikbaar, maar sommige DDL-instructies hebben extensies met betrekking tot schijfplaatsing en niet-ondersteunde functies.

- CREATE- en ALTER DATABASE-instructies hebben meer dan drie dozijn opties. De instructies bevatten bestandsplaatsing, FILESTREAM en servicebroker-opties die alleen van toepassing zijn op SQL Server. Dit maakt mogelijk niet uit of u databases maakt voordat u migreert, maar als u T-SQL-code migreert die databases maakt, moet u [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) vergelijken met de SQL Server-syntaxis bij [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) om ervoor te zorgen dat alle opties die u gebruikt worden ondersteund. CREATE DATABASE voor Azure SQL Database heeft ook servicedoelstelling en elastische schaalopties die alleen van toepassing zijn op SQL Database.
- De tabelinstructies MAKEN en WIJZIGEN hebben Bestandstabelopties die niet kunnen worden gebruikt in SQL Database omdat FILESTREAM niet wordt ondersteund.
- CREATE- en ALTER-inloginstructies worden ondersteund, maar SQL Database biedt niet alle opties. Om uw database draagbaarder te maken, moedigt SQL Database het gebruik van opgenomen databasegebruikers aan in plaats van waar mogelijk aan te meldingen. Zie [AANMELDING MAKEN/ALTER LOGIN](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) en [Aanmeldingen en gebruikers beheren](sql-database-manage-logins.md)voor meer informatie.

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-syntaxis die niet wordt ondersteund in Azure SQL-database

Naast Transact-SQL-instructies met betrekking tot de niet-ondersteunde functies die worden beschreven in [azure SQL Database-functievergelijking](sql-database-features.md), worden de volgende instructies en groepen instructies niet ondersteund. Als zodanig, als uw database te migreren is met behulp van een van de volgende functies, re-engineer en uw T-SQL om deze T-SQL functies en instructies te elimineren.

- Systeemobjecten sorteren
- Verbinding gerelateerd: Eindpuntverklaringen. SQL Database biedt geen ondersteuning voor Windows-verificatie, maar ondersteunt wel de vergelijkbare Azure Active Directory-verificatie. Voor sommige verificatietypen is de nieuwste versie van SSMS vereist. Zie [Verbinding maken met SQL Database of SQL Data Warehouse met behulp van Azure Active Directory-verificatie](sql-database-aad-authentication.md) voor meer informatie.
- Databaseoverschrijdende query’s met drie of vier onderdeelnamen. (Alleen-lezen query’s die databaseoverschrijdend zijn worden ondersteund dankzij [elastische databasequery’s](sql-database-elastic-query-overview.md).)
- Databaseoverschrijdend het eigendom koppelen, instelling `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Gebruik in plaats daarvan 'EXECUTE AS USER'.
- Versleuteling wordt ondersteund, behalve voor Extensible Key Management
- Gebeurtenis: gebeurtenissen, gebeurtenismeldingen, querymeldingen
- Bestandsplaatsing: syntaxis met betrekking tot databasebestandsplaatsing, -grootte en databasebestanden die automatisch worden beheerd door Microsoft Azure.
- Hoge beschikbaarheid: syntaxis met betrekking tot hoge beschikbaarheid, die wordt beheerd via uw Microsoft Azure-account. Dit omvat syntaxis voor back-ups, herstellen, Always On, databasespiegeling, de back-upfunctie voor logboekbestanden en herstelmodi.
- Logboeklezer: syntaxis die is gebaseerd op de logboeklezer, die niet beschikbaar is in SQL Database: Push Replicatie, Gegevensvastleggen wijzigen. SQL Database kan abonnee zijn op een artikel over push-replicatie.
- Functies: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: syntaxis met betrekking tot hardwaregerelateerde serverinstellingen: zoals geheugen, werknemersthreads, CPU-affiniteit, traceflags. Gebruik in plaats daarvan servicelagen en rekengroottes.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` `OPENDATASOURCE`, en vierdelige namen
- .NET Framework: CLR-integratie met SQL Server
- Semantische zoekopdrachten
- Serverreferenties: gebruik in plaats daarvan [referenties met een databasebereik.](https://msdn.microsoft.com/library/mt270260.aspx)
- Items op serverniveau: `sys.login_token`serverrollen, . `GRANT`, `REVOKE`, en `DENY` van de machtigingen op serverniveau zijn niet beschikbaar, hoewel enkele hiervan worden vervangen door machtigingen op databaseniveau. Sommige handige DMV’s op serverniveau hebben vergelijkbare DMV’s op databaseniveau.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-opties en `RECONFIGURE`. Sommige opties zijn beschikbaar met [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: syntaxis die is gebaseerd op de SQL Server Agent of de MSDB-database: waarschuwingen, operators, centrale beheerservers. Gebruik in plaats daarvan opties voor scripts, zoals Azure PowerShell.
- SQL Server-audit: gebruik sql-databasecontrole in plaats daarvan.
- SQL Server-tracering
- Trace flags: Sommige traceflagitems zijn verplaatst naar compatibiliteitsmodi.
- Transact-SQL-foutopsporing
- Triggers: triggers binnen het serverbereik of aanmeldingstriggers
- `USE`-instructie: als u de databasecontext wilt wijzigen naar een andere database, moet u een nieuwe verbinding maken met de nieuwe database.

## <a name="full-transact-sql-reference"></a>Volledige naslaginformatie voor Transact-SQL

Zie [Transact-SQL Reference (Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) in SQL Server Books Online voor meer informatie over transact-SQL-grammatica, gebruik en voorbeelden.

### <a name="about-the-applies-to-tags"></a>Over het label 'Van toepassing op'

De Transact-SQL-referentie bevat artikelen met betrekking tot SQL Server-versies 2008 tot nu. Onder de artikeltitel bevindt zich een pictogrambalk, met de vier SQL Server-platforms en met vermelding van de toepasbaarheid. Beschikbaarheidsgroepen zijn bijvoorbeeld geïntroduceerd in SQL Server 2012. Het artikel [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) geeft aan dat de instructie van toepassing is op **SQL Server (vanaf 2012).** De instructie is niet van toepassing op SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse en Parallel Data Warehouse.

In sommige gevallen kan het algemene onderwerp van een artikel in een product worden gebruikt, maar er zijn kleine verschillen tussen producten. De verschillen worden aangegeven op de middelpunten in het artikel, indien van toepassing. In sommige gevallen kan het algemene onderwerp van een artikel in een product worden gebruikt, maar er zijn kleine verschillen tussen producten. De verschillen worden aangegeven op de middelpunten in het artikel, indien van toepassing. Het artikel TRIGGER MAKEN is bijvoorbeeld beschikbaar in SQL Database. Maar de **optie ALL SERVER** voor triggers op serverniveau geeft aan dat triggers op serverniveau niet kunnen worden gebruikt in SQL Database. Gebruik in plaats daarvan triggers op databaseniveau.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL Database-functievergelijking](sql-database-features.md)voor een lijst met functies die worden ondersteund en niet worden ondersteund door SQL Database. De lijst op deze pagina vult dat richtlijnen en functies artikel, en richt zich op Transact-SQL verklaringen.

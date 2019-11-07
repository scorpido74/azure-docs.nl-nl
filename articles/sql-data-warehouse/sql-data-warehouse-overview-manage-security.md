---
title: Een Data Base beveiligen
description: Tips voor het beveiligen van een data base in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c51a945bae7cc0b03c219bc041d64f4703baef19
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692571"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Een data base in SQL Data Warehouse beveiligen
> [!div class="op_single_selector"]
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

In dit artikel worden de basis beginselen beschreven van het beveiligen van uw Azure SQL Data Warehouse-data base. In dit artikel wordt met name aan de slag met resources voor het beperken van toegang, het beveiligen van gegevens en het bewaken van activiteiten in een Data Base.

## <a name="connection-security"></a>Verbindingsbeveiliging
Verbindingsbeveiliging verwijst naar de manier waarop u verbindingen met uw database beperkt en beveiligt met behulp van firewallregels en verbindingsversleuteling.

Firewallregels worden zowel door de server als de database gebruikt om verbindingspogingen van IP-adressen die niet expliciet zijn goedgekeurd te weigeren. Als u verbindingen van uw toepassing of het open bare IP-adres van de client computer wilt toestaan, moet u eerst een firewall regel op server niveau maken met behulp van de Azure Portal, REST API of Power shell. Als best practice moet u het IP-adresbereik dat is toegestaan door uw serverfirewall zoveel mogelijk beperken.  Om toegang te krijgen tot Azure SQL Data Warehouse vanaf uw lokale computer, zorgt u ervoor dat de firewall op uw netwerk en lokale computer uitgaande communicatie toestaat op TCP-poort 1433.  

SQL Data Warehouse maakt gebruik van firewall regels op server niveau. Het biedt geen ondersteuning voor firewall regels op database niveau. Zie [Azure SQL database firewall][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule]voor meer informatie.

Verbindingen met uw SQL Data Warehouse worden standaard versleuteld.  Het wijzigen van de verbindings instellingen om versleuteling uit te scha kelen, wordt genegeerd.

## <a name="authentication"></a>Authentication
Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL Data Warehouse ondersteunt momenteel SQL Server-verificatie met een gebruikers naam en wacht woord en met Azure Active Directory. 

Wanneer u de logische server voor uw database hebt gemaakt, hebt u een aanmelding 'serverbeheerder' opgegeven met een gebruikersnaam en wachtwoord. Met deze referenties kunt u zich bij elke Data Base op die server als de eigenaar van de data base of "dbo" verifiëren via SQL Server-verificatie.

Als best practice, moeten gebruikers van uw organisatie echter een ander account gebruiken om te verifiëren. Op deze manier kunt u de machtigingen die aan de toepassing worden verleend, beperken en het risico op schadelijke activiteiten verminderen voor het geval uw toepassings code kwetsbaar is voor een SQL-injectie aanval. 

Als u een SQL Server geverifieerde gebruiker wilt maken, maakt u verbinding met de **hoofd** database op uw server met de aanmelding van de server beheerder en maakt u een nieuwe Server aanmelding.  Daarnaast is het een goed idee om een gebruiker in de hoofd database voor Azure SQL Data Warehouse gebruikers te maken. Als u een gebruiker maakt in de Master, kan een gebruiker zich aanmelden met hulpprogram ma's als SSMS zonder een database naam op te geven.  Ook kunnen ze de object Verkenner gebruiken om alle data bases op een SQL-Server weer te geven.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Maak vervolgens verbinding met uw **SQL Data Warehouse-data base** met uw server beheerders aanmelding en een database gebruiker te maken op basis van de Server aanmelding die u hebt gemaakt.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Als u een gebruiker wilt machtigen om aanvullende bewerkingen uit te voeren, zoals het maken van aanmeldingen of het maken van nieuwe data bases, wijst u de gebruiker toe aan de `Loginmanager` en `dbmanager` rollen in de hoofd database. Zie [data bases en aanmeldingen beheren in Azure SQL database][Managing databases and logins in Azure SQL Database]voor meer informatie over deze aanvullende rollen en het verifiëren van een SQL database.  Zie [verbinding maken met SQL data warehouse met behulp van Azure Active Directory-verificatie][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]voor meer informatie.

## <a name="authorization"></a>Autorisatie
Autorisatie verwijst naar wat u in een Azure SQL Data Warehouse-Data Base kunt doen. Autorisatie bevoegdheden worden bepaald door lidmaatschappen en machtigingen van de rol. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen. Als u rollen wilt beheren, kunt u de volgende opgeslagen procedures gebruiken:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Het serverbeheerdersaccount waarmee u verbinding maakt is lid van db_owner, die geautoriseerd is om alle bewerkingen binnen de database uit te voeren. Gebruik dit account voor het implementeren van schema-updates en andere beheerbewerkingen. Gebruik het 'ApplicationUser'-account met beperktere machtigingen om vanuit van uw toepassing verbinding te maken met de database met de minste bevoegdheden die nodig zijn voor uw toepassing.

Er zijn manieren om verder te beperken wat een gebruiker kan doen met Azure SQL Data Warehouse:

* Met gedetailleerde [machtigingen][Permissions] kunt u bepalen welke bewerkingen u op afzonderlijke kolommen, tabellen, weer gaven, schema's, procedures en andere objecten in de-data base. Gebruik gedetailleerde machtigingen om de meeste controle te hebben en de minimale machtigingen te verlenen die nodig zijn. 
* Andere [database rollen][Database roles] dan db_datareader en db_datawriter kunnen worden gebruikt voor het maken van krachtigere toepassings gebruikers accounts of minder krachtige beheer accounts. De ingebouwde vaste database rollen bieden een eenvoudige manier om machtigingen te verlenen, maar kunnen wel tot het verlenen van meer machtigingen dan nodig zijn.
* [Opgeslagen procedures][Stored procedures] kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

In het volgende voor beeld wordt lees toegang verleend aan een door de gebruiker gedefinieerd schema.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Het beheren van data bases en logische servers van de Azure Portal of het gebruik van de Azure Resource Manager-API wordt bepaald door de roltoewijzingen van uw portal-gebruikers account. Zie op [rollen gebaseerd toegangs beheer in azure Portal][Role-based access control in Azure portal]voor meer informatie.

## <a name="encryption"></a>Versleuteling
Met Azure SQL Data Warehouse Transparent Data Encryption (TDE) kunt u zich beschermen tegen de dreiging van schadelijke activiteiten door uw gegevens in rust te versleutelen en ontsleutelen.  Wanneer u uw data base versleutelt, worden gekoppelde back-ups en transactie logboek bestanden versleuteld zonder dat er wijzigingen hoeven te worden aangebracht in uw toepassingen. TDE versleutelt de opslag van een volledige data base met behulp van een symmetrische sleutel die de database versleutelings sleutel wordt genoemd. 

In SQL Database wordt de database versleutelings sleutel beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke SQL Database-Server. Micro soft roteert deze certificaten ten minste elke 90 dagen automatisch. De versleutelings algoritme die door SQL Data Warehouse wordt gebruikt, is AES-256. Zie [transparent Data Encryption][Transparent Data Encryption]voor een algemene beschrijving van TDe.

U kunt uw data base versleutelen met behulp van de [Azure Portal][Encryption with Portal] of [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Volgende stappen
Zie [verbinding maken met SQL Data Warehouse][Connect to SQL Data Warehouse]voor meer informatie en voor beelden over het maken van verbinding met uw SQL data warehouse met andere protocollen.

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure

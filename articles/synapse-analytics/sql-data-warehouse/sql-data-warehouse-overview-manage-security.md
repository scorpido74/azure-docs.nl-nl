---
title: Een database beveiligen
description: Tips voor het beveiligen van een Data Base en het ontwikkelen van oplossingen in een Synapse SQL-groeps resource.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 06f62fd656357e16396a0458a9afee12dcfa507f
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629366"
---
# <a name="secure-a-database-in-azure-synapse"></a>Een data base in azure Synapse beveiligen

> [!div class="op_single_selector"]
>
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Dit artikel begeleidt u stapsgewijs door de basis beginselen van het beveiligen van uw Synapse SQL-groep. In dit artikel wordt met name aan de slag met resources voor het beperken van toegang, het beveiligen van gegevens en het bewaken van activiteiten in een Data Base die is ingericht met een SQL-groep.

## <a name="connection-security"></a>Verbindingsbeveiliging

Verbindingsbeveiliging verwijst naar de manier waarop u verbindingen met uw database beperkt en beveiligt met behulp van firewallregels en verbindingsversleuteling.

Firewall regels worden door de [logische SQL-Server](../../azure-sql/database/logical-servers.md) en de bijbehorende data bases gebruikt om verbindings pogingen te weigeren van IP-adressen die niet expliciet zijn goedgekeurd. Als u verbindingen van uw toepassing of het open bare IP-adres van de client computer wilt toestaan, moet u eerst een firewall regel op server niveau maken met behulp van de Azure Portal, REST API of Power shell.

Als best practice moet u de IP-adresbereiken zo veel mogelijk beperken via de firewall op server niveau.  Als u toegang wilt krijgen tot de SQL-groep vanaf uw lokale computer, zorgt u ervoor dat de firewall op uw netwerk en lokale computer uitgaande communicatie toestaat op TCP-poort 1433.  

Azure Synapse Analytics maakt gebruik van IP-firewall regels op server niveau. Het biedt geen ondersteuning voor IP-firewall regels op database niveau. Zie [Azure SQL database firewall-regels](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor meer informatie.

Verbindingen met uw SQL-groep worden standaard versleuteld.  Het wijzigen van de verbindings instellingen om versleuteling uit te scha kelen, wordt genegeerd.

## <a name="authentication"></a>Verificatie

Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL-pool ondersteunt momenteel SQL Server verificatie met een gebruikers naam en wacht woord en met Azure Active Directory.

Wanneer u de server voor uw data base hebt gemaakt, hebt u de aanmelding ' Server beheerder ' opgegeven met een gebruikers naam en wacht woord. Met deze referenties kunt u zich bij elke Data Base op die server als de eigenaar van de data base of "dbo" verifiëren via SQL Server-verificatie.

Als best practice, moeten gebruikers van uw organisatie echter een ander account gebruiken om te verifiëren. Op deze manier kunt u de machtigingen die aan de toepassing worden verleend, beperken en het risico op schadelijke activiteiten verminderen voor het geval uw toepassings code kwetsbaar is voor een SQL-injectie aanval.

Als u een SQL Server geverifieerde gebruiker wilt maken, maakt u verbinding met de **hoofd** database op uw server met de aanmelding van de server beheerder en maakt u een nieuwe Server aanmelding.  Het is een goed idee om ook een gebruiker in de hoofd database te maken. Als u een gebruiker maakt in de Master, kan een gebruiker zich aanmelden met hulpprogram ma's als SSMS zonder een database naam op te geven.  Ook kunnen ze de object Verkenner gebruiken om alle data bases op een server weer te geven.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Maak vervolgens verbinding met uw **SQL-groeps database** met de aanmelding van de server beheerder en maakt u een database gebruiker op basis van de Server aanmelding die u hebt gemaakt.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Als u een gebruiker wilt machtigen om aanvullende bewerkingen uit te voeren, zoals het maken van aanmeldingen of het maken van nieuwe data bases, wijst u de gebruiker toe aan de `Loginmanager` `dbmanager` rollen en in de hoofd database.

Zie [data bases en aanmeldingen beheren in Azure SQL database](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor meer informatie over deze aanvullende rollen en het verifiëren van een SQL database.  Zie [verbinding maken met behulp van Azure Active Directory-verificatie](sql-data-warehouse-authentication.md)voor meer informatie over verbinding maken met behulp van Azure Active Directory.

## <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat u in een Data Base kunt doen zodra u bent geverifieerd en verbonden. Autorisatie bevoegdheden worden bepaald door lidmaatschappen en machtigingen van de rol. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen. Als u rollen wilt beheren, kunt u de volgende opgeslagen procedures gebruiken:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Het serverbeheerdersaccount waarmee u verbinding maakt is lid van db_owner, die geautoriseerd is om alle bewerkingen binnen de database uit te voeren. Gebruik dit account voor het implementeren van schema-updates en andere beheerbewerkingen. Gebruik het 'ApplicationUser'-account met beperktere machtigingen om vanuit van uw toepassing verbinding te maken met de database met de minste bevoegdheden die nodig zijn voor uw toepassing.

Er zijn manieren om verder te beperken wat een gebruiker in de data base kan doen:

* Met gedetailleerde [machtigingen](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kunt u bepalen welke bewerkingen u op afzonderlijke kolommen, tabellen, weer gaven, schema's, procedures en andere objecten in de-data base. Gebruik gedetailleerde machtigingen om de meeste controle te hebben en de minimale machtigingen te verlenen die nodig zijn.
* Andere [database rollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dan db_datareader en db_datawriter kunnen worden gebruikt voor het maken van krachtigere toepassings gebruikers accounts of minder krachtige beheer accounts. De ingebouwde vaste database rollen bieden een eenvoudige manier om machtigingen te verlenen, maar kunnen wel tot het verlenen van meer machtigingen dan nodig zijn.
* [Opgeslagen procedures](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

In het volgende voor beeld wordt lees toegang verleend aan een door de gebruiker gedefinieerd schema.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Het beheren van data bases en servers van de Azure Portal of het gebruik van de Azure Resource Manager-API wordt bepaald door de roltoewijzingen van uw portal-gebruikers account. Zie op [rollen gebaseerd toegangs beheer in azure Portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor meer informatie.

## <a name="encryption"></a>Versleuteling

Met Transparent Data Encryption (TDE) kunt u zich beschermen tegen de dreiging van schadelijke activiteiten door uw gegevens in rust te versleutelen en ontsleutelen. Wanneer u uw data base versleutelt, worden gekoppelde back-ups en transactie logboek bestanden versleuteld zonder dat er wijzigingen hoeven te worden aangebracht in uw toepassingen. TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel (de zogeheten databaseversleutelingssleutel).

In SQL Database wordt de database versleutelings sleutel beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke server. Micro soft roteert deze certificaten ten minste elke 90 dagen automatisch. De gebruikte versleutelings algoritme is AES-256. Zie [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor een algemene beschrijving van TDe.

U kunt uw data base versleutelen met behulp van de [Azure Portal](sql-data-warehouse-encryption-tde.md) of [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Volgende stappen

Zie [verbinding maken met SQL-groep](../sql/connect-overview.md)voor meer informatie en voor beelden over het maken van verbinding met uw magazijn met verschillende protocollen.

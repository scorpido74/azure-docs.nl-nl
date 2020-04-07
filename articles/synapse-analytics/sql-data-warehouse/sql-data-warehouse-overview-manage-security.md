---
title: Een database beveiligen
description: Tips voor het beveiligen van een database en het ontwikkelen van oplossingen in een Synapse SQL-poolbron.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 0c30294f2ca139a602074a980810e7c6737c4e2d
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742982"
---
# <a name="secure-a-database-in-azure-synapse"></a>Een database beveiligen in Azure Synapse

> [!div class="op_single_selector"]
>
> * [Overzicht van beveiliging](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

In dit artikel vindt u de basisprincipes van het beveiligen van uw Synapse SQL-pool. In dit artikel wordt u met name aan de slag gegaan met resources voor het beperken van de toegang, het beveiligen van gegevens en het controleren van activiteiten op een database die is ingericht met SQL-pool.

## <a name="connection-security"></a>Verbindingsbeveiliging

Verbindingsbeveiliging verwijst naar de manier waarop u verbindingen met uw database beperkt en beveiligt met behulp van firewallregels en verbindingsversleuteling.

Firewallregels worden door zowel de server als de database gebruikt om verbindingspogingen van IP-adressen die niet expliciet op de witte lijst zijn gezet, af te wijzen. Als u verbindingen wilt toestaan vanaf het openbare IP-adres van uw toepassing of clientmachine, moet u eerst een firewallregel op serverniveau maken met behulp van de Azure-portal, REST API of PowerShell.

Als best practice moet u het IP-adresbereik dat is toegestaan door uw serverfirewall zoveel mogelijk beperken.  Als u sql-pool vanaf uw lokale computer wilt openen, moet u ervoor zorgen dat de firewall op uw netwerk en lokale computer uitgaande communicatie op TCP-poort 1433 mogelijk maken.  

Azure Synapse Analytics maakt gebruik van IP-firewallregels op serverniveau. Het ondersteunt geen IP-firewallregels op databaseniveau. Zie Azure SQL [Database firewallregels](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor meer informatie

Verbindingen met uw SQL-groep worden standaard versleuteld.  Het wijzigen van verbindingsinstellingen om versleuteling uit te schakelen wordt genegeerd.

## <a name="authentication"></a>Verificatie

Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL-groep ondersteunt momenteel SQL Server-verificatie met een gebruikersnaam en wachtwoord en met Azure Active Directory.

Wanneer u de logische server voor uw database hebt gemaakt, hebt u een aanmelding 'serverbeheerder' opgegeven met een gebruikersnaam en wachtwoord. Met behulp van deze referenties u zich verifiëren naar elke database op die server als de eigenaar van de database, of dbo via SQL Server Authentication.

Als aanbevolen toepassing moeten de gebruikers van uw organisatie echter een ander account gebruiken om te verifiëren. Op deze manier u de machtigingen voor de toepassing beperken en de risico's van schadelijke activiteiten verminderen in het geval uw toepassingscode kwetsbaar is voor een SQL-injectieaanval.

Als u een SQL Server Authenticated-gebruiker wilt maken, maakt u verbinding met de **hoofddatabase** op uw server met uw serverbeheeraanmelding en maakt u een nieuwe serveraanmelding.  Het is een goed idee om ook een gebruiker in de hoofddatabase te maken. Als u een gebruiker in master maakt, kan een gebruiker zich aanmelden met behulp van hulpprogramma's zoals SSMS zonder een databasenaam op te geven.  Het stelt hen ook in staat om de objectexplorer te gebruiken om alle databases op een SQL-server te bekijken.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Maak vervolgens verbinding met uw **SQL-pooldatabase** met uw serverbeheerder-aanmelding en maak een databasegebruiker op basis van de serverlogin die u hebt gemaakt.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Als u een gebruiker toestemming wilt geven om aanvullende bewerkingen uit te `Loginmanager` `dbmanager` voeren, zoals het maken van aanmeldingen of het maken van nieuwe databases, wijst u de gebruiker toe aan de hoofdrollen in de hoofddatabase.

Zie [Databases en aanmeldingen beheren in Azure SQL Database voor](../../sql-database/sql-database-manage-logins.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)meer informatie over deze extra rollen en het verifiëren in een SQL-database.  Zie [Verbinding maken met Azure Active Directory voor](sql-data-warehouse-authentication.md)meer informatie over verbinding maken met Azure Active Directory.

## <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat u doen in een database zodra u bent geverifieerd en verbonden. Autorisatiebevoegdheden worden bepaald door rollidmaatschappen en machtigingen. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen. Als u rollen wilt beheren, u de volgende opgeslagen procedures gebruiken:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Het serverbeheerdersaccount waarmee u verbinding maakt is lid van db_owner, die geautoriseerd is om alle bewerkingen binnen de database uit te voeren. Gebruik dit account voor het implementeren van schema-updates en andere beheerbewerkingen. Gebruik het 'ApplicationUser'-account met beperktere machtigingen om vanuit van uw toepassing verbinding te maken met de database met de minste bevoegdheden die nodig zijn voor uw toepassing.

Er zijn manieren om verder te beperken wat een gebruiker kan doen in de database:

* Met granulaire [machtigingen](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) u bepalen welke bewerkingen u uitvoeren op afzonderlijke kolommen, tabellen, weergaven, schema's, procedures en andere objecten in de database. Gebruik gedetailleerde machtigingen om de meeste controle te hebben en de minimale machtigingen toe te kennen die nodig zijn.
* [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anders dan db_datareader en db_datawriter kunnen worden gebruikt om krachtigere gebruikersaccounts van toepassingen of minder krachtige beheeraccounts te maken. De ingebouwde vaste databaserollen bieden een eenvoudige manier om machtigingen te verlenen, maar kunnen resulteren in het verlenen van meer machtigingen dan nodig is.
* [Opgeslagen procedures](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

In het volgende voorbeeld wordt leestoegang tot een door de gebruiker gedefinieerd schema toegekend.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Het beheren van databases en logische servers vanuit de Azure-portal of het gebruik van de Azure Resource Manager API wordt beheerd door de roltoewijzingen van uw portalgebruikersaccount. Zie [Op rollen gebaseerd toegangsbeheer in Azure-portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor meer informatie.

## <a name="encryption"></a>Versleuteling

Transparent Data Encryption (TDE) helpt beschermen tegen de dreiging van kwaadaardige activiteiten door uw gegevens in rust te versleutelen en te decoderen. Wanneer u uw database versleutelt, worden gekoppelde back-ups en transactielogboekbestanden versleuteld zonder dat er wijzigingen in uw toepassingen nodig zijn. TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel (de zogeheten databaseversleutelingssleutel).

In SQL Database wordt de databaseversleutelingssleutel beschermd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke SQL Database-server. Microsoft draait deze certificaten automatisch ten minste om de 90 dagen. Het gebruikte encryptie-algoritme is AES-256. Zie [Transparante gegevensversleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor een algemene beschrijving van TDE.

U uw database versleutelen met de [Azure-portal](sql-data-warehouse-encryption-tde.md) of [T-SQL.](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="next-steps"></a>Volgende stappen

Zie Verbinding maken met [SQL-pool](sql-data-warehouse-connect-overview.md)voor meer informatie en voorbeelden over verbinding maken met uw magazijn met verschillende protocollen.

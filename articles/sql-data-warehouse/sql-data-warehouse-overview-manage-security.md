---
title: Een Data Base beveiligen
description: Tips voor het beveiligen van een Data Base en het ontwikkelen van oplossingen in de SQL-pool resource van SQL Analytics.
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
tags: azure-synapse
ms.openlocfilehash: 89ec405a348e3ace851fd5f5e17283a8036692a5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355607"
---
# <a name="secure-a-database-in-azure-synapse"></a>Een data base in azure Synapse beveiligen
> [!div class="op_single_selector"]
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

In dit artikel wordt stapsgewijs uitgelegd hoe u de SQL-groep in SQL Analytics kunt beveiligen. In dit artikel wordt met name aan de slag met resources voor het beperken van toegang, het beveiligen van gegevens en het bewaken van activiteiten in een Data Base die is ingericht met een SQL-groep.

## <a name="connection-security"></a>Verbindingsbeveiliging
Verbindingsbeveiliging verwijst naar de manier waarop u verbindingen met uw database beperkt en beveiligt met behulp van firewallregels en verbindingsversleuteling.

Firewall regels worden door de server en de data base gebruikt om verbindings pogingen af te wijzen van IP-adressen die niet expliciet zijn white list. Als u verbindingen van uw toepassing of het open bare IP-adres van de client computer wilt toestaan, moet u eerst een firewall regel op server niveau maken met behulp van de Azure Portal, REST API of Power shell. 

Als best practice moet u het IP-adresbereik dat is toegestaan door uw serverfirewall zoveel mogelijk beperken.  Als u toegang wilt krijgen tot de SQL-groep vanaf uw lokale computer, zorgt u ervoor dat de firewall op uw netwerk en lokale computer uitgaande communicatie toestaat op TCP-poort 1433.  

Azure Synapse Analytics maakt gebruik van IP-firewall regels op server niveau. Het biedt geen ondersteuning voor IP-firewall regels op database niveau. Zie [Azure SQL database firewall-regels](../sql-database/sql-database-firewall-configure.md) voor meer informatie.

Verbindingen met uw SQL-groep worden standaard versleuteld.  Het wijzigen van de verbindings instellingen om versleuteling uit te scha kelen, wordt genegeerd.

## <a name="authentication"></a>Verificatie
Verificatie verwijst naar hoe u uw identiteit bewijst bij het maken van verbinding met de database. SQL-pool ondersteunt momenteel SQL Server verificatie met een gebruikers naam en wacht woord en met Azure Active Directory. 

Wanneer u de logische server voor uw database hebt gemaakt, hebt u een aanmelding 'serverbeheerder' opgegeven met een gebruikersnaam en wachtwoord. Met deze referenties kunt u zich bij elke Data Base op die server als de eigenaar van de data base of "dbo" verifiëren via SQL Server-verificatie.

Als best practice, moeten gebruikers van uw organisatie echter een ander account gebruiken om te verifiëren. Op deze manier kunt u de machtigingen die aan de toepassing worden verleend, beperken en het risico op schadelijke activiteiten verminderen voor het geval uw toepassings code kwetsbaar is voor een SQL-injectie aanval. 

Als u een SQL Server geverifieerde gebruiker wilt maken, maakt u verbinding met de **hoofd** database op uw server met de aanmelding van de server beheerder en maakt u een nieuwe Server aanmelding.  Het is een goed idee om ook een gebruiker in de hoofd database te maken. Als u een gebruiker maakt in de Master, kan een gebruiker zich aanmelden met hulpprogram ma's als SSMS zonder een database naam op te geven.  Ook kunnen ze de object Verkenner gebruiken om alle data bases op een SQL-Server weer te geven.

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

Als u een gebruiker wilt machtigen om aanvullende bewerkingen uit te voeren, zoals het maken van aanmeldingen of het maken van nieuwe data bases, wijst u de gebruiker toe aan de `Loginmanager` en `dbmanager` rollen in de hoofd database. 

Zie [data bases en aanmeldingen beheren in Azure SQL database](../sql-database/sql-database-manage-logins.md)voor meer informatie over deze aanvullende rollen en het verifiëren van een SQL database.  Zie [verbinding maken met behulp van Azure Active Directory-verificatie](sql-data-warehouse-authentication.md)voor meer informatie over verbinding maken met behulp van Azure Active Directory.

## <a name="authorization"></a>Autorisatie
Autorisatie verwijst naar wat u in een Data Base kunt doen zodra u bent geverifieerd en verbonden. Autorisatie bevoegdheden worden bepaald door lidmaatschappen en machtigingen van de rol. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen. Als u rollen wilt beheren, kunt u de volgende opgeslagen procedures gebruiken:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Het serverbeheerdersaccount waarmee u verbinding maakt is lid van db_owner, die geautoriseerd is om alle bewerkingen binnen de database uit te voeren. Gebruik dit account voor het implementeren van schema-updates en andere beheerbewerkingen. Gebruik het 'ApplicationUser'-account met beperktere machtigingen om vanuit van uw toepassing verbinding te maken met de database met de minste bevoegdheden die nodig zijn voor uw toepassing.

Er zijn manieren om verder te beperken wat een gebruiker in de data base kan doen:

* Met gedetailleerde [machtigingen](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) kunt u bepalen welke bewerkingen u op afzonderlijke kolommen, tabellen, weer gaven, schema's, procedures en andere objecten in de-data base. Gebruik gedetailleerde machtigingen om de meeste controle te hebben en de minimale machtigingen te verlenen die nodig zijn. 
* Andere [database rollen](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15) dan db_datareader en db_datawriter kunnen worden gebruikt voor het maken van krachtigere toepassings gebruikers accounts of minder krachtige beheer accounts. De ingebouwde vaste database rollen bieden een eenvoudige manier om machtigingen te verlenen, maar kunnen wel tot het verlenen van meer machtigingen dan nodig zijn.
* [Opgeslagen procedures](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

In het volgende voor beeld wordt lees toegang verleend aan een door de gebruiker gedefinieerd schema.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Het beheren van data bases en logische servers van de Azure Portal of het gebruik van de Azure Resource Manager-API wordt bepaald door de roltoewijzingen van uw portal-gebruikers account. Zie op [rollen gebaseerd toegangs beheer in azure Portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure)voor meer informatie.

## <a name="encryption"></a>Versleuteling
Met Transparent Data Encryption (TDE) kunt u zich beschermen tegen de dreiging van schadelijke activiteiten door uw gegevens in rust te versleutelen en ontsleutelen. Wanneer u uw data base versleutelt, worden gekoppelde back-ups en transactie logboek bestanden versleuteld zonder dat er wijzigingen hoeven te worden aangebracht in uw toepassingen. TDE versleutelt de opslag van een volledige data base met behulp van een symmetrische sleutel die de database versleutelings sleutel wordt genoemd. 

In SQL Database wordt de database versleutelings sleutel beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke SQL Database-Server. Micro soft roteert deze certificaten ten minste elke 90 dagen automatisch. De gebruikte versleutelings algoritme is AES-256. Zie [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15)voor een algemene beschrijving van TDe.

U kunt uw data base versleutelen met behulp van de [Azure Portal](sql-data-warehouse-encryption-tde.md) of [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Volgende stappen
Zie [verbinding maken met SQL-groep](sql-data-warehouse-connect-overview.md)voor meer informatie en voor beelden over het maken van verbinding met uw magazijn met verschillende protocollen.

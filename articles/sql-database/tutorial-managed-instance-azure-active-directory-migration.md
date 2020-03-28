---
title: SQL ServerWindows-gebruikers en -groepen migreren naar beheerde instantie met Behulp van T-SQL
description: Meer informatie over het migreren van SQL Server on-premises Windows-gebruikers en -groepen naar beheerde instantie
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227926"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Zelfstudie: SQL Server on-premises Windows-gebruikers en -groepen migreren naar azure SQL Database-beheerde instantie met behulp van de syntaxis van T-SQL DDL

> [!NOTE]
> De syntaxis die wordt gebruikt om gebruikers en groepen te migreren naar beheerde instantie in dit artikel, bevindt zich in **een openbare preview**.

In dit artikel wordt u door het proces geleid van het migreren van uw on-premises Windows-gebruikers en -groepen in uw SQL Server naar een bestaande Azure SQL Database-beheerde instantie met behulp van T-SQL-syntaxis.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Aanmeldingen maken voor SQL Server
> - Een testdatabase maken voor migratie
> - Aanmeldingen, gebruikers en rollen maken
> - Back-upmaken en uw database herstellen naar beheerde instantie (MI)
> - Gebruikers handmatig migreren naar MI met behulp van DE SYNTAXIS VAN DE GEBRUIKER WIJZIGEN
> - Verificatie testen met de nieuwe toegewezen gebruikers

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, zijn de volgende vereisten van toepassing:

- Het Windows-domein is gefedereerd met Azure Active Directory (Azure AD).
- Toegang tot Active Directory om gebruikers/groepen te maken.
- Een bestaande SQL Server in uw on-premises omgeving.
- Een bestaand beheerde exemplaar. Zie [Snelstart: een azure SQL Database beheerde instantie maken.](sql-database-managed-instance-get-started.md)
  - A `sysadmin` in het beheerde exemplaar moet worden gebruikt om Azure AD-aanmeldingen te maken.
- [Maak een Azure AD-beheerder voor beheerde instantie](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- U verbinding maken met uw beheerde instantie binnen uw netwerk. Zie de volgende artikelen voor meer informatie: 
    - [Uw toepassing verbinden met het beheerde exemplaar van Azure SQL Database](sql-database-managed-instance-connect-app.md)
    - [Snelstart: een point-to-site-verbinding met een Azure SQL Database Managed Instance configureren vanuit on-premises](sql-database-managed-instance-configure-p2s.md)
    - [Openbaar eindpunt configureren in beheerd exemplaar van Azure SQL Database](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Syntaxis van T-SQL DDL

Hieronder vindt u de Syntaxis van T-SQL DDL die wordt gebruikt om on-premises Windows-gebruikers van SQL Server te ondersteunen en migratie naar beheerde instantie te beheren met Azure AD-verificatie.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumenten

_Domeinnaam_</br>
Hiermee geeft u de domeinnaam van de gebruiker op.

_Gebruikersnaam_</br>
Hiermee geeft u de naam op van de gebruiker die in de database is geïdentificeerd.

_= loginName\@domainName.com_</br>
Een gebruiker opnieuw in- en weergeven in de Azure AD-aanmelding

_Groupname_</br>
Hiermee geeft u de naam op van de groep die in de database is geïdentificeerd.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Deel 1: Aanmeldingen maken voor on-premises gebruikers en groepen van SQL Server

> [!IMPORTANT]
> Met de volgende syntaxis wordt een gebruiker en een groep aanmelding in uw SQL Server. U moet ervoor zorgen dat de gebruiker en groep in uw Active Directory (AD) voorkomen voordat u de onderstaande syntaxis uitvoert. </br> </br>
> Gebruikers: testUser1, testGroupUser </br>
> Groep: migratie - testGroepGebruiker moet deel uitmaken van de migratiegroep in AD

In het onderstaande voorbeeld wordt een login in SQL Server gemaakt voor een account met de naam _testUser1_ onder het domein _aadsqlmi._ 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

Maak een database voor deze test.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Deel 2: Windows-gebruikers en -groepen maken en vervolgens rollen en machtigingen toevoegen

Gebruik de volgende syntaxis om de testgebruiker te maken.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Controleer de gebruikersmachtigingen:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Maak een rol en wijs uw testgebruiker toe aan deze rol:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Gebruik de volgende query om gebruikersnamen weer te geven die aan een specifieke rol zijn toegewezen:

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

Gebruik de volgende syntaxis om een groep te maken. Voeg vervolgens de groep `db_owner`toe aan de rol .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

Maak een testtabel en voeg enkele gegevens toe met de volgende syntaxis:

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Deel 3: Back-upmaken en herstellen van de individuele gebruikersdatabase naar beheerde instantie

Maak een back-up van de migratiedatabase met het artikel [Databases kopiëren met back-up en herstel](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)of gebruik de volgende syntaxis:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Volg onze [Quickstart: Een database herstellen naar een beheerde instantie.](sql-database-managed-instance-get-started-restore.md)

## <a name="part-4-migrate-users-to-managed-instance"></a>Deel 4: Gebruikers migreren naar beheerde instantie

> [!NOTE]
> De Azure AD-beheerder voor beheerde instantiefunctionaliteit na het maken is gewijzigd. Zie [Nieuwe Azure AD-beheerfunctionaliteit voor MI voor](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)meer informatie.

Voer de opdracht GEBRUIKER WIJZIGEN uit om het migratieproces op beheerde instantie te voltooien.

1. Meld u aan bij uw beheerde instantie met het Azure AD-beheeraccount voor beheerde instantie. Maak vervolgens uw Azure AD-aanmelding in de beheerde instantie met de volgende syntaxis. Zie [Zelfstudie: Beheerde instantiebeveiliging in Azure SQL-database met Azure AD-serverprincipals (aanmeldingen)](sql-database-managed-instance-aad-security-tutorial.md)voor meer informatie.

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. Controleer uw migratie op de juiste database, tabel en principals.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Gebruik de syntaxis VAN GEBRUIKER WIJZIGEN om de on-premises gebruiker toe te weidsen aan de Azure AD-aanmelding.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. Gebruik de syntaxis VAN GEBRUIKER WIJZIGEN om de on-premises groep toe te zetten naar de Azure AD-aanmelding.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Deel 5: Azure AD-gebruikers- of groepsverificatie testen

Test het verifiëren naar beheerde instantie met behulp van de gebruiker die eerder is toegewezen aan de Azure AD-aanmelding met behulp van de syntaxis GEBRUIKER WIJZIGEN.
 
1. Meld u aan bij de federatieve VM met uw MI-abonnement als`aadsqlmi\testUser1`
1. Meld u met SQL Server Management Studio (SSMS) aan bij uw `migration`beheerde instantie met behulp van Active Directory **Integrated-verificatie** en maakt verbinding met de database.
    1. U zich ook testUser1@aadsqlmi.net aanmelden met de referenties met de SSMS-optie **Active Directory – Universal met MFA-ondersteuning.** In dit geval u het mechanisme voor één aanmelding echter niet gebruiken en moet u een wachtwoord typen. U hoeft geen federatieve VM te gebruiken om u aan te melden bij uw beheerde instantie.
1. Als onderdeel van het rollid **SELECT** `test` kunt u kiezen uit de tabel

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Test het verifiëren naar een beheerde instantie `migration`met behulp van een lid van een Windows-groep . De `aadsqlmi\testGroupUser` gebruiker had vóór de migratie aan de groep `migration` moeten zijn toegevoegd.

1. Meld u aan bij de federatieve VM met uw MI-abonnement als`aadsqlmi\testGroupUser` 
1. Met SSMS met **Active Directory Integrated-verificatie** verbinding maken met de MI-server en de database`migration`
    1. U zich ook testGroupUser@aadsqlmi.net aanmelden met de referenties met de SSMS-optie **Active Directory – Universal met MFA-ondersteuning.** In dit geval u het mechanisme voor één aanmelding echter niet gebruiken en moet u een wachtwoord typen. U hoeft geen federatieve VM te gebruiken om u aan te melden bij uw beheerde instantie. 
1. Als onderdeel `db_owner` van de rol u een nieuwe tabel maken.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Als gevolg van een bekend ontwerpprobleem voor Azure SQL DB, mislukt een tabelinstructie die is uitgevoerd als lid van een groep met de volgende fout: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> De huidige tijdelijke oplossing is het maken van een tabel met een bestaand schema in het geval hierboven <dbo.new>

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: SQL Server migreren naar een Azure SQL Database beheerd exemplaar offline met DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
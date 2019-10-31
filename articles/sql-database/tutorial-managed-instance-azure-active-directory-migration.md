---
title: SQL Server on-premises Windows-gebruikers en-groepen migreren naar Azure SQL Database beheerde instantie met de syntaxis van T-SQL DDL | Microsoft Docs
description: Meer informatie over het migreren van SQL Server on-premises Windows-gebruikers en-groepen naar een beheerd exemplaar
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: b27c9991fd86334c87806772cbd641dd72aad1f6
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163966"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Zelf studie: SQL Server on-premises Windows-gebruikers en-groepen migreren naar Azure SQL Database beheerd exemplaar met behulp van de syntaxis van T-SQL DDL

> [!NOTE]
> De syntaxis die wordt gebruikt voor het migreren van gebruikers en groepen naar een beheerd exemplaar in dit artikel, is in **open bare preview**.

In dit artikel vindt u meer over het migratie proces van uw on-premises Windows-gebruikers en-groepen in uw SQL Server naar een bestaand Azure SQL Database beheerd exemplaar met de T-SQL-syntaxis.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Aanmeldingen voor SQL Server maken
> - Een test database maken voor migratie
> - Aanmeldingen, gebruikers en rollen maken
> - Back-up maken en herstellen van de Data Base naar een beheerd exemplaar (MI)
> - Gebruikers hand matig migreren naar MI met ALTER USER syntax
> - Verificatie testen met de nieuwe toegewezen gebruikers

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie gelden de volgende vereisten:

- Het Windows-domein is federatief met Azure Active Directory (Azure AD).
- Toegang tot Active Directory om gebruikers/groepen te maken.
- Een bestaande SQL Server in uw on-premises omgeving.
- Een bestaand beheerd exemplaar. Zie [Quick Start: een door Azure SQL database beheerd exemplaar maken](sql-database-managed-instance-get-started.md).
  - Een `sysadmin` in het beheerde exemplaar moet worden gebruikt voor het maken van Azure AD-aanmeldingen.
- [Maak een Azure AD-beheerder voor het beheerde exemplaar](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- U kunt verbinding maken met uw beheerde instantie binnen uw netwerk. Raadpleeg de volgende artikelen voor aanvullende informatie: 
    - [Uw toepassing verbinden met Azure SQL Database beheerde instantie](sql-database-managed-instance-connect-app.md)
    - [Snelstartgids: een punt-naar-site-verbinding naar een door Azure SQL Database beheerd exemplaar van on-premises configureren](sql-database-managed-instance-configure-p2s.md)
    - [Openbaar eindpunt configureren in beheerd exemplaar van Azure SQL Database](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Syntaxis van T-SQL DDL

Hieronder vindt u de syntaxis voor T-SQL DDL die wordt gebruikt ter ondersteuning van SQL Server on-premises Windows-gebruikers en-groepen migratie naar een beheerd exemplaar met Azure AD-verificatie.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumenten

_Naam_</br>
Hiermee geeft u de domein naam van de gebruiker.

_Gebruikers_</br>
Hiermee geeft u de naam op van de gebruiker die in de data base is geïdentificeerd.

_= aanmeldings naam\@domainName.com_</br>
Hiermee wordt een gebruiker opnieuw toegewezen aan de Azure AD-aanmelding

_groupName_</br>
Hiermee geeft u de naam op van de groep die in de data base is geïdentificeerd.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Deel 1: aanmeldingen maken voor SQL Server on-premises gebruikers en groepen

> [!IMPORTANT]
> Met de volgende syntaxis maakt u een gebruiker en een groeps aanmelding in uw SQL Server. U moet ervoor zorgen dat de gebruiker en groep bestaan in uw Active Directory (AD) voordat u de onderstaande syntaxis uitvoert. </br> </br>
> Gebruikers: testUser1, testGroupUser </br>
> Groep: migratie-testGroupUser moeten deel uitmaken van de migratie groep in AD

In het volgende voor beeld wordt een aanmelding gemaakt in SQL Server voor een account met de naam _testUser1_ onder het domein _aadsqlmi_. 

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

Maak een Data Base voor deze test.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Deel 2: Windows-gebruikers en-groepen maken en vervolgens rollen en machtigingen toevoegen

Gebruik de volgende syntaxis om de test gebruiker te maken.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Controleer de gebruikers machtigingen:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Maak een rol en wijs uw test gebruiker toe aan deze rol:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Gebruik de volgende query om gebruikers namen weer te geven die zijn toegewezen aan een specifieke rol:

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

Gebruik de volgende syntaxis om een groep te maken. Voeg vervolgens de groep toe aan de rol `db_owner`.

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

Maak een test tabel en voeg enkele gegevens toe met behulp van de volgende syntaxis:

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

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Deel 3: een back-up maken van de afzonderlijke gebruikers database en deze herstellen naar een beheerd exemplaar

Maak een back-up van de migratie database met behulp van het artikel [Copy data bases with Backup and Restore](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), of gebruik de volgende syntaxis:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Volg onze [Snelstartgids: een Data Base herstellen naar een beheerd exemplaar](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Deel 4: gebruikers migreren naar een beheerd exemplaar

Voer de opdracht ALTER USER uit om het migratie proces voor een beheerd exemplaar te volt ooien.

1. Meld u aan bij uw beheerde exemplaar met behulp van het SQL-beheerders account voor het beheerde exemplaar. Maak vervolgens uw Azure AD-aanmelding in het beheerde exemplaar met de volgende syntaxis. Zie voor meer informatie [zelf studie: beheerde exemplaar beveiliging in Azure SQL database met behulp van Azure ad server-principals (aanmeldingen)](sql-database-managed-instance-aad-security-tutorial.md).

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

1. Controleer de migratie voor de juiste data base, tabel en principals.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Gebruik de syntaxis ALTER USER om de on-premises gebruiker toe te wijzen aan de Azure AD-aanmelding.

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

1. Gebruik de syntaxis ALTER USER om de on-premises groep toe te wijzen aan de Azure AD-aanmelding.

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

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Deel 5: de verificatie van Azure AD-gebruikers of-groepen testen

Test de verificatie voor het beheerde exemplaar met behulp van de gebruiker die eerder is toegewezen aan de Azure AD-aanmelding met de syntaxis ALTER USER.
 
1. Meld u aan bij de federatieve VM met uw MI-abonnement als `aadsqlmi\testUser1`
1. Meld u met behulp van SQL Server Management Studio (SSMS) aan bij uw beheerde exemplaar met behulp van **Active Directory geïntegreerde** verificatie, waarmee verbinding wordt gemaakt met de data base `migration`.
    1. U kunt zich ook aanmelden met de testUser1@aadsqlmi.net referenties met de optie SSMS **Active Directory – Universal met MFA-ondersteuning**. In dit geval kunt u echter het mechanisme voor eenmalige aanmelding niet gebruiken en moet u een wacht woord invoeren. U hoeft geen federatieve virtuele machine te gebruiken om u aan te melden bij uw beheerde exemplaar.
1. Als onderdeel van de functie leden **selecteren**, kunt u uit de tabel `test` selecteren

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Test de verificatie bij een beheerd exemplaar met behulp van een lid van een Windows-groep `migration`. De gebruiker `aadsqlmi\testGroupUser` moet zijn toegevoegd aan de groep `migration` vóór de migratie.

1. Meld u aan bij de federatieve VM met uw MI-abonnement als `aadsqlmi\testGroupUser` 
1. Gebruik SSMS met **Active Directory geïntegreerde** verificatie om verbinding te maken met de mi-server en de-data base `migration`
    1. U kunt zich ook aanmelden met de testGroupUser@aadsqlmi.net referenties met de optie SSMS **Active Directory – Universal met MFA-ondersteuning**. In dit geval kunt u echter het mechanisme voor eenmalige aanmelding niet gebruiken en moet u een wacht woord invoeren. U hoeft geen federatieve virtuele machine te gebruiken om u aan te melden bij uw beheerde exemplaar. 
1. Als onderdeel van de `db_owner` rol kunt u een nieuwe tabel maken.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Als gevolg van een bekend ontwerp probleem voor Azure SQL DB, mislukt het maken van een tabel instructie die wordt uitgevoerd als lid van een groep, met de volgende fout: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> De huidige tijdelijke oplossing is het maken van een tabel met een bestaand schema in bovenstaand geval < dbo. New >

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: SQL Server naar een beheerde instantie van Azure SQL Database migreren met behulp van DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
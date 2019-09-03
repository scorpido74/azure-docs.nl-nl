---
title: Beveiliging van Azure SQL Database Managed Instance met behulp van Azure AD-server-principals (aanmeldingen) | Microsoft Docs
description: Informatie over technieken en functies voor het beveiligen van een beheerd exemplaar in Azure SQL Database en over aanmeldingen bij Azure AD-server-principals (aanmeldingen)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/20/2019
ms.openlocfilehash: 37098411f465c611dc9d2e2443f369e01d6e338c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70230998"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Zelfstudie: Beveiliging van een beheerd exemplaar in Azure SQL Database met behulp van Azure AD-server-principals (aanmeldingen)

Managed Instance biedt bijna alle beveiligingsfuncties die de meest recente SQL Server on-premises (Enterprise Edition) Database Engine ook heeft:

- Beperken van toegang in een geïsoleerde omgeving
- Verificatiemechanismen gebruiken die identiteit vereisen (Azure AD, SQL-verificatie)
- Autorisatie via op rollen gebaseerde lidmaatschappen en machtigingen
- Beveiligingsfuncties inschakelen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een AD-aanmelding (Azure Active Directory) voor een beheerd exemplaar maken
> - Machtigingen verlenen aan Azure AD-server-principals (aanmeldingen) in een beheerd exemplaar
> - Azure AD-gebruikers maken op basis van Azure AD-server-principals (aanmeldingen)
> - Machtigingen aan Azure AD-gebruikers toewijzen en databasebeveiliging beheren
> - Imitatie gebruiken voor Azure AD-gebruikers
> - Databaseoverschrijdende query’s gebruiken voor Azure AD-gebruikers
> - Leren over beveiligingsfuncties, zoals beveiliging tegen bedreigingen, controle, gegevensmaskering en versleuteling

> [!NOTE]
> De functie Azure AD-server-principals (aanmeldingen) voor beheerde exemplaren bevindt zich nog in een **openbare preview-versie**.

Zie de artikelen [Overzicht van Azure SQL Database Managed Instance](sql-database-managed-instance-index.yml) en [-mogelijkheden](sql-database-managed-instance.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Een beheerd exemplaar van Azure SQL Database
  - Volg dit artikel: [Snelstart: Een beheerd exemplaar van Azure SQL Database maken](sql-database-managed-instance-get-started.md)
- Toegang krijgen tot uw beheerde exemplaar en [het inrichten van een Azure AD-beheerder voor het beheerde exemplaar](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Voor meer informatie zie:
    - [Uw toepassing verbinding laten maken met een beheerd exemplaar](sql-database-managed-instance-connect-app.md) 
    - [Connectiviteitsarchitectuur van beheerde exemplaren](sql-database-managed-instance-connectivity-architecture.md)
    - [Verificatie van Azure Active Directory configureren en beheren met SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>De toegang tot uw beheerde exemplaar beperken

Beheerde exemplaren kunnen worden geopend via een privé-IP-adres. Net als bij een geïsoleerd SQL Server on-premises omgeving, moeten toepassingen of gebruikers toegang hebben tot het beheerde exemplaar van het netwerk (VNet) voordat een verbinding tot stand kan worden gebracht. Zie het volgende artikel [Uw toepassing verbinden met een beheerd exemplaar](sql-database-managed-instance-connect-app.md) voor meer informatie.

Het is ook mogelijk om een service-eind punt op het beheerde exemplaar te configureren, waardoor open bare verbindingen op dezelfde manier als Azure SQL Database worden toegestaan. Voor meer informatie raadpleegt u het volgende artikel, het [configureren van het open bare eind punt in Azure SQL database Managed instance](sql-database-managed-instance-public-endpoint-configure.md).

> [!NOTE] 
> Zelfs als service-eind punten zijn ingeschakeld [SQL database firewall regels](sql-database-firewall-configure.md) niet van toepassing zijn. Het beheerde exemplaar heeft een eigen [ingebouwde firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) voor het beheren van de connectiviteit.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Een AD-server-principal (aanmelding) voor een beheerd exemplaar maken met SSMS

De eerste Azure AD-server-principal (aanmelding) moet worden gemaakt met het standaard SQL Server-account (niet-Azure AD) dat een `sysadmin` is. Zie de volgende artikelen voor voorbeelden van verbinding maken met uw beheerd exemplaar:

- [Snelstart: Azure VM configureren om verbinding te maken met een beheerd exemplaar](sql-database-managed-instance-configure-vm.md)
- [Snelstart: Vanuit on-premises een punt-naar-site-verbinding configureren naar een beheerd exemplaar](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> De Azure AD-beheerder die werd gebruikt voor het instellen van het beheerde exemplaar kan niet worden gebruikt om een Azure AD-server-principal (aanmelding) binnen het beheerde exemplaar te maken. U moet de eerste Azure AD-server-principal (aanmelding) maken met een SQL Server-account dat een `sysadmin` is. Dit is een tijdelijke beperking die wordt verwijderd zodra Azure AD-server-principals (aanmeldingen) algemeen beschikbaar zijn. U krijgt de volgende fout te zien als u probeert een Azure AD-beheerdersaccount te gebruiken om de aanmelding te maken: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Meld u aan bij uw beheerde exemplaar met behulp van een standaard SQL Server-account (niet-azure AD) dat een `sysadmin` is, met behulp van [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

3. Gebruik in het queryvenster de volgende syntaxis om een aanmelding te maken voor een lokaal Azure AD-account:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    In dit voorbeeld maakt u een aanmelding voor het account nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Selecteer **Uitvoeren** op de werkbalk om de aanmelding te maken.

5. Controleer de zojuist toegevoegde aanmelding door de volgende T-SQL-opdracht uit te voeren:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Zie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) voor meer informatie.

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Machtigingen verlenen om aanmeldingen bij een beheerd exemplaar toe te staan

Voor het maken van andere Azure AD-server-principals (aanmeldingen), moeten SQL Server-rollen of -machtigingen aan de principal (SQL of Azure AD) worden verleend.

### <a name="sql-authentication"></a>SQL-verificatie

- Als de aanmelding een SQL-principal is, kunnen alleen aanmeldingen die deel uitmaken van de `sysadmin`-rol van de opdracht create gebruiken om aanmeldingen te maken voor een Azure AD-account.

### <a name="azure-ad-authentication"></a>Azure Active Directory-verificatie

- Om ervoor te zorgen dat de zojuist gemaakte Azure AD-server-principal (aanmelding) andere aanmeldingen voor andere Azure AD-gebruikers, -groepen of -toepassingen kan maken, verleent u de aanmelding de `sysadmin`- of `securityadmin`-serverrol. 
- Minimaal moet de machtiging **ALTER ANY LOGIN** worden toegewezen aan de Azure AD-server-principal (aanmelding) om andere Azure AD-server-principals (aanmeldingen) te kunnen maken. 
- De machtiging die aan de zojuist gemaakte Azure AD-server-principals (aanmeldingen) in de master wordt verleend, is standaard: **CONNECT SQL** en **VIEW ANY DATABASE**.
- De `sysadmin`-serverrol kan worden verleend aan veel Azure AD-server-principals (aanmeldingen) in een beheerd exemplaar.

Ga als volgt te werk om de aanmelding toe te voegen aan de `sysadmin`-serverrol:

1. Meld u opnieuw aan bij het beheerde exemplaar of gebruik de bestaande verbinding met de SQL-principal die een `sysadmin` is.

1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

1. Verleen de Azure AD-server-principal (aanmelding) de `sysadmin`-serverrol met behulp van de volgende T-SQL-syntaxis:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    In het volgende voorbeeld wordt de `sysadmin`-serverrol verleend aan de aanmelding nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Extra Azure AD-server-principals (aanmeldingen) maken met behulp van SSMS

Nadat de Azure AD-server-principal (aanmelding) is gemaakt en is voorzien van `sysadmin`-bevoegdheden, kan die aanmelding extra aanmeldingen maken met behulp van de component **FROM EXTERNAL PROVIDER** met **CREATE LOGIN**.

1. Maak verbinding met het beheerde exemplaar met de Azure AD-server-principal (aanmelding), met behulp van SQL Server Management Studio. Voer de hostnaam van uw beheerd exemplaar in. Voor verificatie in SSMS zijn er drie opties waaruit u kunt kiezen wanneer u zich aanmeldt bij een Azure AD-account:

   - Active Directory - Universal met ondersteuning voor MFA
   - Active Directory - wachtwoord
   - Active Directory - geïntegreerd </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Zie het volgende artikel voor meer informatie: [Universele verificatie met SQL Database en SQL Data Warehouse (SSMS-ondersteuning voor MFA)](sql-database-ssms-mfa-authentication.md)

1. Selecteer **Active Directory - Universal met ondersteuning voor MFA**. Hiermee wordt een venster voor Multi-Factor Authentication (MFA) geopend. Meld u aan met uw Azure AD-wachtwoord.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. Klik in SSMS **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
1. Gebruik in het queryvenster de volgende syntaxis om een aanmelding te maken voor een ander Azure AD-account:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    In dit voorbeeld maakt u een aanmelding voor de Azure AD-gebruiker bob@aadsqlmi.net, wiens domein aadsqlmi.net is gefedereerd met de Azure AD aadsqlmi.onmicrosoft.com.

    Voer de volgende T-SQL-opdracht uit. Federatieve Azure AD-accounts zijn de beheerde instantie vervangingen voor on-premises Windows-aanmeldingen en-gebruikers.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Maak een database in het beheerd exemplaar met behulp van de [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)-syntaxis. Deze database wordt gebruikt voor het testen van aanmeldingen in de volgende sectie.
    1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
    1. Gebruik in het queryvenster de volgende syntaxis om een database te maken met de naam **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Maak een aanmelding voor een beheerd exemplaar voor een groep in Azure AD. De groep moet aanwezig zijn in Azure AD voordat u de aanmelding naar een beheerd exemplaar kunt toevoegen. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Maak een groep met de naam _mygroup_ en voeg leden toe aan deze groep.

1. Open een nieuw queryvenster in SQL Server Management Studio.

    In dit voorbeeld wordt ervan uitgegaan dat er een groep bestaat met de naam _mygroup_ in de Azure AD. Voer de volgende opdracht uit:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Als test meldt u zich aan bij het beheerde exemplaar met de zojuist gemaakte aanmelding of groep. Open een nieuwe verbinding naar het beheerde exemplaar en gebruik de nieuwe aanmelding voor verificatie.
1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query** als nieuwe verbinding.
1. Controleer servermachtigingen voor de zojuist gemaakte Azure AD-server-principal (aanmelding) door de volgende opdracht uit te voeren:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Ook gastgebruikers van Azure AD worden ondersteund voor aanmeldingen bij een beheerd exemplaar, maar alleen wanneer ze zijn toegevoegd als onderdeel van een Azure AD-groep. Een Azure AD-gastgebruiker is een account dat vanuit een andere Azure AD wordt uitgenodigd voor de Azure AD waarvan het beheerde exemplaar deel uitmaakt. joe@contoso.com (Azure AD-Account) of steve@outlook.com (MSA-Account) kunnen bijvoorbeeld worden toegevoegd aan een groep in de Azure AD aadsqlmi. Zodra de gebruikers aan een groep zijn toegevoegd, kan er een aanmelding worden gemaakt in de **hoofd**database van het beheerde exemplaar voor de groep met de syntaxis **CREATE LOGIN**. Gastgebruikers die lid zijn van deze groep, kunnen verbinding maken met het beheerde exemplaar met behulp van hun huidige aanmeldingsgegevens (bijvoorbeeld joe@contoso.com of steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Een Azure AD-gebruiker maken op basis van de Azure AD-server-principal (aanmelding) en die gebruiker machtigingen verlenen

Autorisatie voor afzonderlijke databases werkt in Managed Instance nagenoeg hetzelfde als bij SQL Server on-premise. Er kan een gebruiker worden gemaakt op basis van een bestaande aanmelding in een database en worden voorzien van machtigingen voor die database, of worden toegevoegd aan een databaserol.

Nu we een database met de naam **MyMITestDB** hebben gemaakt plus een aanmelding met alleen standaardmachtigingen, bestaat de volgende stap uit het maken van een gebruiker op basis van deze aanmelding. Op dit moment kan de aanmelding verbinding maken met het beheerde exemplaar en alle databases zien, maar er kan niet met de databases worden gecommuniceerd. Als u zich aanmeldt met het Azure AD-account waarvoor de standaardmachtigingen gelden en u de zojuist gemaakte database probeert uit te vouwen, krijgt u de volgende fout te zien:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Zie [Aan de slag met machtigingen voor database-engines](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions) voor meer informatie over het verlenen van databasemachtigingen.

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Een Azure AD-gebruiker en een voorbeeldtabel maken

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.
1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
1. Gebruik in het queryvenster de volgende syntaxis om een Azure AD-gebruiker te maken op basis van een Azure AD-server-principal (aanmelding):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    In het volgende voorbeeld wordt een gebruiker bob@aadsqlmi.net gemaakt op basis van de aanmelding bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Er kan ook een Azure AD-gebruiker worden gemaakt op basis van een Azure AD-server-principal (aanmelding) voor een groep.

    In het volgende voorbeeld maakt u een aanmelding voor de Azure AD-groep _mygroup_ die in uw Azure AD bestaat.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Alle gebruikers die deel uitmaken van **mygroup**, hebben toegang tot de **MyMITestDB**-database.

    > [!IMPORTANT]
    > Bij het maken van een **USER** op basis van een Azure AD-server-principal (aanmelding) is de user_name hetzelfde als de login_name van **LOGIN**.

    Zie [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) voor meer informatie.

1. Maak in een nieuw queryvenster een testtabel met de volgende T-SQL-opdracht:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Maak een verbinding in SSMS met de gebruiker die is gemaakt. U merkt dat de tabel **TestTable** die eerder door de `sysadmin` werd gemaakt, niet is te zien. De gebruiker heeft machtigingen nodig voor het lezen van gegevens uit de database.

1. U kunt nagaan wat de huidige machtigingen van de gebruiker zijn door de volgende opdracht uit te voeren:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Gebruikers toevoegen aan rollen op databaseniveau

Om ervoor te zorgen dat de gebruiker de gegevens in de database kan zien, geven we [rollen op databaseniveau](/sql/relational-databases/security/authentication-access/database-level-roles) aan de gebruiker.

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.

1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

1. Geef de Azure AD-gebruiker de databaserol `db_datareader` met behulp van de volgende T-SQL-syntaxis:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    In het volgende voorbeeld krijgen de gebruiker bob@aadsqlmi.net en de groep _mygroup_ `db_datareader` machtigingen voor de **MyMITestDB**-database:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Controleer de Azure AD-gebruiker die in de database werd gemaakt door de volgende opdracht uit te voeren:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Maak een nieuwe verbinding naar het beheerd exemplaar met de gebruiker die is toegevoegd aan de `db_datareader`-rol.
1. Vouw de database in **Objectverkenner** uit om de tabel weer te geven.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Open een nieuw queryvenster en voer de volgende SELECT-instructie uit:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Kunt u gegevens uit de tabel zien? De kolommen zouden moeten worden geretourneerd.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Principals op Azure AD-serverniveau (aanmeldingen) imiteren

Managed Instance biedt ondersteuning voor het imiteren van principals op Azure AD-serverniveau (aanmeldingen).

### <a name="test-impersonation"></a>Imitatie testen

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.

1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

1. Voer in het queryvenster de volgende opdracht uit om een opgeslagen procedure te maken:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Gebruik de volgende opdracht om te zien dat de gebruiker die u imiteert bij het uitvoeren van de opgeslagen **procedure\@Bob aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Test de imitatie met behulp van de instructie EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Alleen de principals op SQL-serverniveau (aanmeldingen) die deel uitmaken van de `sysadmin`-rol kunnen de volgende bewerkingen voor Azure AD-principals uitvoeren: 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Gebruik van query's tussen meerdere databases in beheerde exemplaren

Query's tussen meerdere databases worden ondersteund voor Azure AD-accounts met Azure AD-server-principals (aanmeldingen). Als u een query tussen meerdere databases met een Azure AD-groep wilt testen, moeten we een andere database en tabel maken. U kunt het maken van een andere database en tabel overslaan als die al bestaan.

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.
1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
1. Gebruik in het queryvenster de volgende opdracht om een database te maken met de naam **MyMITestDB2** en een tabel met de naam **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Voer in een nieuw queryvenster de volgende opdracht uit om de gebruiker _mygroup_ te maken in de nieuwe database **MyMITestDB2** en SELECT-machtigingen voor die database te verlenen aan _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Meld u aan bij het beheerde exemplaar via SQL Server Management Studio als een lid van de Azure AD-groep _mygroup_. Open een nieuw queryvenster en voer de volgende SELECT-instructie tussen meerdere databases uit:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    U krijgt dan de tabelresultaten uit **TestTable2** te zien.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Aanvullende scenario's ondersteund voor Azure AD-server-principals (aanmeldingen) (openbare preview) 

- Beheer van SQL Agent en taakuitvoeringen worden ondersteund voor Azure AD-server-principals (aanmeldingen).
- Database-back-up en herstelbewerkingen kunnen worden uitgevoerd door Azure AD-server-principals (aanmeldingen).
- [Controle](sql-database-managed-instance-auditing.md) van alle instructies met betrekking tot Azure AD-server-principal (aanmelding) en verificatiegebeurtenissen.
- Exclusieve beheerdersverbinding voor Azure AD-server-principals (aanmeldingen) die lid zijn van de `sysadmin`-serverrol.
- Azure AD-server-principals (aanmeldingen) worden ondersteund bij gebruik van het [sqlcmd-hulpprogramma](/sql/tools/sqlcmd-utility) en [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Aanmeldingstriggers worden ondersteund voor aanmeldingsgebeurtenissen die afkomstig zijn van Azure AD-server-principal s(aanmeldingen).
- Service Broker en DB-mail kunnen worden ingesteld voor gebruik van Azure AD-server-principals (aanmeldingen).


## <a name="next-steps"></a>Volgende stappen

### <a name="enable-security-features"></a>Beveiligingsfuncties inschakelen

Zie het artikel [Beveiligingsfuncties voor mogelijkheden van beheerde exemplaren](sql-database-managed-instance.md#azure-sql-database-security-features) voor een uitgebreide lijst met manieren om uw database te beveiligen. De volgende beveiligingsfuncties worden besproken:

- [Controle van beheerde exemplaren](sql-database-managed-instance-auditing.md) 
- [Altijd versleuteld](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detectie van bedreigingen](sql-database-managed-instance-threat-detection.md) 
- [Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking)
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) 
- [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Mogelijkheden van beheerde exemplaren

Een volledig overzicht van de mogelijkheden van een beheerd exemplaar is hier te vinden:

> [!div class="nextstepaction"]
> [Mogelijkheden van beheerde exemplaren](sql-database-managed-instance.md)

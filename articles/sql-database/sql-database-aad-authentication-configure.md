---
title: Azure Active Directory-verificatie configureren
description: Informatie over het maken van verbinding met SQL Database, een beheerd exemplaar en SQL Data Warehouse met behulp van Azure Active Directory-verificatie-na het configureren van Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: dc2661bbc443201d6a2da4b5efb7ecdc2caad444
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732566"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Azure Active Directory verificatie met SQL configureren en beheren

In dit artikel wordt beschreven hoe u Azure AD maakt en vult en vervolgens Azure AD gebruikt met Azure [SQL database](sql-database-technical-overview.md), een [beheerd exemplaar](sql-database-managed-instance.md)en [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Zie [Azure Active Directory-verificatie](sql-database-aad-authentication.md)voor een overzicht.

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL Server en op zowel SQL Database-als SQL Data Warehouse-data bases die zijn gemaakt op de Azure SQL-Server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]  
> Het is niet mogelijk om verbinding te maken met SQL Server die worden uitgevoerd op een virtuele Azure-machine met een Azure Active Directory-account. Gebruik in plaats daarvan een domein Active Directory account.

## <a name="create-and-populate-an-azure-ad"></a>Een Azure AD maken en vullen

Een Azure AD maken en deze vullen met gebruikers en groepen. Azure AD kan het eerste door Azure AD beheerde domein zijn. Azure AD kan ook een on-premises Active Directory Domain Services zijn die federatief is voor Azure AD.

Zie [Uw on-premises identiteiten integreren met Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Uw domeinnaam toevoegen in Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Uw Azure AD-adreslijst beheren](../active-directory/fundamentals/active-directory-administer.md), [Azure AD beheren met Windows PowerShell](/powershell/azure/overview) en [Poorten en protocollen waarvoor hybride identiteit is vereist](../active-directory/hybrid/reference-connect-ports.md) voor meer informatie.

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen

1. Koppel uw Azure-abonnement aan Azure Active Directory door de Directory een vertrouwde Directory te maken voor het Azure-abonnement dat als host fungeert voor de data base. Zie [hoe Azure-abonnementen worden gekoppeld aan Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie.

2. Gebruik de Directory wisselaar in de Azure Portal om over te scha kelen naar het abonnement dat is gekoppeld aan het domein.

   > [!IMPORTANT]
   > Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. De vertrouwensrelatie die een abonnement heeft met een directory is anders dan de relatie die een abonnement heeft met andere resources in Azure (websites, databases, enzovoort); deze resources lijken meer op onderliggende resources van een abonnement. Als een abonnement is verlopen, wordt toegang tot de andere resources die zijn gekoppeld aan het abonnement ook geblokkeerd. De directory blijft echter wel aanwezig in Azure, en u kunt er een ander abonnement aan koppelen om de directorygebruikers te blijven beheren. Zie informatie over de [toegang tot resources in azure](../active-directory/active-directory-b2b-admin-add-users.md)voor meer informatie over resources. Zie [een Azure-abonnement koppelen aan of toevoegen aan Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie over deze vertrouwde relatie.

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Een Azure AD-beheerder maken voor Azure SQL Server

Elke Azure SQL-Server (die als host fungeert voor een SQL Database of SQL Data Warehouse) begint met één beheerders account van de server die de beheerder is van de volledige Azure SQL-Server. Er moet een tweede SQL Server-beheerder worden gemaakt die een Azure AD-account is. Deze principal is gemaakt als een Inge sloten database gebruiker in de hoofd database. Als Administrators zijn de accounts van de server beheerder lid van de **db_owner** rol in elke gebruikers database en voeren ze elke gebruikers database in als de **dbo** -gebruiker. Zie [data bases en aanmeldingen beheren in Azure SQL database](sql-database-manage-logins.md)voor meer informatie over de beheerders accounts van de server.

Bij het gebruik van Azure Active Directory met geo-replicatie moet de Azure Active Directory-beheerder worden geconfigureerd voor zowel de primaire als de secundaire server. Als een server geen Azure Active Directory-beheerder heeft, Azure Active Directory aanmeldingen en gebruikers een ' kan geen verbinding maken ' op de server fout.

> [!NOTE]
> Gebruikers die niet zijn gebaseerd op een Azure AD-account (inclusief het beheerders account van de Azure SQL-Server), kunnen geen gebruikers op basis van Azure AD maken, omdat ze geen toestemming hebben om voorgestelde database gebruikers te valideren met de Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Een Azure Active Directory beheerder inrichten voor uw beheerde exemplaar

> [!IMPORTANT]
> Volg deze stappen alleen als u een beheerd exemplaar inricht. Deze bewerking kan alleen worden uitgevoerd door de beheerder globaal/bedrijf of een bevoegde beheerdersrol in azure AD. In de volgende stappen wordt het proces voor het verlenen van machtigingen voor gebruikers met verschillende bevoegdheden in de directory beschreven.

> [!NOTE]
> Voor Azure AD-beheerders voor MI die vóór GA zijn gemaakt, maar door gaan met het gebruik van post GA, is er geen functionele wijziging in het bestaande gedrag. Zie voor meer informatie de [nieuwe Azure AD-beheer functionaliteit voor mi](#new-azure-ad-admin-functionality-for-mi) sectie voor meer informatie.

Uw beheerde exemplaar heeft machtigingen nodig voor het lezen van Azure AD om taken zoals de verificatie van gebruikers via lidmaatschap van een beveiligings groep of het maken van nieuwe gebruikers te kunnen uitvoeren. Om dit te laten werken, moet u machtigingen verlenen aan het beheerde exemplaar voor het lezen van Azure AD. Er zijn twee manieren om dit te doen: vanuit de portal en Power shell. In de volgende stappen worden beide methoden beschreven.

1. Selecteer in het Azure Portal in de rechter bovenhoek uw verbinding om een lijst met mogelijke Active Directors weer te geven.

2. Kies de juiste Active Directory als de standaard-Azure AD.

   Met deze stap koppelt u het abonnement dat is gekoppeld aan Active Directory met een beheerd exemplaar, om ervoor te zorgen dat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als het beheerde exemplaar.

3. Ga naar Managed instance en selecteer een exemplaar dat u wilt gebruiken voor Azure AD-integratie.

   ![Aad](./media/sql-database-aad-authentication/aad.png)

4. Selecteer de banner boven op de pagina Active Directory beheer en Ken machtigingen toe aan de huidige gebruiker. Als u bent aangemeld als globaal/bedrijfs beheerder in azure AD, kunt u dit doen vanuit de Azure Portal of Power shell gebruiken met het onderstaande script.

    ![machtigingen verlenen-Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Nadat de bewerking is voltooid, wordt in de rechter bovenhoek de volgende melding weer gegeven:

    ![voltooid](./media/sql-database-aad-authentication/success.png)

6. U kunt nu uw Azure AD-beheerder kiezen voor uw beheerde exemplaar. Selecteer op de pagina Active Directory-beheer de optie admin-opdracht **instellen** .

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. Zoek op de pagina AAD-beheerder naar een gebruiker, selecteer de gebruiker of groep die u als beheerder wilt en selecteer **selecteren**.

   Op de pagina Active Directory beheer worden alle leden en groepen van uw Active Directory weer gegeven. Gebruikers of groepen die grijs worden weer gegeven, kunnen niet worden geselecteerd, omdat ze niet worden ondersteund als Azure AD-Administrators. Zie de lijst met ondersteunde beheerders in [Azure AD-functies en-beperkingen](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Op rollen gebaseerd toegangs beheer (RBAC) is alleen van toepassing op de Azure Portal en wordt niet door gegeven aan SQL Server.

    ![Azure Active Directory beheerder toevoegen](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Selecteer boven aan de pagina Active Directory-beheer de optie **Opslaan**.

    ![opslaan](./media/sql-database-aad-authentication/save.png)

    Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weer gegeven in het vak Active Directory-beheerder.

Nadat u een Azure AD-beheerder hebt ingericht voor uw beheerde exemplaar, kunt u beginnen met het maken van Azure AD-server principals (aanmeldingen) met de syntaxis voor het maken van de <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">aanmelding</a> . Zie [overzicht van beheerde exemplaren](sql-database-managed-instance.md#azure-active-directory-integration)voor meer informatie.

> [!TIP]
> Als u later een beheerder wilt verwijderen, selecteert u aan de bovenkant van de pagina Active Directory-beheer de optie **beheerder verwijderen**en selecteert u vervolgens **Opslaan**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nieuwe Azure AD-beheer functionaliteit voor MI

De volgende tabel bevat een overzicht van de functionaliteit voor de open bare preview Azure AD-aanmeldings beheerder voor MI, en een nieuwe functionaliteit die wordt geleverd met GA voor Azure AD-aanmeldingen.

| Azure AD-aanmeldings beheer voor MI tijdens de open bare preview | GA-functionaliteit voor Azure AD-beheerder voor MI |
| --- | ---|
| Gedraagt zich op een vergelijk bare manier als Azure AD-beheerder voor SQL Database, waarmee Azure AD-verificatie mogelijk is, maar de Azure AD-beheerder kan geen Azure AD-of SQL-aanmeldingen in de hoofd database voor MI maken. | De Azure AD-beheerder heeft sysadmin-machtigingen en kan AAD-en SQL-aanmeldingen maken in de hoofd database voor MI. |
| Is niet aanwezig in de sys. server_principals weer gave | Is aanwezig in de weer gave sys. server_principals |
| Hiermee kunnen individuele Azure AD-gast gebruikers worden ingesteld als Azure AD-beheerder voor MI. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/b2b/add-users-administrator.md)voor meer informatie. | U moet een Azure AD-groep maken met gast gebruikers als leden om deze groep in te stellen als een Azure AD-beheerder voor MI. Zie [Azure AD Business to Business Support](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)(Engelstalig) voor meer informatie. |

Als best practice voor bestaande Azure AD-beheerders voor MI gemaakt vóór GA en nog steeds met behulp van post GA, stel de Azure AD-beheerder opnieuw in met Azure Portal de optie beheerder verwijderen en beheerder instellen voor dezelfde Azure AD-gebruiker of-groep.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Bekende problemen met de Azure AD-aanmeldings GA voor MI

- Als er zich een Azure AD-aanmelding in de hoofd database voor MI bevindt, gemaakt met de T-SQL-opdracht `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`, kan deze niet worden ingesteld als een Azure AD-beheerder voor STORINGen. U krijgt een fout melding bij het instellen van de aanmelding als een Azure AD-beheerder met behulp van de Azure Portal, Power shell of de CLI-opdrachten om de Azure AD-aanmelding te maken.
  - De aanmelding moet worden verwijderd uit de hoofd database met behulp van de opdracht `DROP LOGIN [myaadaccount]`voordat het account kan worden gemaakt als een Azure AD-beheerder.
  - Het Azure AD-beheerders account instellen in de Azure Portal nadat de `DROP LOGIN` is geslaagd. 
  - Als u de Azure AD-beheerders account niet kunt instellen, controleert u de hoofd database van het beheerde exemplaar voor de aanmelding. Gebruik de volgende opdracht: `SELECT * FROM sys.server_principals`
  - Als u een Azure AD-beheerder voor MI instelt, wordt er automatisch een aanmelding in de hoofd database voor dit account gemaakt. Door de Azure AD-beheerder te verwijderen, wordt de aanmelding automatisch verwijderd uit de hoofd database.

- Individuele Azure AD-gast gebruikers worden niet ondersteund als Azure AD-Administrators voor MI. Gast gebruikers moeten deel uitmaken van een Azure AD-groep om te worden ingesteld als Azure AD-beheerder. Op dit moment worden gast gebruikers voor een ander Azure AD-account niet grijs weer gegeven op de Blade Azure Portal, zodat gebruikers de beheerders installatie kunnen voortzetten. Het opslaan van gast gebruikers als een Azure AD-beheerder leidt ertoe dat de installatie mislukt.
  - Als u een gast gebruiker een Azure AD-beheerder voor MI wilt maken, neemt u de gast gebruiker op in een Azure AD-groep en stelt u deze groep in als een Azure AD-beheerder.

### <a name="powershell-for-sql-managed-instance"></a>Power shell voor door SQL beheerd exemplaar

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Als u Power shell-cmdlets wilt uitvoeren, moet Azure PowerShell zijn geïnstalleerd en worden uitgevoerd. Zie voor gedetailleerde informatie [Installeren en configureren van Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

Voer de volgende Azure PowerShell opdrachten uit om een Azure AD-beheerder in te richten:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets die worden gebruikt voor het inrichten en beheren van Azure AD-beheerder voor SQL Managed instance:

| Naam van cmdlet | Beschrijving |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Voorziet in het huidige abonnement van een Azure AD-beheerder voor SQL Managed instance. (Moet afkomstig zijn van het huidige abonnement)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Hiermee verwijdert u een Azure AD-beheerder voor SQL Managed instance in het huidige abonnement. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retourneert informatie over een Azure AD-beheerder voor SQL Managed instance in het huidige abonnement.|

Met de volgende opdracht wordt informatie opgehaald over een Azure AD-beheerder voor een beheerd exemplaar met de naam ManagedInstance01 die is gekoppeld aan een resource groep met de naam ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Met de volgende opdracht wordt een Azure AD-Beheerders groep met de naam Dba's voor het beheerde exemplaar met de naam ManagedInstance01 ingericht. Deze server is gekoppeld aan de ResourceGroup01 van de resource groep.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Met de volgende opdracht wordt de Azure AD-beheerder verwijderd voor het beheerde exemplaar met de naam ManagedInstanceName01 dat is gekoppeld aan de resource groep ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt ook een Azure AD-beheerder voor SQL Managed instance inrichten door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[AZ SQL mi AD-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Richt een Azure Active Directory beheerder in voor het beheerde SQL-exemplaar. (Moet afkomstig zijn van het huidige abonnement) |
|[AZ SQL mi AD-admin Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory beheerder voor het door SQL beheerde exemplaar. |
|[AZ SQL mi AD-administrator list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor SQL Managed instance. |
|[AZ SQL mi AD-Admin Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Hiermee wordt de Active Directory beheerder voor een beheerd exemplaar van SQL bijgewerkt. |

Zie [AZ SQL mi](/cli/azure/sql/mi)voor meer informatie over cli-opdrachten.

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Een Azure Active Directory-beheerder voor uw Azure SQL Database-server inrichten

> [!IMPORTANT]
> Volg deze stappen alleen als u een Azure SQL Database Server of Data Warehouse inricht.

De volgende twee procedures laten zien hoe u een Azure Active Directory beheerder kunt inrichten voor uw Azure SQL-Server in de Azure Portal en met behulp van Power shell.

### <a name="azure-portal"></a>Azure Portal

1. Ga naar de [Azure-portal](https://portal.azure.com/) en selecteer in de rechterbovenhoek uw verbinding om een lijst met mogelijke Active Directories weer te geven. Kies de juiste Active Directory als de standaard-Azure AD. In deze stap wordt de aan het abonnement gekoppelde Active Directory gekoppeld aan de Azure SQL-server, zodat u zeker weet dat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als SQL Server. (De Azure SQL-server kan Azure SQL Database of Azure SQL Data Warehouse hosten.)

    ![ad-kiezen][8]

2. Zoek en selecteer **SQL Server**.

    ![SQL-servers zoeken en selecteren](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Op deze pagina kunt u, voordat u **SQL-servers**selecteert, de **ster** naast de naam selecteren om de categorie te *favoriet* en **SQL-servers** aan de linkernavigatiebalk toe te voegen.

3. Selecteer op **SQL Server** pagina **Active Directory beheerder**.

4. Selecteer op de pagina **Active Directory-beheerder** de optie **beheerder instellen**.

    ![SQL-servers Active Directory beheerder instellen](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Zoek op de pagina **Beheerder toevoegen** een gebruiker. Selecteer de gebruiker of groep die beheerder moet zijn en selecteer **Selecteren**. (Op de pagina Active Directory-beheerder ziet u alle leden en groepen van uw Active Directory.) Gebruikers of groepen die grijs zijn gekleurd, kunnen niet worden geselecteerd omdat ze niet worden ondersteund als beheerders voor Azure AD. (Zie de lijst met ondersteunde beheerders in de sectie **functies en beperkingen van Azure AD** van het [gebruik van Azure Active Directory verificatie voor verificatie met SQL database of SQL Data Warehouse](sql-database-aad-authentication.md).) Op rollen gebaseerd toegangs beheer (RBAC) is alleen van toepassing op de portal en wordt niet door gegeven aan SQL Server.

    ![Azure Active Directory beheerder selecteren](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Selecteer boven aan de pagina **Active Directory-beheerder** de optie **Opslaan**.

    ![beheerder opslaan](./media/sql-database-aad-authentication/save-admin.png)

Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak **Active Directory-beheerder**.

   > [!NOTE]
   > Als u de Azure AD-beheerder instelt, mag de naam van de beheerder (gebruiker of groep) niet al aanwezig zijn in de virtuele hoofddatabase als een gebruiker van SQL Server-verificatie. Indien wel aanwezig, mislukt het instellen van de Azure AD-beheerder, waarna het maken ervan wordt teruggedraaid en er wordt aangegeven dat deze beheerder (naam) al bestaat. Omdat een dergelijke gebruiker van SQL Server-verificatie geen deel uitmaakt van Azure AD, mislukt het verbinding maken met de server met Azure AD-verificatie.

Als u later een beheerder wilt verwijderen, selecteert u aan de bovenkant van de pagina **Active Directory-beheer** de optie **beheerder verwijderen**en selecteert u vervolgens **Opslaan**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Power shell voor Azure SQL Database en Azure SQL Data Warehouse

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Als u Power shell-cmdlets wilt uitvoeren, moet Azure PowerShell zijn geïnstalleerd en worden uitgevoerd. Zie voor gedetailleerde informatie [Installeren en configureren van Azure PowerShell](/powershell/azure/overview). Voer de volgende Azure PowerShell opdrachten uit om een Azure AD-beheerder in te richten:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets die worden gebruikt voor het inrichten en beheren van Azure AD-beheerder voor Azure SQL Database en Azure SQL Data Warehouse:

| Naam van cmdlet | Beschrijving |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Richt een Azure Active Directory beheerder in voor Azure SQL Server of Azure SQL Data Warehouse. (Moet afkomstig zijn van het huidige abonnement) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Hiermee verwijdert u een Azure Active Directory beheerder voor Azure SQL Server of Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor de Azure SQL-Server of Azure SQL Data Warehouse. |

Gebruik de Power shell-opdracht Get-Help voor meer informatie over elk van deze opdrachten. Bijvoorbeeld `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Met het volgende script wordt een Azure AD-Beheerders groep met de naam **DBA_Group** (object-id `40b79501-b343-44ed-9ce7-da4c8cc7353f`) ingericht voor de **demo_server** server in een resource groep met de naam **groep-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

De para meter **DisplayName** invoer accepteert de Azure AD-weergave naam of de principal-naam van de gebruiker. Bijvoorbeeld ``DisplayName="John Smith"`` en ``DisplayName="johns@contoso.com"``. Voor Azure AD-groepen wordt alleen de Azure AD-weergave naam ondersteund.

> [!NOTE]
> De Azure PowerShell opdracht ```Set-AzSqlServerActiveDirectoryAdministrator``` voor komt niet dat u Azure AD-beheerders kunt inrichten voor niet-ondersteunde gebruikers. Een niet-ondersteunde gebruiker kan worden ingericht, maar kan geen verbinding maken met een Data Base.

In het volgende voor beeld wordt de optionele **ObjectID**gebruikt:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> De **ObjectID** voor Azure AD is vereist wanneer de **DisplayName** niet uniek is. Als u de waarden voor **ObjectID** en **DisplayName** wilt ophalen, gebruikt u de sectie Active Directory van klassieke Azure-Portal en bekijkt u de eigenschappen van een gebruiker of groep.

In het volgende voor beeld wordt informatie gegeven over de huidige Azure AD-beheerder voor Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

In het volgende voor beeld wordt een Azure AD-beheerder verwijderd:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt een Azure AD-beheerder inrichten door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[AZ SQL Server AD-admin Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Richt een Azure Active Directory beheerder in voor Azure SQL Server of Azure SQL Data Warehouse. (Moet afkomstig zijn van het huidige abonnement) |
|[AZ SQL Server AD-admin Delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory beheerder voor Azure SQL Server of Azure SQL Data Warehouse. |
|[AZ SQL Server AD-administrator list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor de Azure SQL-Server of Azure SQL Data Warehouse. |
|[AZ SQL Server AD-Admin Update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Hiermee wordt de Active Directory-beheerder voor een Azure SQL-Server of Azure SQL Data Warehouse bijgewerkt. |

Zie [AZ SQL Server](/cli/azure/sql/server)(Engelstalig) voor meer informatie over cli-opdrachten.

* * *

> [!NOTE]
> U kunt ook een Azure Active Directory beheerder inrichten met behulp van de REST Api's. Zie voor meer informatie [Service Management rest API naslag informatie en bewerkingen voor Azure SQL database bewerkingen voor Azure SQL database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Uw client computers configureren

Op alle client computers, van waaruit uw toepassingen of gebruikers verbinding maken met Azure SQL Database of Azure SQL Data Warehouse met behulp van Azure AD-identiteiten, moet u de volgende software installeren:

- .NET Framework 4,6 of hoger van [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory-verificatie bibliotheek voor SQL Server (*ADAL. DLL*). Hieronder vindt u de Download koppelingen voor het installeren van de nieuwste SSMS-, ODBC-en OLE DB-Stuur Programma's die de *ADAL bevatten. DLL* -bibliotheek.
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC-stuur programma 17 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB stuur programma 18 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

U kunt aan de volgende vereisten voldoen:

- Het installeren van de nieuwste versie van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) of [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) voldoet aan de .NET Framework 4,6-vereiste.
    - SSMS installeert de x86-versie van *ADAL. DLL*.
    - SSDT installeert de amd64-versie van *ADAL. DLL*.
    - De nieuwste Visual Studio- [down loads van Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) voldoen aan de vereiste van .NET Framework 4,6, maar installeert de vereiste amd64-versie van *ADAL niet. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Inge sloten database gebruikers in uw data base maken die zijn toegewezen aan Azure AD-identiteiten

> [!IMPORTANT]
> Managed instance biedt nu ondersteuning voor Azure AD-server-principals (aanmeldingen), waarmee u aanmeldingen kunt maken van Azure AD-gebruikers,-groepen of-toepassingen. Met Azure AD-server-principals (aanmeldingen) kunt u zich bij uw beheerde exemplaar verifiëren zonder dat database gebruikers worden gemaakt als Inge sloten database gebruiker. Zie [overzicht van beheerde exemplaren](sql-database-managed-instance.md#azure-active-directory-integration)voor meer informatie. Zie <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">login maken</a>voor de syntaxis voor het maken van Azure ad-server principals (aanmeldingen).

Voor verificatie met Azure Active Directory is vereist dat databasegebruikers als gebruikers van ingesloten databases worden gemaakt. Een gebruiker van een ingesloten database op basis van een Azure AD-identiteit is een databasegebruiker die zich niet kan aanmelden bij de hoofddatabase en die wordt verwezen naar een identiteit in de Azure AD-directory die aan de database is gekoppeld. De Azure AD-identiteit kan een individueel gebruikersaccount of een groep zijn. Zie [Contained Database Users- Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Gebruikers van ingesloten databases: een draagbare database maken) voor meer informatie over gebruikers van ingesloten databases.

> [!NOTE]
> Databasegebruikers kunnen niet in de Azure-portal worden gemaakt (met uitzondering van beheerders). RBAC-rollen worden niet doorgegeven aan SQL Server, SQL Database of SQL Data Warehouse. Azure RBAC-rollen worden gebruikt voor het beheren van Azure-resources en zijn niet van toepassing op databasemachtigingen. Bijvoorbeeld: de rol **Inzender voor SQL Server** verleent geen toegang om verbinding te maken met SQL Database of SQL Data Warehouse. De toestemming tot het verlenen van toegang moet rechtstreeks plaatsvinden in de database met behulp van Transact-SQL-instructies.

> [!WARNING]
> Speciale tekens als de dubbele punt `:` of de ampersand `&` worden niet ondersteund indien deze worden opgenomen als gebruikersnamen in de instructies T-SQL CREATE LOGIN of CREATE USER.

Als u een Inge sloten database gebruiker op basis van Azure AD wilt maken (met uitzonde ring van de server beheerder die eigenaar is van de data base), maakt u verbinding met de data base met een Azure AD-identiteit, als een gebruiker met ten minste de machtiging **wijzigen van een gebruiker** . Gebruik vervolgens de volgende Transact-SQL-syntaxis:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan de User Principal name van een Azure AD-gebruiker of de weergave naam voor een Azure AD-groep zijn.

**Voor beelden:** Een Inge sloten database gebruiker maken die een Azure AD Federated-of beheerde domein gebruiker vertegenwoordigt:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Als u een Inge sloten database gebruiker wilt maken die een Azure AD-of federatieve domein groep vertegenwoordigt, geeft u de weergave naam van een beveiligings groep op:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Een Inge sloten database gebruiker maken die een toepassing vertegenwoordigt die verbinding maakt met behulp van een Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Voor deze opdracht is vereist dat SQL-toegang tot Azure AD (de externe provider) namens de aangemelde gebruiker is. Soms ontstaan er omstandigheden die ertoe leiden dat Azure AD een uitzonde ring terugstuurt naar SQL. In deze gevallen ziet de gebruiker SQL-fout 33134, die het AAD-specifieke fout bericht moet bevatten. In de meeste gevallen geeft de fout aan dat de toegang wordt geweigerd of dat de gebruiker zich moet inschrijven in MFA om toegang te krijgen tot de resource, of dat toegang tussen toepassingen van de eerste partij moet worden afgehandeld via pre-autorisatie. In de eerste twee gevallen wordt het probleem meestal veroorzaakt door het beleid voor voorwaardelijke toegang dat is ingesteld in de AAD-Tenant van de gebruiker: Hiermee wordt voor komen dat de gebruiker toegang heeft tot de externe provider. Het bijwerken van het CA-beleid om toegang tot de toepassing ' 00000002-0000-0000-C000-000000000000 te gebruiken ' toe te staan (de toepassings-ID van de AAD-Graph API) moet het probleem oplossen. In het geval dat de fout toegang geeft tussen toepassingen van de eerste partij, moet worden verwerkt via pre-autorisatie. het probleem is dat de gebruiker is aangemeld als service-principal. De opdracht moet slagen als deze door een gebruiker wordt uitgevoerd.

> [!TIP]
> U kunt een gebruiker niet rechtstreeks maken op basis van een andere Azure Active Directory dan de Azure Active Directory die aan uw Azure-abonnement is gekoppeld. Leden van andere Active Directory-gebruikers die zijn geïmporteerd in de gekoppelde Active Directory (ook wel externe gebruikers genoemd) kunnen wel worden toegevoegd aan een Active Directory groep in de Tenant Active Directory. Door een Inge sloten database gebruiker te maken voor die AD-groep, kunnen de gebruikers van de externe Active Directory toegang krijgen tot SQL Database.

Zie [Create User (Transact-SQL) (Engelstalig)](https://msdn.microsoft.com/library/ms173463.aspx)voor meer informatie over het maken van Inge sloten database gebruikers op basis van Azure Active Directory identiteiten.

> [!NOTE]
> Als u de Azure Active Directory beheerder voor Azure SQL Server verwijdert, kunnen gebruikers van Azure AD-verificatie geen verbinding maken met de server. Zo nodig kunnen niet-herbruikbare Azure AD-gebruikers hand matig worden verwijderd door een SQL Database beheerder.

> [!NOTE]
> Als er een **time-out**voor de verbinding is verlopen, moet u mogelijk de para meter `TransparentNetworkIPResolution` van de Connection String instellen op ONWAAR. Zie voor meer informatie [verbindingstime-out probleem met .NET Framework 4.6.1-TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Wanneer u een database gebruiker maakt, ontvangt die gebruiker de machtiging **verbinden** en kan deze verbinding maken met die data base als lid van de **open bare** rol. In eerste instantie zijn er machtigingen verleend aan de **open bare** rol of machtigingen die zijn verleend aan een Azure AD-groep waarvan ze lid zijn. Wanneer u een Inge sloten database gebruiker op basis van Azure AD hebt ingericht, kunt u de gebruiker extra machtigingen verlenen, op dezelfde manier als u de machtiging voor elk ander type gebruiker toewijst. Verleen doorgaans machtigingen aan database rollen en voeg gebruikers toe aan rollen. Zie [basis beginselen van machtigingen voor data base-engine](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)voor meer informatie. Zie [data bases en aanmeldingen beheren in Azure SQL database](sql-database-manage-logins.md)voor meer informatie over speciale SQL database rollen.
Een federatieve domein gebruikers account dat in een beheerd domein als een externe gebruiker wordt geïmporteerd, moet de identiteit van het beheerde domein gebruiken.

> [!NOTE]
> Azure AD-gebruikers worden in de metagegevens van de database gemarkeerd met het type E (EXTERNAL_USER) en voor groepen met het type X (EXTERNAL_GROUPS). Zie [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) voor meer informatie.

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Verbinding maken met de gebruikers database of het data warehouse met behulp van SSMS of SSDT  

Als u wilt controleren of de Azure AD-beheerder juist is ingesteld, maakt u verbinding met de **hoofd** database met behulp van het Azure AD-beheerders account.
Als u een Inge sloten database gebruiker op basis van Azure AD wilt inrichten (met uitzonde ring van de server beheerder die eigenaar is van de data base), maakt u verbinding met de data base met een Azure AD-identiteit die toegang heeft tot de data base.

> [!IMPORTANT]
> Ondersteuning voor Azure Active Directory-verificatie is beschikbaar met [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) en [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. De release van de 2016 van de augustus van SSMS bevat ook ondersteuning voor Active Directory universele verificatie, waarmee beheerders Multi-Factor Authentication kunnen vereisen met behulp van een telefoon gesprek, tekst bericht, Smart Cards met een pincode of een melding voor mobiele apps.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Een Azure AD-identiteit gebruiken om verbinding te maken met behulp van SSMS of SSDT

De volgende procedures laten zien hoe u verbinding maakt met een SQL database met een Azure AD-identiteit met behulp van SQL Server Management Studio-of SQL Server database Hulpprogramma's.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde verificatie Active Directory

Gebruik deze methode als u bent aangemeld bij Windows met behulp van uw Azure Active Directory referenties van een federatief domein.

1. Start Management Studio of Hulpprogram Ma's voor gegevens en selecteer in het dialoog venster **verbinding maken met server** (of **verbinding maken met data base-engine**) in het vak **verificatie** de optie **Active Directory geïntegreerd**. Er is geen wacht woord nodig of het kan worden ingevoerd omdat uw bestaande referenties worden weer gegeven voor de verbinding.

    ![Geïntegreerde AD-verificatie selecteren][11]

2. Selecteer de knop **Opties** en typ op de pagina **verbindings eigenschappen** in het vak **verbinding maken met data base** de naam van de gebruikers database waarmee u verbinding wilt maken. (De optie **AD-domein naam of Tenant-id**) wordt alleen ondersteund voor **Universeel met MFA-verbindings** opties, anders wordt deze grijs weer gegeven.)  

    ![De database naam selecteren][13]

## <a name="active-directory-password-authentication"></a>Wachtwoordverificatie via Active Directory

Gebruik deze methode wanneer u verbinding maakt met een principal-naam van Azure AD met behulp van het door Azure AD beheerde domein. U kunt dit ook gebruiken voor federatieve accounts zonder toegang tot het domein, bijvoorbeeld wanneer u extern werkt.

Gebruik deze methode om te verifiëren bij SQL data base/DW met Azure AD voor native of federatieve Azure AD-gebruikers. Een systeem eigen gebruiker wordt afzonderlijk gemaakt in azure AD en wordt geverifieerd met behulp van de gebruikers naam en het wacht woord, terwijl een federatieve gebruiker een Windows-gebruiker is met Azure AD. De laatste methode (met behulp van gebruikers & wacht woord) kan worden gebruikt wanneer een gebruiker hun Windows-referentie wil gebruiken, maar de lokale machine is niet gekoppeld aan het domein (bijvoorbeeld door middel van een externe toegang). In dit geval kan een Windows-gebruiker zijn of haar domein account en wacht woord aanduiden en kan worden geverifieerd bij SQL data base/DW met federatieve referenties.

1. Start Management Studio of Hulpprogram Ma's voor gegevens en selecteer in het dialoog venster **verbinding maken met server** (of **verbinding maken met data base-engine**) in het vak **verificatie** de optie **Active Directory-wacht woord**.

2. Typ in het vak **gebruikers naam** uw Azure Active Directory gebruikers naam in de notatie **username\@Domain.com**. Gebruikers namen moeten een account zijn uit het Azure Active Directory of een account van een domein met de Azure Active Directory.

3. Typ in het vak **wacht woord** het wacht woord van uw gebruiker voor de account van de Azure Active Directory of het federatieve domein.

    ![AD-wachtwoord verificatie selecteren][12]

4. Selecteer de knop **Opties** en typ op de pagina **verbindings eigenschappen** in het vak **verbinding maken met data base** de naam van de gebruikers database waarmee u verbinding wilt maken. (Zie de afbeelding in de vorige optie.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Een Azure AD-identiteit gebruiken om verbinding te maken vanuit een client toepassing

De volgende procedures laten zien hoe u verbinding maakt met een SQL database met een Azure AD-identiteit van een client toepassing.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde verificatie Active Directory

Als u geïntegreerde Windows-verificatie wilt gebruiken, moet de Active Directory van uw domein federatief zijn met Azure Active Directory. Uw client toepassing (of een service) die verbinding maakt met de data base moet worden uitgevoerd op een computer die lid is van een domein, onder de domein referenties van de gebruiker.

Als u verbinding wilt maken met een Data Base met behulp van geïntegreerde verificatie en een Azure AD-identiteit, moet het sleutel woord voor verificatie in de data base connection string worden ingesteld op Active Directory geïntegreerd. Het volgende C# code voorbeeld maakt gebruik van ADO .net.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Het sleutel woord connection string `Integrated Security=True` wordt niet ondersteund om verbinding te maken met Azure SQL Database. Wanneer u een ODBC-verbinding maakt, moet u spaties verwijderen en de verificatie instellen op ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Wachtwoordverificatie via Active Directory

Als u verbinding wilt maken met een Data Base met behulp van geïntegreerde verificatie en een Azure AD-identiteit, moet het sleutel woord voor verificatie zijn ingesteld op Active Directory wacht woord. Het connection string moet gebruikers-ID/UID en wacht woord/PWD tref woorden en waarden bevatten. Het volgende C# code voorbeeld maakt gebruik van ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Meer informatie over Azure AD-verificatie methoden met behulp van de voorbeeld code voorbeelden die beschikbaar zijn in [Azure AD Authentication github demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Met deze verificatie methode kunnen middelste laag Services verbinding maken met Azure SQL Database of Azure SQL Data Warehouse door een token van Azure Active Directory (AAD) te verkrijgen. Hiermee kunt u geavanceerde scenario's maken, waaronder verificatie op basis van certificaten. U moet vier basis stappen volt ooien voor het gebruik van Azure AD-token verificatie:

1. Registreer uw toepassing bij Azure Active Directory en haal de client-ID op voor uw code.
2. Maak een database gebruiker die de toepassing vertegenwoordigt. (Eerder in stap 6 voltooid.)
3. Een certificaat op de client computer maken, wordt de toepassing uitgevoerd.
4. Voeg het certificaat toe als sleutel voor uw toepassing.

Voorbeeld connection string:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Zie [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)(Engelstalig) voor meer informatie. Zie [aan de slag met verificatie op basis van certificaten in azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)voor meer informatie over het toevoegen van een certificaat.

### <a name="sqlcmd"></a>sqlcmd

De volgende instructies zijn een verbinding met versie 13,1 van Sqlcmd, die beschikbaar is via het [Download centrum](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` met de `-G` opdracht werkt niet met systeem identiteiten en vereist een gebruiker-Principal-aanmelding.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Problemen met Azure AD-verificatie oplossen

Meer informatie over het oplossen van problemen met Azure AD-verificatie vindt u in de volgende blog: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database-toegang en -beheer](sql-database-control-access.md) voor een overzicht van toegang en beheer in SQL Database.
- Zie [Aanmeldingen, gebruikers en databaserollen](sql-database-manage-logins.md) voor een overzicht van aanmeldingen, gebruikers en databaserollen in SQL Database.
- Zie [Principals](https://msdn.microsoft.com/library/ms181127.aspx) voor meer informatie over database-principals.
- Zie [Databaserollen](https://msdn.microsoft.com/library/ms189121.aspx) voor meer informatie over databaserollen.
- Zie [SQL Database-firewallregels](sql-database-firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

---
title: Azure Active Directory-verificatie configureren
description: Meer informatie over het maken van verbinding met SQL Database, beheerde instantie en Azure Synapse Analytics met Azure Active Directory Authentication - na het configureren van Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 0e244ea185011bbb7d9f0facad399bb9b577bbc2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419840"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Verificatie van Azure Active Directory configureren en beheren met SQL

In dit artikel ziet u hoe u Azure AD maakt en invult en vervolgens Azure AD gebruikt met Azure [SQL Database (SQL DB),](sql-database-technical-overview.md) [beheerde instantie (MI)](sql-database-managed-instance.md)en [Azure Synapse Analytics (voorheen Azure SQL Data Warehouse).](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Zie [Azure Active Directory Authentication](sql-database-aad-authentication.md)voor een overzicht .

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database als Azure Synapse. Voor de eenvoud wordt SQL Database gebruikt bij het verwijzen naar zowel SQL Database als Azure Synapse.

> [!IMPORTANT]  
> Verbinding maken met SQL Server die wordt uitgevoerd op een Azure VM wordt niet ondersteund met een Azure Active Directory-account. Gebruik in plaats daarvan een Active Directory-account voor een domein.

## <a name="azure-ad-authentication-methods"></a>Azure AD-verificatiemethoden

Azure AD-verificatie ondersteunt de volgende verificatiemethoden:

- Azure AD-cloud-identiteiten
- Hybride azure-identiteiten van AD die ondersteuning bieden voor:
  - Cloudverificatie met twee opties in combinatie met naadloze single sign-on (SSO)
    - Azure AD-verificatie van wachtwoordhash
    - Azure AD-verificatie voor doorgeefgegevens
  - Federatieve verificatie

Voor meer informatie over Azure AD-verificatiemethoden en welke te kiezen zie het artikel:
- [De juiste verificatiemethode kiezen voor uw azure Active Directory hybride identiteitsoplossing](../active-directory/hybrid/choose-ad-authn.md)

Zie de volgende artikelen voor meer informatie over hybride azure-identiteiten van AD, de installatie en synchronisatie:

- Wachtwoordhashverificatie - [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Verificatie doorgeven - [Azure Active Directory Pass-Through-verificatie](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federatieve verificatie - [Active Directory Federation Services implementeren in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) en Azure AD Connect en [federatie](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Alle bovenstaande verificatiemethoden worden ondersteund voor SQL DB (single database- en databasepools), beheerde instantie en Azure Synapse.

## <a name="create-and-populate-an-azure-ad"></a>Een Azure-AD maken en invullen

Maak een Azure AD en vul deze in met gebruikers en groepen. Azure AD kan het oorspronkelijke door Azure AD beheerde domein zijn. Azure AD kan ook een on-premises Active Directory Domain Services zijn die wordt gefedereerd met het Azure AD.

Zie [Uw on-premises identiteiten integreren met Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Uw domeinnaam toevoegen in Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Uw Azure AD-adreslijst beheren](../active-directory/fundamentals/active-directory-administer.md), [Azure AD beheren met Windows PowerShell](/powershell/azure/overview) en [Poorten en protocollen waarvoor hybride identiteit is vereist](../active-directory/hybrid/reference-connect-ports.md) voor meer informatie.

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen

1. Koppel uw Azure-abonnement aan Azure Active Directory door van de directory een vertrouwde map te maken voor het Azure-abonnement dat de database host. Zie Hoe [Azure-abonnementen zijn gekoppeld aan Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie.

2. Gebruik de directoryswitcher in de Azure-portal om over te schakelen naar het abonnement dat is gekoppeld aan domein.

   > [!IMPORTANT]
   > Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. De vertrouwensrelatie die een abonnement heeft met een directory is anders dan de relatie die een abonnement heeft met andere resources in Azure (websites, databases, enzovoort); deze resources lijken meer op onderliggende resources van een abonnement. Als een abonnement is verlopen, wordt toegang tot de andere resources die zijn gekoppeld aan het abonnement ook geblokkeerd. De directory blijft echter wel aanwezig in Azure, en u kunt er een ander abonnement aan koppelen om de directorygebruikers te blijven beheren. Zie [Toegang tot resources in Azure begrijpen voor](../active-directory/active-directory-b2b-admin-add-users.md)meer informatie over resources. Zie [Een Azure-abonnement koppelen aan of toevoegen aan Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie over deze vertrouwde relatie.

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Een Azure AD-beheerder voor Azure SQL-server maken

Elke Azure SQL-server (die een SQL Database of Azure Synapse host) begint met één serverbeheerdersaccount dat de beheerder is van de gehele Azure SQL-server. Er moet een tweede SQL Server-beheerder worden gemaakt, dat is een Azure AD-account. Deze principal wordt gemaakt als een opgenomen databasegebruiker in de hoofddatabase. Als beheerders zijn de serverbeheerdersaccounts lid van de **db_owner** rol in elke gebruikersdatabase en voeren ze elke gebruikersdatabase in als de **dbo-gebruiker.** Zie [Databases en Aanmeldingen beheren in Azure SQL Database](sql-database-manage-logins.md)voor meer informatie over de serverbeheerdersaccounts.

Wanneer u Azure Active Directory gebruikt met georeplicatie, moet de Azure Active Directory-beheerder zijn geconfigureerd voor zowel de primaire als de secundaire servers. Als een server geen Azure Active Directory-beheerder heeft, worden azure Active Directory-aanmeldingen en gebruikers ontvangen een fout 'Kan geen verbinding maken' met server.

> [!NOTE]
> Gebruikers die niet zijn gebaseerd op een Azure AD-account (inclusief het Azure SQL-serveradministratoraccount), kunnen geen azure AD-gebaseerde gebruikers maken, omdat ze geen toestemming hebben om voorgestelde databasegebruikers te valideren met het Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Een Azure Active Directory-beheerder inrichten voor uw beheerde instantie

> [!IMPORTANT]
> Volg deze stappen alleen als u een beheerde instantie indient. Deze bewerking kan alleen worden uitgevoerd door de beheerder van globale/bedrijfsbedrijven of een beheerder van een bevoorrechte rol in Azure AD. Volgende stappen beschrijven het proces van het verlenen van machtigingen voor gebruikers met verschillende bevoegdheden in directory.

> [!NOTE]
> Voor Azure AD-beheerders voor MI die vóór GA zijn gemaakt, maar doorgaan met het werken na GA, is er geen functionele wijziging in het bestaande gedrag. Zie de nieuwe [Azure AD-beheerfunctionaliteit voor MI voor](#new-azure-ad-admin-functionality-for-mi) meer informatie.

Uw beheerde instantie heeft machtigingen nodig om Azure AD te lezen om taken zoals verificatie van gebruikers via lidmaatschap van beveiligingsgroepen of het maken van nieuwe gebruikers met succes uit te voeren. Om dit te laten werken, moet u machtigingen verlenen aan beheerde instantie om Azure AD te lezen. Er zijn twee manieren om het te doen: van Portal en PowerShell. De volgende stappen beide methoden.

1. Ga naar de Azure-portal en selecteer in de rechterbovenhoek uw verbinding om een lijst met mogelijke Active Directories weer te geven.

2. Kies de juiste Active Directory als de standaard-Azure AD.

   Met deze stap wordt het abonnement gekoppeld aan Active Directory gekoppeld aan Beheerde instantie, zodat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als het beheerde exemplaar.

3. Navigeer naar Beheerde instantie en selecteer er een die u wilt gebruiken voor Azure AD-integratie.

   ![Aad](./media/sql-database-aad-authentication/aad.png)

4. Selecteer de banner boven op de pagina Active Directory-beheerder en geef toestemming aan de huidige gebruiker. Als u bent aangemeld als globale/bedrijfsbeheerder in Azure AD, u dit doen vanuit de Azure-portal of powershell gebruiken met het onderstaande script.

    ![machtigingen-portal verlenen](./media/sql-database-aad-authentication/grant-permissions.png)

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

5. Nadat de bewerking is voltooid, wordt de volgende melding in de rechterbovenhoek weergegeven:

    ![voltooid](./media/sql-database-aad-authentication/success.png)

6. Nu u uw Azure AD-beheerder kiezen voor uw beheerde exemplaar. Selecteer daarvoor op de pagina Active Directory-beheer de opdracht **Beheerder instellen.**

    ![setbeheerder](./media/sql-database-aad-authentication/set-admin.png)

7. Zoek op de pagina AAD-beheerders naar een gebruiker, selecteer de gebruiker of groep die beheerder wilt zijn en selecteer **Vervolgens Selecteren**.

   Op de pagina Active Directory-beheerders worden alle leden en groepen van uw Active Directory weergegeven. Gebruikers of groepen met een grijs niveau kunnen niet worden geselecteerd omdat ze niet worden ondersteund als Azure AD-beheerders. Bekijk de lijst met ondersteunde beheerders in [Azure AD-functies en -beperkingen](sql-database-aad-authentication.md#azure-ad-features-and-limitations). RBAC (Role-based access control) is alleen van toepassing op de Azure-portal en wordt niet gepropageerd naar SQL Server.

    ![Azure Active Directory-beheerder toevoegen](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Selecteer boven aan de pagina Active Directory-beheer de optie **Opslaan**.

    ![save](./media/sql-database-aad-authentication/save.png)

    Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak Active Directory-beheerder.

Nadat u een Azure AD-beheerder hebt ingericht voor uw beheerde instantie, u beginnen met het maken van Azure AD-serverprincipals (aanmeldingen) met de <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">syntaxis AANMELDING MAKEN.</a> Zie [overzicht van beheerde instantie voor](sql-database-managed-instance.md#azure-active-directory-integration)meer informatie .

> [!TIP]
> Als u een beheerder later wilt verwijderen, selecteert u boven aan de pagina Active Directory-beheer de optie **Beheerder verwijderen**en selecteert u **Opslaan**.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Nieuwe Azure AD-beheerfunctionaliteit voor MI

In de onderstaande tabel worden de functionaliteit voor de openbare preview azure AD-inlogbeheerder voor MI samengevat, versus een nieuwe functionaliteit die wordt geleverd met GA voor Azure AD-aanmeldingen.

| Azure AD-aanmeldingsbeheerder voor MI tijdens openbare preview | GA-functionaliteit voor Azure AD-beheerder voor MI |
| --- | ---|
| Gedraagt zich op dezelfde manier als Azure AD-beheerder voor SQL Database, waarmee Azure AD-verificatie wordt ingemaakt, maar de Azure AD-beheerder kan geen Azure AD- of SQL-aanmeldingen maken in de master db voor MI. | Azure AD-beheerder heeft sysadmin-machtigingen en kan AAD- en SQL-aanmeldingen maken in master db voor MI. |
| Is niet aanwezig in de sys.server_principals weergave | Is aanwezig in de sys.server_principals weergave |
| Hiermee kunnen afzonderlijke Azure AD-gastgebruikers worden ingesteld als Azure AD-beheerder voor MI. Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](../active-directory/b2b/add-users-administrator.md)voor meer informatie. | Hiervoor moet een Azure AD-groep worden gemaakt met gastgebruikers als leden om deze groep in te stellen als Azure AD-beheerder voor MI. Zie [Azure AD-business to business support voor](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)meer informatie. |

Als aanbevolen praktijk voor bestaande Azure AD-beheerders voor MI die vóór GA zijn gemaakt en nog steeds postGA werken, stelt u de Azure AD-beheerder opnieuw in met de azure-portal 'Beheerder verwijderen' en de optie Beheerder instellen voor dezelfde Azure AD-gebruiker of -groep.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Bekende problemen met de Azure AD-aanmeldingsga voor MI

- Als er een Azure AD-aanmelding bestaat in de hoofddatabase voor MI, gemaakt met de opdracht `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`T-SQL, kan deze niet worden ingesteld als een Azure AD-beheerder voor MI. Er wordt een fout weergegeven waarin de aanmelding wordt ingesteld als Azure AD-beheerder met behulp van de azure-portal-, PowerShell- of CLI-opdrachten om de Azure AD-aanmelding te maken.
  - De aanmelding moet worden verwijderd in `DROP LOGIN [myaadaccount]`de hoofddatabase met behulp van de opdracht , voordat het account kan worden gemaakt als een Azure AD-beheerder.
  - Stel het Azure AD-beheeraccount in `DROP LOGIN` de Azure-portal in nadat het is gelukt. 
  - Als u het Azure AD-beheeraccount niet instellen, schakelt u de hoofddatabase van het beheerde exemplaar in voor de aanmelding. Gebruik de volgende opdracht:`SELECT * FROM sys.server_principals`
  - Als u een Azure AD-beheerder voor MI instelt, wordt automatisch een aanmelding in de hoofddatabase voor dit account gemaakt. Als u de Azure AD-beheerder verwijdert, wordt de aanmelding automatisch verwijderd uit de hoofddatabase.

- Individuele Azure AD-gastgebruikers worden niet ondersteund als Azure AD-beheerders voor MI. Gastgebruikers moeten deel uitmaken van een Azure AD-groep die moet worden ingesteld als Azure AD-beheerder. Momenteel wordt het Azure-portalblad niet grijs voor gastgebruikers voor een ander Azure AD, zodat gebruikers de beheerinstelling kunnen voortzetten. Als u gastgebruikers opslaat als Azure AD-beheerder, mislukt de installatie.
  - Als u van een gastgebruiker een Azure AD-beheerder voor MI wilt maken, neemt u de gastgebruiker op in een Azure AD-groep en stelt u deze groep in als een Azure AD-beheerder.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell voor SQL-beheerde instantie

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u PowerShell-cmdlets wilt uitvoeren, moet Azure PowerShell zijn geïnstalleerd en uitgevoerd. Zie [How to install and configure Azure PowerShell](/powershell/azure/overview) (Azure PowerShell installeren en configureren) voor gedetailleerde informatie.

> [!IMPORTANT]
> De PowerShell Azure Resource Manager (RM)-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. De AzureRM-module blijft bugfixes ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek. Zie De nieuwe Azure [PowerShell Az-module introduceren](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit ervan.

Als u een Azure AD-beheerder wilt inrichten, voert u de volgende Azure PowerShell-opdrachten uit:

- Connect-AzAccount
- Select-AzAbonnement

Cmdlets die worden gebruikt voor het inrichten en beheren van Azure AD-beheervoor SQL-beheerde instantie:

| Naam van cmdlet | Beschrijving |
| --- | --- |
| [Set-azsqlinstanceActiveDirectoryAdministrator instellen](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Voorziet in een Azure AD-beheerder voor SQL-beheerde instantie in het huidige abonnement. (Moet van het huidige abonnement zijn)|
| [Remove-azsqlinstanceActivedirectoryadministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Hiermee verwijdert u een Azure AD-beheerder voor SQL-beheerde instantie in het huidige abonnement. |
| [Get-azsqlinstanceActivedirectoryadministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retourneert informatie over een Azure AD-beheerder voor SQL-beheerde instantie in het huidige abonnement.|

Met de volgende opdracht wordt informatie over een Azure AD-beheerder weergegeven voor een beheerd exemplaar met de naam ManagedInstance01 dat is gekoppeld aan een resourcegroep met de naam ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

In de volgende opdrachtwordt een Azure AD-beheerdersgroep met de naam DBA's ingericht voor het beheerde exemplaar ManagedInstance01. Deze server is gekoppeld aan resourcegroep ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Met de volgende opdracht wordt de Azure AD-beheerder verwijderd voor het beheerde exemplaar ManagedInstanceName01 dat is gekoppeld aan de resourcegroep ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U ook een Azure AD-beheerder inrichten voor SQL-beheerde instantie door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[az sql mi ad-admin maken](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Voorziet een Azure Active Directory-beheerder voor SQL-beheerde instantie. (Moet van het huidige abonnement zijn) |
|[az sql mi ad-admin verwijderen](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory-beheerder voor SQL-beheerde instantie. |
|[az sql mi ad-admin lijst](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor SQL-beheerde instantie. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Hiermee wordt de Active Directory-beheerder voor een SQL-beheerde instantie bijgewerkt. |

Zie az sql mi voor meer informatie over [CLI-opdrachten.](/cli/azure/sql/mi)

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Een Azure Active Directory-beheerder voor uw Azure SQL Database-server inrichten

> [!IMPORTANT]
> Volg deze stappen alleen als u een Azure SQL Database-server of Azure Synapse Analytics indient.

In de volgende twee procedures ziet u hoe u een Azure Active Directory-beheerder indient voor uw Azure SQL-server in de Azure-portal en met PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Ga naar de [Azure-portal](https://portal.azure.com/) en selecteer in de rechterbovenhoek uw verbinding om een lijst met mogelijke Active Directories weer te geven. Kies de juiste Active Directory als de standaard-Azure AD. In deze stap wordt de aan het abonnement gekoppelde Active Directory gekoppeld aan de Azure SQL-server, zodat u zeker weet dat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als SQL Server. (De Azure SQL-server kan host zijn van Azure SQL Database of Azure Synapse.)

    ![ad-kiezen][8]

2. Zoeken naar en selecteer **SQL-server**.

    ![SQL-servers zoeken en selecteren](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Op deze pagina u, voordat u **SQL-servers**selecteert, de **ster** naast de naam selecteren *om* de categorie favoriet te maken en **SQL-servers** toevoegen aan de linkernavigatiebalk.

3. Selecteer active **directory-beheerder**op de pagina **SQL Server** .

4. Selecteer op de pagina **Active Directory-beheer** de optie **Beheerder instellen**.

    ![SQL-servers stellen Active Directory-beheerder in](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. Zoek op de pagina **Beheerder toevoegen** een gebruiker. Selecteer de gebruiker of groep die beheerder moet zijn en selecteer **Selecteren**. (Op de pagina Active Directory-beheerder ziet u alle leden en groepen van uw Active Directory.) Gebruikers of groepen die grijs zijn gekleurd, kunnen niet worden geselecteerd omdat ze niet worden ondersteund als beheerders voor Azure AD. (Zie de lijst met ondersteunde beheerders in de sectie **Azure AD-onderdelen en -beperkingen** van [Azure Active Directory Authentication gebruiken voor verificatie met SQL Database of Azure Synapse](sql-database-aad-authentication.md).) RBAC (Role-based access control) is alleen van toepassing op de portal en wordt niet gepropageerd naar SQL Server.

    ![Azure Active Directory-beheerder selecteren](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. Selecteer boven aan de pagina **Active Directory-beheerder** de optie **Opslaan**.

    ![beheerder opslaan](./media/sql-database-aad-authentication/save-admin.png)

Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak **Active Directory-beheerder**.

   > [!NOTE]
   > Als u de Azure AD-beheerder instelt, mag de naam van de beheerder (gebruiker of groep) niet al aanwezig zijn in de virtuele hoofddatabase als een gebruiker van SQL Server-verificatie. Indien wel aanwezig, mislukt het instellen van de Azure AD-beheerder, waarna het maken ervan wordt teruggedraaid en er wordt aangegeven dat deze beheerder (naam) al bestaat. Omdat een dergelijke gebruiker van SQL Server-verificatie geen deel uitmaakt van Azure AD, mislukt het verbinding maken met de server met Azure AD-verificatie.

Als u een beheerder later wilt verwijderen, selecteert u boven aan de pagina **Active Directory-beheer** de optie **Beheerder verwijderen**en selecteert u **Opslaan**.

### <a name="powershell-for-azure-sql-database-and-azure-synapse"></a>PowerShell voor Azure SQL Database en Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u PowerShell-cmdlets wilt uitvoeren, moet Azure PowerShell zijn geïnstalleerd en uitgevoerd. Zie [How to install and configure Azure PowerShell](/powershell/azure/overview) (Azure PowerShell installeren en configureren) voor gedetailleerde informatie. Als u een Azure AD-beheerder wilt inrichten, voert u de volgende Azure PowerShell-opdrachten uit:

- Connect-AzAccount
- Select-AzAbonnement

Cmdlets die worden gebruikt voor het inrichten en beheren van Azure AD-beheer voor Azure SQL Database en Azure Synapse:

| Naam van cmdlet | Beschrijving |
| --- | --- |
| [Set-azsqlserverActiveDirectoryAdministrator instellen](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Voorziet een Azure Active Directory-beheerder voor Azure SQL Server of Azure Synapse. (Moet van het huidige abonnement zijn) |
| [Remove-azsqlserverActiveDirectoryadministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Hiermee verwijdert u een Azure Active Directory-beheerder voor Azure SQL-server of Azure Synapse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor de Azure SQL-server of Azure Synapse. |

Gebruik PowerShell-opdrachthulp om meer informatie voor elk van deze opdrachten te bekijken. Bijvoorbeeld `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

In de volgende scriptbepalingen wordt **DBA_Group** een Azure `40b79501-b343-44ed-9ce7-da4c8cc7353f`AD-beheerdersgroep met de naam DBA_Group (object-id) voor de **demo_server-server** in een brongroep met de naam **Groep-23:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

De parameter **DisplayName-invoer** accepteert de weergavenaam van Azure AD of de gebruikersnaam. Bijvoorbeeld, ``DisplayName="John Smith"`` en. ``DisplayName="johns@contoso.com"`` Voor Azure AD-groepen wordt alleen de Azure AD-weergavenaam ondersteund.

> [!NOTE]
> De opdracht ```Set-AzSqlServerActiveDirectoryAdministrator``` Azure PowerShell voorkomt niet dat u Azure AD-beheerders instelt voor niet-ondersteunde gebruikers. Een niet-ondersteunde gebruiker kan worden ingericht, maar kan geen verbinding maken met een database.

In het volgende voorbeeld wordt de optionele **ObjectID gebruikt:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> De Azure **AD-objectid** is vereist wanneer de **DisplayName** niet uniek is. Als u de **objectid-** en **DisplayName-waarden wilt** ophalen, gebruikt u de sectie Active Directory van Azure Classic Portal en bekijkt u de eigenschappen van een gebruiker of groep.

In het volgende voorbeeld wordt informatie geretourneerd over de huidige Azure AD-beheerder voor Azure SQL-server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

In het volgende voorbeeld wordt een Azure AD-beheerder verwijderd:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U een Azure AD-beheerder inrichten door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[az sql server ad-admin maken](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Voorziet een Azure Active Directory-beheerder voor Azure SQL Server of Azure Synapse. (Moet van het huidige abonnement zijn) |
|[az sql server ad-admin verwijderen](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory-beheerder voor Azure SQL-server of Azure Synapse. |
|[az sql server ad-admin lijst](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor de Azure SQL-server of Azure Synapse. |
|[update az sql server ad-admin](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Werkt de Active Directory-beheerder bij voor een Azure SQL-server of Azure Synapse. |

Zie az sql server voor meer informatie over [CLI-opdrachten.](/cli/azure/sql/server)

* * *

> [!NOTE]
> U ook een Azure Active Directory Administrator inrichten met behulp van de REST API's. Zie [Service Management REST API-referentie en -bewerkingen voor Azure SQL Database Operations voor Azure SQL Database Voor](/rest/api/sql/) meer informatie.

## <a name="configure-your-client-computers"></a>Uw clientcomputers configureren

Op alle clientmachines, waarvan uw toepassingen of gebruikers verbinding maken met Azure SQL Database of Azure Synapse met Azure AD-identiteiten, moet u de volgende software installeren:

- .NET Framework 4.6 [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)of hoger van .
- Azure Active Directory Authentication Library for SQL Server *(ADAL. DLL*). Hieronder vindt u de downloadlinks om de nieuwste SSMS-, ODBC- en OLE DB-stuurprogramma te installeren die de *ADAL bevat. DLL* bibliotheek.
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC-stuurprogramma 17 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB Driver 18 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

U aan deze vereisten voldoen door:

- Het installeren van de nieuwste versie van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) of SQL Server Data [Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) voldoet aan de .NET Framework 4.6-vereiste.
    - SSMS installeert de x86-versie van *ADAL. DLL*.
    - SSDT installeert de amd64 versie van *ADAL. DLL*.
    - De nieuwste Visual Studio van [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) voldoet aan de .NET Framework 4.6-vereiste, maar installeert niet de vereiste amd64-versie van *ADAL. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Opgenomen databasegebruikers maken in uw database die zijn toegewezen aan Azure AD-identiteiten

> [!IMPORTANT]
> Beheerde instantie ondersteunt nu Azure AD-serverprincipals (aanmeldingen), waarmee u aanmeldingen maken van Azure AD-gebruikers, -groepen of -toepassingen. Azure AD-serverprincipals (aanmeldingen) bieden de mogelijkheid om uw beheerde instantie te verifiëren zonder dat databasegebruikers moeten worden gemaakt als een gegevensgebruikers met een capaciteit. Zie [Overzicht van beheerde instantie voor](sql-database-managed-instance.md#azure-active-directory-integration)meer informatie . Zie <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">AANMAKEN VAN AANMAKEN</a>voor syntaxis bij het maken van Azure AD-serverprincipals (aanmeldingen.

Voor verificatie met Azure Active Directory is vereist dat databasegebruikers als gebruikers van ingesloten databases worden gemaakt. Een gebruiker van een ingesloten database op basis van een Azure AD-identiteit is een databasegebruiker die zich niet kan aanmelden bij de hoofddatabase en die wordt verwezen naar een identiteit in de Azure AD-directory die aan de database is gekoppeld. De Azure AD-identiteit kan een individueel gebruikersaccount of een groep zijn. Zie [Contained Database Users- Making Your Database Portable](https://msdn.microsoft.com/library/ff929188.aspx) (Gebruikers van ingesloten databases: een draagbare database maken) voor meer informatie over gebruikers van ingesloten databases.

> [!NOTE]
> Databasegebruikers kunnen niet in de Azure-portal worden gemaakt (met uitzondering van beheerders). RBAC-rollen worden niet gepropageerd naar SQL Server, SQL Database of Azure Synapse. Azure RBAC-rollen worden gebruikt voor het beheren van Azure-resources en zijn niet van toepassing op databasemachtigingen. De rol **SQL Server Contributor** verleent bijvoorbeeld geen toegang tot verbinding met de SQL Database of Azure Synapse. De toestemming tot het verlenen van toegang moet rechtstreeks plaatsvinden in de database met behulp van Transact-SQL-instructies.

> [!WARNING]
> Speciale tekens als de dubbele punt `:` of de ampersand `&` worden niet ondersteund indien deze worden opgenomen als gebruikersnamen in de instructies T-SQL CREATE LOGIN of CREATE USER.

Als u een azure AD-gebaseerde databasegebruiker (anders dan de serverbeheerder die eigenaar is van de database) wilt maken, maakt u verbinding met de database met een Azure AD-identiteit, als gebruiker met ten minste de toestemming **voor elke gebruiker wijzigen.** Gebruik vervolgens de volgende syntaxis Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan de gebruikersnaam van een Azure AD-gebruiker zijn of de weergavenaam voor een Azure AD-groep.

**Voorbeelden:** Ga als lid van het werk om een opgenomen databasegebruiker te maken die een Azure AD-gebruiker vertegenwoordigt die een azure-ad-gebruiker of beheerd domein vertegenwoordigt:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Als u een opgenomen databasegebruiker wilt maken die een Azure AD- of federatieve domeinnaamgroep vertegenwoordigt, geeft u de weergavenaam van een beveiligingsgroep op:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Ga als lid van het nieuwe bedrijf naar een databasegebruiker die een toepassing vertegenwoordigt die verbinding maakt met een Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Deze opdracht vereist dat SQL-toegang tot Azure AD (de 'externe provider') namens de ingelogde gebruiker. Soms ontstaan er omstandigheden waardoor Azure AD een uitzondering terugstuurt naar SQL. In deze gevallen ziet de gebruiker SQL-fout 33134, die het AAD-specifieke foutbericht moet bevatten. Meestal zal de fout zeggen dat de toegang wordt geweigerd, of dat de gebruiker zich moet inschrijven voor MFA om toegang te krijgen tot de bron, of dat de toegang tussen first-party-toepassingen moet worden afgehandeld via preautorisatie. In de eerste twee gevallen wordt het probleem meestal veroorzaakt door beleid voor voorwaardelijke toegang dat is ingesteld in de AAD-tenant van de gebruiker: ze verhinderen dat de gebruiker toegang krijgt tot de externe provider. Het bijwerken van het CA-beleid om toegang te verlenen tot de toepassing '00000002-0000-0000-c0000-0000000000000000' (de toepassings-id van de AAD Graph API) moet het probleem oplossen. In het geval dat de fout zegt toegang tussen first-party toepassingen moeten worden behandeld via preauthorization, het probleem is omdat de gebruiker is aangemeld als een service principal. De opdracht moet slagen als deze wordt uitgevoerd door een gebruiker.

> [!TIP]
> U niet rechtstreeks een gebruiker maken vanuit een Azure Active Directory anders dan de Azure Active Directory die is gekoppeld aan uw Azure-abonnement. Leden van andere Active Directories die geïmporteerde gebruikers zijn in de bijbehorende Active Directory (bekend als externe gebruikers) kunnen echter worden toegevoegd aan een Active Directory-groep in de tenant Active Directory. Door een opgenomen databasegebruiker voor die AD-groep te maken, kunnen de gebruikers uit de externe Active Directory toegang krijgen tot SQL Database.

Zie [GEBRUIKER maken (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)voor meer informatie over het maken van opgenomen databasegebruikers op basis van Azure Active Directory-identiteiten.

> [!NOTE]
> Als u de Azure Active Directory-beheerder voor Azure SQL-server verwijdert, kan een Azure AD-verificatiegebruiker geen verbinding maken met de server. Indien nodig kunnen onbruikbare Azure AD-gebruikers handmatig worden verwijderd door een SQL Database-beheerder.

> [!NOTE]
> Als u een **verbindingstime-out hebt ontvangen,** `TransparentNetworkIPResolution` moet u mogelijk de parameter van de verbindingstekenreeks op false instellen. Zie [Time-outprobleem van verbinding met .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)voor meer informatie.

Wanneer u een databasegebruiker maakt, ontvangt die gebruiker de **connect-machtiging** en kan deze verbinding maken met die database als lid van de **openbare** rol. In eerste instantie zijn de enige machtigingen die beschikbaar zijn voor de gebruiker eventuele machtigingen die zijn verleend aan de **openbare** rol, of machtigingen die zijn verleend aan Azure AD-groepen waarvan ze lid zijn. Zodra u een azure AD-gebaseerde databasegebruiker indient, u de gebruiker aanvullende machtigingen verlenen, op dezelfde manier als u toestemming verleent aan een ander type gebruiker. Geef doorgaans machtigingen voor databaserollen en voeg gebruikers toe aan rollen. Zie [Basisbeginselen voor databaseenginemachtigingen](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)voor meer informatie. Zie [Databases en aanmeldingen beheren in Azure SQL Database](sql-database-manage-logins.md)voor meer informatie over speciale SQL Database-rollen.
Een federatief domeingebruikersaccount dat als externe gebruiker in een beheerd domein wordt geïmporteerd, moet de beheerde domeinidentiteit gebruiken.

> [!NOTE]
> Azure AD-gebruikers worden in de metagegevens van de database gemarkeerd met het type E (EXTERNAL_USER) en voor groepen met het type X (EXTERNAL_GROUPS). Zie [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) voor meer informatie.

## <a name="connect-to-the-user-database-or-azure-synapse-by-using-ssms-or-ssdt"></a>Verbinding maken met de gebruikersdatabase of Azure Synapse met SSMS of SSDT  

Als u wilt bevestigen dat de Azure AD-beheerder goed is ingesteld, maakt u verbinding met de **hoofddatabase** met het Azure AD-beheerdersaccount.
Als u een azure AD-gebaseerde databasegebruiker (anders dan de serverbeheerder die eigenaar is van de database) wilt inrichten, maakt u verbinding met de database met een Azure AD-identiteit die toegang heeft tot de database.

> [!IMPORTANT]
> Ondersteuning voor Azure Active Directory-verificatie is beschikbaar met [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) en SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. De release van SSMS in augustus 2016 bevat ook ondersteuning voor Active Directory Universal Authentication, waarmee beheerders Multi-Factor Authentication kunnen vereisen via een telefoongesprek, sms, smartcards met pin of mobiele app-melding.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Een Azure AD-identiteit gebruiken om verbinding te maken met SSMS of SSDT

In de volgende procedures ziet u hoe u verbinding maakt met een SQL-database met een Azure AD-identiteit met SQL Server Management Studio of SQL Server Database Tools. 

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde active directory-verificatie

Gebruik deze methode als u bent aangemeld bij Windows met behulp van uw Azure Active Directory-referenties van een federatief domein of een beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding voor verificatie van doorgeef- en wachtwoordhash. Zie [Azure Active Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.

1. Start Management Studio of Data Tools en selecteer in het dialoogvenster **Verbinding maken met server** (of Verbinding maken met **databaseengine)** in het vak **Verificatie** de optie Azure Active Directory **- Integrated**. Er is geen wachtwoord nodig of kan worden ingevoerd omdat uw bestaande referenties worden gepresenteerd voor de verbinding.

    ![Ad-geïntegreerde verificatie selecteren][11]

2. Selecteer de knop **Opties** en typ op de pagina **Verbindingseigenschappen** in het vak **Verbinding maken met de database** de naam van de gebruikersdatabase waarmee u verbinding wilt maken. Zie voor meer informatie het artikel [Multi-factor AAD auth](sql-database-ssms-mfa-authentication.md#azure-ad-domain-name-or-tenant-id-parameter) over de verschillen tussen de verbindingseigenschappen voor SSMS 17.x en 18.x. 

    ![De databasenaam selecteren][13]

### <a name="active-directory-password-authentication"></a>Verificatie van Active Directory-wachtwoord

Gebruik deze methode wanneer u verbinding maakt met een hoofdnaam van Azure AD met het beheerde Azure AD-domein. U het ook gebruiken voor federatieve accounts zonder toegang tot het domein, bijvoorbeeld wanneer u op afstand werkt.

Gebruik deze methode om te verifiëren voor SQL DB of MI met Azure AD-gebruikers van de cloud of gebruikers die hybride azure-identiteiten van Azure AD gebruiken. Deze methode ondersteunt gebruikers die hun Windows-referentie willen gebruiken, maar hun lokale machine is niet verbonden met het domein (bijvoorbeeld met behulp van externe toegang). In dit geval kan een Windows-gebruiker zijn domeinaccount en wachtwoord aangeven en kan hij zich verifiëren bij SQL DB, MI of Azure Synapse.

1. Start Management Studio of Data Tools en selecteer in het dialoogvenster **Verbinding maken met server** (of Verbinding maken met **databaseengine)** in het vak **Verificatie** de optie Azure Active Directory **- Password**.

2. Typ **in** het vak Gebruikersnaam uw Azure Active Directory-gebruikersnaam in de **indeling gebruikersnaam\@domain.com**. Gebruikersnamen moeten een account zijn van Azure Active Directory of een account uit een beheerd of federatief domein met Azure Active Directory.

3. Typ **in** het vak Wachtwoord uw gebruikerswachtwoord voor het Azure Active Directory-account of beheerd/gefedereerd domeinaccount.

    ![Ad-wachtwoordverificatie selecteren][12]

4. Selecteer de knop **Opties** en typ op de pagina **Verbindingseigenschappen** in het vak **Verbinding maken met de database** de naam van de gebruikersdatabase waarmee u verbinding wilt maken. (Zie de afbeelding in de vorige optie.)

### <a name="active-directory-interactive-authentication"></a>Interactieve Active Directory-verificatie

Gebruik deze methode voor interactieve verificatie met of zonder multi-factor authenticatie (MFA), waarbij wachtwoord interactief wordt aangevraagd. Deze methode kan worden gebruikt om te verifiëren voor SQL DB, MI en Azure Synapse voor Azure AD-identiteitsgebruikers met alleen Azure-cloud of gebruikers die hybride azure-identiteiten gebruiken.

Zie [Multi-factor AAD-verificatie gebruiken met Azure SQL Database en Azure Synapse Analytics (SSMS-ondersteuning voor MFA) voor](sql-database-ssms-mfa-authentication.md)meer informatie.

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Een Azure AD-identiteit gebruiken om verbinding te maken vanuit een clienttoepassing

In de volgende procedures ziet u hoe u verbinding maakt met een SQL-database met een Azure AD-identiteit vanuit een clienttoepassing.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde active directory-verificatie

Als u geïntegreerde Windows-verificatie wilt gebruiken, moet de Active Directory van uw domein worden gefedereerd met Azure Active Directory of moet het een beheerd domein zijn dat is geconfigureerd voor naadloze eenmalige aanmelding voor verificatie van doorgeef- of wachtwoordhash. Zie [Azure Active Directory Seamless Single Sign-On](../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) voor geïntegreerde Windows-verificatie wordt niet ondersteund voor naadloze single sign-on voor pass-through en password hash authenticatie.

Uw clienttoepassing (of een service) die verbinding maakt met de database, moet worden uitgevoerd op een door een domein verbonden machine onder de domeinreferenties van een gebruiker.

Als u verbinding wilt maken met een database met geïntegreerde verificatie en een `Active Directory Integrated`Azure AD-identiteit, moet het trefwoord Verificatie in de tekenreeks voor databaseverbinding worden ingesteld op . In het volgende C#-codevoorbeeld wordt ADO .NET gebruikt.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Het trefwoord `Integrated Security=True` verbindingstekenreeks wordt niet ondersteund voor verbinding maken met Azure SQL Database. Wanneer u een ODBC-verbinding maakt, moet u spaties verwijderen en Verificatie instellen op 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Verificatie van Active Directory-wachtwoord

Als u verbinding wilt maken met een database met alleen Azure AD-gebruikersaccounts voor identiteitsgebruikers `Active Directory Password`of gebruikers van azure AD-identiteiten, moet het trefwoord Verificatie zijn ingesteld op . De verbindingstekenreeks moet gebruikers-id/UID en Password/PWD-trefwoorden en -waarden bevatten. In het volgende C#-codevoorbeeld wordt ADO .NET gebruikt.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Meer informatie over Azure AD-verificatiemethoden met behulp van de democodevoorbeelden die beschikbaar zijn in [Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Met deze verificatiemethode kunnen services op het middenniveau [JSON Web Tokens (JWT)](../active-directory/develop/id-tokens.md) worden verkregen om verbinding te maken met Azure SQL Database of Azure Synapse door een token te verkrijgen van Azure Active Directory (AAD). Met deze methode kunnen verschillende toepassingsscenario's worden toegepast, waaronder serviceidentiteiten, serviceprincipals en toepassingen met behulp van verificatie op basis van certificaten. U moet vier basisstappen uitvoeren om Azure AD-tokenverificatie te gebruiken:

1. Registreer uw toepassing bij Azure Active Directory en ontvang de client-id voor uw code.
2. Maak een databasegebruiker die de toepassing vertegenwoordigt. (Eerder voltooid in stap 6.)
3. Maak een certificaat op de clientcomputer en de toepassing.
4. Voeg het certificaat toe als sleutel voor uw aanvraag.

Voorbeeldverbindingstekenreeks:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Zie [SQL Server Security Blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)voor meer informatie . Zie Aan de slag [met verificatie op basis van certificaten in Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)voor informatie over het toevoegen van een certificaat.

### <a name="sqlcmd"></a>sqlcmd

De volgende instructies verbinden met versie 13.1 van sqlcmd, die beschikbaar is in het [Downloadcentrum.](https://www.microsoft.com/download/details.aspx?id=53591)

> [!NOTE]
> `sqlcmd`met `-G` de opdracht werkt niet met systeemidentiteiten, en vereist een gebruiker belangrijkste login.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Azure AD-verificatie oplossen

Richtlijnen voor het oplossen van problemen met Azure AD-verificatie vindt u in de volgende blog:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Volgende stappen

- Zie Aanmeldingen, gebruikers, databaserollen en machtigingen in SQL Database voor een overzicht van [aanmeldingen, gebruikers, databaserollen en gebruikersaccounts.](sql-database-manage-logins.md)
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

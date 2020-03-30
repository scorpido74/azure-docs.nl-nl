---
title: ActiveDirectoryInteractive maakt verbinding met SQL
description: C# Code voorbeeld, met uitleg, voor het verbinden met Azure SQL Database met sqlAuthenticationMethod.ActiveDirectoryInteractive modus.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692299"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Verbinding maken met Azure SQL Database met Azure Multi-Factor Authentication

In dit artikel wordt een C#-programma weergegeven dat verbinding maakt met Azure SQL Database. Het programma maakt gebruik van verificatie in de interactieve modus, die [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)ondersteunt.

Zie [Azure Active Directory-ondersteuning in SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory)voor meer informatie over ondersteuning voor multifactorverificatie voor SQL-hulpprogramma's.

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Meervoudige verificatie voor Azure SQL-database

Vanaf .NET Framework versie 4.7.2 [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) heeft het enum een nieuwe waarde: `ActiveDirectoryInteractive`. In een client C#-programma stuurt de enum-waarde het systeem aan om de interactieve azure-modus (Azure AD) van Azure Directory te gebruiken die multifactorverificatie ondersteunt om verbinding te maken met een Azure SQL-database. De gebruiker die het programma uitvoert, ziet de volgende dialoogvensters:

* Een dialoogvenster met een Azure AD-gebruikersnaam en waarin het wachtwoord van de gebruiker wordt gevraagd.

   Als het domein van de gebruiker is gefedereerd met Azure AD, wordt dit dialoogvenster niet weergegeven, omdat er geen wachtwoord nodig is.

   Als in het Azure AD-beleid multifactorverificatie aan de gebruiker wordt opgelegd, worden de volgende twee dialoogvensters weergegeven.

* De eerste keer dat een gebruiker multifactorauthenticatie doorloopt, geeft het systeem een dialoogvenster weer waarin wordt gevraagd om een mobiel telefoonnummer om sms-berichten naar te verzenden. Elk bericht bevat de *verificatiecode* die de gebruiker in het volgende dialoogvenster moet invoeren.

* Een dialoogvenster waarin wordt gevraagd om een verificatiecode voor verificatie van meerdere factoren, die het systeem naar een mobiele telefoon heeft verzonden.

Zie [Aan de slag met Azure Multi-Factor Authentication in de cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)voor informatie over het configureren van Azure AD om multifactorverificatie te vereisen.

Zie [Multi-factor authenticatie configureren voor SQL Server Management Studio en Azure AD voor](sql-database-ssms-mfa-authentication-configure.md)schermafbeeldingen van deze dialoogvensters.

> [!TIP]
> U zoeken naar .NET Framework API's met de [toolpagina .NET API Browser](https://docs.microsoft.com/dotnet/api/).
>
> U ook rechtstreeks zoeken met de [optionele parameter ?term=&lt;zoekwaarde.&gt; ](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Uw C#-toepassing configureren in de Azure-portal

Voordat u begint, moet u een [Azure SQL Database-server](sql-database-get-started-portal.md) hebben gemaakt en beschikbaar hebben.

### <a name="register-your-app-and-set-permissions"></a>Uw app registreren en machtigingen instellen

Als u Azure AD-verificatie wilt gebruiken, moet uw C#-programma zich registreren als een Azure AD-toepassing. Als u een app wilt registreren, moet u een Azure AD-beheerder zijn of een gebruiker die de rol Azure *AD-toepassingsontwikkelaar* heeft toegewezen. Zie [Beheerders- en niet-beheerdersrollen toewijzen aan gebruikers met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)meer informatie over het toewijzen van rollen.

Als u een app-registratie voltooit, genereert en geeft u een **toepassings-id weer**. Uw programma moet deze ID bevatten om verbinding te maken.

Ga als het gaat om het registreren en instellen van de benodigde machtigingen voor uw aanvraag:

1. Selecteer in de Azure Portal de nieuwe registratie van **Azure Active Directory** > **App- registraties** > **New registration**.

    ![App-registratie](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Nadat de app-registratie is gemaakt, wordt de waarde van de **toepassings-id** gegenereerd en weergegeven.

    ![App-id weergegeven](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Api-machtigingen** > **toevoegen Voeg een machtiging toe.**

    ![Instellingen voor machtigingen voor geregistreerde app](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecteer **API's die mijn organisatie gebruikt** > Azure SQL **Database** in de zoek- > te typen en Selecteer Azure **SQL Database**.

    ![Toegang toevoegen tot API voor Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecteer **Gedelegeerde machtigingen** > **user_impersonation** > Machtigingen**toevoegen**.

    ![Machtigingen delegeren aan API voor Azure SQL Database](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Een Azure AD-beheerder instellen voor uw SQL Database-server

Als uw C#-programma moet worden uitgevoerd, moet een Azure SQL-serverbeheerder een Azure AD-beheerder toewijzen voor uw SQL Database-server. 

Selecteer **op** de pagina SQL Server de optie **Beheerder van Active Directory-beheerset** > **Set admin**.

Zie de schermafbeeldingen in [Azure Active Directory-verificatie configureren en beheren met SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)voor meer informatie over Azure AD-beheerders en gebruikers voor Azure SQL Database.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Een niet-beheerdersgebruiker toevoegen aan een specifieke database (optioneel)

Een Azure AD-beheerder voor een SQL Database-server kan het voorbeeldprogramma C# uitvoeren. Een Azure AD-gebruiker kan het programma uitvoeren als deze zich in de database bevindt. Een Azure AD SQL-beheerder of een Azure AD-gebruiker `ALTER ANY USER` die al in de database aanwezig is en de toestemming heeft voor de database, kan een gebruiker toevoegen.

U een gebruiker aan de [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) database toevoegen met de SQL-opdracht. Een voorbeeld is `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Zie [Azure Active Directory Authentication gebruiken voor verificatie met SQL Database, Managed Instance of SQL Data Warehouse voor](sql-database-aad-authentication.md)meer informatie.

## <a name="new-authentication-enum-value"></a>Nieuwe authenticatie enum waarde

Het c#-voorbeeld is [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) gebaseerd op de naamruimte. Van bijzonder belang voor Multi-Factor `SqlAuthenticationMethod`Authenticatie is het enum , die de volgende waarden heeft:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Gebruik deze waarde met een Azure AD-gebruikersnaam om multifactorverificatie te implementeren. Deze waarde is de focus van het huidige artikel. Het produceert een interactieve ervaring door dialoogvensters weer te geven voor het gebruikerswachtwoord en vervolgens voor multi-factorauthenticatievalidatie als multifactorauthenticatie aan deze gebruiker wordt opgelegd. Deze waarde is beschikbaar vanaf .NET Framework versie 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Gebruik deze waarde voor een *federatieve* account. Voor een federatief account is de gebruikersnaam bekend bij het Windows-domein. Deze verificatiemethode biedt geen ondersteuning voor multifactorverificatie.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Gebruik deze waarde voor verificatie waarvoor een Azure AD-gebruikersnaam en -wachtwoord vereist zijn. Azure SQL Database doet de verificatie. Deze methode biedt geen ondersteuning voor multifactorauthenticatie.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>C#-parameterwaarden instellen vanuit de Azure-portal

Als het C#-programma succesvol moet worden uitgevoerd, moet u de juiste waarden toewijzen aan statische velden. Hier worden velden weergegeven met voorbeeldwaarden. Ook worden de Azure-portallocaties weergegeven waar u de benodigde waarden verkrijgen.

| Statische veldnaam | Voorbeeldwaarde | Waar in Azure-portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL-servers** > **filteren op naam** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active** > **Directory-gebruiker** > Nieuwe**gastgebruiker** |
| Initial_DatabaseName | "myDatabase" | **SQL-servers** > **SQL-databases** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-11111111111" | **Azure Active Directory** > **App-registraties** > **Zoeken op naam** > **Toepassings-id** |
| RedirectUri (RedirectUri) | nieuwe Uri("https://mywebserver.com/") | **Azure Active Directory** > **App-registraties** > **Zoeken op naam** > *[Uw-App-registratie]* > **Instellingen** > **RedirectURIs**<br /><br />Voor dit artikel is elke geldige waarde prima voor RedirectUri, omdat deze hier niet wordt gebruikt. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifiëren met SQL Server Management Studio

Voordat u het C#-programma uitvoert, is het een goed idee om te controleren of uw instellingen en configuraties correct zijn in SQL Server Management Studio (SSMS). Elke C# programma fout kan dan worden beperkt tot broncode.

### <a name="verify-sql-database-firewall-ip-addresses"></a>IP-adressen van SQL Database-firewallfirewall verifiëren

Voer SSMS uit vanaf dezelfde computer, in hetzelfde gebouw, waar u van plan bent het C#-programma uit te voeren. Voor deze test is elke **verificatiemodus** OK. Zie [Azure SQL Database-firewallregels op serverniveau en firewallregels](sql-database-firewall-configure.md) op databaseniveau voor hulp als er aanwijzingen zijn dat de firewall van de databaseserver uw IP-adres niet accepteert.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory multi-factor-verificatie verifiëren

Voer SSMS opnieuw uit, dit keer met **verificatie** ingesteld op **Active Directory - Universal met MFA-ondersteuning**. Deze optie vereist SSMS-versie 17.5 of hoger.

Zie [Multifactorverificatie configureren voor SSMS en Azure AD voor](sql-database-ssms-mfa-authentication-configure.md)meer informatie.

> [!NOTE]
> Als u een gastgebruiker in de database bent, moet u ook de Azure AD-domeinnaam voor de database opgeven: Selecteer**AD-domeinnaam of tenant-id** **opties** > . Als u de domeinnaam in de Azure-portal wilt vinden, selecteert u **Azure Active Directory** > **Custom-domeinnamen**. In het C#-voorbeeldprogramma is het niet nodig om een domeinnaam op te geven.

## <a name="c-code-example"></a>Voorbeeld van C# code

Het voorbeeld C#-programma is gebaseerd op de [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL-assemblage.

Als u dit pakket wilt installeren, selecteert u **Project** > **Manage NuGet-pakketten**in Visual Studio. Zoeken naar en installeren **van Microsoft.IdentityModel.Clients.ActiveDirectory**.

Dit is een voorbeeld van C# broncode.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Dit is een voorbeeld van de C#-testuitvoer.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)

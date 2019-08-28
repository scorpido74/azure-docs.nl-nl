---
title: SQL Database-verbinding beveiligen met beheerde identiteit - Azure App Service | Microsoft Docs
description: Meer informatie over hoe u de connectiviteit van de data base veiliger maakt met behulp van een beheerde identiteit en hoe u deze kunt Toep assen op andere Azure-Services.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8a0b974e9b64d477e53c37757b4f2fa952befba2
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061863"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Zelfstudie: Azure SQL Database-verbinding vanuit App Service beveiligen met een beheerde identiteit

[App Servicex](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie in Azure. De service bevat ook een [beheerde identiteit](overview-managed-identity.md) voor uw app. Dit is een gebruiksklare oplossing voor het beveiligen van toegang tot [Azure SQL Database](/azure/sql-database/) en andere Azure-services. Beheerde identiteiten in App Service maken uw app veiliger doordat geheimen in uw app, zoals referenties in de verbindingsreeksen, worden verwijderd. In deze zelf studie gaat u beheerde identiteit toevoegen aan de voor beeld-web-app die u hebt gemaakt in een van de volgende zelf studies: 

- [Zelfstudie: Een ASP.NET-app bouwen in azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Zelfstudie: Een ASP.NET Core-en SQL Database-app bouwen in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Wanneer u klaar bent, maakt uw voorbeeld-app veilig verbinding met SQL Database zonder dat een gebruikersnaam en wachtwoorden zijn vereist.

> [!NOTE]
> De stappen in deze zelf studie ondersteunen de volgende versies:
> 
> - .NET Framework 4.7.2 en hoger.
> - .NET Core 2,2 en hoger.
>

Wat u leert:

> [!div class="checklist"]
> * Beheerde identiteiten inschakelen
> * SQL Database toegang verlenen tot de beheerde identiteit
> * Entity Framework configureren voor het gebruik van Azure AD-verificatie met SQL Database
> * Verbinding maken met SQL Database vanuit Visual Studio met behulp van Azure AD-verificatie

> [!NOTE]
>Azure AD-verificatie _wijkt af_ van [geïntegreerde Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) -authenticatie in on-premises Active Directory (AD DS). AD DS en Azure AD gebruiken volledig verschillende verificatie protocollen. Zie [Documentatie voor Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit artikel gaat verder waar u bent gebleven in [Zelfstudie: Bouw een ASP.net-app in azure met](app-service-web-tutorial-dotnet-sqldatabase.md) SQL database [of zelf studie: Bouw een ASP.NET Core-en SQL Database-app](app-service-web-tutorial-dotnetcore-sqldb.md)in azure app service. Als u dat nog niet hebt gedaan, volgt u eerst een van de twee zelf studies. U kunt de stappen voor uw eigen .NET-app ook aanpassen met SQL Database.

Als u fouten wilt opsporen in uw app met SQL Database als de back-end, zorgt u ervoor dat de client verbinding van uw computer is toegestaan. Als dat niet het geval is, voegt u het IP-adres van de client toe aan de hand van de stappen in [IP-firewall regels op server niveau beheren met de Azure Portal](../sql-database/sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Database toegang verlenen aan Azure AD-gebruiker

Schakel eerst Azure AD-verificatie in om SQL Database door een Azure AD-gebruiker toe te wijzen als Active Directory beheerder van de SQL Database-Server. Deze gebruiker wijkt af van de Microsoft-account die u hebt gebruikt om u aan te melden voor uw Azure-abonnement. Dit moet een gebruiker zijn die u hebt gemaakt, geïmporteerd, gesynchroniseerd of uitgenodigd voor Azure AD. Zie [Azure AD-functies en-beperkingen in SQL database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)voor meer informatie over de toegestane Azure AD-gebruikers.

Als uw Azure AD-Tenant nog geen gebruiker heeft, maakt u er een door de stappen te volgen op [gebruikers toevoegen of verwijderen met Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Zoek de object-id van de Azure AD-gebruiker [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) met behulp van de en vervang  *\<User-Principal-name >* . Het resultaat wordt opgeslagen in een variabele.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Als u de lijst met alle UPN-namen in azure AD wilt zien `az ad user list --query [].userPrincipalName`, voert u uit.
>

Voeg deze Azure AD-gebruiker toe als Active Directory- [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) beheerder met behulp van de opdracht in de Cloud shell. Vervang `.database.windows.net`  *\<* in de volgende opdracht Server naam > door de naam van de SQL database-server (zonder het achtervoegsel).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Zie [een Azure Active Directory beheerder voor uw Azure SQL database server inrichten](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) voor meer informatie over het toevoegen van een Active Directory-beheerder

## <a name="set-up-visual-studio"></a>Visual Studio instellen

Als u de ontwikkeling en fout opsporing in Visual Studio wilt inschakelen, voegt u uw Azure AD-gebruiker toe in Visual Studio door**instellingen** voor **Bestands** > accounts te selecteren in het menu en op **een account toevoegen**te klikken.

Als u de Azure AD-gebruiker voor Azure-service verificatie wilt instellen, selecteert u **extra** > **Opties** in het menu en selecteert u **Azure service-verificatie** > **accounts**selecteren. Selecteer de Azure AD-gebruiker die u hebt toegevoegd en klik op **OK**.

U kunt nu uw app ontwikkelen en fouten opsporen met de SQL Database als back-end, met behulp van Azure AD-verificatie.

## <a name="modify-your-project"></a>Uw project wijzigen

De stappen die u voor uw project uitvoert, zijn afhankelijk van het feit of het een ASP.NET-project of een ASP.NET Core project is.

- [ASP.NET wijzigen](#modify-aspnet)
- [ASP.NET Core wijzigen](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET wijzigen

Open Package Manager Console in Visual Studio en voeg het NuGet-pakket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) toe:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

In *Web. config*werkt u vanaf de bovenkant van het bestand en brengt u de volgende wijzigingen aan:

- Voeg `<configSections>`in de volgende sectie declaratie in toe:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Voeg onder de `</configSections>` eindtag de volgende XML-code toe voor `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Zoek de Connection String met `MyDbConnection` de naam en `connectionString` Vervang de `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`waarde door. _Vervang\<server-name >_ en  _\<db-name >_ door de naam van de server en de data base.

Dat is alles wat u nodig hebt om verbinding te maken met SQL Database. Als u fouten opspoort in Visual Studio, gebruikt de code de Azure AD-gebruiker die u hebt geconfigureerd in [Visual Studio instellen](#set-up-visual-studio). U stelt de SQL Database Server later in om verbinding te maken met de beheerde identiteit van uw App Service-app.

Typ `Ctrl+F5` om de app opnieuw uit te voeren. Dezelfde ruwe app in uw browser is nu rechtstreeks verbonden met de Azure SQL Database, met behulp van Azure AD-verificatie. Met deze installatie kunt u database migraties uitvoeren vanuit Visual Studio.

### <a name="modify-aspnet-core"></a>ASP.NET Core wijzigen

Open Package Manager Console in Visual Studio en voeg het NuGet-pakket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) toe:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

In de [zelf studie ASP.net core en SQL database](app-service-web-tutorial-dotnetcore-sqldb.md)wordt `MyDbConnection` de Connection String helemaal niet gebruikt omdat de lokale ontwikkel omgeving een SQLite-database bestand gebruikt en de Azure-productie omgeving een Connection String van app service gebruikt. Met Active Directory-verificatie wilt u dat beide omgevingen dezelfde connection string gebruiken. In *appSettings. json*vervangt u de waarde van de `MyDbConnection` connection string door:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Verwijder in *Startup.cs*de sectie code die u hebt toegevoegd vóór:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

En vervang deze door de volgende code:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Vervolgens geeft u de Entity Framework database context met het toegangs token voor de SQL Database. Voeg in *Data\MyDatabaseContext.cs*de volgende code toe in de accolades van de lege `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` constructor:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Deze demonstratie code is synchroon voor duidelijkheid. Zie voor meer informatie [asynchrone hand leiding voor constructors](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Dat is alles wat u nodig hebt om verbinding te maken met SQL Database. Als u fouten opspoort in Visual Studio, gebruikt de code de Azure AD-gebruiker die u hebt geconfigureerd in [Visual Studio instellen](#set-up-visual-studio). U stelt de SQL Database Server later in om verbinding te maken met de beheerde identiteit van uw App Service-app. De `AzureServiceTokenProvider` klasse slaat het token in de cache op in het geheugen en haalt dit net vóór de verval datum op uit Azure AD. U hebt geen aangepaste code nodig om het token te vernieuwen.

Typ `Ctrl+F5` om de app opnieuw uit te voeren. Dezelfde ruwe app in uw browser is nu rechtstreeks verbonden met de Azure SQL Database, met behulp van Azure AD-verificatie. Met deze installatie kunt u database migraties uitvoeren vanuit Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Beheerde identiteits connectiviteit gebruiken

Vervolgens configureert u uw App Service-app om verbinding te maken met SQL Database met een door het systeem toegewezen beheerde identiteit.

### <a name="enable-managed-identity-on-app"></a>Beheerde identiteit op de app inschakelen

Als u een beheerde identiteit voor uw Azure-app wilt inschakelen, gebruikt u de opdracht [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) in de Cloud Shell. Vervang in de volgende opdracht de  *app-naam>\<* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Hier volgt een voor beeld van de uitvoer:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Beheerde identiteit toevoegen aan een Azure AD-groep

Als u deze identiteit toegang wilt verlenen tot uw SQL Database, moet u deze toevoegen aan een [Azure AD-groep](../active-directory/fundamentals/active-directory-manage-groups.md). Voeg het Cloud Shell toe aan een nieuwe groep met de naam _myAzureSQLDBAccessGroup_, zoals wordt weer gegeven in het volgende script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Als u de volledige JSON-uitvoer voor elke opdracht wilt zien, haal dan de `--query objectId --output tsv`-parameters weg.

### <a name="grant-permissions-to-azure-ad-group"></a>Machtigingen verlenen aan de Azure AD-groep

Meld u in de Cloud Shell aan bij SQL Database met behulp van de SQLCMD-opdracht. _Vervang\<server naam >_ door de naam van de SQL database-server,  _\<DB-naam >_ met de database naam die uw app gebruikt en  _\<Aad-User-name >_ en _\<Aad-Password >_ met de referenties van uw Azure AD-gebruiker.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Voer in de SQL-prompt voor de gewenste data base de volgende opdrachten uit om de Azure AD-groep toe te voegen en de machtigingen te verlenen die uw app nodig heeft. Bijvoorbeeld: 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Typ `EXIT` om terug te keren naar de Cloud Shell-prompt.

### <a name="modify-connection-string"></a>De verbindingsreeks wijzigen

Houd er rekening mee dat dezelfde wijzigingen die u hebt aangebracht in *Web. config* of *appSettings. json* werkt met de beheerde identiteit, dus het enige wat u moet doen, is om de bestaande connection string te verwijderen in app service, waarmee Visual Studio de eerste keer wordt geïmplementeerd met de implementatie van uw app. tegelijk. Gebruik de volgende opdracht, maar vervang  *\<app-name >* door de naam van uw app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Uw wijzigingen publiceren

U hoeft nu alleen nog maar uw wijzigingen naar Azure te publiceren.

**Als u een [zelf studie hebt ontvangen: Bouw een ASP.net-app in azure met](app-service-web-tutorial-dotnet-sqldatabase.md)SQL database**en publiceer uw wijzigingen in Visual Studio. Klik in de **Solution Explorer** met de rechtermuisknop op uw project **DotNetAppSqlDb** en selecteer **Publiceren**.

![Publiceren vanuit Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klik op de publicatiepagina op **Publiceren**. 

**Als u een [zelf studie hebt ontvangen: Bouw een ASP.net core-en SQL database-app](app-service-web-tutorial-dotnetcore-sqldb.md)in azure app service**, publiceer uw wijzigingen met git, met de volgende opdrachten:

```bash
git commit -am "configure managed identity"
git push azure master
```

Wanneer de nieuwe webpagina uw takenlijst weergeeft, maakt uw app verbinding met de database met behulp van de beheerde identiteit.

![Azure-app na Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

U zou nu de takenlijst moeten kunnen bewerken als voorheen.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Beheerde identiteiten inschakelen
> * SQL Database toegang verlenen tot de beheerde identiteit
> * Entity Framework configureren voor het gebruik van Azure AD-verificatie met SQL Database
> * Verbinding maken met SQL Database vanuit Visual Studio met behulp van Azure AD-verificatie

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)

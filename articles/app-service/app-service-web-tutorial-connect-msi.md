---
title: 'Zelfstudie: Toegang tot gegevens met een beheerde identiteit'
description: Meer informatie over het veiliger maken van databaseconnectiviteit door gebruik te maken van een beheerde identiteit en ook hoe u deze toepassen op andere Azure-services.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: mvc, cli-validate
ms.openlocfilehash: 787809e4132defa101bb82659e8af1a2d9f1b9b6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453774"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Zelfstudie: De Azure SQL Database-verbinding vanuit App Service beveiligen met een beheerde identiteit

[App Servicex](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie in Azure. De service bevat ook een [beheerde identiteit](overview-managed-identity.md) voor uw app. Dit is een gebruiksklare oplossing voor het beveiligen van toegang tot [Azure SQL Database](/azure/sql-database/) en andere Azure-services. Beheerde identiteiten in App Service maken uw app veiliger doordat geheimen in uw app, zoals referenties in de verbindingsreeksen, worden verwijderd. In deze zelfstudie voegt u beheerde identiteit toe aan de voorbeeldwebapp die u in een van de volgende zelfstudies hebt ingebouwd: 

- [Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Zelfstudie: Een ASP.NET Core- en SQL Database-app maken in Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Wanneer u klaar bent, maakt uw voorbeeld-app veilig verbinding met SQL Database zonder dat een gebruikersnaam en wachtwoorden zijn vereist.

> [!NOTE]
> De stappen in deze zelfstudie ondersteunen de volgende versies:
> 
> - .NET Framework 4.7.2
> - .NET Core 2.2
>

Wat je leert:

> [!div class="checklist"]
> * Beheerde identiteiten inschakelen
> * SQL Database toegang verlenen tot de beheerde identiteit
> * Entiteitskader configureren om Azure AD-verificatie te gebruiken met SQL Database
> * Verbinding maken met SQL Database vanuit Visual Studio met Azure AD-verificatie

> [!NOTE]
>Azure AD-verificatie _verschilt_ van [geïntegreerde Windows-verificatie](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) in on-premises Active Directory (AD DS). AD DS en Azure AD gebruiken totaal verschillende verificatieprotocollen. Zie [Documentatie voor Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit artikel gaat verder waar u was gebleven in [Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) of [Tutorial: maak een ASP.NET Core- en SQL Database-app in Azure App Service.](app-service-web-tutorial-dotnetcore-sqldb.md) Als u nog niet hebt, volg een van de twee tutorials eerste. U de stappen voor uw eigen .NET-app ook aanpassen met SQL Database.

Als u uw app wilt debuggen met SQL Database als back-end, controleert u of u clientverbinding vanaf uw computer hebt toegestaan. Als dit niet het adres is, voegt u het client-IP toe door de stappen te volgen bij [IP-firewallregels op serverniveau beheren met behulp van de Azure-portal.](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Toegang verlenen tot de database aan Azure AD-gebruiker

Schakel eerst Azure AD-verificatie in op SQL Database door een Azure AD-gebruiker toe te stellen als Active Directory-beheerder van de SQL Database-server. Deze gebruiker verschilt van het Microsoft-account dat u hebt gebruikt om u aan te melden voor uw Azure-abonnement. Het moet een gebruiker zijn die u hebt gemaakt, geïmporteerd, gesynchroniseerd of uitgenodigd in Azure AD. Zie [Azure AD-functies en -beperkingen in SQL Database](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)voor meer informatie over toegestane Azure AD-gebruikers.

Als uw Azure AD-tenant nog geen gebruiker heeft, maakt u er een door de stappen bij [Gebruikers toevoegen of verwijderen met Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Zoek de object-id van de [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) Azure * \<AD-gebruiker *met behulp van de gebruikersnaam en vervang>. Het resultaat wordt opgeslagen in een variabele.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Als u de lijst met alle hoofdnamen `az ad user list --query [].userPrincipalName`van gebruikers wilt weergeven in Azure AD, voert u .
>

Voeg deze Azure AD-gebruiker toe [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) als Active Directory-beheerder met de opdracht Cloud Shell. Vervang in de volgende opdracht * \<servernaam>* door de `.database.windows.net` naam van de SQL Database-server (zonder achtervoegsel).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Zie [Een Azure Active Directory-beheerder voor uw Azure SQL Database Server inrichten voor](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) meer informatie over het toevoegen van een Active Directory-beheerder.

## <a name="set-up-visual-studio"></a>Visual Studio instellen

### <a name="windows"></a>Windows
Visual Studio voor Windows is geïntegreerd met Azure AD-verificatie. Als u ontwikkeling en foutopsporing in Visual Studio wilt inschakelen, voegt u uw Azure AD-gebruiker toe in Visual Studio door > **Bestandsaccountinstellingen** in het menu te selecteren en klikt u op **Een account toevoegen**. **File**

Als u de Azure AD-gebruiker wilt instellen voor Azure-serviceverificatie, selecteert u Extra > **beheeropties** in het menu en selecteert u **vervolgens Azure Service Authentication** > Account**Selection**. **Tools** Selecteer de Azure AD-gebruiker die u hebt toegevoegd en klik op **OK**.

U bent nu klaar om uw app te ontwikkelen en te debuggen met de SQL Database als back-end, met Azure AD-verificatie.

### <a name="macos"></a>MacOS

Visual Studio voor Mac is niet geïntegreerd met Azure AD-verificatie. De [Microsoft.Azure.Services.AppAuthentication-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) die u later zult gebruiken, kan echter tokens van Azure CLI gebruiken. Om ontwikkeling en foutopsporing in Visual Studio mogelijk te maken, moet u azure CLI eerst op uw lokale machine [installeren.](https://docs.microsoft.com/cli/azure/install-azure-cli)

Zodra Azure CLI is geïnstalleerd op uw lokale machine, meldt u zich aan bij Azure CLI met de volgende opdracht met uw Azure AD-gebruiker:

```bash
az login --allow-no-subscriptions
```
U bent nu klaar om uw app te ontwikkelen en te debuggen met de SQL Database als back-end, met Azure AD-verificatie.

## <a name="modify-your-project"></a>Uw project wijzigen

Welke stappen u voor uw project volgt, is afhankelijk van de vraag of het een ASP.NET project of een ASP.NET Core-project is.

- [ASP.NET wijzigen](#modify-aspnet)
- [ASP.NET-kern wijzigen](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET wijzigen

Open Package Manager Console in Visual Studio en voeg het NuGet-pakket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) toe:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

Werk *in Web.config*vanaf de bovenkant van het bestand en breng de volgende wijzigingen aan:

- Daarin `<configSections>`de volgende sectieverklaring:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Voeg onder `</configSections>` de afsluitende tag `<SqlAuthenticationProviders>`de volgende XML-code toe voor .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Zoek de aangeroepen `MyDbConnection` verbindingstekenreeks en vervang de waarde ervan `connectionString` door `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Vervang _ \<servernaam>_ en _ \<db-name>_ door uw servernaam en databasenaam.

> [!NOTE]
> De SqlAuthenticationProvider die u zojuist hebt geregistreerd, is gebaseerd op de app-bibliotheek die u eerder hebt geïnstalleerd. Standaard wordt een door het systeem toegewezen identiteit gebruikt. Als u een door de gebruiker toegewezen identiteit wilt gebruiken, moet u een extra configuratie opgeven. Zie [ondersteuning voor verbindingstekenreeksen](../key-vault/general/service-to-service-authentication.md#connection-string-support) voor de AppAuthentication-bibliotheek.

Dat is alles wat je nodig hebt om verbinding te maken met SQL Database. Wanneer u foutopsporing doet in Visual Studio, gebruikt uw code de Azure AD-gebruiker die u hebt geconfigureerd in [Visual Studio instellen.](#set-up-visual-studio) U stelt de SQL Database-server later zo in dat de verbinding met de beheerde identiteit van uw App Service-app mogelijk is.

Typ `Ctrl+F5` om de app opnieuw uit te voeren. Dezelfde CRUD-app in uw browser maakt nu rechtstreeks verbinding met de Azure SQL-database, met Azure AD-verificatie. Met deze instelling u databasemigraties uitvoeren vanuit Visual Studio.

### <a name="modify-aspnet-core"></a>ASP.NET-kern wijzigen

Open Package Manager Console in Visual Studio en voeg het NuGet-pakket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) toe:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

In de [ASP.NET Core- en SQL Database-zelfstudie](app-service-web-tutorial-dotnetcore-sqldb.md)wordt de `MyDbConnection` verbindingstekenreeks helemaal niet gebruikt omdat de lokale ontwikkelomgeving een Sqlite-databasebestand gebruikt en de Azure-productieomgeving een verbindingstekenreeks van App Service gebruikt. Met Active Directory-verificatie wilt u dat beide omgevingen dezelfde verbindingstekenreeks gebruiken. Vervang in *appsettings.json*de `MyDbConnection` waarde van de verbindingstekenreeks door:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Verwijder *in Startup.cs*de codesectie die u eerder hebt toegevoegd:

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

En vervang het door de volgende code:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Vervolgens levert u de entiteitsframeworkdatabasecontext met het toegangstoken voor de SQL-database. Voeg in *Data\MyDatabaseContext.cs*de volgende code toe in `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` de krullende accolades van de lege constructor:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Deze demonstratiecode is synchroon voor helderheid en eenvoud.

Dat is alles wat je nodig hebt om verbinding te maken met SQL Database. Wanneer u foutopsporing doet in Visual Studio, gebruikt uw code de Azure AD-gebruiker die u hebt geconfigureerd in [Visual Studio instellen.](#set-up-visual-studio) U stelt de SQL Database-server later zo in dat de verbinding met de beheerde identiteit van uw App Service-app mogelijk is. De `AzureServiceTokenProvider` klasse slaat het token in het geheugen in de cache en haalt het vlak voor het verstrijken op uit Azure AD. Je hebt geen aangepaste code nodig om het token te vernieuwen.

> [!TIP]
> Als de Azure AD-gebruiker die u hebt `GetAccessTokenAsync("https://database.windows.net/", tenantid)` geconfigureerd, toegang heeft tot meerdere tenants, belt u met de gewenste tenant-id om het juiste toegangstoken op te halen.

Typ `Ctrl+F5` om de app opnieuw uit te voeren. Dezelfde CRUD-app in uw browser maakt nu rechtstreeks verbinding met de Azure SQL-database, met Azure AD-verificatie. Met deze instelling u databasemigraties uitvoeren vanuit Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Beheerde identiteitsconnectiviteit gebruiken

Vervolgens configureert u uw App Service-app om verbinding te maken met SQL Database met een door het systeem toegewezen beheerde identiteit.

> [!NOTE]
> Hoewel de instructies in deze sectie voor een door het systeem toegewezen identiteit zijn, kan een door de gebruiker toegewezen identiteit net zo gemakkelijk worden gebruikt. Om dit te doen. u hebt de `az webapp identity assign command` wijziging nodig om de gewenste door de gebruiker toegewezen identiteit toe te wijzen. Controleer vervolgens bij het maken van de SQL-gebruiker de naam van de door de gebruiker toegewezen identiteitsbron in plaats van de sitenaam.

### <a name="enable-managed-identity-on-app"></a>Beheerde identiteit inschakelen in de app

Als u een beheerde identiteit voor uw Azure-app wilt inschakelen, gebruikt u de opdracht [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) in de Cloud Shell. Vervang in de volgende opdracht * \<>app-naam *.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Hier is een voorbeeld van de uitvoer:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="grant-permissions-to-managed-identity"></a>Machtigingen verlenen aan beheerde identiteit

> [!NOTE]
> Als u wilt, u de identiteit toevoegen aan een [Azure AD-groep](../active-directory/fundamentals/active-directory-manage-groups.md)en vervolgens SQL Database-toegang verlenen tot de Azure AD-groep in plaats van de identiteit. Met de volgende opdrachten wordt bijvoorbeeld de beheerde identiteit van de vorige stap toegevoegd aan een nieuwe groep genaamd _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Meld u in de Cloud Shell aan bij SQL Database met behulp van de SQLCMD-opdracht. Vervang _ \<servernaam>_ door de naam van uw SQL _ \<Database-server, de naam van de DB>_ door de databasenaam die uw app gebruikt en _ \<de>_ en _ \<aad-wachtwoord_>door de referenties van uw Azure AD-gebruiker.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Voer in de SQL-prompt voor de gewenste database de volgende opdrachten uit om de Azure AD-groep toe te voegen en de machtigingen toe te kennen die uw app nodig heeft. Bijvoorbeeld: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

identiteitsnaam>is de naam van de beheerde identiteit in Azure AD. * \<* Als de identiteit is toegewezen aan het systeem, is de naam altijd hetzelfde als de naam van uw App Service-app. Als u machtigingen wilt verlenen voor een Azure AD-groep, gebruikt u in plaats daarvan de weergavenaam van de groep (bijvoorbeeld *myAzureSQLDBAccessGroup).*

Typ `EXIT` om terug te keren naar de Cloud Shell-prompt.

> [!NOTE]
> De back-endservices van beheerde identiteiten onderhouden ook [een tokencache](overview-managed-identity.md#obtain-tokens-for-azure-resources) die het token voor een doelbron alleen bijwerkt wanneer deze verloopt. Als u een fout maakt bij het configureren van uw SQL Database-machtigingen en probeert de machtigingen te wijzigen *nadat* u een token met uw app hebt geprobeerd te krijgen, krijgt u eigenlijk geen nieuw token met de bijgewerkte machtigingen totdat het token in de cache is verlopen.

### <a name="modify-connection-string"></a>De verbindingsreeks wijzigen

Vergeet niet dat dezelfde wijzigingen die u hebt aangebracht in *Web.config* of *appsettings.json* werkt met de beheerde identiteit, dus het enige wat u hoeft te doen is het verwijderen van de bestaande verbindingstekenreeks in App Service, die Visual Studio de eerste keer heeft gemaakt voor het implementeren van uw app. Gebruik de volgende opdracht, maar vervang * \<app-naam>* met de naam van uw app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Uw wijzigingen publiceren

U hoeft nu alleen nog maar uw wijzigingen naar Azure te publiceren.

**Als u afkomstig bent uit [Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database,](app-service-web-tutorial-dotnet-sqldatabase.md)** publiceert u de wijzigingen in Visual Studio. Klik in de **Solution Explorer** met de rechtermuisknop op uw project **DotNetAppSqlDb** en selecteer **Publiceren**.

![Publiceren vanuit Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klik op de publicatiepagina op **Publiceren**. 

**Als u afkomstig bent uit [Zelfstudie: Een ASP.NET Core- en SQL Database-app in Azure App Service,](app-service-web-tutorial-dotnetcore-sqldb.md)** publiceert u de wijzigingen met Git, met de volgende opdrachten:

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
> * Entiteitskader configureren om Azure AD-verificatie te gebruiken met SQL Database
> * Verbinding maken met SQL Database vanuit Visual Studio met Azure AD-verificatie

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)

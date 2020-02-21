---
title: 'Zelf studie: Linux ASP.NET Core met SQL DB'
description: Meer informatie over het verkrijgen van een gegevensgestuurde Linux ASP.NET Core-app in Azure App Service, met verbinding met een SQL Database.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9012f09f75c60ebd0b3743d030b83c0a3aa78648
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524084"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Een ASP.NET Core-en SQL Database-app bouwen in Azure App Service in Linux

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Linux. Zie _Een .NET Core- en SQL Database-app maken in Azure App Service_ als u in App Service wilt implementeren in [Windows](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

[App Service onder Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. In deze zelfstudie leert u hoe u een .NET Core-app maakt en hoe u deze verbindt met een SQL-database. Als u klaar bent, hebt u een .NET Core MVC-app die onder Linux in App Service wordt uitgevoerd.

![app die onder Linux in App Service wordt uitgevoerd](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een SQL-database in Azure maken
> * Een .NET Core-app verbinden met SQL Database
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* [Git installeren](https://git-scm.com/)
* [Installeer .NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="create-local-net-core-app"></a>Lokale .NET Core-app maken

In deze stap stelt u het lokale .NET Core-project in.

### <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

In het terminalvenster, `cd` in een werkmap.

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen en de hoofdmap ervan te wijzigen.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Het voorbeeldproject bevat een eenvoudige CRUD-app (create-read-update-delete) die gebruikmaakt van [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>De toepassing uitvoeren

Voer de volgende opdrachten uit om de vereiste pakketten te installeren, databasemigraties uit te voeren en de toepassing te starten.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Ga naar `http://localhost:5000` in een browser. Selecteer de koppeling **Nieuwe maken** en maak enkele _taakitems_.

![is verbonden met SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Als u .NET Core wilt stoppen, drukt u in de terminal op `Ctrl+C`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>SQL-productiedatabase maken

In deze stap maakt u een SQL-database in Azure. Als de app is geïmplementeerd in Azure, wordt deze cloud-database gebruikt.

Voor SQL Database wordt in deze zelfstudie gebruikgemaakt van [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Een logische SQL Database-server maken

Maak een logische Azure SQL Database-server in Cloud Shell met de opdracht [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Vervang de tijdelijke aanduiding voor de *\<server naam >* door een unieke SQL database naam. Deze naam wordt gebruikt als onderdeel van het SQL Database-eindpunt, `<server-name>.database.windows.net`. De naam moet dus uniek zijn voor alle logische servers in Azure. De naam mag alleen kleine letters, cijfers en het afbreekstreepje (-) bevatten, en moet tussen de 3 en 50 tekens lang zijn. Vervang ook *\<DB-gebruikers naam >* en *\<DB-wacht woord >* door de gebruikers naam en het wacht woord van uw keuze. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Wanneer de logische SQL Database-server wordt gemaakt, toont de Azure CLI informatie die lijkt op het volgende voorbeeld:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server-name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server-name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Een serverfirewallregel configureren

Maak een [firewallregel op Azure SQL Database-serverniveau](../../sql-database/sql-database-firewall-configure.md) met de opdracht [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Als zowel het IP-beginadres als het IP-eindadres zijn ingesteld op 0.0.0.0, wordt de firewall alleen geopend voor andere Azure-resources. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Een database maken

Maak een database met een [prestatieniveau van S0](../../sql-database/sql-database-service-tiers-dtu.md) op de server met de opdracht [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Verbindingsreeks maken

Vervang de volgende teken reeks door de *\<server naam >* , *\<db-username >* en *\<db-wachtwoord >* die u eerder hebt gebruikt.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

Dit is de verbindingsreeks voor uw app .NET Core-app. Kopieer deze voor later gebruik.

## <a name="deploy-app-to-azure"></a>App in Azure implementeren

In deze stap implementeert u de met SQL Database verbonden .NET Core-app met App Service onder Linux.

### <a name="configure-local-git-deployment"></a>Lokale Git-implementatie configureren

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-connection-string"></a>connection string configureren

Als u verbindingsreeksen voor de Azure-app wilt instellen, gebruikt u de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Vervang in de volgende opdracht de *\<app-naam >* en de para meter *\<verbindingsteken reeks >* met de Connection String die u eerder hebt gemaakt.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

In ASP.NET Core kunt u deze benoemde connection string (`MyDbConnection`) gebruiken met het standaard patroon, zoals elke connection string die is opgegeven in *appSettings. json*. In dit geval wordt `MyDbConnection` ook gedefinieerd in het bestand *appSettings. json*. Bij het uitvoeren van App Service heeft de connection string die in App Service gedefinieerd, voor rang op de connection string die is gedefinieerd in het bestand *appSettings. json*. De code gebruikt de waarde *appSettings. json* tijdens de lokale ontwikkeling en dezelfde code gebruikt de app service waarde wanneer deze wordt geïmplementeerd.

Zie [verbinding maken met SQL database in productie](#connect-to-sql-database-in-production)om te zien hoe de Connection String in uw code wordt verwezen.

### <a name="configure-environment-variable"></a>Omgevings variabele configureren

Vervolgens stelt u de instelling voor de app `ASPNETCORE_ENVIRONMENT` in op _Productie_. Met deze instelling kunt u zien of u in azure werkt, omdat u SQLite gebruikt voor uw lokale ontwikkel omgeving en SQL Database voor uw Azure-omgeving.

In het volgende voorbeeld wordt de app-instelling `ASPNETCORE_ENVIRONMENT` in de Azure-app geconfigureerd. Vervang de tijdelijke aanduiding voor de *\<app-naam >* .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Zie [verbinding maken met SQL database in productie](#connect-to-sql-database-in-production)om te zien hoe de omgevings variabele in uw code wordt verwezen.

### <a name="connect-to-sql-database-in-production"></a>Verbinding maken met SQL Database in productie

Open Startup.cs in de lokale opslagplaats en zoek de volgende code:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Vervang deze door de volgende code, waarin de omgevingsvariabelen worden gebruikt die u eerder hebt geconfigureerd.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Als deze code detecteert dat deze in productie wordt uitgevoerd (wat de Azure-omgeving aanduidt), gebruikt deze de connection string die u hebt geconfigureerd om verbinding te maken met de SQL Database. Zie [Access Environment Varia bles](configure-language-dotnetcore.md#access-environment-variables)(Engelstalig) voor meer informatie over de manier waarop app-instellingen worden gebruikt in app service.

De `Database.Migrate()`-aanroep helpt u wanneer deze wordt uitgevoerd in azure, omdat hiermee automatisch de data bases worden gemaakt die uw .NET core-app nodig heeft, op basis van de migratie configuratie.

Sla uw wijzigingen op en voer deze door naar de Git-opslagplaats.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader in de webbrowser naar de geïmplementeerde app.

```bash
http://<app-name>.azurewebsites.net
```

Voeg enkele taakitems toe.

![app die onder Linux in App Service wordt uitgevoerd](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gefeliciteerd!** U voert een gegevensgestuurde .NET Core-app uit in App Service onder Linux.

## <a name="update-locally-and-redeploy"></a>Lokaal bijwerken en opnieuw implementeren

In deze stap wijzigt u het databaseschema en publiceert u het in Azure.

### <a name="update-your-data-model"></a>Gegevensmodel bijwerken

Open _Models\Todo.cs_ in de code-editor. Voeg de volgende eigenschap toe aan de klasse `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First Migrations lokaal uitvoeren

Voer enkele opdrachten uit om de lokale database bij te werken.

```bash
dotnet ef migrations add AddProperty
```

Werk de lokale database bij:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Nieuwe eigenschap gebruiken

Breng enkele wijzigingen aan de code aan zodat de eigenschap `Done` kan worden gebruikt. Om deze zelfstudie eenvoudig te houden, wijzigt u eerst de weergaven `Index` en `Create` om de eigenschap in actie te zien.

Open _Controllers\TodosController.cs_.

Zoek de methode `Create()` en voeg `Done` toe aan de lijst met eigenschappen in het attribuut `Bind`. Als u klaar bent, ziet uw methode `Create()` er uit als de onderstaande code:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Open _Views\Todos\Create.cshtml_.

In de Razor-code zou u een `<div class="form-group">`-element voor `Description` moeten zien en vervolgens een ander element `<div class="form-group">` voor `CreatedDate`. Voeg direct na deze twee elementen een ander element `<div class="form-group">` voor `Done` toe:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Open _Views\Todos\Index.cshtml_.

Zoek het lege element `<th></th>`. Vlak boven dit element voegt u de volgende Razor-code toe:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Zoek het element `<td>` dat de taghelpers voor `asp-action` bevat. Vlak boven dit element voegt u de volgende Razor-code toe:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Meer hebt u niet nodig om de wijzigingen in de weergaven `Index` en `Create` te zien.

### <a name="test-your-changes-locally"></a>De wijzigingen lokaal testen

Voer de app lokaal uit.

```bash
dotnet run
```

Ga in de browser naar `http://localhost:5000/`. U kunt nu een taakitem toevoegen en **Gereed** aanvinken. Het moet nu als een voltooid taakitem worden weergegeven op uw startpagina. In de weergave `Edit` wordt het veld `Done` niet getoond omdat u de weergave `Edit` niet hebt gewijzigd.

### <a name="publish-changes-to-azure"></a>Wijzigingen publiceren in Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Zodra `git push` is voltooid, gaat u naar de Azure-app en probeert u de nieuwe functionaliteit uit.

![Azure-app na Code First Migration](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Alle bestaande taakitems worden nog steeds weergegeven. Wanneer u uw .NET core-app opnieuw publiceert, gaan bestaande gegevens in uw SQL Database verloren. En met Entity Framework Core Migrations wordt alleen het gegevensschema gewijzigd. De bestaande gegevens blijven ongewijzigd.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

Het voorbeeldproject volgt al de instructies in [ASP.NET Core-logboekregistratie in Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) met twee configuratiewijzigingen:

- Bevat een verwijzing naar `Microsoft.Extensions.Logging.AzureAppServices` in *DotNetCoreSqlDb.csproj*.
- Roept `loggerFactory.AddAzureWebAppDiagnostics()` aan in *Startup.cs*.

> [!NOTE]
> Het logboekniveau van het project is ingesteld op `Information` in *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Zie [Logboekregistratie in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) voor meer informatie over het aanpassen van de ASP.NET Core-logboeken.

## <a name="manage-your-azure-app"></a>Uw Azure-app beheren

Ga naar de [Azure-portal](https://portal.azure.com) om de app te zien die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![App Service-pagina in Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Een SQL-database in Azure maken
> * Een .NET Core-app verbinden met SQL Database
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken vanaf Azure naar uw terminal streamen
> * De app in Azure Portal beheren

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

---
title: 'Zelfstudie: PHP-app met MySQL'
description: Informatie over het werkend krijgen van een PHP-app in Azure, met verbinding naar een MySQL-database in Azure. Laravel wordt gebruikt in de tutorial.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 11/25/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 2dcf1bef27b5d9e1a740f136361b756f59293b00
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80046746"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure"></a>Zelfstudie: Een PHP- en MySQL-app bouwen in Azure

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Windows. Zie [Een PHP- en MySQL-app bouwen in Azure App Service op Linux ](./containers/tutorial-php-mysql-app.md) om een app te implementeren in App Service op _Linux_.
>

[Azure App Service](overview.md) biedt een zeer schaalbare, zelfpatchende webhostingservice. In deze zelfstudie wordt getoond hoe u een PHP-app in Azure maakt en hoe u deze verbinding laat maken met een MySQL-database. Wanneer u klaar bent, hebt u een [Laravel](https://laravel.com/)-app die in Azure App Service wordt uitgevoerd.

![PHP-app uitgevoerd in Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een MySQL-database in Azure maken
> * Een PHP-app verbinden met MySQL
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* [Git installeren](https://git-scm.com/)
* [Installeer PHP 5.6.4 of hoger](https://php.net/downloads.php)
* [Composer installeren](https://getcomposer.org/doc/00-intro.md)
* Schakel de volgende PHP-extensies in die Laravel nodig heeft: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [MySQL installeren en starten](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Lokale MySQL voorbereiden

In deze stap maakt u een database in uw lokale MySQL-server voor gebruik in deze zelfstudie.

### <a name="connect-to-local-mysql-server"></a>Verbinding maken met lokale MySQL-server

Maak in een terminalvenster verbinding met uw lokale MySQL-server. U kunt dit terminalvenster gebruiken om alle opdrachten in deze zelfstudie uit te voeren.

```bash
mysql -u root -p
```

Als u wordt gevraagd om een wachtwoord, voert u het wachtwoord in voor het `root`-account. Als u het wachtwoord van uw rootaccount niet meer weet, bekijkt u [MySQL: het opnieuw instellen van het hoofdwachtwoord](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Als uw opdracht succesvol is uitgevoerd, wordt uw MySQL-server uitgevoerd. Als dit niet het geval is, zorgt u ervoor dat de lokale MySQL-server is gestart door de [Stappen na installatie van MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) te volgen.

### <a name="create-a-database-locally"></a>Lokaal een database maken

Maak een database bij de prompt `mysql`.

```sql 
CREATE DATABASE sampledb;
```

Sluit uw serververbinding door `quit` te typen.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Lokaal een PHP-app maken
In deze stap krijgt u een Laravel-voorbeeld-app, configureert u de databaseverbinding en voert u deze lokaal uit. 

### <a name="clone-the-sample"></a>Het voorbeeld klonen

Voer in het terminalvenster de opdracht `cd` naar een werkmap uit.

Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` naar de gekloonde map.
Installeer de vereiste pakketten.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL-verbinding configureren

Maak een tekstbestand met de naam *.env* in de hoofdmap van de opslagplaats. Kopieer de volgende variabelen naar het bestand *.env*. Vervang _ &lt;_ de root_password>tijdelijke aanduiding door het wachtwoord van de MySQL-hoofdgebruiker.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Zie [Laravel-omgeving configureren](https://laravel.com/docs/5.4/configuration#environment-configuration) voor informatie over hoe Laravel het _.env_-bestand gebruikt.

### <a name="run-the-sample-locally"></a>Het voorbeeld lokaal uitvoeren

Voer [Laravel-databasemigraties](https://laravel.com/docs/5.4/migrations) uit om de tabellen te maken die de toepassing nodig heeft. Als u wilt zien welke tabellen worden gemaakt in de migraties, zoek dan in de map _database/migrations_ in de Git-opslagplaats.

```bash
php artisan migrate
```

Genereer een nieuwe sleutel voor de Laravel-toepassing.

```bash
php artisan key:generate
```

Voer de toepassing uit.

```bash
php artisan serve
```

Ga naar `http://localhost:8000` in een browser. Voeg een paar taken op de pagina toe.

![PHP maakt verbinding met MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Druk op `Ctrl + C` in het terminalvenster om de PHP-server te stoppen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>MySQL in Azure maken

In deze stap maakt u een MySQL-database in [Azure Database for MySQL](/azure/mysql). Later configureert u de PHP-toepassing om verbinding te maken met deze database.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Een MySQL-server maken

Maak in de Cloud Shell een server in [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) Azure Database voor MySQL met de opdracht.

Vervang in de volgende opdracht een * \<* unieke servernaam voor de mysql_server_name>tijdelijke aanduiding, een gebruikersnaam voor de * \<admin_user>* en een wachtwoord voor de * \<admin_password>* tijdelijke aanduiding. De servernaam wordt gebruikt als onderdeel van het MySQL-eindpunt (`https://<mysql_server_name>.mysql.database.azure.com`). De naam moet dus uniek zijn voor alle servers in Azure.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name> --location "West Europe" --admin-user <admin_user> --admin-password <admin_password> --sku-name B_Gen5_1
```

> [!NOTE]
> Aangezien er in deze zelfstudie verschillende referenties aan de orde komen, zijn `--admin-user` en `--admin-password` ingesteld op dummy-waarden om verwarring te voorkomen. Volg in een productieomgeving de aanbevolen beveiligingsprocedures bij het kiezen van een goede gebruikersnaam en goed wachtwoord voor uw MySQL-server in Azure.
>
>

Wanneer de MySQL-server is gemaakt, toont de Azure CLI informatie die lijkt op de informatie in het volgende voorbeeld:

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...    +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Een serverfirewall configureren

Maak in de Cloud Shell een firewallregel voor uw MySQL-server om clientverbindingen toe te staan met behulp van de [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) opdracht. Als zowel het IP-beginadres als het IP-eindadres zijn ingesteld op 0.0.0.0, wordt de firewall alleen geopend voor andere Azure-resources. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> U kunt uw firewallregel nog beperkender maken door [alleen de uitgaande IP-adressen te gebruiken die in uw app worden gebruikt](overview-inbound-outbound-ips.md#find-outbound-ips).
>

Voer in de Cloud Shell de opdracht opnieuw uit om toegang vanaf uw lokale computer toe te staan door * \<your_ip_address>* te vervangen door [uw lokale IPv4-IP-adres.](https://www.whatsmyip.org/)

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Lokaal verbinding maken met MySQL-server in productie

Maak in het lokale terminalvenster verbinding met de MySQL-server in Azure. Gebruik de waarde die _ &lt; _u eerder hebt opgegeven voor mysql_server_name>. Wanneer u wordt gevraagd om een wachtwoord, gebruikt u het wachtwoord dat u hebt opgegeven bij het maken van de database in Azure.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p<PASSWORD> --ssl-mode=REQUIRED --ssl-ca=<PATH_TO_PEM>

```

### <a name="create-a-production-database"></a>Een productiedatabase maken

Maak een database bij de prompt `mysql`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Een gebruiker met machtigingen maken

Maak een databasegebruiker met de naam _phpappuser_ en geef deze alle bevoegdheden in de `sampledb`-database. Ten behoeve van de eenvoud wordt in de zelfstudie ook hier _MySQLAzure2017_ als wachtwoord gebruikt.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Sluit uw serververbinding door `quit` te typen.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>App verbinden met Azure MySQL

In deze stap verbindt u de PHP-toepassing met de MySQL-database die u in Azure Database for MySQL hebt gemaakt.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Verbinding met de database configureren

Maak in de hoofdmap van de opslagplaats een _.env.production_-bestand en kopieer de volgende variabelen ernaartoe. Vervang de _ &lt;_ tijdelijke aanduiding mysql_server_name>in zowel *DB_HOST* als *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Sla de wijzigingen op.

> [!TIP]
> Dit bestand is voor het beveiligen van uw MySQL-verbindingsgegevens al uitgesloten van de Git-opslagplaats (Zie _.gitignore_ in de hoofdmap van de opslagplaats). Later leert u hoe u omgevingsvariabelen in App Service configureert om verbinding te maken met uw database in Azure Database for MySQL. Met omgevingsvariabelen heeft u het *.env*-bestand in App Service niet nodig.
>

### <a name="configure-ssl-certificate"></a>SSL-certificaat configureren

Azure Database for MySQL dwingt standaard SSL-verbindingen van clients af. Voor verbinding met uw MySQL-database in Azure moet u het [_.pem_-certificaat gebruiken dat is opgegeven door Azure Database for MySQL](../mysql/howto-configure-ssl.md).

Open _config/database.php_ en voeg de parameters `sslmode` en `options` toe aan `connections.mysql`, zoals wordt weergegeven in de volgende code.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

Het certificaat `BaltimoreCyberTrustRoot.crt.pem` is voor uw gemak in deze zelfstudie in de opslagplaats geleverd. 

### <a name="test-the-application-locally"></a>De toepassing lokaal testen

Voer Laravel-databasemigraties uit met _.env.production_ als het omgevingsbestand om de tabellen te maken in uw MySQL-database in Azure Database for MySQL. Vergeet niet dat _. env.production_ de verbindingsgegevens heeft voor uw MySQL-database in Azure.

```bash
php artisan migrate --env=production --force
```

_. env.production_ heeft nog geen geldige toepassingssleutel. Genereer hiervoor een nieuwe in de terminal.

```bash
php artisan key:generate --env=production --force
```

Voer de voorbeeldtoepassing uit met _. env.production_ als het omgevingsbestand.

```bash
php artisan serve --env=production
```

Navigeer naar `http://localhost:8000`. Als de pagina zonder fouten wordt geladen, maakt de PHP-toepassing verbinding met de MySQL-database in Azure.

Voeg een paar taken op de pagina toe.

![PHP maakt verbinding met Azure Database for MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Typ `Ctrl + C` in de terminal om PHP te stoppen.

### <a name="commit-your-changes"></a>Uw wijzigingen doorvoeren

Voer de volgende Git-opdrachten uit om uw wijzigingen door te voeren:

```bash
git add .
git commit -m "database.php updates"
```

Uw app kan worden geïmplementeerd.

## <a name="deploy-to-azure"></a>Implementeren in Azure

In deze stap implementeert u de met MySQL verbonden PHP-toepassing naar Azure App Service.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Database-instellingen configureren

Zoals eerder uiteengezet, kunt u verbinding met uw Azure MySQL-database maken met omgevingsvariabelen in App Service.

In de Cloud Shell stelt u omgevingsvariabelen in als _app settings_ met behulp van de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

De volgende opdracht configureert de app-instellingen `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` en `DB_PASSWORD`. Vervang de naam van de tijdelijke _ &lt;aanduidingen>_ en _ &lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

U kunt de PHP-methode [getenv](https://www.php.net/manual/en/function.getenv.php) gebruiken voor toegang tot de instellingen. De Laravel-code gebruikt een [env](https://laravel.com/docs/5.4/helpers#method-env)-wrapper boven de PHP `getenv`. Zo is de MySQL-configuratie in _config/database.php_ bijvoorbeeld vergelijkbaar met de volgende code:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel-omgevingsvariabelen configureren en instellen

Laravel heeft een toepassingssleutel nodig in App Service. U kunt deze configureren met app-instellingen.

In het lokale terminalvenster gebruikt u `php artisan` voor het genereren van een nieuwe toepassingssleutel zonder deze op te slaan in _.env_.

```bash
php artisan key:generate --show
```

Stel in de Cloud Shell de toepassingssleutel in [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) de App Service-app in met de opdracht. Vervang de tijdelijke _ &lt;aanduidingen appname>_ en _ &lt;outputofphpartisankey:genereren>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` geeft Laravel de opdracht foutopsporingsinformatie te retourneren wanneer de geïmplementeerde app fouten tegenkomt. Als een productietoepassing wordt uitgevoerd, stelt u deze in op `false`, wat een veiliger optie is.

### <a name="set-the-virtual-application-path"></a>Het pad van de virtuele toepassing instellen

Stel het pad van de virtuele toepassing voor de app in. Deze stap is vereist omdat de [levenscyclus van de Laravel-toepassing](https://laravel.com/docs/5.4/lifecycle) in de _openbare_ map begint en niet in de hoofdmap van de toepassing. Andere PHP-frameworks waarvan de levenscyclus start in de hoofdmap, kunnen functioneren zonder handmatige configuratie van het pad voor de virtuele toepassing.

Stel in de Cloud Shell het virtuele [`az resource update`](/cli/azure/resource#az-resource-update) toepassingspad in met de opdracht. Vervang de _ &lt;appnaam>_ tijdelijke aanduiding.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Azure App Service wijst standaard het hoofdpad voor virtuele toepassingen (_/_) naar de hoofdmap van de geïmplementeerde toepassingsbestanden _(sites\wwwroot)._

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> U ziet dat het implementatieproces aan het einde [Composer](https://getcomposer.org/)-pakketten installeert. In App Service worden deze automatische taken tijdens de standaardimplementatie niet uitgevoerd, dus bevat deze voorbeeldopslagplaats drie extra bestanden in de hoofdmap om deze in te schakelen:
>
> - `.deployment` - Dit bestand draagt App Service op om `bash deploy.sh` uit te voeren als het aangepaste implementatiescript.
> - `deploy.sh`: het aangepaste implementatiescript. Als u het bestand bekijkt, ziet u dat `php composer.phar install` wordt uitgevoerd na `npm install`.
> - `composer.phar` - Het Composer-pakketbeheer.
>
> U kunt deze aanpak gebruiken om een stap toe te voegen aan de op Git gebaseerde implementatie naar App Service. Zie het [Script voor aangepaste implementatie](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) voor meer informatie.
>

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar `http://<app_name>.azurewebsites.net` en voeg een paar taken toe aan de lijst.

![PHP-app uitgevoerd in Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

U voert nu een gegevensgestuurde PHP-app uit in Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Het model lokaal bijwerken en opnieuw implementeren

In deze stap maakt u een eenvoudige wijziging in het `task`-gegevensmodel en de web-app en publiceert u de update vervolgens naar Azure.

Voor het takenscenario wijzigt u de toepassing, zodat u een taak als voltooid kunt markeren.

### <a name="add-a-column"></a>Een kolom toevoegen

Ga in het lokale terminalvenster naar de hoofdmap van de Git-opslagplaats.

Genereer een nieuwe databasemigratie voor de tabel `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Deze opdracht laat u de naam zien van het migratiebestand dat wordt gegenereerd. Zoek dit bestand in _database/migrations_ en open het.

Vervang de `up`-methode door de volgende code:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

De bovenstaande code voegt een Booleaanse kolom toe aan de tabel `tasks` met de naam `complete`.

Vervang de methode `down` door de volgende code voor de terugdraaiactie:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Voer in het lokale terminalvenster Laravel-database-migraties uit om de wijziging in de lokale database te maken.

```bash
php artisan migrate
```

Op basis van de [Laravel-naamgevingsconventie](https://laravel.com/docs/5.4/eloquent#defining-models) wordt het model `Task` (zie _app/Task.php_) standaard toegewezen aan de tabel `tasks`.

### <a name="update-application-logic"></a>Toepassingslogica bijwerken

Open het bestand *routes/web.php*. De toepassing definieert hier de routes en bedrijfslogica.

Voeg aan het einde van het bestand een route toe met de volgende code:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

De bovenstaande code voert een eenvoudige update uit op het gegevensmodel door het omschakelen van de waarde van `complete`.

### <a name="update-the-view"></a>De weergave bijwerken

Open het bestand *resources/views/tasks.blade.php*. Zoek de `<tr>`-openingstag en vervang deze door:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

De bovenstaande code verandert de rijkleur, afhankelijk van of de taak voltooid is.

U hebt de volgende code in deze regel:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Vervang de gehele regel door de volgende code:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

De bovenstaande code voegt de verzendknop toe die verwijst naar de route die u eerder hebt gedefinieerd.

### <a name="test-the-changes-locally"></a>De wijzigingen lokaal testen

Voer in het lokale terminalvenster de ontwikkelaarsserver uit vanuit de hoofdmap van de Git-opslagplaats.

```bash
php artisan serve
```

Om de status van de taak te zien wijzigen, gaat u naar `http://localhost:8000` en selecteert u het selectievakje.

![Selectievakje toegevoegd aan de taak](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

Typ `Ctrl + C` in de terminal om PHP te stoppen.

### <a name="publish-changes-to-azure"></a>Wijzigingen publiceren in Azure

Voer in het lokale terminalvenster Laravel-databasemigraties uit met de productieverbindingsreeks om de wijziging te maken in de Azure-database.

```bash
php artisan migrate --env=production --force
```

Voer uw wijzigingen door in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Zodra `git push` is voltooid, gaat u naar de Azure-app en probeert u de nieuwe functionaliteit uit.

![In Azure gepubliceerde model- en databasewijzigingen](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Als u taken hebt toegevoegd, worden deze in de database bewaard. Updates van het gegevensschema laten bestaande gegevens intact.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

Terwijl uw PHP-toepassing in Azure App Service wordt uitgevoerd, kunt u de consolelogboeken doorgesluisd krijgen naar uw terminal. Op die manier krijgt u de dezelfde diagnostische berichten om toepassingsfouten op te sporen.

Als u logboekstreaming [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) wilt starten, gebruikt u de opdracht in de Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Nadat logboekstreaming is gestart, vernieuwt u de Azure-app in de browser om wat webverkeer te genereren. U kunt nu zien dat consolelogboeken worden doorgegeven aan de terminal. Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

Typ `Ctrl`+`C` om op elk gewenst moment te stoppen met logboekstreaming.

> [!TIP]
> Een PHP-toepassing kan gebruikmaken van de standaard [error_log()](https://php.net/manual/function.error-log.php) voor uitvoer naar de console. De voorbeeldtoepassing hanteert deze benadering in _app/Http/routes.php_.
>
> Als webframework [maakt Laravel gebruik van Monolog](https://laravel.com/docs/5.4/errors) als provider voor logboekregistratie. Zie [PHP: het gebruik van Monolog voor aanmelding bij console (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) om ervoor te zorgen dat Monolog berichten uitvoert naar de console.
>
>

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar [Azure Portal](https://portal.azure.com) om de app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-php-mysql/access-portal.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals stoppen, starten, opnieuw starten, bladeren en verwijderen.

Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

![App Service-pagina in Azure Portal](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een MySQL-database in Azure maken
> * Een PHP-app verbinden met MySQL
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan een web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)

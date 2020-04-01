---
title: 'Zelfstudie: Linux Ruby app met Postgres'
description: Meer informatie over het werken van een Linux Ruby-app in Azure App Service, met verbinding met een PostgreSQL-database in Azure. Rails wordt gebruikt in de tutorial.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 03/27/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 7339bb1ec2c16e9d6c83d2cbae8810b660b60765
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80045485"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Een Ruby- en een Postgres-app maken in Azure App Service op Linux

[Azure App Service on Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. In deze zelfstudie ziet u hoe u een Ruby-app maakt en deze verbindt met een PostgreSQL-database. Wanneer u klaar bent, hebt u een [Ruby on Rails](https://rubyonrails.org/)-app die op App Service op Linux wordt uitgevoerd.

![Ruby on Rails-app uitgevoerd in Azure App Service](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een PostgreSQL-database in Azure maken
> * Een Ruby on Rails-app verbinden met PostgreSQL
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* [Git installeren](https://git-scm.com/)
* [Ruby 2.3 installeren](https://www.ruby-lang.org/en/documentation/installation/)
* [Ruby on Rails 5.1 installeren](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [PostgreSQL installeren en uitvoeren](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Lokale Postgres voorbereiden

In deze stap maakt u een database op de lokale Postgres-server om te gebruiken voor deze zelfstudie.

### <a name="connect-to-local-postgres-server"></a>Verbinding maken met de lokale Postgres-server

Open het terminalvenster en voer `psql` uit om verbinding te maken met de lokale Postgres-server.

```bash
sudo -u postgres psql
```

Als de verbinding is geslaagd, wordt de Postgres-database uitgevoerd. Zo niet, dan start u de lokale Postgres-database door de stappen uit te voeren in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Typ `\q` om de Postgres-client af te sluiten. 

Maak een Postgres-gebruiker die databases kan maken door de volgende opdracht uit te voeren. Doe dit met behulp van de Linux-gebruikersnaam waarmee u bent aangemeld.

```bash
sudo -u postgres createuser -d <signed-in-user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Maak een lokale Ruby on Rails-app
In deze stap krijgt u een Ruby on Rails-voorbeeld-app, configureert u de databaseverbinding en voert u deze lokaal uit. 

### <a name="clone-the-sample"></a>Het voorbeeld klonen

Voer in het terminalvenster de opdracht `cd` naar een werkmap uit.

Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` naar de gekloonde map. Installeer de vereiste pakketten.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Het voorbeeld lokaal uitvoeren

Voer [de Rails-migraties](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) uit om de tabellen te maken die de toepassing nodig heeft. Als u wilt zien welke tabellen worden gemaakt in de migraties, zoek dan in de map _db/migreren_ in de Git-opslagplaats.

```bash
rake db:create
rake db:migrate
```

Voer de toepassing uit.

```bash
rails server
```

Ga naar `http://localhost:3000` in een browser. Voeg een paar taken op de pagina toe.

![Ruby on Rails is verbonden met Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Typ `Ctrl + C` in de terminal om de Rails-server te stoppen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Postgres maken in Azure

In deze stap maakt u een Postgres-database in [Azure Database for Postgres](/azure/postgresql/). Later configureert u de Ruby on Rails-toepassing om verbinding te maken met deze database.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Een Postgres-server maken

Maak een PostgreSQL-server met de [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) opdracht.

Voer de volgende opdracht uit in de Cloud Shell en vervang een unieke servernaam voor de * \<postgres-servernaam>* tijdelijke aanduiding. De servernaam moet uniek zijn op alle servers in Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres-server-name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Wanneer de Azure Database for PostgreSQL-server wordt gemaakt, toont de Azure CLI informatie die lijkt op het volgende voorbeeld:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres-server-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres-server-name>",
  "location": "westeurope",
  "name": "<postgres-server-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Een serverfirewall configureren

Maak in de Cloud Shell een firewallregel voor uw Postgres-server om clientverbindingen toe te staan met behulp van de [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create) opdracht. Als zowel het IP-beginadres als het IP-eindadres zijn ingesteld op 0.0.0.0, wordt de firewall alleen geopend voor andere Azure-resources. Vervang een unieke servernaam voor de * \<postgres-server-naam>* tijdelijke aanduiding.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres-server-name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> U kunt uw firewallregel nog beperkender maken door [alleen de uitgaande IP-adressen te gebruiken die in uw app worden gebruikt](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Lokaal verbinding maken met Postgres-server in productie

Maak in Cloud Shell verbinding met de Postgres-server in Azure. Gebruik de waarde die u eerder hebt opgegeven voor de _ &lt;postgres-servernaam>_ tijdelijke aanduidingen.

```bash
psql -U adminuser@<postgres-server-name> -h <postgres-server-name>.postgres.database.azure.com postgres
```

Gebruik _My5up3r$tr0ngPa$w0rd!_, dat u hebt opgegeven toen u de database-server maakte, wanneer u wordt gevraagd om een wachtwoord.

### <a name="create-a-production-database"></a>Een productiedatabase maken

Maak een database bij de prompt `postgres`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Een gebruiker met machtigingen maken

Maak een databasegebruiker met de naam _railsappuser_ en geef deze alle bevoegdheden in de `sampledb`-database.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017';
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Sluit uw serververbinding door `\q` te typen.

## <a name="connect-app-to-azure-postgres"></a>App verbinden met Azure Postgres

In deze stap verbindt u de Ruby on Rails-toepassing met de Postgres-database die u hebt gemaakt in Azure Database for PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Verbinding met de database configureren

Open in de opslagplaats _config/database.yml_. Aan de onderkant van het bestand kunt u de productievariabelen vervangen door de volgende code. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Sla de wijzigingen op.

### <a name="test-the-application-locally"></a>De toepassing lokaal testen

Stel de volgende omgevingsvariabelen in wanneer u weer in de lokale terminal bent:

```bash
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres-server-name>
export DB_PASSWORD=MyPostgresAzure2017
```

Voer Rails-databasemigraties uit met de productiewaarden die u zojuist hebt geconfigureerd, om de tabellen te maken in de Postgres-database in Azure Database for PostgreSQL.

```bash
rake db:migrate RAILS_ENV=production
```

Wanneer dit wordt uitgevoerd in de productieomgeving, heeft de Rails-toepassing vooraf gecompileerde assets nodig. Genereer de vereiste assets met de volgende opdracht:

```bash
rake assets:precompile
```

De Rails-productieomgeving gebruikt ook geheimen om de beveiliging beheren. Genereer een geheime sleutel.

```bash
rails secret
```

Sla de geheime sleutel op naar de respectieve variabelen die worden gebruikt door de productieomgeving van Rails. Voor uw gemak gebruikt u dezelfde sleutel voor beide variabelen.

```bash
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
```

Schakel de productieomgeving van Rails in om JavaScript- en CSS-bestanden te verzenden.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Voer de voorbeeldtoepassing uit in de productieomgeving.

```bash
rails server -e production
```

Navigeer naar `http://localhost:3000`. Als de pagina zonder fouten wordt geladen, maakt de Ruby on Rails-toepassing verbinding met de Postgres-database in Azure.

Voeg een paar taken op de pagina toe.

![Ruby on Rails maakt verbinding met Azure Database for PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Typ `Ctrl + C` in de terminal om de Rails-server te stoppen.

### <a name="commit-your-changes"></a>Uw wijzigingen doorvoeren

Voer de volgende Git-opdrachten uit om uw wijzigingen door te voeren:

```bash
git add .
git commit -m "database.yml updates"
```

Uw app kan worden geïmplementeerd.

## <a name="deploy-to-azure"></a>Implementeren in Azure

In deze stap implementeert u de met Postgres verbonden Rails-toepassing naar Azure App Service.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Database-instellingen configureren

In App Service stelt u omgevingsvariabelen in als _app-instellingen_ met behulp van de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in de Cloud Shell.

De volgende opdracht in de Cloud Shell configureert de app-instellingen `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, en `DB_PASSWORD`. Vervang de tijdelijke _ &lt;appname>_ en _ &lt;postgres-server-name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres-server-name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Rails-omgevingsvariabelen configureren

Genereer in de lokale terminal [een nieuw geheim](configure-language-ruby.md#set-secret_key_base-manually) voor de productieomgeving Rails in Azure.

```bash
rails secret
```

Configureer de variabelen die zijn vereist voor de productieomgeving van Rails.

Vervang in de volgende opdracht Cloud Shell de twee _ &lt;output-of-rails-secret>_ tijdelijke aanduidingen door de nieuwe geheime sleutel die u in de lokale terminal hebt gegenereerd.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` geeft de standaard Ruby-container opdracht tot het vooraf compileren van assets bij elke Git-implementatie. Zie Voor meer informatie [Precompile assets](configure-language-ruby.md#precompile-assets) en [Serve static assets](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

Voeg in het lokale terminalvenster een externe Azure-instantie toe aan uw lokale Git-opslagplaats.

```bash
git remote add azure <paste-copied-url-here>
```

Push naar de externe Azure-instantie om de Ruby on Rails-toepassing te implementeren. U wordt gevraagd naar het wachtwoord dat u eerder hebt opgegeven als onderdeel van het maken van de implementatiegebruiker.

```bash
git push azure master
```

Tijdens de implementatie communiceert Azure App Service de voortgang naar Git.

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

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar `http://<app-name>.azurewebsites.net` en voeg een paar taken toe aan de lijst.

![Ruby on Rails-app uitgevoerd in Azure App Service](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Gefeliciteerd, u voert een gegevensgestuurde Ruby on Rails-app uit in Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Het model lokaal bijwerken en opnieuw implementeren

In deze stap maakt u een eenvoudige wijziging in het `task`-gegevensmodel en de web-app en publiceert u de update vervolgens naar Azure.

Voor het takenscenario wijzigt u de toepassing, zodat u een taak als voltooid kunt markeren.

### <a name="add-a-column"></a>Een kolom toevoegen

Ga in de terminal naar de hoofdmap van de Git-opslagplaats.

Genereer een nieuwe migratie die een Booleaanse kolom met de naam `Done` toevoegt aan de tabel `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Met deze opdracht genereert u een nieuw migratiebestand in de map _db/migreren_.


Voer in de terminal Rais-databasemigraties uit om de wijziging in de lokale database door te voeren.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Toepassingslogica bijwerken

Open het bestand *app/controllers/tasks_controller.rb*. Zoek de volgende regel aan het einde van het bestand:

```rb
params.require(:task).permit(:Description)
```

Wijzig deze regel zodanig dat de nieuwe parameter `Done` inbegrepen is.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>De weergaven bijwerken

Open het bestand *app/views/tasks/_form.html.erb*, dit is het formulier Bewerken.

Zoek de regel `<%=f.error_span(:Description) %>` en voeg de volgende code direct eronder in:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Open het bestand *app/views/tasks/show.html.erb*, dit is de pagina met Weergaven van één enkel gegevensrecord. 

Zoek de regel `<dd><%= @task.Description %></dd>` en voeg de volgende code direct eronder in:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Open het bestand *app/views/tasks/index.html.erb*, dit is de Index-pagina voor alle records.

Zoek de regel `<th><%= model_class.human_attribute_name(:Description) %></th>` en voeg de volgende code direct eronder in:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Zoek in hetzelfde bestand de regel `<td><%= task.Description %></td>` en voeg de volgende code direct eronder in:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>De wijzigingen lokaal testen

Voer in de lokale terminal de Rails-server uit.

```bash
rails server
```

Om de status van de taak te zien wijzigen, gaat u naar `http://localhost:3000` om items toe te voegen of te bewerken.

![Selectievakje toegevoegd aan de taak](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Typ `Ctrl + C` in de terminal om de Rails-server te stoppen.

### <a name="publish-changes-to-azure"></a>Wijzigingen publiceren in Azure

Voer in de terminal Rails-databasemigraties voor de productie-omgeving uit, om de wijziging in de Azure-database te maken.

```bash
rake db:migrate RAILS_ENV=production
```

Voer uw wijzigingen door in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Zodra `git push` is voltooid, gaat u naar de Azure-app en probeert u de nieuwe functionaliteit uit.

![In Azure gepubliceerde model- en databasewijzigingen](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Als u taken hebt toegevoegd, worden deze in de database bewaard. Updates van het gegevensschema laten bestaande gegevens intact.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar [Azure Portal](https://portal.azure.com) om de app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/tutorial-php-mysql-app/access-portal.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals stoppen, starten, opnieuw starten, bladeren en verwijderen.

Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

![App Service-pagina in Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Postgres-database maken in Azure
> * Een Ruby on Rails-app verbinden met Postgres
> * De app implementeren in Azure
> * Het gegevensmodel bijwerken en de app opnieuw implementeren
> * Logboeken met diagnostische gegevens vanaf Azure streamen
> * De app in Azure Portal beheren

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelfstudie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

Of bekijk andere bronnen:

> [!div class="nextstepaction"]
> [Ruby-app configureren](configure-language-ruby.md)

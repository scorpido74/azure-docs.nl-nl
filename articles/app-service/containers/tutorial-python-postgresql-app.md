---
title: 'Zelfstudie: Linux Python app met Postgre'
description: Meer informatie over het werken van een Linux Python-app in Azure App Service, met verbinding met een PostgreSQL-database in Azure. Django wordt gebruikt in deze tutorial.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523925"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Zelfstudie: Een Python-webapp (Django) uitvoeren met PostgreSQL in Azure App Service

[Azure App Service](app-service-linux-intro.md) biedt een zeer schaalbare, zelfpatchende webhostingservice. In deze zelfstudie ziet u hoe u een python Django-webapp met gegevens verbinden met een Azure Database for PostgreSQL-database en de app implementeren en uitvoeren op Azure App Service.

![Python Django-webapp in Azure App-service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Database voor PostgreSQL-database maken en er een web-app mee verbinden
> * De web-app implementeren voor Azure App Service
> * Diagnostische logboeken weergeven
> * De web-app beheren in de Azure-portal

U de stappen in dit artikel volgen op macOS, Linux of Windows. De stappen zijn in de meeste gevallen vergelijkbaar, hoewel de verschillen niet gedetailleerd zijn in deze zelfstudie. De meeste van de `bash` onderstaande voorbeelden gebruiken een terminal venster op Linux. 

## <a name="prerequisites"></a>Vereisten

Voordat u begint met deze zelfstudie:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installeer [Git](https://git-scm.com/).
- [Python 3](https://www.python.org/downloads/)installeren .
- [PostgreSQL](https://www.postgresql.org/download/)installeren en uitvoeren .

## <a name="test-postgresql-installation-and-create-a-database"></a>PostgreSQL-installatie testen en een database maken

Maak eerst verbinding met uw lokale PostgreSQL-server en maak een database: 

Voer in een lokaal `psql` terminalvenster verbinding met uw lokale PostgreSQL-server als ingebouwde `postgres` gebruiker.

```bash
sudo su - postgres
psql
```
of
```PowerShell
psql -U postgres
```

Als de verbinding is geslaagd, wordt de PostgreSQL-database uitgevoerd. Zo niet, controleer dan of de lokale PostgreSQL-database wordt gestart door de instructies voor uw besturingssysteem te volgen op [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Maak een nieuwe database genaamd *pollsdb,* en het opzetten van een database gebruiker genaamd *manager* met wachtwoord *supersecretpass:*

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Typ `\q` om de PostgreSQL-client af te sluiten.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>De lokale Python-app maken en uitvoeren

Stel vervolgens de voorbeeldpython Django-web-app in en voer deze uit.

De [djangoapp](https://github.com/Azure-Samples/djangoapp) sample repository bevat de data-driven [Django](https://www.djangoproject.com/) polls app die je krijgt door het schrijven van je eerste [Django app](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) in de Django documentatie te volgen.

### <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Voer in een terminalvenster de volgende opdrachten uit om de opslagplaats voor voorbeeld-apps te klonen en de nieuwe werkmap te wijzigen:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>De virtuele Python-omgeving configureren

Maak en activeer een Virtuele Python-omgeving om uw app uit te voeren.

```bash
python3 -m venv venv
source venv/bin/activate
```
of
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

Voer `venv` *in* de omgeving env.sh of *env.ps1* uit om de omgevingsvariabelen in te stellen die *azuresite/settings.py* gebruikt voor de instellingen voor databaseverbindingen.

```bash
source ./env.sh
```
of
```PowerShell
.\env.ps1
```

Installeer de vereiste pakketten van *requirements.txt,* voer [Django-migraties](https://docs.djangoproject.com/en/2.1/topics/migrations/)uit en [maak een beheerdersgebruiker:](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>De web-app uitvoeren

Nadat u de beheerdersgebruiker hebt gemaakt, voert u de Django-server uit.

```bash
python manage.py runserver
```

Wanneer de Django-web-app volledig is geladen, wordt iets als het volgende bericht geretourneerd:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Ga naar *http:\//localhost:8000* in een browser. U ziet het bericht **Geen polls zijn beschikbaar.** 

Ga naar *\/http: /localhost:8000/admin* en meld u aan met de beheerdersgebruiker die u in de laatste stap hebt gemaakt. Selecteer **Toevoegen** naast **Vragen**en maak een enquêtevraag met een aantal keuzes.

![Python Django-app uitvoeren in App Services lokaal](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga naar *http:\//localhost:8000* opnieuw om de opiniepeilingsvraag te zien en de vraag te beantwoorden. De lokale Django-voorbeeldtoepassing schrijft en slaat gebruikersgegevens op in de lokale PostgreSQL-database.

Als u de Django-server wilt stoppen, typt u Ctrl+C in de terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

De meeste resterende stappen in dit artikel gebruiken Azure CLI-opdrachten in de Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Maken en verbinding maken met Azure Database voor PostgreSQL

In deze sectie maakt u een Azure Database voor PostgreSQL-server en -database en verbindt u uw web-app ermee. Wanneer u uw web-app implementeert naar Azure App Service, gebruikt de app deze clouddatabase. 

### <a name="create-a-resource-group"></a>Een resourcegroep maken

U een nieuwe brongroep maken voor uw Azure Database voor PostgreSQL-server of een bestaande brongroep gebruiken. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

U maakt een PostgreSQL-server met de [az-postgresserver en maakt](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) opdracht in de Cloud Shell.

> [!NOTE]
> Controleer voordat u een Azure Database voor PostgreSQL-server maakt, welke [rekengeneratie](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) beschikbaar is in uw regio. Als uw regio geen Gen4-hardware ondersteunt, wijzigt u de *naam -sku-in* de volgende opdrachtregel in een waarde die in uw regio wordt ondersteund, zoals Gen5. 

Vervang in de volgende opdracht * \<de>van postgresql-namen* door een unieke servernaam. De servernaam maakt deel uit van uw *PostgreSQL-eindpunt https://\<postgresql-naam>.postgres.database.azure.com,* dus de naam moet uniek zijn voor alle servers in Azure. 

Vervang * \<de naam resourcegroep>* en * \<regio>* door de naam en het gebied van de resourcegroep die u wilt gebruiken. Maak * \<* gebruikersreferenties voor het account voor beheerdersgebruikers>en * \<beheerders>-wachtwoord. * Denk * \<* aan de>en * \<het beheerderswachtwoord>* om later te gebruiken voor het aanmelden bij de PostgreSQL-server en databases.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Wanneer de Azure Database voor PostgreSQL-server wordt gemaakt, retourneert de Azure CLI JSON-code als het volgende voorbeeld:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Firewallregels maken voor de Azure Database voor PostgreSQL-server

Voer de [opdracht firewall-regel voor de firewall van AZ-postgres uit](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) om toegang te krijgen tot de database vanuit Azure-bronnen. Vervang de * \<postgresql-naam>* en * \<resourcegroup-naam>* tijdelijke aanduidingen door uw waarden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Met de vorige instelling kunnen netwerkverbindingen vanaf alle IP-adressen binnen het Azure-netwerk worden verbroken. Probeer voor productiegebruik de meest beperkende firewallregels te configureren die mogelijk zijn door [alleen de uitgaande IP-adressen toe](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)te staan die uw app gebruikt.

Voer `firewall-rule create` de opdracht opnieuw uit om toegang vanaf uw lokale computer toe te staan. Vervang * \<uw ip-adres>* door uw lokale [IPv4-IP-adres.](https://www.whatsmyip.org/) Vervang de * \<postgresql-naam>* en * \<resourcegroup-naam>* tijdelijke aanduidingen door uw eigen waarden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Maken en verbinding maken met de Azure Database for PostgreSQL-database

Maak verbinding met uw Azure Database voor PostgreSQL-server door de volgende opdracht uit te voeren. Gebruik je eigen * \<postgresql-naam>* en * \<admin-gebruikersnaam>* en meld je aan met het wachtwoord dat je hebt gemaakt.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Net zoals u deed in uw lokale PostgreSQL-server, maakt u een database en gebruiker in de Azure Database voor PostgreSQL-server:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Het is een aanbevolen manier om databasegebruikers met beperkte machtigingen voor specifieke apps te maken, in plaats van de beheerdersgebruiker te gebruiken. De `manager` gebruiker heeft volledige `pollsdb` bevoegdheden voor *alleen* de database.

Typ `\q` om de PostgreSQL-client af te sluiten.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>App-connectiviteit met de Azure PostgreSQL-database testen

Bewerk uw lokale *env.sh* of *env.ps1-bestand* om naar uw Cloud PostgreSQL-database te wijzen, door * \<postgresql-naam>* te vervangen door uw Azure Database voor PostgreSQL-servernaam.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
of
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Voer `venv` in de omgeving in uw lokale terminalvenster de bewerkte *env.sh* of *env.ps1*uit. 
```bash
source ./env.sh
```
of
```PowerShell
.\env.ps1
```

Voer Django-migratie uit naar de Azure-database en maak een beheerdersgebruiker.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Zodra de gebruiker met beheerdersrechten is gemaakt, voert u de Django-server uit.

```bash
python manage.py runserver
```

Ga in een browser naar *http:\//localhost:8000*en je ziet het bericht Geen polls zijn weer **beschikbaar.** 

Ga naar *\/http: /localhost:8000/admin,* meld u aan met de beheerdergebruiker die u hebt gemaakt en maak een pollvraag zoals voorheen.

![Python Django-app uitvoeren in App Services lokaal](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga naar *http:\//localhost:8000* opnieuw en zie de pollvraag weergegeven. Uw app schrijft nu gegevens naar de Azure Database for PostgreSQL-database.

Als u de Django-server wilt stoppen, typt u Ctrl+C in de terminal.

## <a name="deploy-the-web-app-to-azure-app-service"></a>De web-app implementeren voor Azure App Service

In deze stap implementeert u de Azure Database voor De Python-app met PostgreSQL-database verbonden naar Azure App Service.

### <a name="configure-repository"></a>Opslagplaats configureren

Omdat deze zelfstudie een Django-voorbeeld gebruikt, moet u een aantal instellingen wijzigen en toevoegen in uw *djangoapp/azuresite/settings.py-bestand* om te werken met Azure App Service. 

1. In Django wordt de `HTTP_HOST`-header gevalideerd in binnenkomende aanvragen. Als uw Django-webapp in appservice werkt, moet u de volledig gekwalificeerde domeinnaam van de app toevoegen aan de toegestane hosts. 
   
   *Azuresite/settings.py* bewerken om `ALLOWED_HOSTS` de regel als volgt te wijzigen:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django ondersteunt geen [weergave van statische bestanden in productie.](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/) Voor deze zelfstudie gebruikt u [WhiteNoise](https://whitenoise.evans.io/en/stable/) om het serveren van de bestanden in te schakelen. Het WhiteNoise pakket was al geïnstalleerd met *requirements.txt*. 
   
   Als u Django wilt configureren om WhiteNoise te gebruiken, vindt `whitenoise.middleware.WhiteNoiseMiddleware` u in *azuresite/settings.py*de `MIDDLEWARE` instelling en voegt u vlak na de regel toe aan de `django.middleware.security.SecurityMiddleware` lijst. De `MIDDLEWARE`-instelling ziet er ongeveer als volgt uit:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Voeg aan het einde van *azuresite/settings.py*de volgende regels toe:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Zie de [WhiteNoise-documentatie](https://whitenoise.evans.io/en/stable/)voor meer informatie over het configureren van WhiteNoise.

> [!IMPORTANT]
> In de sectie met database-instellingen worden de aanbevolen beveiligingsprocedures gevolgd voor het gebruik van omgevingsvariabelen. Zie [Django Documentation: implementatiechecklist](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)voor volledige implementatieaanbevelingen.

Verbind je wijzigingen in je fork van de *djangoapp* repository:

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Eerder in de zelfstudie hebt u omgevingsvariabelen gedefinieerd om verbinding te kunnen maken met de PostgreSQL-database.

In Azure App Service stelt u omgevingsvariabelen in als *app-instellingen*, met behulp van de opdracht [az webapp config-appsettings.](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)

Voer in de Azure Cloud Shell de volgende opdracht uit om de databaseverbindingsgegevens op te geven als app-instellingen. Vervang * \<app-naam>, * * \<>van de brongroep *en * \<>* met uw eigen waarden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Zie [Toegankelijkheidsomgevingsvariabelen](how-to-configure-python.md#access-environment-variables)voor informatie over hoe uw code toegang heeft tot deze app-instellingen.

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

*requirements.txt* in de hoofdmap van de opslagplaats is zichtbaar voor de App Service-implementatieserver. Vervolgens wordt Python-pakketbeheer automatisch uitgevoerd na `git push`.

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar de geïmplementeerde app met URL *\//\<http: app-name>.azurewebsites.net*. Het duurt enige tijd om te starten, omdat de container moet worden gedownload en uitgevoerd wanneer de app voor de eerste keer wordt aangevraagd. Als er een time-out optreedt op de pagina of als er een foutbericht wordt weergegeven, wacht u een paar minuten en vernieuwt u de pagina.

U ziet de pollvragen die u eerder hebt gemaakt. 

App Service detecteert een Django-project in uw repository door te `manage.py startproject` zoeken naar een *wsgi.py-bestand* in elke submap, dat standaard wordt gemaakt. Wanneer App Service het bestand vindt, wordt de Django-web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](how-to-configure-python.md) voor meer informatie over het laden van Python-apps in App Service.

Ga naar *\//\<http: app-name>.azurewebsites.net/admin* en meld u aan met de beheergebruiker die u hebt gemaakt. Maak desgevraagd nog meer pollvragen.

![Python Django-app uitvoeren in App Services in Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Gefeliciteerd!** U voert een Python-web-app (Django) uit in Azure App Service voor Linux.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Uw app beheren in de Azure-portal

Zoek in de [Azure-portal](https://portal.azure.com)naar de app die u hebt gemaakt.

![Navigeren naar uw Python Django-app in de Azure-portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier u ook basisbeheertaken uitvoeren, zoals bladeren, stoppen, opnieuw starten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![Uw Python Django-app beheren op de pagina Overzicht in de Azure-portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie voor meer informatie over het toewijzen van een aangepaste DNS-naam aan uw app:

> [!div class="nextstepaction"]
> [Zelfstudie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

Of bekijk andere bronnen:

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

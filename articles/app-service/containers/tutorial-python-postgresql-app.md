---
title: 'Zelf studie: Linux python-app met Postgre'
description: Meer informatie over hoe u een Linux python-app kunt ophalen in Azure App Service, met verbinding met een PostgreSQL-data base in Azure. Django wordt in deze zelf studie gebruikt.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523925"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Zelf studie: een Python-web-app (Django) uitvoeren met PostgreSQL in Azure App Service

[Azure App Service](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze zelf studie ziet u hoe u een gegevensgestuurde python django-Web-App verbindt met een Azure Database for PostgreSQL-data base en hoe u de app op Azure App Service implementeert en uitvoert.

![Python django-web-app in Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Database for PostgreSQL-data base maken en er een web-app aan koppelen
> * De web-app implementeren op Azure App Service
> * Diagnostische logboeken weergeven
> * De web-app in de Azure Portal beheren

U kunt de stappen in dit artikel volgen op macOS, Linux of Windows. De stappen zijn in de meeste gevallen vergelijkbaar, maar verschillen worden niet in deze zelf studie beschreven. In de meeste van de onderstaande voor beelden wordt een `bash`-Terminal venster in Linux gebruikt. 

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installeer [Git](https://git-scm.com/).
- Installeer [python 3](https://www.python.org/downloads/).
- Installeer en voer [postgresql](https://www.postgresql.org/download/)uit.

## <a name="test-postgresql-installation-and-create-a-database"></a>PostgreSQL-installatie testen en een Data Base maken

Eerst maakt u verbinding met de lokale PostgreSQL-server en maakt u een Data Base: 

Voer in een lokaal Terminal venster `psql` uit om verbinding te maken met de lokale PostgreSQL-server als de ingebouwde `postgres` gebruiker.

```bash
sudo su - postgres
psql
```
of
```PowerShell
psql -U postgres
```

Als de verbinding is geslaagd, wordt de PostgreSQL-database uitgevoerd. Zo niet, controleer dan of de lokale PostgreSQL-database wordt gestart door de instructies voor uw besturingssysteem te volgen op [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Maak een nieuwe Data Base met de naam *pollsdb*en stel een database gebruiker met de naam *Manager* in met wacht woord *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Typ `\q` om de PostgreSQL-client af te sluiten.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>De lokale python-app maken en uitvoeren

Vervolgens stelt u de voor beeld-web-app python django in en voert u deze uit.

De [djangoapp](https://github.com/Azure-Samples/djangoapp) -voorbeeld opslagplaats bevat de gegevensgestuurde [Django](https://www.djangoproject.com/) -polls-app die u krijgt door [uw eerste Django-app te schrijven](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) in de Django-documentatie.

### <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Voer in een Terminal venster de volgende opdrachten uit om de opslag plaats van de voor beeld-app te klonen en ga naar de nieuwe werkmap:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>De virtuele python-omgeving configureren

Maak en activeer een virtuele python-omgeving om uw app uit te voeren.

```bash
python3 -m venv venv
source venv/bin/activate
```
of
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

Voer in de `venv` omgeving *env.sh* of *env. ps1* in om de omgevings variabelen in te stellen die *azuresite/Settings. py* gebruiken voor de verbindings instellingen van de data base.

```bash
source ./env.sh
```
of
```PowerShell
.\env.ps1
```

Installeer de vereiste pakketten vanuit *Requirements. txt*, Voer [Django-migraties](https://docs.djangoproject.com/en/2.1/topics/migrations/)uit en [Maak een gebruiker met beheerders](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)rechten:

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>De web-app uitvoeren

Nadat u de gebruiker met beheerders rechten hebt gemaakt, voert u de Django-server uit.

```bash
python manage.py runserver
```

Wanneer de Django-web-app volledig is geladen, wordt het volgende bericht weer gegeven:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Ga naar *http:\//localhost: 8000* in een browser. Er wordt een bericht weer gegeven dat **er geen polls beschikbaar zijn**. 

Ga naar *http:\//localhost: 8000/admin* en meld u aan met de beheerders gebruiker die u in de laatste stap hebt gemaakt. Selecteer **toevoegen** naast **vragen**en maak een poll-vraag met enkele keuzes.

![Python django-app in App Services lokaal uitvoeren](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga opnieuw naar *http:\//localhost: 8000* om de vraag van de vragen te zien en de vraag te beantwoorden. De lokale Django-voorbeeld toepassing schrijft en slaat gebruikers gegevens op in de lokale PostgreSQL-data base.

Als u de Django-server wilt stoppen, typt u CTRL + C in de Terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

De meeste resterende stappen in dit artikel gebruiken Azure CLI-opdrachten in de Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Azure Database for PostgreSQL maken en verbinden

In deze sectie maakt u een Azure Database for PostgreSQL-server en-data base en verbindt u uw web-app. Wanneer u uw web-app op Azure App Service implementeert, gebruikt de app deze Cloud database. 

### <a name="create-a-resource-group"></a>Een resourcegroep maken

U kunt een nieuwe resource groep maken voor uw Azure Database for PostgreSQL-server of een bestaande resource groep gebruiken. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

U maakt een PostgreSQL-server met de opdracht [AZ post gres server Create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) in de Cloud shell.

> [!NOTE]
> Controleer voordat u een Azure Database for PostgreSQL server maakt of [Compute Generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) beschikbaar is in uw regio. Als uw regio geen Gen4-hardware ondersteunt, wijzigt u *--SKU-naam* in de volgende opdracht regel in een waarde die wordt ondersteund in uw regio, zoals GEN5. 

Vervang in de volgende opdracht *\<postgresql-name >* door een unieke server naam. De server naam maakt deel uit van uw PostgreSQL *-eind punt https://\<postgresql-name >. post gres. data base. Azure. com*, dus de naam moet uniek zijn voor alle servers in Azure. 

Vervang *\<ResourceGroup-naam >* en *\<regio >* door de naam en de regio van de resource groep die u wilt gebruiken. Voor *\<beheerder-gebruikers naam >* en *\<beheerders wachtwoord >* , maakt u gebruikers referenties voor het beheerders account van de data base. Onthoud de *\<beheerder-gebruikers naam >* en *\<beheerders wachtwoord >* om later te gebruiken voor het aanmelden bij de postgresql-server en-data bases.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Wanneer de Azure Database for PostgreSQL-server wordt gemaakt, retourneert de Azure CLI JSON-code zoals in het volgende voor beeld:

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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Firewall regels voor de Azure Database for PostgreSQL-server maken

Voer de opdracht [AZ post gres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) uit om toegang tot de data base vanuit Azure-resources toe te staan. Vervang de *\<postgresql-naam >* en *\<resourcegroup-naam >* tijdelijke aanduidingen door uw waarden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Met de voor gaande instelling worden netwerk verbindingen van alle IP-adressen binnen het Azure-netwerk toegestaan. Voor productie gebruik kunt u de meest beperkende firewall regels configureren door [alleen de uitgaande IP-adressen toe te staan die uw app gebruikt](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Voer de `firewall-rule create` opdracht opnieuw uit om toegang vanaf uw lokale computer toe te staan. Vervang *\<uw IP-adres >* door [uw lokale IPv4 IP-adres](https://www.whatsmyip.org/). Vervang de *\<postgresql-naam >* en *\<resourcegroup-naam >* tijdelijke aanduidingen door uw eigen waarden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Maken en verbinding maken met de Azure Database for PostgreSQL-data base

Maak verbinding met uw Azure Database for PostgreSQL-server door de volgende opdracht uit te voeren. Gebruik uw eigen *\<postgresql >* en *\<beheerder-gebruikers naam >* en meld u aan met het wacht woord dat u hebt gemaakt.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Net zoals u in de lokale PostgreSQL-server hebt gedaan, maakt u een Data Base en gebruiker op de Azure Database for PostgreSQL-server:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Het is een best practice voor het maken van database gebruikers met beperkte machtigingen voor specifieke apps, in plaats van de gebruiker met beheerders rechten. De `manager` gebruiker heeft volledige bevoegdheden voor *alleen* de `pollsdb`-data base.

Typ `\q` om de PostgreSQL-client af te sluiten.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>App-verbinding met de Azure PostgreSQL-data base testen

Bewerk het bestand Local *env.sh* of *env. ps1* om naar uw Cloud postgresql-data base te verwijzen door *\<postgresql name >* te vervangen door de naam van uw Azure database for postgresql-server.

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

Voer in de omgeving `venv` in uw lokale terminal venster de bewerking *env.sh* of *env. ps1*uit. 
```bash
source ./env.sh
```
of
```PowerShell
.\env.ps1
```

Voer Django-migratie uit naar de Azure-data base en maak een gebruiker met beheerders rechten.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Zodra de gebruiker met beheerdersrechten is gemaakt, voert u de Django-server uit.

```bash
python manage.py runserver
```

Ga in een browser naar *http:\//localhost: 8000*en u ziet het bericht dat **er geen polls meer beschikbaar zijn** . 

Ga naar *http:\//localhost: 8000/admin*, Meld u aan met de door de gebruiker gemaakte beheerder en maak een poll vraag zoals eerder.

![Python django-app in App Services lokaal uitvoeren](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga opnieuw naar *http:\//localhost: 8000* en zie de poll-vraag die wordt weer gegeven. Uw app is nu bezig met het schrijven van gegevens naar de Azure Database for PostgreSQL-data base.

Als u de Django-server wilt stoppen, typt u CTRL + C in de Terminal.

## <a name="deploy-the-web-app-to-azure-app-service"></a>De web-app implementeren op Azure App Service

In deze stap implementeert u de met Azure Database for PostgreSQL data base gekoppelde python-app op Azure App Service.

### <a name="configure-repository"></a>Opslagplaats configureren

Omdat in deze zelf studie een Django-voor beeld wordt gebruikt, moet u enkele instellingen in het bestand *djangoapp/azuresite/Settings. py* wijzigen en toevoegen om met Azure app service te werken. 

1. In Django wordt de `HTTP_HOST`-header gevalideerd in binnenkomende aanvragen. Als u wilt dat uw Django-web-app werkt in App Service, moet u de Fully Qualified Domain Name van de app toevoegen aan de toegestane hosts. 
   
   Bewerk *azuresite/Settings. py* om de `ALLOWED_HOSTS` regel als volgt te wijzigen:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django biedt geen ondersteuning voor het leveren [van statische bestanden in de productie](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). Voor deze zelf studie gebruikt u [WhiteNoise](https://whitenoise.evans.io/en/stable/) om de bestanden in te scha kelen. Het WhiteNoise-pakket is al geïnstalleerd met de *vereisten. txt*. 
   
   Als u Django wilt configureren voor het gebruik van WhiteNoise, gaat u in *azuresite/Settings. py*naar de instelling `MIDDLEWARE` en voegt u `whitenoise.middleware.WhiteNoiseMiddleware` toe aan de lijst, net na de `django.middleware.security.SecurityMiddleware` regel. De `MIDDLEWARE`-instelling ziet er ongeveer als volgt uit:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Voeg aan het einde van *azuresite/Settings. py*de volgende regels toe:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Zie de [WhiteNoise-documentatie](https://whitenoise.evans.io/en/stable/)voor meer informatie over het configureren van WhiteNoise.

> [!IMPORTANT]
> In de sectie met database-instellingen worden de aanbevolen beveiligingsprocedures gevolgd voor het gebruik van omgevingsvariabelen. Zie [Django-documentatie: implementatie controlelijst](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)voor volledige aanbevelingen voor de implementatie.

Voer uw wijzigingen door in uw Fork van de *djangoapp* -opslag plaats:

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

In Azure App Service stelt u omgevings variabelen in als *app-instellingen*met behulp van de opdracht [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) .

Voer in de Azure Cloud Shell de volgende opdracht uit om de details van de database verbinding op te geven als app-instellingen. Vervang\<naam van de *app >* , *\<resourcegroup-name >* en *\<postgresql-name >* met uw eigen waarden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Zie [Access Environment Varia bles](how-to-configure-python.md#access-environment-variables)(Engelstalig) voor meer informatie over de manier waarop uw code toegang heeft tot deze app-instellingen.

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

Blader naar de geïmplementeerde app met URL *http:\//\<app-name >. azurewebsites. net*. Het duurt enige tijd om te starten omdat de container moet worden gedownload en uitgevoerd wanneer de app voor de eerste keer wordt aangevraagd. Als er een time-out optreedt op de pagina of als er een foutbericht wordt weergegeven, wacht u een paar minuten en vernieuwt u de pagina.

U moet de poll vragen zien die u eerder hebt gemaakt. 

App Service detecteert een Django-project in uw opslag plaats door te zoeken naar een *wsgi.py* -bestand in elke submap, die `manage.py startproject` standaard wordt gemaakt. Wanneer App Service het bestand vindt, wordt de Django-web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](how-to-configure-python.md) voor meer informatie over het laden van Python-apps in App Service.

Ga naar *http:\//\<app-name >. azurewebsites. net/admin* en meld u aan met de door u gemaakte beheer gebruiker. Als u wilt, kunt u nog wat vragen stellen.

![Python django-app uitvoeren in App Services in azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Gefeliciteerd!** U voert een Python-web-app (Django) uit in Azure App Service voor Linux.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Uw app beheren in de Azure Portal

Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer de app die u hebt gemaakt.

![Navigeer naar uw python django-app in de Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheer taken uitvoeren, zoals bladeren, stoppen, opnieuw starten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![Beheer uw python django-app op de pagina overzicht in de Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelf studie voor meer informatie over het toewijzen van een aangepaste DNS-naam aan uw app:

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

Of Bekijk andere bronnen:

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

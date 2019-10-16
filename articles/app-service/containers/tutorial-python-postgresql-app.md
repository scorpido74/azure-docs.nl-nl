---
title: 'Zelf studie: python (Django) Web-app met PostgreSQL in Linux-Azure App Service'
description: Meer informatie over het uitvoeren van een gegevensgestuurde python-web-app (Django) in azure, met verbinding met een PostgreSQL-data base.
services: app-service\web
documentationcenter: python
author: cephalin
manager: gwallace
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: c816d2ee76002f60963415b1027579eb6db94089
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329982"
---
# <a name="build-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Een Python-web-app (Django) bouwen met PostgreSQL in Azure App Service

[App Service on Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. Deze zelf studie laat zien hoe u een gegevensgestuurde python-web-app (Django) kunt maken met behulp van PostgreSQL als de back-end van de data base. Wanneer u klaar bent, hebt u een Django-webtoepassing die wordt uitgevoerd in Azure App Service op Linux.

![Python django-web-app in App Service in Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een PostgreSQL-database maken in Azure
> * Een Python-web-app verbinden met PostgreSQL
> * De Python-web-app implementeren in azure
> * Diagnostische logboeken weergeven
> * De python-Web-App beheren in de Azure Portal

> [!NOTE]
> Kijk [hier](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) voordat u een Azure Database for PostgreSQL gaat maken om te zien welke compute-generatie beschikbaar is in uw regio.

U kunt de stappen in dit artikel voor macOS-, Linux-en Windows-instructies volgen in de meeste gevallen, maar de verschillen worden niet in deze zelf studie beschreven.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

1. [Git installeren](https://git-scm.com/)
2. [Python installeren](https://www.python.org/downloads/)
3. [PostgreSQL installeren en uitvoeren](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Lokale installatie van PostgreSQL testen en een database maken

Voer in het lokale terminalvenster `psql` uit om verbinding te maken met de lokale PostgreSQL-server.

```bash
sudo -u postgres psql postgres
```

Als er een foutbericht weergegeven dat vergelijkbaar is met `unknown user: postgres`, is het mogelijk dat uw PostgreSQL-installatie is geconfigureerd met uw aangemelde gebruikersnaam. Probeer in dat geval de volgende opdracht.

```bash
psql postgres
```

Als de verbinding is geslaagd, wordt de PostgreSQL-database uitgevoerd. Zo niet, controleer dan of de lokale PostgreSQL-database wordt gestart door de instructies voor uw besturingssysteem te volgen op [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Maak een database met de naam *pollsdb* en stel een afzonderlijke databasegebruiker in met de naam *manager* en het wachtwoord *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Typ `\q` om de PostgreSQL-client af te sluiten.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Lokale Python-app maken

In deze stap stelt u het lokale Python Django-project in.

### <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Open het terminalvenster en `CD` naar een werkmap.

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Deze voorbeeldopslagplaats bevat een [Django](https://www.djangoproject.com/)-toepassing. Het is dezelfde gegevensgestuurde app die u krijgt door de [zelfstudie Aan de slag in de Django-documentatie](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) te volgen. In deze zelf studie leert u niet Django, maar laat u zien hoe u een Django-web-app (of een andere gegevensgestuurde python-app) kunt implementeren en uitvoeren om Azure App Service.

### <a name="configure-environment"></a>Omgeving configureren

Maak een virtuele Python-omgeving en gebruik een script om de instellingen voor de databaseverbinding in te stellen.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

De omgevingsvariabelen die worden gedefinieerd in *env.sh* en *env.ps1* worden in _azuresite/settings.py_ gebruikt om de database-instellingen te definiëren.

### <a name="run-app-locally"></a>De app lokaal uitvoeren

Installeer de vereiste pakketten, [voer Django-migraties uit](https://docs.djangoproject.com/en/2.1/topics/migrations/) en [maak een gebruiker met beheerdersrechten](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Zodra de gebruiker met beheerdersrechten is gemaakt, voert u de Django-server uit.

```bash
python manage.py runserver
```

Wanneer de Django-web-app volledig is geladen, ziet u iets dat lijkt op het volgende bericht:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Ga naar `http://localhost:8000` in een browser. Als het goed is, ziet u nu het bericht `No polls are available.`. 

Ga naar `http://localhost:8000/admin` en meld u aan met de beheerders gebruiker die u in de laatste stap hebt gemaakt. Selecteer **toevoegen** naast **vragen** en maak een poll-vraag met enkele keuzes.

![Lokaal uitgevoerde Python Django-toepassing](./media/tutorial-python-postgresql-app/django-admin-local.png)

Ga opnieuw naar `http://localhost:8000` en zie de poll-vraag die wordt weer gegeven.

Met de Django-voorbeeldtoepassing worden gebruikersgegevens opgeslagen in de database. Als het gelukt is om een poll-vraag toe te voegen, worden met de app gegevens naar de lokale PostgreSQL-database geschreven.

U kunt op elk gewenst moment in het terminalvenster op Ctrl+C drukken om de Django-server te stoppen.

## <a name="create-a-production-postgresql-database"></a>Productie-PostgreSQL-database maken

In deze stap maakt u een PostgreSQL-database in Azure. Als de app is geïmplementeerd in Azure, wordt deze cloud-database gebruikt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Maak een PostgreSQL-server met de opdracht [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) in Cloud Shell.

Vervang in de volgende voorbeeld opdracht *\<postgresql-name >* met een unieke server naam en vervang *\<admin-username >* en *\<admin-Password >* met de gewenste gebruikers referenties. De gebruikersreferenties zijn voor het gebruikersaccount van de databasebeheerder. De servernaam wordt gebruikt als onderdeel van het PostgreSQL-eindpunt (`https://<postgresql-name>.postgres.database.azure.com`). De naam moet dus uniek zijn voor alle servers in Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Wanneer de Azure Database for PostgreSQL-server wordt gemaakt, toont de Azure CLI informatie die lijkt op het volgende voorbeeld:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Onthoud \<admin-username > en \<admin-Password > voor later. U hebt deze later nodig als u zich gaat aanmelden bij de Postgre-server en de bijbehorende databases.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Firewallregels maken voor de PostgreSQL-server

Voer in Cloud Shell de volgende Azure CLI-opdracht uit om toegang te verlenen tot de database vanaf Azure-resources.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Met deze instelling worden netwerkverbindingen toegestaan vanaf alle IP-adressen binnen het Azure-netwerk. Voor gebruik in productieomgevingen moet u proberen om de meest beperkende firewallregels te configureren door [alleen de uitgaande IP-adressen te gebruiken die uw app gebruikt](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Voer in de Cloud Shell de opdracht opnieuw uit om toegang vanaf uw lokale computer toe te staan door *\<your-IP-adres >* te vervangen door [uw lokale IPv4-IP-adres](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>De Python-app verbinden met de productiedatabase

In deze stap verbindt u uw Django-web-app met de Azure Database for PostgreSQL-server die u hebt gemaakt.

### <a name="create-empty-database-and-user-access"></a>Lege database maken en gebruikerstoegang

Maak in Cloud Shell verbinding met de database door de onderstaande opdracht uit te voeren. Wanneer u wordt gevraagd om uw beheerderswachtwoord, gebruikt u hetzelfde wachtwoord dat u hebt opgegeven in [Een Azure Database for PostgreSQL-server maken](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Net zoals bij uw lokale Postgres-server, maakt u de database en de gebruiker op de Azure-Postgres-server.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Typ `\q` om de PostgreSQL-client af te sluiten.

> [!NOTE]
> Het wordt aanbevolen om databasegebruikers te maken met beperkte machtigingen voor specifieke toepassingen, in plaats van de gebruiker met beheerdersrechten te gebruiken. In dit voorbeeld heeft de gebruiker `manager` volledige rechten voor _alleen_ de database `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>De app-verbinding met de productiedatabase testen

Wijzig in het lokale terminalvenster de omgevingsvariabelen voor de database (die u eerder hebt geconfigureerd door *env.sh* of  *env.ps1* uit te voeren):

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Voer Django-migratie uit in de Azure-database en maak een gebruiker met beheerdersrechten.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Zodra de gebruiker met beheerdersrechten is gemaakt, voert u de Django-server uit.

```bash
python manage.py runserver
```

Ga opnieuw naar `http://localhost:8000`. Als het goed is, ziet u het bericht `No polls are available.` opnieuw. 

Ga naar `http://localhost:8000/admin` en meld u aan met de door de gebruiker gemaakte beheerder en maak een poll-vraag zoals voorheen.

![Lokaal uitgevoerde Python Django-toepassing](./media/tutorial-python-postgresql-app/django-admin-local.png)

Ga opnieuw naar `http://localhost:8000` en zie de poll-vraag die wordt weer gegeven. Er worden nu via de app gegevens naar de database in Azure geschreven.

## <a name="deploy-to-azure"></a>Implementatie in Azure

In deze step implementeert u de met Postgres verbonden Python-toepassing naar Azure App Service.

### <a name="configure-repository"></a>Opslagplaats configureren

In Django wordt de `HTTP_HOST`-header gevalideerd in binnenkomende aanvragen. Als u wilt dat uw Django-web-app werkt in App Service, moet u de volledig gekwalificeerde domein naam van de app toevoegen aan de toegestane hosts. Open _azuresite/settings.py_ en ga naar de `ALLOWED_HOSTS`-instelling. Wijzig de regel in:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Django biedt geen ondersteuning voor [het gebruiken van statische bestanden in een productieomgeving](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). U moet dit dus handmatig inschakelen. Voor deze zelfstudie gebruikt u [WhiteNoise](https://whitenoise.evans.io/en/stable/). Het WhiteNoise-pakket is al opgenomen in _requirements.txt_. U hoeft alleen maar Django te configureren om dit pakket te gebruiken. 

Ga in _azuresite/settings.py_ naar de `MIDDLEWARE`-instelling en voeg de middleware `whitenoise.middleware.WhiteNoiseMiddleware` toe aan de lijst, net onder de middleware `django.middleware.security.SecurityMiddleware`. De `MIDDLEWARE`-instelling ziet er ongeveer als volgt uit:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Voeg aan het eind van _azuresite/settings.py_ de volgende regels toe.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Zie de [WhiteNoise-documentatie](https://whitenoise.evans.io/en/stable/) voor meer informatie over het configureren van WhiteNoise.

> [!IMPORTANT]
> In de sectie met database-instellingen worden de aanbevolen beveiligingsprocedures gevolgd voor het gebruik van omgevingsvariabelen. Zie de [Django-documentatie: controlelijst voor implementaties](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) voor de volledige aanbevelingen voor implementaties.

Voer de wijzigingen door in de opslagplaats.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Implementatie gebruiker configureren

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Eerder in de zelfstudie hebt u omgevingsvariabelen gedefinieerd om verbinding te kunnen maken met de PostgreSQL-database.

In App Service stelt u omgevingsvariabelen in als _app-instellingen_ met behulp van de opdracht [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell.

In het volgende voorbeeld worden de details van de databaseverbinding als app-instellingen opgegeven. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Zie [toegang tot omgevings variabelen](how-to-configure-python.md#access-environment-variables)voor meer informatie over de manier waarop deze app-instellingen in uw code worden geopend.

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

_requirements.txt_ in de hoofdmap van de opslagplaats is zichtbaar voor de App Service-implementatieserver. Vervolgens wordt Python-pakketbeheer automatisch uitgevoerd na `git push`.

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar de geïmplementeerde app. Het duurt even voordat de web-app wordt gestart omdat de container moet worden gedownload en uitgevoerd wanneer de app voor het eerst wordt aangevraagd. Als er een time-out optreedt op de pagina of als er een foutbericht wordt weergegeven, wacht u een paar minuten en vernieuwt u de pagina.

```bash
http://<app-name>.azurewebsites.net
```

Als het goed is, ziet u de poll-vraag die u eerder hebt gemaakt. 

In App Service wordt een Django-project gedetecteerd in de opslagplaats door in elke submap te zoeken naar een _wsgi.py_ die standaard wordt gemaakt met `manage.py startproject`. Wanneer het bestand wordt gevonden, wordt de Django-web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](how-to-configure-python.md) voor meer informatie over het laden van Python-apps in App Service.

Ga naar `<app-name>.azurewebsites.net` en meld u aan met dezelfde beheer gebruiker die u hebt gemaakt. Als u wilt, kunt u meer poll-vragen maken.

![Lokaal uitgevoerde Python Django-toepassing](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Gefeliciteerd!** U voert een Python-web-app (Django) uit in Azure App Service voor Linux.

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Uw app beheren in de Azure Portal

Ga naar de [Azure-portal](https://portal.azure.com) om de app te zien die u hebt gemaakt.

Selecteer in het menu links **app Services**en selecteer vervolgens de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/tutorial-python-postgresql-app/app-resource.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![App Service-pagina in Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een PostgreSQL-database maken in Azure
> * Een Python-web-app verbinden met PostgreSQL
> * De Python-web-app implementeren in azure
> * Diagnostische logboeken weergeven
> * De python-Web-App beheren in de Azure Portal

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

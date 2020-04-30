---
title: 'Zelf studie: python implementeren (Django) met post gres'
description: Meer informatie over het maken van een python-app met een PostgreSQL-data base en om deze te implementeren op Azure App Service in Linux. In de zelf studie wordt een Django-voor beeld-app gebruikt voor demonstratie.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085753"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Zelf studie: een Python-web-app (Django) implementeren met PostgreSQL in Azure App Service

Deze zelf studie laat zien hoe u een gegevensgestuurde python-web-app (Django) implementeert voor [Azure app service](app-service-linux-intro.md) en deze verbindt met een Azure database for PostgreSQL-data base. App Service biedt een uiterst schaal bare webhostingservice met self-patch functie.

![Python django-web-app implementeren in Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Database for PostgreSQL-data base maken
> * Code implementeren in Azure App Service en verbinding maken met post gres
> * Uw code bijwerken en opnieuw implementeren
> * Diagnostische logboeken weergeven
> * De web-app in de Azure Portal beheren

U kunt de stappen in dit artikel volgen op macOS, Linux of Windows.

## <a name="install-dependencies"></a>Afhankelijkheden installeren

Voordat u met deze zelf studie begint:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installeer [Azure cli](/cli/azure/install-azure-cli).
- Installeer [Git](https://git-scm.com/).
- Installeer [python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Voer in een Terminal venster de volgende opdrachten uit om de opslag plaats van de voor beeld-app te klonen en ga naar de hoofdmap van de opslag plaats:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

De djangoapp-voorbeeld opslagplaats bevat de gegevensgestuurde [Django](https://www.djangoproject.com/) -polls-app die u krijgt door [uw eerste Django-app te schrijven](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) in de Django-documentatie. Deze vindt u hier voor uw gemak.

## <a name="prepare-app-for-app-service"></a>App voorbereiden voor App Service

Net als bij veel python-webframeworksen [vereist Django bepaalde wijzigingen voordat ze kunnen worden uitgevoerd op een productie server](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)en het is niet anders met app service. U moet enkele instellingen in het standaard bestand *azuresite/Settings. py* wijzigen en toevoegen zodat de app werkt nadat deze is geïmplementeerd in app service. 

Kijk eens naar *azuresite/Production. py*, waarmee de benodigde configuratie voor app service wordt gemaakt. Kort vindt u het volgende:

- Alle instellingen overnemen van *azuresite/Settings. py*.
- Voeg de Fully Qualified Domain Name van de App Service app toe aan de toegestane hosts. 
- Gebruik [WhiteNoise](https://whitenoise.evans.io/en/stable/) om statische bestanden in productie in te scha kelen, omdat Django standaard geen statische bestanden in de productie onderhoudt. Het WhiteNoise-pakket is al opgenomen in *requirements.txt*.
- Configuratie voor PostgreSQL-data base toevoegen. Django maakt standaard gebruik van Sqlite3 als de data base, maar is niet geschikt voor productie-apps. Het [psycopg2-binary-](https://pypi.org/project/psycopg2-binary/) pakket is al opgenomen in *Requirements. txt*.
- De post gres-configuratie maakt gebruik van omgevings variabelen. Later leert u hoe u omgevings variabelen instelt in App Service.

*azuresite/Production. py* is opgenomen in de opslag plaats voor het gemak, maar is nog niet gebruikt door de app. Om ervoor te zorgen dat de instellingen worden gebruikt in App Service, moet u twee bestanden, *Manage.py* en *azuresite/wsgi. py*, configureren om deze te openen.

- Wijzig in *Manage.py*de volgende regel:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Met de volgende code:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    U stelt de omgevings variabele `DJANGO_ENV` later in wanneer u uw app service-app configureert.

- Maak in *azuresite/wsgi. py*dezelfde wijziging als hierboven.

    In App Service gebruikt u *Manage.py* om database migraties uit te voeren en app service gebruikt u *azuresite/wsgi. py* om uw django-app uit te voeren in de productie omgeving. Met deze wijziging in beide bestanden zorgt u ervoor dat de productie-instellingen in beide gevallen worden gebruikt.

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

U moet Azure CLI reeds geïnstalleerd hebben. Met [Azure cli](/cli/azure/what-is-azure-cli) kunt u werken met Azure-resources vanaf de opdracht regel Terminal. 

Voer de [`az login`](/cli/azure/reference-index#az-login) volgende opdracht uit om u aan te melden bij Azure:

```azurecli
az login
```

Volg de instructies in de terminal om u aan te melden bij uw Azure-account. Wanneer u klaar bent, worden uw abonnementen weer gegeven in JSON-indeling in de Terminal uitvoer.

## <a name="create-postgres-database-in-azure"></a>Post gres-data base maken in azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

In deze sectie maakt u een Azure Database for PostgreSQL-server en-data base. Installeer de `db-up` extensie met de volgende opdracht om te starten:

```azurecli
az extension add --name db-up
```

Maak de post gres-data base in azure [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) met de opdracht, zoals wordt weer gegeven in het volgende voor beeld. Vervang * \<postgresql-name>* door een *unieke* naam (het server-eind punt is *https://\<postgresql-name>. postgres.database.Azure.com*). Geef voor * \<Administrator-gebruikers naam>* en * \<beheerders wachtwoord>* referenties op om een beheerders gebruiker voor deze post gres-server te maken.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Deze opdracht kan enige tijd in beslag nemen omdat het volgende het geval is:

- Hiermee maakt u een [resource groep](../../azure-resource-manager/management/overview.md#terminology) met de naam `myResourceGroup`, als deze nog niet bestaat. Elke Azure-resource moet een van de volgende zijn. `--resource-group` is optioneel.
- Hiermee maakt u een post gres-server met de gebruiker met beheerders rechten.
- Hiermee maakt `pollsdb` u een Data Base.
- Hiermee staat u toegang vanaf uw lokale IP-adres toe.
- Hiermee staat u toegang vanuit Azure-Services toe.
- Maak een database gebruiker met toegang tot de `pollsdb` data base.

U kunt alle stappen afzonderlijk uitvoeren met andere `az postgres` opdrachten en `psql`, maar `az postgres up` dit is allemaal in één stap voor u.

Wanneer de opdracht is voltooid, kunt u de uitvoer regels zoeken die `Ran Database Query:`met worden uitgevoerd. Hiermee wordt de database gebruiker weer gegeven die voor u is gemaakt, met `root` de gebruikers `Pollsdb1`naam en het wacht woord. U gebruikt deze later om uw app te verbinden met de data base.

<!-- not all locations support az postgres up -->
> [!TIP]
> Als u de locatie voor uw post gres-server wilt opgeven, `--location <location-name>`neemt u `<location_name>` het argument op, waarbij een van de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)is. U kunt de regio's die beschikbaar zijn voor uw abonnement ophalen [`az account list-locations`](/cli/azure/account#az-account-list-locations) met de opdracht.

## <a name="deploy-the-app-service-app"></a>De App App Service implementeren

In deze sectie maakt u de App App Service. U verbindt deze app met de post gres-data base die u hebt gemaakt en implementeert uw code.

### <a name="create-the-app-service-app"></a>De App App Service maken

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Zorg ervoor dat u weer terug bent in de hoofdmap`djangoapp`van de opslag plaats (), omdat de app vanuit deze map wordt geïmplementeerd.

Maak een App Service-app met [`az webapp up`](/cli/azure/webapp#az-webapp-up) de opdracht, zoals wordt weer gegeven in het volgende voor beeld. Vervang * \<app-name>* door een *unieke* naam (het server eindpunt is *https://\<app-name>. azurewebsites.net*). Toegestane tekens voor * \<de app-naam>* zijn `0` - `9` `A` - `Z`, en `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Deze opdracht kan enige tijd in beslag nemen omdat het volgende het geval is:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Hiermee wordt automatisch een [resource groep](../../azure-resource-manager/management/overview.md#terminology) gegenereerd.
- Maakt de [app service plan](../overview-hosting-plans.md) *myAppServicePlan* in de prijs categorie Basic (B1), als deze nog niet bestaat. `--plan`en `--sku` zijn optioneel.
- Hiermee wordt de App App Service gemaakt als deze nog niet bestaat.
- Hiermee schakelt u de standaard logboek registratie voor de app in, als deze nog niet is ingeschakeld.
- Uploadt de opslag plaats met behulp van een ZIP-implementatie waarbij build Automation is ingeschakeld.

Zodra de implementatie is voltooid, ziet u een JSON-uitvoer zoals het volgende:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Kopieer de waarde van * \<app-Resource-Group>*. U hebt deze later nodig om de app te configureren. 

> [!TIP]
> U kunt dezelfde opdracht later gebruiken om wijzigingen te implementeren en Diagnostische logboeken direct in te scha kelen met:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

De voorbeeld code is nu geïmplementeerd, maar de app maakt nog geen verbinding met de post gres-data base in Azure. U doet dit nu.

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Wanneer u uw app lokaal uitvoert, kunt u de omgevings variabelen instellen in de terminal sessie. In App Service kunt u dit doen met *app-instellingen*met behulp van de opdracht [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

Voer de volgende opdracht uit om de details van de database verbinding op te geven als app-instellingen. Vervang * \<app-name>*, * \<app-Resource-Group>* en * \<postgresql>* door uw eigen waarden. Houd er rekening mee dat `root` de `Pollsdb1` gebruikers referenties en voor u `az postgres up`zijn gemaakt door.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Zie [Access Environment Varia bles](how-to-configure-python.md#access-environment-variables)(Engelstalig) voor meer informatie over de manier waarop uw code toegang heeft tot deze app-instellingen.

### <a name="run-database-migrations"></a>Database migraties uitvoeren

Als u database migraties wilt uitvoeren in App Service, opent u een SSH-sessie in de browser door te navigeren naar *https://\<App-name>. scm.azurewebsites.net/webssh/host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Voer in de SSH-sessie de volgende opdrachten uit:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar de geïmplementeerde app met URL *http\//\<: app-name>. azurewebsites.net* in een browser. Er wordt een bericht weer gegeven dat **er geen polls beschikbaar zijn**. 

Blader naar *http:\//\<app-naam>. azurewebsites.net/admin* en meld u aan met de beheerders gebruiker die u in de laatste stap hebt gemaakt. Selecteer **toevoegen** naast **vragen**en maak een poll-vraag met enkele keuzes.

Blader naar de geïmplementeerde app met URL *http\//\<: app-name>. azurewebsites.net/admin*en maak enkele poll vragen. U kunt de vragen zien op *http:\//\<app-name>. azurewebsites.net/*. 

![Python django-app uitvoeren in App Services in azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Blader naar de geïmplementeerde app met URL *http\//\<: app-name>. azurewebsites.net* opnieuw om de vraag te bekijken en de vraag te beantwoorden.

App Service detecteert een Django-project in uw opslag plaats door te zoeken naar een *wsgi.py* -bestand in `manage.py startproject` elke submap, die standaard wordt gemaakt. Wanneer App Service het bestand vindt, wordt de Django-web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](how-to-configure-python.md) voor meer informatie over het laden van Python-apps in App Service.

**Voltooid!** U voert een Python-web-app (Django) uit in Azure App Service voor Linux.

## <a name="develop-app-locally-and-redeploy"></a>App lokaal ontwikkelen en opnieuw implementeren

In deze sectie ontwikkelt u uw app in uw lokale omgeving en implementeert u uw code opnieuw naar App Service.

### <a name="set-up-locally-and-run"></a>Lokaal instellen en uitvoeren

Als u uw lokale ontwikkel omgeving wilt instellen en de voor beeld-app voor de eerste keer wilt uitvoeren, voert u de volgende opdrachten uit:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Wanneer de Django-web-app volledig is geladen, wordt het volgende bericht weer gegeven:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Ga naar *http:\//localhost: 8000* in een browser. Er wordt een bericht weer gegeven dat **er geen polls beschikbaar zijn**. 

Ga naar *http:\//localhost: 8000/admin* en meld u aan met de beheerders gebruiker die u in de laatste stap hebt gemaakt. Selecteer **toevoegen** naast **vragen**en maak een poll-vraag met enkele keuzes.

![Python django-app in App Services lokaal uitvoeren](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga opnieuw naar *http\/:/localhost: 8000* om de vraag te bekijken en de vraag te beantwoorden. De lokale Django-voorbeeld toepassing schrijft en slaat gebruikers gegevens op in een lokale Sqlite3-data base, zodat u zich geen zorgen hoeft te maken over het opruimen van uw productie database. Om ervoor te zorgen dat uw ontwikkel omgeving overeenkomt met de Azure-omgeving, kunt u overwegen om een post gres-data base lokaal te gebruiken.

Als u de Django-server wilt stoppen, typt u CTRL + C.

### <a name="update-the-app"></a>De app bijwerken

Als u wilt zien hoe het maken van app-updates werkt, maakt `polls/models.py`u een kleine wijziging in. De regel zoeken:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

En wijzig deze in:

```python
choice_text = models.CharField(max_length=100)
```

Door het gegevens model te wijzigen, moet u een nieuwe Django-migratie maken. Doe dit met de volgende opdracht:

```
python manage.py makemigrations
```

U kunt uw wijzigingen lokaal testen door migraties uit te voeren, de ontwikkel server uit te voeren en te navigeren naar *http:\//localhost: 8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Code opnieuw implementeren naar Azure

Als u de wijzigingen opnieuw wilt implementeren, voert u de volgende opdracht uit vanuit de hoofdmap van de opslag plaats:

```azurecli
az webapp up --name <app-name>
```

App Service detecteert dat de app bestaat en alleen de code implementeert.

### <a name="rerun-migrations-in-azure"></a>Migraties opnieuw uitvoeren in azure

Omdat u wijzigingen in het gegevens model hebt aangebracht, moet u de database migraties in App Service opnieuw uitvoeren. Open een SSH-sessie in de browser door te navigeren *naar\<https://app-name>. scm.azurewebsites.net/webssh/host*. Voer de volgende opdrachten uit:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>App in productie controleren

Blader naar *http:\//\<app-name>. azurewebsites.net* en Bekijk de wijzigingen die live in productie hebben. 

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

U hebt toegang tot de console logboeken die in de container zijn gegenereerd.

> [!TIP]
> `az webapp up`Hiermee schakelt u de standaard logboek registratie in. Uit prestatie overwegingen wordt de logboek registratie na enige tijd vanzelf uitgeschakeld, maar wordt weer ingeschakeld telkens wanneer u het `az webapp up` opnieuw uitvoert. Als u deze hand matig wilt inschakelen, voert u de volgende opdracht uit:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Voer de volgende Azure CLI-opdracht uit om de logboek stroom te bekijken:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt de logboek bestanden ook vanuit de browser controleren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Als u het streamen van Logboeken op `Ctrl` + `C`elk gewenst moment wilt stoppen, typt u.

## <a name="manage-your-app-in-the-azure-portal"></a>Uw app beheren in de Azure Portal

Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer de app die u hebt gemaakt.

![Navigeer naar uw python django-app in de Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheer taken uitvoeren, zoals bladeren, stoppen, opnieuw starten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![Beheer uw python django-app op de pagina overzicht in de Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources in de toekomst niet meer nodig hebt, verwijdert u de resource groepen door de volgende opdrachten uit te voeren:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure Database for PostgreSQL-data base maken
> * Code implementeren in Azure App Service en verbinding maken met post gres
> * Uw code bijwerken en opnieuw implementeren
> * Diagnostische logboeken weergeven
> * De web-app in de Azure Portal beheren

Ga naar de volgende zelf studie voor meer informatie over het toewijzen van een aangepaste DNS-naam aan uw app:

> [!div class="nextstepaction"]
> [Zelf studie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

Of Bekijk andere bronnen:

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

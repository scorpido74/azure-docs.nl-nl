---
title: 'Zelfstudie: Python (Django) implementeren met Postgres'
description: Meer informatie over het maken van een Python-app met een PostgreSQL-database en implementeren in Azure App Service op Linux. De tutorial maakt gebruik van een Django voorbeeld app voor demonstratie.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085753"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Zelfstudie: Een Python-webapp (Django) implementeren met PostgreSQL in Azure App Service

In deze zelfstudie ziet u hoe u een python-web-app (Met gegevens gestuurde Python) implementeert naar [Azure App Service](app-service-linux-intro.md) en deze koppelen aan een Azure Database voor PostgreSQL-database. App Service biedt een zeer schaalbare, zelfpatchende webhostingservice.

![Python Django-webapp implementeren in Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-database voor PostgreSQL-database maken
> * Code implementeren in Azure App Service en verbinding maken met Postgres
> * Uw code bijwerken en opnieuw implementeren
> * Diagnostische logboeken weergeven
> * De web-app beheren in de Azure-portal

U de stappen in dit artikel volgen op macOS, Linux of Windows.

## <a name="install-dependencies"></a>Afhankelijkheden installeren

Voordat u begint met deze zelfstudie:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli)installeren .
- Installeer [Git](https://git-scm.com/).
- [Python 3](https://www.python.org/downloads/)installeren .

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Voer in een terminalvenster de volgende opdrachten uit om de opslagplaats van de voorbeeld-app te klonen en wijzig in de bronmaproot:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

De djangoapp sample repository bevat de data-driven [Django](https://www.djangoproject.com/) polls app die je krijgt door [het schrijven van je eerste Django app](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) in de Django documentatie te volgen. Het is hier voor uw gemak.

## <a name="prepare-app-for-app-service"></a>App voorbereiden op App-service

Net als veel Python-webframeworks vereist Django [bepaalde wijzigingen voordat ze in een productieserver kunnen worden uitgevoerd](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)en dat is niet anders met App Service. U moet een aantal instellingen wijzigen en toevoegen in het standaard bestand *azuresite/settings.py,* zodat de app werkt nadat deze is geïmplementeerd in App Service. 

Neem een kijkje op *azuresite / production.py*, die de nodige configuratie voor App Service maakt. Kortom, het doet het volgende:

- Alle instellingen overnemen van *azuresite/settings.py*.
- Voeg de volledig gekwalificeerde domeinnaam van de App Service-app toe aan de toegestane hosts. 
- Gebruik [WhiteNoise](https://whitenoise.evans.io/en/stable/) om statische bestanden in productie te laten weergeven, omdat Django standaard geen statische bestanden in productie weergeeft. Het WhiteNoise-pakket is al opgenomen in *requirements.txt*.
- Configuratie toevoegen voor PostgreSQL-database. Django gebruikt Sqlite3 standaard als database, maar is niet geschikt voor productie-apps. De [psycopg2-binaire](https://pypi.org/project/psycopg2-binary/) pakket is al opgenomen in *requirements.txt*.
- De Postgres-configuratie maakt gebruik van omgevingsvariabelen. Later kom je erachter hoe je omgevingsvariabelen instelt in App Service.

*azuresite/production.py* is voor het gemak opgenomen in de repository, maar wordt nog niet gebruikt door de app. Om ervoor te zorgen dat de instellingen worden gebruikt in App Service, moet u twee bestanden configureren, *manage.py* en *azuresite/wsgi.py*, om toegang te krijgen tot deze bestanden.

- Wijzig *in manage.py*de volgende regel:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Naar de volgende code:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    U stelt de omgevingsvariabele `DJANGO_ENV` later in wanneer u uw App Service-app configureert.

- Breng in *azuresite/wsgi.py*dezelfde wijziging aan als hierboven.

    In App Service gebruikt u *manage.py* om databasemigraties uit te voeren en gebruikt App Service *azuresite/wsgi.py* om uw Django-app in productie uit te voeren. Deze wijziging in beide bestanden zorgt ervoor dat de productie-instellingen in beide gevallen worden gebruikt.

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

U moet Azure CLI al hebben geïnstalleerd. [Met Azure CLI](/cli/azure/what-is-azure-cli) u werken met Azure-bronnen vanuit de opdrachtregelterminal. 

Als u zich wilt [`az login`](/cli/azure/reference-index#az-login) aanmelden bij Azure, voert u de opdracht uit:

```azurecli
az login
```

Volg de instructies in de terminal om u aan te melden bij uw Azure-account. Wanneer u klaar bent, worden uw abonnementen weergegeven in JSON-indeling in de terminaluitvoer.

## <a name="create-postgres-database-in-azure"></a>Postgres-database maken in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

In deze sectie maakt u een Azure Database voor PostgreSQL-server en -database. Installeer de extensie `db-up` met de volgende opdracht om de extensie te starten:

```azurecli
az extension add --name db-up
```

Maak de Postgres-database [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) in Azure met de opdracht, zoals in het volgende voorbeeld wordt weergegeven. Vervang * \<de>van postgresql-naam* door een *unieke* naam (het servereindpunt is *https://\<postgresql-naam>.postgres.database.azure.com).* Voor * \<>en*>voor beheerdersgebruikersnaam en * \<beheerder-wachtwoord *, geeft u referenties op om een beheerdersgebruiker voor deze Postgres-server te maken.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Deze opdracht kan een tijdje duren omdat het het volgende doet:

- Hiermee maakt u `myResourceGroup`een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) met de aangeroepen als deze niet bestaat. Elke Azure-bron moet in een van deze bronnen staan. `--resource-group` is optioneel.
- Hiermee maakt u een Postgres-server met de beheerdersgebruiker.
- Hiermee `pollsdb` maakt u een database.
- Hiermee u toegang krijgen vanaf uw lokale IP-adres.
- Hiermee u toegang krijgen vanuit Azure-services.
- Maak een databasegebruiker met `pollsdb` toegang tot de database.

U alle stappen afzonderlijk `az postgres` met andere `psql`opdrachten `az postgres up` doen en, maar doet ze allemaal in één stap voor u.

Wanneer de opdracht is voltooid, zoekt `Ran Database Query:`u de uitvoerregels die bij . Ze tonen de databasegebruiker die voor u `root` is `Pollsdb1`gemaakt, met de gebruikersnaam en het wachtwoord. U gebruikt ze later om uw app aan de database te koppelen.

<!-- not all locations support az postgres up -->
> [!TIP]
> Als u de locatie voor uw Postgres-server wilt opgeven, moet u het argument `--location <location-name>`opnemen , waar `<location_name>` een van de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)is. Met de [`az account list-locations`](/cli/azure/account#az-account-list-locations) opdracht u de regio's beschikbaar krijgen voor uw abonnement.

## <a name="deploy-the-app-service-app"></a>De App Service-app implementeren

In deze sectie maakt u de App Service-app. Je verbindt deze app met de Postgres-database die je hebt gemaakt en implementeert je code.

### <a name="create-the-app-service-app"></a>De app App Service maken

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Zorg ervoor dat u weer in`djangoapp`de hoofdmap van de opslagplaats bent ( ), omdat de app vanuit deze map wordt geïmplementeerd.

Maak een App Service-app met de [`az webapp up`](/cli/azure/webapp#az-webapp-up) opdracht, zoals in het volgende voorbeeld wordt weergegeven. Vervang * \<app-naam>* door een *unieke* naam (het servereindpunt is *\<https:// app-naam>.azurewebsites.net).* Toegestane tekens `A` - `Z`voor `0` - `9` * \<app-naam>* `-`zijn , en .

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Deze opdracht kan een tijdje duren omdat het het volgende doet:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Hiermee genereert u automatisch een [brongroep.](../../azure-resource-manager/management/overview.md#terminology)
- Hiermee maakt u het [App Service-abonnement](../overview-hosting-plans.md) *myAppServicePlan* in de prijscategorie Basis (B1), als deze niet bestaat. `--plan`en `--sku` zijn optioneel.
- Hiermee maakt u de App Service-app als deze niet bestaat.
- Hiermee schakelt u standaardlogboekregistratie in voor de app, als deze nog niet is ingeschakeld.
- Uploadt de repository met ZIP-implementatie met buildautomatisering ingeschakeld.

Zodra de implementatie is voltooid, ziet u een JSON-uitvoer zoals:

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

Kopieer de waarde van * \<>app-resourcegroep *. Je hebt het nodig om de app later te configureren. 

> [!TIP]
> U dezelfde opdracht later gebruiken om eventuele wijzigingen te implementeren en diagnostische logboeken onmiddellijk in te schakelen met:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

De voorbeeldcode is nu geïmplementeerd, maar de app maakt nog geen verbinding met de Postgres-database in Azure. Jij doet dit als volgende.

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Wanneer u uw app lokaal uitvoert, u de omgevingsvariabelen instellen in de terminalsessie. In App Service doe je dat met *app-instellingen*, met behulp van de opdracht [az webapp config appsettings.](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)

Voer de volgende opdracht uit om de databaseverbindingsgegevens op te geven als app-instellingen. Vervang * \<>, * * \<app-resourcegroep-groep>* en * \<postgresql-naam>* door uw eigen waarden. Vergeet niet dat `root` de `Pollsdb1` gebruikersreferenties en `az postgres up`zijn gemaakt voor u door .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Zie [Toegankelijkheidsomgevingsvariabelen](how-to-configure-python.md#access-environment-variables)voor informatie over hoe uw code toegang heeft tot deze app-instellingen.

### <a name="run-database-migrations"></a>Databasemigraties uitvoeren

Als u databasemigraties wilt uitvoeren in App Service, opent u een SSH-sessie in de browser door te navigeren naar *https://\<app-naam>.scm.azurewebsites.net/webssh/host:*

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

Blader naar de geïmplementeerde app met URL *\//\<http: app-name>.azurewebsites.net* in een browser. U ziet het bericht **Geen polls zijn beschikbaar.** 

Blader naar *\//\<http: app-name>.azurewebsites.net/admin* en meld u aan met de beheerdersgebruiker die u in de laatste stap hebt gemaakt. Selecteer **Toevoegen** naast **Vragen**en maak een enquêtevraag met een aantal keuzes.

Blader naar de geïmplementeerde app met URL *\//\<http: app-name>.azurewebsites.net/admin*en maak enkele enquêtevragen. U de vragen bekijken op *\//\<http: app-name>.azurewebsites.net/*. 

![Python Django-app uitvoeren in App Services in Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Blader naar de geïmplementeerde app met URL *\//\<http: app-name>.azurewebsites.net* opnieuw om de pollvraag te zien en de vraag te beantwoorden.

App Service detecteert een Django-project in uw repository door te `manage.py startproject` zoeken naar een *wsgi.py-bestand* in elke submap, dat standaard wordt gemaakt. Wanneer App Service het bestand vindt, wordt de Django-web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](how-to-configure-python.md) voor meer informatie over het laden van Python-apps in App Service.

**Gefeliciteerd!** U voert een Python-web-app (Django) uit in Azure App Service voor Linux.

## <a name="develop-app-locally-and-redeploy"></a>App lokaal ontwikkelen en opnieuw implementeren

In deze sectie ontwikkelt u uw app in uw lokale omgeving en implementeert u uw code opnieuw in App Service.

### <a name="set-up-locally-and-run"></a>Lokaal instellen en uitvoeren

Voer de volgende opdrachten uit om uw lokale ontwikkelomgeving in te stellen en de voorbeeld-app voor de eerste keer uit te voeren:

# <a name="bash"></a>[Bash](#tab/bash)

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

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

Wanneer de Django-web-app volledig is geladen, wordt iets als het volgende bericht geretourneerd:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Ga naar *http:\//localhost:8000* in een browser. U ziet het bericht **Geen polls zijn beschikbaar.** 

Ga naar *\/http: /localhost:8000/admin* en meld u aan met de beheerdersgebruiker die u in de laatste stap hebt gemaakt. Selecteer **Toevoegen** naast **Vragen**en maak een enquêtevraag met een aantal keuzes.

![Python Django-app uitvoeren in App Services lokaal](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga naar *http:\//localhost:8000* opnieuw om de opiniepeilingsvraag te zien en de vraag te beantwoorden. De lokale Django-voorbeeldtoepassing schrijft en slaat gebruikersgegevens op in een lokale Sqlite3-database, zodat u zich geen zorgen hoeft te maken over het verknoeien van uw productiedatabase. Als u wilt dat uw ontwikkelomgeving overeenkomt met de Azure-omgeving, u overwegen om een Postgres-database lokaal te gebruiken.

Typ Ctrl+C als u de Django-server wilt stoppen.

### <a name="update-the-app"></a>De app bijwerken

Gewoon om te zien hoe het maken `polls/models.py`van app-updates werkt, maak een kleine verandering in . Zoek de lijn:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

En verander het in:

```python
choice_text = models.CharField(max_length=100)
```

Als u het gegevensmodel wijzigt, moet u een nieuwe Django-migratie maken. Doe dit met de volgende opdracht:

```
python manage.py makemigrations
```

U uw wijzigingen lokaal testen door migraties uit te voeren, de ontwikkelingsserver uit te voeren en naar *\/http: /localhost:8000/admin*te navigeren:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Code opnieuw implementeren in Azure

Als u de wijzigingen opnieuw wilt implementeren, voert u de volgende opdracht uit vanuit de hoofdmap van de opslagplaats:

```azurecli
az webapp up --name <app-name>
```

App Service detecteert dat de app bestaat en implementeert alleen de code.

### <a name="rerun-migrations-in-azure"></a>Migraties opnieuw uitvoeren in Azure

Omdat u wijzigingen hebt aangebracht in het gegevensmodel, moet u databasemigraties opnieuw uitvoeren in App Service. Open een SSH-sessie in de browser door te navigeren naar *https://\<app-naam>.scm.azurewebsites.net/webssh/host*. Voer de volgende opdrachten uit:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>App in productie bekijken

Blader naar *\//\<http: app-name>.azurewebsites.net* en bekijk de wijzigingen die live in productie worden uitgevoerd. 

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

U hebt toegang tot de consolelogboeken die vanuit de container worden gegenereerd.

> [!TIP]
> `az webapp up`schakelt de standaardlogboekregistratie voor u in. Om prestatieredenen schakelt deze logboekregistratie zichzelf na enige tijd uit, maar wordt elke keer dat u weer wordt uitgevoerd weer ingeschakeld. `az webapp up` Voer de volgende opdracht uit om de opdracht handmatig in te schakelen:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Voer de volgende opdracht Azure CLI uit om de logboekstroom weer te geven:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U ook de logbestanden `https://<app-name>.scm.azurewebsites.net/api/logs/docker`vanuit de browser bekijken op.

Typ op elk gewenst moment `Ctrl` + `C`het streamen van logboeken.

## <a name="manage-your-app-in-the-azure-portal"></a>Uw app beheren in de Azure-portal

Zoek in de [Azure-portal](https://portal.azure.com)naar de app die u hebt gemaakt.

![Navigeren naar uw Python Django-app in de Azure-portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier u ook basisbeheertaken uitvoeren, zoals bladeren, stoppen, opnieuw starten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![Uw Python Django-app beheren op de pagina Overzicht in de Azure-portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verwacht dat u deze bronnen in de toekomst nodig hebt, verwijdert u de brongroepen door de volgende opdrachten uit te voeren:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heb je geleerd:

> [!div class="checklist"]
> * Een Azure-database voor PostgreSQL-database maken
> * Code implementeren in Azure App Service en verbinding maken met Postgres
> * Uw code bijwerken en opnieuw implementeren
> * Diagnostische logboeken weergeven
> * De web-app beheren in de Azure-portal

Ga naar de volgende zelfstudie voor meer informatie over het toewijzen van een aangepaste DNS-naam aan uw app:

> [!div class="nextstepaction"]
> [Zelfstudie: aangepaste DNS-naam toewijzen aan uw app](../app-service-web-tutorial-custom-domain.md)

Of bekijk andere bronnen:

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

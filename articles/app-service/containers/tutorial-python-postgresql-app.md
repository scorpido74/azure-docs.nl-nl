---
title: 'Zelfstudie: Python (Django) implementeren met Postgres'
description: Leer hoe u een Python-app maakt met een PostgreSQL-database en deze implementeert naar Azure App Service op Linux. In de zelfstudie wordt ter illustratie een Django-voorbeeldapp gebruikt.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- tracking-python
ms.openlocfilehash: 29aeae7683c46b1e10acdf1b2c4a7183c22eb408
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84807320"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Zelfstudie: Een Python-web-app (Django) implementeren met PostgreSQL in Azure App Service

In deze zelfstudie ziet u hoe u een gegevensgestuurde web-app van Python (Django) naar [Azure App Service](app-service-linux-intro.md) implementeert en deze koppelt aan een Azure Database for PostgreSQL-database. App Service biedt een uiterst schaalbare webhostingservice met self-patchfunctie.

![Python Django web-app implementeren naar Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Database for PostgreSQL-database maken
> * Code implementeren naar Azure App Service en koppelen aan Postgres
> * Uw code bijwerken en opnieuw implementeren
> * Diagnostische logboeken weergeven
> * De web-app in Azure Portal beheren

U kunt de stappen in dit artikel volgen op macOS, Linux of Windows.

## <a name="install-dependencies"></a>Afhankelijkheden installeren

Voordat u met deze zelfstudie begint:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installeer [Azure CLI](/cli/azure/install-azure-cli).
- Installeer [Git](https://git-scm.com/).
- Installeer [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

In een terminalvenster voert u de volgende opdrachten uit om de opslagplaats van de voorbeeldapp te klonen en te wijzigen in de hoofdmap van de opslagplaats:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

De voorbeeldopslagplaats djangoapp bevat de gegevensgestuurde [Django](https://www.djangoproject.com/)-polls-app die u kunt verkrijgen door [Uw eerste Django-app schrijven](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) in de Django-documentatie te volgen. Deze wordt hier voor het gemak aangeboden.

## <a name="prepare-app-for-app-service"></a>Apps voorbereiden voor App Service

Zoals vele andere Python-webframeworks zijn voor Django [bepaalde wijzigingen vereist voordat ze kunnen worden uitgevoerd in een productieserver](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/). Dit geldt ook voor App Service. U moet een aantal instellingen in het standaardbestand *azuresite/settings.py* wijzigen en toevoegen zodat de app ook werkt nadat deze naar App Service is geïmplementeerd. 

Bekijk *azuresite/production.py*. Hiermee maakt u de noodzakelijke configuratie voor App Service. Kort gezegd voert u hiermee het volgende uit:

- Neem alle instellingen van *azuresite/settings.py* over.
- Voeg de Fully Qualified Domain Name van de App Service-app toe aan de toegestane hosts. 
- Gebruik [WhiteNoise](https://whitenoise.evans.io/en/stable/) om het verwerken van statische bestanden in productie in te schakelen, omdat Django standaard geen statische bestanden in productie verwerkt. Het WhiteNoise-pakket is al opgenomen in *requirements.txt*.
- Voeg configuratie toe voor de PostgreSQL-database. Standaard gebruikt Django Sqlite3 als de database, maar deze is niet geschikt voor productie-apps. Het [psycopg2-binary](https://pypi.org/project/psycopg2-binary/)-pakket is al opgenomen in *requirements.txt*.
- In de Postgres-configuratie worden omgevingsvariabelen gebruikt. Later leert u hoe u omgevingsvariabelen in App Service instelt.

*azuresite/production.py* staat voor het gemak in de opslagplaats, maar deze wordt nog niet gebruikt door de app. Om ervoor te zorgen dat de instellingen ervan worden gebruikt in App Service, moet u twee bestanden, *manage.py* en *azuresite/wsgi.py*, configureren om toegang te kunnen krijgen.

- In *manage.py* wijzigt u de volgende regel:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    In de volgende code:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Stel de omgevingsvariabele `DJANGO_ENV` later in, wanneer u uw App Service-app configureert.

- Ook in *azuresite/wsgi.py* voert u de bovenstaande wijziging in.

    In App Service gebruikt u *manage.py* om databasemigraties uit te voeren, en in App Service wordt *azuresite/wsgi.py* gebruikt om uw Django-app in productie uit te voeren. Deze wijziging in beide bestanden zorgt ervoor dat in beide gevallen de productie-instellingen worden gebruikt.

## <a name="sign-in-to-azure-cli"></a>Aanmelden bij Azure CLI

Als het goed is, hebt u Azure CLI al geïnstalleerd. In [Azure CLI](/cli/azure/what-is-azure-cli) kunt u werken met Azure-resources uit de opdrachtregelterminal. 

Voer de opdracht [`az login`](/cli/azure/reference-index#az-login) uit om u bij Azure aan te melden:

```azurecli
az login
```

Volg de instructies in de terminal om u aan te melden bij uw Azure-account. Wanneer u klaar bent, worden uw abonnementen weergegeven in JSON-indeling in de terminaluitvoer.

## <a name="create-postgres-database-in-azure"></a>Een Postgres-database maken in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

In deze sectie maakt u een Azure Database for PostgreSQL-server en -database. Installeer eerst de `db-up`-extensie met de volgende opdracht:

```azurecli
az extension add --name db-up
```

Maak de Postgres-database in Azure met de opdracht [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), zoals in het volgende voorbeeld wordt weergegeven. Vervang *\<postgresql-name>* door een *unieke* naam (het servereindpunt is *https://\<postgresql-name>.postgres.database.azure.com*). Geef voor *\<admin-username>* en *\<admin-password>* referenties op om een gebruiker met beheerdersrechten te maken voor deze Postgres-server.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Deze opdracht kan enige tijd in beslag nemen, omdat het volgende wordt uitgevoerd:

- Er wordt een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) gemaakt met de naam `myResourceGroup`, als deze nog niet bestaat. Elke Azure-resource moet zich in een van deze groepen bevinden. `--resource-group` is optioneel.
- Er wordt een Postgres-server gemaakt met de gebruiker met beheerdersrechten.
- Hiermee maakt u een `pollsdb`-database.
- Er wordt toegang verleend vanaf uw lokale IP-adres.
- Er wordt toegang verleend vanuit Azure-services.
- Maak een databasegebruiker met toegang tot de `pollsdb`-database.

U kunt alle stappen afzonderlijk uitvoeren met andere `az postgres`-opdrachten en `psql`, maar met `az postgres up` worden alle stappen in één keer voor u uitgevoerd.

Wanneer de opdracht is voltooid, zoekt u de uitvoerregels die beginnen met `Ran Database Query:`. Hier ziet u de databasegebruiker die voor u is gemaakt, met de gebruikersnaam `root` en het wachtwoord `Pollsdb1`. Deze gaat u later gebruiken om uw app met de database te verbinden.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>` kan worden ingesteld op een van de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/). U kunt de regio's beschikbaar maken voor uw abonnement met de opdracht [`az account list-locations`](/cli/azure/account#az-account-list-locations). Voor productie-apps plaatst u uw database en uw app in dezelfde locatie.

## <a name="deploy-the-app-service-app"></a>De App Service-app implementeren

In deze sectie maakt u de App Service-app. U gaat deze app verbinden met de Postgres-database die u hebt gemaakt en uw code implementeren.

### <a name="create-the-app-service-app"></a>De App Service-app maken

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Controleer of u zich weer in de hoofdmap van de opslagplaats (`djangoapp`) bevindt, omdat de app vanuit deze map zal worden geïmplementeerd.

Maak een App Service-app met de opdracht [`az webapp up`](/cli/azure/webapp#az-webapp-up), zoals in het volgende voorbeeld wordt weergegeven. Vervang *\<app-name>* door een *unieke* naam (het servereindpunt is *https://\<app-name>.azurewebsites.net*). De tekens die voor *\<app-name>* zijn toegestaan, zijn `A`-`Z`, `0`-`9` en `-`.

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Deze opdracht kan enige tijd in beslag nemen, omdat het volgende wordt uitgevoerd:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Er wordt automatisch een [resourcegroep](../../azure-resource-manager/management/overview.md#terminology) gegenereerd.
- Het [App Service-plan](../overview-hosting-plans.md) *myAppServicePlan* wordt gemaakt in de Basic-prijscategorie (B1), als deze nog niet bestaat. `--plan` en `--sku` zijn optioneel.
- De App Service-app wordt gemaakt, als deze nog niet bestaat.
- Er worden standaardlogboeken voor de app ingeschakeld, als die niet al is ingeschakeld.
- De opslagplaats wordt geüpload met behulp van ZIP-implementatie, met ingeschakelde bouwautomatisering.

Zodra de implementatie is voltooid, ziet u de volgende JSON-uitvoer:

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

Kopieer de waarde van *\<app-resource-group>* . U moet de app later configureren. 

> [!TIP]
> De permanente instellingen worden opgeslagen in een verborgen *.azure*-map in uw opslagplaats. U kunt de eenvoudige opdracht later gebruiken om eventuele wijzigingen opnieuw te implementeren en direct diagnostische logboeken in te schakelen met:
> 
> ```azurecli
> az webapp up
> ```

De voorbeeldcode wordt nu geïmplementeerd, maar de app maakt nog geen verbinding met de Postgres-database in Azure. Dit gaat u hierna doen.

### <a name="configure-environment-variables"></a>Omgevingsvariabelen configureren

Wanneer u uw app lokaal uitvoert, kunt u de omgevingsvariabelen instellen in de terminalsessie. In App Service doet u dit met *app-instellingen*, met behulp van de opdracht [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Voer de volgende opdracht uit om de details van de databaseverbinding op te geven als app-instellingen. Vervang *\<app-name>* , *\<app-resource-group>* en *\<postgresql-name>* door uw eigen waarden. Onthoud dat de gebruikersreferenties `root` en `Pollsdb1` voor u zijn gemaakt door `az postgres up`.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Zie [Toegang tot omgevingsvariabelen](how-to-configure-python.md#access-environment-variables) voor informatie over de manier waarop uw code toegang heeft tot deze app-instellingen.

### <a name="run-database-migrations"></a>Databasemigraties uitvoeren

Voor het uitvoeren van databasemigraties in App Service opent u een SSH-sessie in de browser door naar *https://\<app-name>.scm.azurewebsites.net/webssh/host* te navigeren:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Voer in de SSH-sessie de volgende opdrachten uit:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install packages
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader in een browser met URL *http:\//\<app-name>.azurewebsites.net* naar de geïmplementeerde app. Als het goed is, ziet u het bericht **Geen polls beschikbaar**. 

Blader naar *http:\//\<app-name>.azurewebsites.net/admin* en meld u aan met behulp van de gebruiker met beheerdersrechten die u in de laatste stap hebt gemaakt. Selecteer **Toevoegen** naast **Vragen** en maak een poll-vraag met enkele antwoordmogelijkheden.

Blader met URL *http:\//\<app-name>.azurewebsites.net/admin* naar de geïmplementeerde app en maak een aantal pollvragen. U ziet de vragen op *http:\//\<app-name>.azurewebsites.net/* . 

![De Python Django-app uitvoeren in App Services in Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Blader met de URL *http:\//\<app-name>.azurewebsites.net* weer naar de geïmplementeerde app om te pollvraag weer te geven en de vraag te beantwoorden.

In App Service wordt een Django-project in de opslagplaats gedetecteerd door in elke submap naar een *wsgi.py*-bestand te zoeken dat standaard wordt gemaakt met `manage.py startproject`. Wanneer App Service het bestand heeft gevonden, wordt de Django web-app geladen. Zie [Ingebouwde Python-installatiekopie configureren](how-to-configure-python.md) voor meer informatie over het laden van Python-apps in App Service.

**Gefeliciteerd!** U voert een Python (Django) web-app uit in Azure App Service voor Linux.

## <a name="develop-app-locally-and-redeploy"></a>Apps lokaal ontwikkelen en opnieuw implementeren

In deze sectie gaat u uw app in uw lokale omgeving ontwikkelen en uw code opnieuw implementeren naar App Service.

### <a name="set-up-locally-and-run"></a>Lokaal instellen en uitvoeren

Voer de volgende opdrachten uit om uw lokale ontwikkelomgeving in te stellen en de voorbeeldapp voor de eerste keer uit te voeren:

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

Wanneer de Django web-app volledig is geladen, wordt zoiets als het volgende bericht geretourneerd:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Ga naar *http:\//localhost:8000* in een browser. Als het goed is, ziet u het bericht **Geen polls beschikbaar**. 

Ga naar *http:\//localhost:8000/admin* en meld u aan als de gebruiker met beheerdersrechten die u hebt gemaakt in de vorige stap. Selecteer **Toevoegen** naast **Vragen** en maak een poll-vraag met enkele antwoordmogelijkheden.

![De Python Django-app lokaal uitvoeren in App Services](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ga weer naar *http:\//localhost:8000* om de pollvraag weer te geven en de vraag te beantwoorden. Gebruikersgegevens worden met de lokale Django-voorbeeldtoepassing geschreven en opgeslagen in een lokale Sqlite3-database, dus u hoeft zich geen zorgen te maken over eventuele problemen met uw productiedatabase. Als u uw ontwikkelomgeving wilt afstemmen op de Azure-omgeving kunt u in plaats daarvan een Postgres-database lokaal gebruiken.

Typ Ctrl+C om de Django-server te stoppen.

### <a name="update-the-app"></a>De app bijwerken

Omdat we alleen willen bekijken hoe het maken van app-updates werkt, brengen we een kleine wijziging aan in `polls/models.py`. Zoek de regel:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

Wijzig dit in het volgende:

```python
choice_text = models.CharField(max_length=100)
```

Door het gegevensmodel te wijzigen, moet u een nieuwe Django-migratie maken. Gebruik hiervoor de volgende opdracht:

```
python manage.py makemigrations
```

U kunt uw wijzigingen lokaal testen door migraties uit te voeren, de ontwikkelserver uit te voeren en naar *http:\//localhost:8000/admin* te navigeren:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Code opnieuw implementeren naar Azure

Als u de wijzigingen opnieuw wilt implementeren, voert u de volgende opdracht uit vanuit de hoofdmap van de opslagplaats:

```azurecli
az webapp up
```

App Service detecteert dat de app bestaat en implementeert de code.

### <a name="rerun-migrations-in-azure"></a>Migraties opnieuw uitvoeren in Azure

Omdat u wijzigingen hebt aangebracht in het gegevensmodel, moet u databasemigraties opnieuw uitvoeren in App Service. Open een SSH-sessie in de browser door naar *https://\<app-name>.scm.azurewebsites.net/webssh/host* te navigeren. Voer de volgende opdrachten uit:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Apps in productie controleren

Blader naar *http:\//\<app-name>.azurewebsites.net* en zie hoe de wijzigingen live in productie worden uitgevoerd. 

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

U hebt vanuit de container toegang tot de consolelogboeken.

> [!TIP]
> Met `az webapp up` worden de standaardlogboeken voor u ingeschakeld. In verband met de prestaties worden deze logboeken na bepaalde tijd vanzelf uitgeschakeld, maar ze worden steeds weer ingeschakeld wanneer u `az webapp up` opnieuw uitvoert. Voer de volgende opdracht uit om de logboeken handmatig in te schakelen:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Voer de volgende Azure CLI-opdracht uit om de logboekstream weer te geven:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.

## <a name="manage-your-app-in-the-azure-portal"></a>De app beheren in Azure Portal

Zoek in [Azure Portal](https://portal.azure.com) naar de app die u hebt gemaakt en selecteer deze.

![Naar uw Python Django-app navigeren in Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

In de portal wordt standaard de pagina **Overzicht** van de app weergegeven. Deze pagina geeft u een overzicht van hoe uw app presteert. Hier kunt u ook algemene beheertaken uitvoeren zoals bladeren, stoppen, opnieuw starten en verwijderen. De tabbladen aan de linkerkant van de pagina tonen de verschillende configuratiepagina's die u kunt openen.

![Uw Python Django-app beheren op de overzichtspagina in Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u denkt dat u deze resources niet meer gaat gebruiken, verwijdert u de resourcegroepen door de volgende opdrachten uit te voeren:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure Database for PostgreSQL-database maken
> * Code implementeren naar Azure App Service en koppelen aan Postgres
> * Uw code bijwerken en opnieuw implementeren
> * Diagnostische logboeken weergeven
> * De web-app in Azure Portal beheren

Ga naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen:

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepaste DNS-naam aan uw app toewijzen](../app-service-web-tutorial-custom-domain.md)

U kunt ook andere resources bekijken:

> [!div class="nextstepaction"]
> [Python-app configureren](how-to-configure-python.md)

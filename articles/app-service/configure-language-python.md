---
title: Linux Python-apps configureren
description: Ontdek hoe u een vooraf gebouwde Python-container voor uw app configureert. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: 88a3876757c3f51d1fc70692976987560ee1b991
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961632"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Een Linux Python-app voor Azure App Service configureren

In dit artikel wordt beschreven hoe Python-apps worden uitgevoerd in [Azure App Service](overview.md) en hoe u het gedrag van Azure App Service zo nodig kunt aanpassen. Python-apps moet worden geïmplementeerd met alle vereiste [pip](https://pypi.org/project/pip/)-modules.

Met de implementatie-engine van App Service wordt automatisch een virtuele omgeving geactiveerd waarin `pip install -r requirements.txt` voor u wordt uitgevoerd wanneer u een [Git-opslagplaats](deploy-local-git.md) implementeert, of een [Zip-pakket](deploy-zip.md) waarvoor buildprocessen zijn ingeschakeld.

In deze handleiding vindt u belangrijke concepten en instructies voor Python-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, moet u eerst de [Python-quickstart](quickstart-python.md) en de [zelfstudie Python met PostgreSQL](tutorial-python-postgresql-app.md) volgen.

> [!NOTE]
> Linux is momenteel de aanbevolen optie voor het uitvoeren van Python-apps in App Service. Zie [Python in de Windows-versie van App Service](/visualstudio/python/managing-python-on-azure-app-service) voor informatie over de Windows-optie.
>

## <a name="show-python-version"></a>Python-versie weergeven

Als u de huidige versie van Python wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle de ondersteunde versies van Python wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

U kunt een niet-ondersteunde versie van Python uitvoeren door in plaats daarvan uw eigen containerinstallatiekopie te bouwen. Voor meer informatie raadpleegt u [Een aangepaste Docker-installatiekopie gebruiken](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-python-version"></a>De Python-versie instellen

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com) om de versie van Python in te stellen op 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>De automatisering van bouwbewerkingen aanpassen

Als u uw app wilt implementeren met behulp van Git of zip-pakketten waarbij bouwautomatisering is ingeschakeld, moet u de volgende stappen voor de App Service-bouwautomatisering in deze volgorde uitvoeren:

1. Voer aangepast script uit als dit door `PRE_BUILD_SCRIPT_PATH` is opgegeven.
1. Voer `pip install -r requirements.txt` uit.
1. Als *manage.py* wordt aangetroffen in de hoofdmap van de opslagplaats, voert u *manage.py collectstatic* uit. Als `DISABLE_COLLECTSTATIC` echter is ingesteld op `true`, kunt u deze stap overslaan.
1. Voer aangepast script uit als dit is opgegeven door `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND` en `DISABLE_COLLECTSTATIC` zijn omgevingsvariabelen die standaard leeg zijn. Als u vooraf gebouwde opdrachten wilt uitvoeren, definieert u `PRE_BUILD_COMMAND`. Als u achteraf gebouwde opdrachten wilt uitvoeren, definieert u `POST_BUILD_COMMAND`. Als u het uitvoeren van collectstatic wilt uitschakelen wanneer u Django-apps bouwt, stelt u `DISABLE_COLLECTSTATIC=true` in.

In het volgende voorbeeld worden de twee variabelen voor een reeks opdrachten opgegeven, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) voor aanvullende omgevingsvariabelen om bouwautomatisering aan te passen.

Zie voor meer informatie over de manier waarop Python-apps door App Service worden uitgevoerd en gebouwd in Linux [Oryx-documentatie: Hoe Python-apps worden gedetecteerd en gebouwd](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

## <a name="container-characteristics"></a>Containerkenmerken

Python-apps die zijn geïmplementeerd in App Service met Linux worden uitgevoerd binnen een Docker-container die is gedefinieerd in de [GitHub-opslagplaats voor App Service Python](https://github.com/Azure-App-Service/python). U vindt de afbeeldingsconfiguraties binnen de versiespecifieke mappen.

Deze container heeft de volgende kenmerken:

- Apps worden uitgevoerd met behulp van de [WSGI HTTP-server Gunicorn](https://gunicorn.org/), met de aanvullende argumenten `--bind=0.0.0.0 --timeout 600`.

- Standaard bevat de basisinstallatiekopie het Flask-webframework, maar de container ondersteunt andere frameworks die voldoen aan WSGI en compatibel zijn met Python 3.7, zoals Django.

- Voor het installeren van extra pakketten, zoals Django, maakt u met `pip freeze > requirements.txt` een bestand [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) in de hoofdmap van uw project. Daarna publiceert u het project naar App Service met Git-implementatie. `pip install -r requirements.txt` wordt dan automatisch uitgevoerd in de container om de afhankelijkheden van uw app te installeren.

## <a name="container-startup-process"></a>Opstartproces met container

Tijdens het opstarten voert de App Service met Linux-container de volgende stappen uit:

1. Gebruik een [aangepaste opstartopdracht](#customize-startup-command) als deze er is.
2. Controleer of er een [Django-app](#django-app) bestaat en start Gunicorn voor deze app als de app is gedetecteerd.
3. Controleer of er een [Flask-app](#flask-app) bestaat en start Gunicorn voor deze app als de app is gedetecteerd.
4. Als er geen andere app wordt gevonden, start u een standaard-app die is ingebouwd in de container.

De volgende secties bevatten aanvullende informatie voor elke optie.

### <a name="django-app"></a>Django-app

Voor Django-apps zoekt App Service in uw app-code naar een bestand met de naam `wsgi.py`. Daarna wordt Gunicorn uitgevoerd met de volgende opdracht:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Voor gedetailleerde controle over de opstartopdracht gebruikt u een [aangepaste opstartopdracht](#customize-startup-command) en vervangt u `<module>` door de naam van de module die *wsgi.py* bevat.

### <a name="flask-app"></a>Flask-app

Voor Flask zoekt App Service naar een bestand met de naam *application.py* of *app.py* en wordt Gunicorn als volgt gestart:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Als de hoofdmodule van de app in een ander bestand is opgenomen, gebruikt u een andere naam voor het app-object. Als u aanvullende argumenten wilt doorgeven aan Gunicorn, gebruikt u een [aangepaste opstartopdracht](#customize-startup-command).

### <a name="default-behavior"></a>Standaardgedrag

Als de App Service geen aangepaste opdracht, Django-app of Flask-app vindt, wordt er een standaard alleen-lezen-app uitgevoerd. Deze bevindt zich in de map _opt/defaultsite_. De standaard-app wordt als volgt weergegeven:

![Standaard App Service op Linux-webpagina](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>Opstartopdracht aanpassen

U kunt het opstartgedrag van de container sturen door een aangepaste Gunicorn-opstartopdracht te geven. Hiervoor moet u de volgende opdracht in de [Cloud Shell](https://shell.azure.com) uitvoeren:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Als u bijvoorbeeld een Flask-app hebt waarvan *hello.py* de belangrijkste module is en het Flask-app-object in dit bestand heet `myapp`, ziet *\<custom-command>* er als volgt uit:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Als de belangrijkste module zich in een submap bevindt, zoals `website`, geeft u deze map op met het argument `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

U kunt ook aanvullende argumenten voor Gunicorn aan *\<custom-command>* toevoegen, zoals `--workers=4`. Zie voor meer informatie [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Gunicorn uitvoeren, docs.gunicorn.org).

Als u een niet-Gunicorn-server wilt gebruiken, zoals [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), kunt u *\<custom-command>* vervangen door bijvoorbeeld:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> App Service negeert eventuele fouten die optreden tijdens de verwerking van een bestand met een aangepaste opstartopdracht en vervolgt het opstartproces door te zoeken naar Django- en Flask-apps. Als u het verwachte gedrag niet ziet, controleert u of uw opstartbestand is toegepast op App Service en of het geen fouten bevat.

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md#configure-app-settings) buiten uw app-code. Vervolgens kunt u ze openen met behulp van het standaardpatroon [os.environ](https://docs.python.org/3/library/os.html#os.environ). Voor toegang tot bijvoorbeeld de app-instelling `WEBSITE_SITE_NAME` gebruikt u de volgende code:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Populaire webframeworks bieden toegang tot de `X-Forwarded-*`-informatie in het patroon van de standaard-app. In [CodeIgniter](https://codeigniter.com/) wordt met [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) standaard de waarde van `X_FORWARDED_PROTO` gecontroleerd.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie in de browser openen

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

- **U ziet de standaard-app nadat de code van uw eigen app is toegepast.** De standaard-app wordt weergegeven omdat u de code van uw app niet hebt toegepast op App Service, of omdat App Service uw app-code niet heeft kunnen vinden en daarom de standaard-app heeft uitgevoerd.
- Start de App Service opnieuw op en wacht 15-20 seconden voordat u de app opnieuw controleert.
- Zorg ervoor dat u App Service voor Linux gebruikt in plaats van een Windows-exemplaar. Voer vanuit de Azure CLI de opdracht `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind` uit, waarbij u `<resource_group_name>` en `<app_service_name>` dienovereenkomstig vervangt. Als het goed is, ziet u `app,linux` als uitvoer. Als dit niet het geval is, maakt u de App Service opnieuw en kiest u Linux.
- Gebruik SSH of de Kudu-console om rechtstreeks verbinding te maken met de App Service en controleer of uw bestanden in *site/wwwroot* staan. Als uw bestanden niet bestaan, controleert u uw implementatieproces en implementeert u de app opnieuw.
- Als uw bestanden bestaan, heeft App Service uw specifieke opstartbestand niet kunnen identificeren. Controleer of de app is gestructureerd zoals App Service dat verwacht voor [Django](#django-app) of [Flask](#flask-app), of gebruik een [aangepaste opstartopdracht](#customize-startup-command).
- **U ziet het bericht 'Service niet beschikbaar' in de browser.** De browser heeft een time-out gegenereerd in afwachting van een reactie van App Service. Dat betekent dat de App Service de Gunicorn-server heeft gestart, maar dat de argumenten die de app-code opgeeft onjuist zijn.
- Vernieuw de browser, met name als u gebruikmaakt van de laagste prijscategorieën in uw App Service-plan. Het is bijvoorbeeld mogelijk dat het opstarten van de app langer duurt wanneer gebruik wordt gemaakt van de gratis prijscategorie en reageert na het vernieuwen van de browser.
- Controleer of de app is gestructureerd zoals App Service dat verwacht voor [Django](#django-app) of [Flask](#flask-app), of gebruik een [aangepaste opstartopdracht](#customize-startup-command).
- [Open de logboekstream](#access-diagnostic-logs).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Python-app met PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Implementeren vanuit een privécontaineropslagplaats](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](faq-app-service-linux.md)
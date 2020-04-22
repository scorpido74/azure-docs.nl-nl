---
title: PHP-apps configureren
description: Meer informatie over het configureren van een vooraf gebouwde PHP-container voor uw app. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758872"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Een Linux PHP-app configureren voor Azure App Service

In deze handleiding ziet u hoe u de ingebouwde PHP-runtime configureert voor web-apps, mobiele back-ends en API-apps in Azure App Service.

Deze handleiding biedt belangrijke concepten en instructies voor PHP-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [PHP quickstart](quickstart-php.md) en [PHP met MySQL-zelfstudie.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>PHP-versie weergeven

Als u de huidige PHP-versie wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com)om alle ondersteunde PHP-versies weer te geven:

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP-versie instellen

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com) om de PHP-versie in te stellen op 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Build-automatisering aanpassen

Als u uw app implementeert met Git- of zip-pakketten waarbij buildautomatisering is ingeschakeld, worden de automatiseringsstappen van de App-service door de volgende reeks doorlopen:

1. Aangepaste script uitvoeren `PRE_BUILD_SCRIPT_PATH`als dit is opgegeven door .
1. Voer `php composer.phar install` uit.
1. Aangepaste script uitvoeren `POST_BUILD_SCRIPT_PATH`als dit is opgegeven door .

`PRE_BUILD_COMMAND`en `POST_BUILD_COMMAND` zijn standaard lege omgevingsvariabelen. Als u pre-build-opdrachten `PRE_BUILD_COMMAND`wilt uitvoeren, definieert u . Als u opdrachten na het `POST_BUILD_COMMAND`bouwen wilt uitvoeren, definieert u .

In het volgende voorbeeld worden de twee variabelen opgeeft aan een reeks opdrachten, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)voor extra omgevingsvariabelen om de buildautomatisering aan te passen.

Zie [Oryx-documentatie: Hoe PHP-apps worden gedetecteerd en gebouwd voor](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)meer informatie over hoe App Service PHP-apps in Linux draait en bouwt.

## <a name="customize-start-up"></a>Opstarten aanpassen

Standaard draait de ingebouwde PHP-container de Apache-server. Bij het opstarten loopt `apache2ctl -D FOREGROUND"`het. Als u wilt, u een andere opdracht uitvoeren bij het opstarten, door de volgende opdracht in de [Cloud Shell](https://shell.azure.com)uit te voeren:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App-service u [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Dan u toegang tot hen met behulp van de standaard [getenv()](https://secure.php.net/manual/function.getenv.php) patroon. Voor toegang tot bijvoorbeeld de app-instelling `DB_HOST` gebruikt u de volgende code:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Siteroot wijzigen

Het webframework van uw keuze kan een submap gebruiken als hoofdmap. [Laravel](https://laravel.com/)gebruikt bijvoorbeeld de `public/` submap als hoofdmap.

De standaard PHP-afbeelding voor App-service maakt gebruik van Apache en u de siteroot voor uw app niet aanpassen. Als u deze beperking wilt omzeilen, voegt u een *.htaccess-bestand* toe aan uw root met de volgende inhoud:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Als u echter liever niet *.htaccess* herschrijft, kunt u uw Laravel-toepassing in plaats daarvan met een [aangepaste Docker-installatiekopie](quickstart-docker-go.md) implementeren.

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Populaire webframeworks bieden toegang tot de `X-Forwarded-*`-informatie in het patroon van de standaard-app. In [CodeIgniter](https://codeigniter.com/) wordt met [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) standaard de waarde van `X_FORWARDED_PROTO` gecontroleerd.

## <a name="customize-phpini-settings"></a>Php.ini-instellingen aanpassen

Als u wijzigingen in uw PHP-installatie moet aanbrengen, u een van de [php.ini-richtlijnen](https://www.php.net/manual/ini.list.php) wijzigen door deze stappen te volgen.

> [!NOTE]
> De beste manier om de PHP-versie en de huidige *php.ini-configuratie* te zien, is door [phpinfo()](https://php.net/manual/function.phpinfo.php) in uw app te bellen.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Richtlijnen aanpassen-niet-PHP_INI_SYSTEM

Als u PHP_INI_USER, PHP_INI_PERDIR en PHP_INI_ALL richtlijnen (zie [php.ini-richtlijnen)](https://www.php.net/manual/ini.list.php)wilt aanpassen, voegt u een *.htaccess-bestand* toe aan de hoofdmap van uw app.

Voeg in het *bestand .htaccess* `php_value <directive-name> <value>` de richtlijnen toe met behulp van de syntaxis. Bijvoorbeeld:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Implementeer uw app opnieuw met de wijzigingen en start deze opnieuw op. Als u deze implementeert met Kudu (bijvoorbeeld met [Git),](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)wordt deze automatisch opnieuw gestart na implementatie.

Als alternatief voor het gebruik van *.htaccess*u [ini_set()](https://www.php.net/manual/function.ini-set.php) in uw app gebruiken om deze niet-PHP_INI_SYSTEM richtlijnen aan te passen.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM richtlijnen aanpassen

Om PHP_INI_SYSTEM richtlijnen aan te passen (zie [php.ini richtlijnen),](https://www.php.net/manual/ini.list.php)u geen gebruik maken van de *.htaccess* aanpak. App Service biedt een `PHP_INI_SCAN_DIR` apart mechanisme met behulp van de app-instelling.

Voer eerst de volgende opdracht uit in de `PHP_INI_SCAN_DIR`Cloud [Shell](https://shell.azure.com) om een app-instelling toe te voegen die de volgende opdracht geeft:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`is de standaardmap waar *php.ini* bestaat. `/home/site/ini`is de aangepaste map waarin u een aangepast *.ini-bestand* toevoegt. U scheidt de `:`waarden met een .

Navigeer naar de SSH-sessie op`https://<app-name>.scm.azurewebsites.net/webssh/host`het web met uw Linux-container ( ).

Maak een `/home/site` aangeroepen `ini`map en maak vervolgens `/home/site/ini` een *.ini-bestand* in de map (bijvoorbeeld *settings.ini)* met de richtlijnen die u wilt aanpassen. Gebruik dezelfde syntaxis die u zou gebruiken in een *php.ini-bestand.* 

> [!TIP]
> In de ingebouwde Linux-containers in App Service wordt */home* gebruikt als aanhoudende gedeelde opslag. 
>

Als u bijvoorbeeld de waarde van [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) de volgende opdrachten wilt wijzigen:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Start de app opnieuw op om de wijzigingen van kracht te laten worden.

## <a name="enable-php-extensions"></a>PHP-extensies inschakelen

De ingebouwde PHP-installaties bevatten de meest gebruikte extensies. U extra extensies inschakelen op dezelfde manier als [bij php.ini-richtlijnen.](#customize-php_ini_system-directives)

> [!NOTE]
> De beste manier om de PHP-versie en de huidige *php.ini-configuratie* te zien, is door [phpinfo()](https://php.net/manual/function.phpinfo.php) in uw app te bellen.
>

Als u extra extensies wilt inschakelen, volgt u de volgende stappen:

Voeg `bin` een map toe aan de hoofdmap van uw app en plaats de `.so` extensiebestanden erin (bijvoorbeeld *mongodb.so).* Zorg ervoor dat de extensies compatibel zijn met de PHP-versie in Azure en VC9- en niet-thread-safe (nts) compatibel zijn.

Implementeer uw wijzigingen.

Volg de stappen in [PHP_INI_SYSTEM richtlijnen aanpassen,](#customize-php_ini_system-directives)voeg de extensies toe aan het aangepaste *.ini-bestand* met de [extensie-](https://www.php.net/manual/ini.core.php#ini.extension) of [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) richtlijnen.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Start de app opnieuw op om de wijzigingen van kracht te laten worden.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in de browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werkende PHP-app zich anders gedraagt in App Service of fouten heeft, probeert u het volgende:

- [Toegang tot de logstream](#access-diagnostic-logs).
- Test de app lokaal in de productiemodus. App Service voert uw Node.js-apps uit in de productiemodus, dus u moet ervoor zorgen dat uw project werkt zoals verwacht in de productiemodus lokaal. Bijvoorbeeld:
    - Afhankelijk van uw *composer.json*kunnen verschillende pakketten worden`require` geïnstalleerd `require-dev`voor de productiemodus (vs. ).
    - Bepaalde webframeworks kunnen statische bestanden anders implementeren in de productiemodus.
    - Bepaalde webframeworks kunnen aangepaste opstartscripts gebruiken wanneer ze in de productiemodus worden uitgevoerd.
- Voer uw app uit in App Service in de foutopsporingsmodus. In [Laravel](https://meanjs.org/)u bijvoorbeeld uw app configureren om foutopsporingsberichten in productie uit te zetten door [de `APP_DEBUG` app-instelling in `true`te stellen op ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: PHP-app met MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)

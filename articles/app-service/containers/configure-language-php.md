---
title: PHP-apps configureren
description: Meer informatie over het configureren van een vooraf gemaakte PHP-container voor uw app. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e4237f1eecb9f6542aac946525ff4583e478c2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905694"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Een Linux PHP-app voor Azure App Service configureren

In deze hand leiding wordt beschreven hoe u de ingebouwde PHP-runtime voor web-apps, mobiele back-ends en API apps in Azure App Service kunt configureren.

Deze hand leiding bevat belang rijke concepten en instructies voor PHP-ontwikkel aars die een ingebouwde Linux-container gebruiken in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [php Quick](quickstart-php.md) start en [php met MySQL zelf studie](tutorial-php-mysql-app.md) .

## <a name="show-php-version"></a>PHP-versie weer geven

Als u de huidige PHP-versie wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle ondersteunde PHP-versies wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP-versie instellen

Voer de volgende opdracht uit in het [Cloud shell](https://shell.azure.com) om de PHP-versie in te stellen op 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>De automatisering van bouwbewerkingen aanpassen

Als u uw app wilt implementeren met behulp van Git of zip-pakketten waarbij bouwautomatisering is ingeschakeld, moet u de volgende stappen voor de App Service-bouwautomatisering in deze volgorde uitvoeren:

1. Voer aangepast script uit als dit door `PRE_BUILD_SCRIPT_PATH` is opgegeven.
1. Voer `php composer.phar install` uit.
1. Voer aangepast script uit als dit is opgegeven door `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND`en `POST_BUILD_COMMAND` zijn omgevings variabelen die standaard leeg zijn. Als u vooraf gebouwde opdrachten wilt uitvoeren, definieert u `PRE_BUILD_COMMAND`. Als u achteraf gebouwde opdrachten wilt uitvoeren, definieert u `POST_BUILD_COMMAND`.

In het volgende voorbeeld worden de twee variabelen voor een reeks opdrachten opgegeven, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) voor aanvullende omgevingsvariabelen om bouwautomatisering aan te passen.

Zie [Oryx documentation](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)(Engelstalig) voor meer informatie over de manier waarop app service php-apps in Linux uitvoert en bouwt.

## <a name="customize-start-up"></a>Opstarten aanpassen

De ingebouwde PHP-container voert standaard de Apache-server uit. Bij het opstarten wordt het uitgevoerd `apache2ctl -D FOREGROUND"` . Als u wilt, kunt u tijdens het opstarten een andere opdracht uitvoeren door de volgende opdracht uit te voeren in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code. Vervolgens kunt u ze openen met het standaard [getenv ()-](https://secure.php.net/manual/function.getenv.php) patroon. Voor toegang tot bijvoorbeeld de app-instelling `DB_HOST` gebruikt u de volgende code:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Hoofdmap van site wijzigen

Het webframework van uw keuze kan een submap gebruiken als hoofdmap van de site. Voor beeld: [Laravel](https://laravel.com/)gebruikt de `public/` submap als hoofdmap van de site.

De standaard PHP-installatie kopie voor App Service gebruikt Apache en het is niet mogelijk om de hoofdmap van de site voor uw app aan te passen. U kunt deze beperking omzeilen door een *. htaccess* -bestand toe te voegen aan de hoofdmap van de opslag plaats met de volgende inhoud:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
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

## <a name="customize-phpini-settings"></a>php.ini-instellingen aanpassen

Als u wijzigingen moet aanbrengen in uw PHP-installatie, kunt u een van de [php.ini-instructies](https://www.php.net/manual/ini.list.php) wijzigen door de volgende stappen uit te voeren.

> [!NOTE]
> De beste manier om de PHP-versie en de huidige *php.ini* configuratie te bekijken is door [phpinfo ()](https://php.net/manual/function.phpinfo.php) aan te roepen in uw app.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Aanpassen-niet-PHP_INI_SYSTEM-instructies

Als u PHP_INI_USER-, PHP_INI_PERDIR-en PHP_INI_ALL-instructies wilt aanpassen (Zie [php.ini-instructies](https://www.php.net/manual/ini.list.php)), voegt u een *. htaccess* -bestand toe aan de hoofdmap van uw app.

Voeg in het *. htaccess* -bestand de instructies toe met behulp van de `php_value <directive-name> <value>` syntaxis. Bijvoorbeeld:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Implementeer uw app opnieuw met de wijzigingen en start deze opnieuw. Als u het implementeert met kudu (bijvoorbeeld met [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), wordt het automatisch opnieuw opgestart na de implementatie.

Als alternatief voor het gebruik van *. htaccess*kunt u [ini_set ()](https://www.php.net/manual/function.ini-set.php) in uw app gebruiken om deze niet-PHP_INI_SYSTEM-instructies aan te passen.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM-instructies aanpassen

Als u PHP_INI_SYSTEM-instructies wilt aanpassen (Zie [php.ini-instructies](https://www.php.net/manual/ini.list.php)), kunt u de methode *. htaccess* niet gebruiken. App Service biedt een afzonderlijk mechanisme voor het gebruik van de `PHP_INI_SCAN_DIR` app-instelling.

Voer eerst de volgende opdracht uit in het [Cloud shell](https://shell.azure.com) om een toepassings instelling toe te voegen `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`is de standaardmap waarin *php.ini* bestaat. `/home/site/ini`is de aangepaste map waarin u een aangepast *. ini* -bestand toevoegt. U scheidt de waarden met een `:` .

Ga naar de Web SSH-sessie met uw Linux-container ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Maak een map in `/home/site` `ini` de naam en maak vervolgens een *. ini* -bestand in de `/home/site/ini` map (bijvoorbeeld *settings.ini)* met de instructies die u wilt aanpassen. Gebruik de syntaxis die u in een *php.ini* -bestand zou gebruiken. 

> [!TIP]
> In de ingebouwde Linux-containers in App Service wordt */Home* gebruikt als permanente gedeelde opslag. 
>

Als u bijvoorbeeld de waarde van [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) wilt wijzigen, voert u de volgende opdrachten uit:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Start de app opnieuw op om de wijzigingen van kracht te laten worden.

## <a name="enable-php-extensions"></a>PHP-uitbrei dingen inschakelen

De ingebouwde PHP-installaties bevatten de meest gebruikte uitbrei dingen. U kunt extra uitbrei dingen op dezelfde manier inschakelen als [php.ini-instructies](#customize-php_ini_system-directives).

> [!NOTE]
> De beste manier om de PHP-versie en de huidige *php.ini* configuratie te bekijken is door [phpinfo ()](https://php.net/manual/function.phpinfo.php) aan te roepen in uw app.
>

Als u extra uitbrei dingen wilt inschakelen, volgt u deze stappen:

Voeg een `bin` map toe aan de hoofdmap van uw app en plaats de `.so` extensie bestanden in de map (bijvoorbeeld *MongoDb.so*). Zorg ervoor dat de uitbrei dingen compatibel zijn met de PHP-versie in Azure en dat deze compatibel zijn met VC9 en niet-thread-safe (NTS).

Implementeer uw wijzigingen.

Volg de stappen in [customize PHP_INI_SYSTEM-instructies](#customize-php_ini_system-directives), voeg de uitbrei dingen toe aan het aangepaste *. ini* -bestand met de [extensie](https://www.php.net/manual/ini.core.php#ini.extension) of [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) -instructies.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Start de app opnieuw op om de wijzigingen van kracht te laten worden.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie in de browser openen

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werkende PHP-app zich op een andere manier gedraagt in App Service of fouten bevat, kunt u het volgende proberen:

- [Open de logboekstream](#access-diagnostic-logs).
- Test de app lokaal in de productie modus. App Service wordt uw app in productie modus uitgevoerd. u moet er dus voor zorgen dat uw project in de productie modus lokaal werkt zoals verwacht. Bijvoorbeeld:
    - Afhankelijk van uw *composer.jsop*kunnen verschillende pakketten worden geïnstalleerd voor de productie modus ( `require` VS. `require-dev` ).
    - Bepaalde web Frameworks kunnen statische bestanden in de productie modus anders implementeren.
    - Bepaalde web Frameworks kunnen aangepaste opstart scripts gebruiken wanneer ze in de productie modus worden uitgevoerd.
- Voer uw app uit in App Service in de foutopsporingsmodus. In [Laravel](https://meanjs.org/)kunt u bijvoorbeeld uw app configureren voor het uitvoeren van debug-berichten in de productie door [de `APP_DEBUG` app-instelling `true` in te stellen op ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: PHP-app met MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)

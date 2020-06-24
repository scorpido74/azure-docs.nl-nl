---
title: Windows PHP-apps configureren
description: Meer informatie over het configureren van een PHP-app in de systeem eigen Windows-exemplaren van App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908089"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>Een Windows PHP-app voor Azure App Service configureren

In deze hand leiding wordt uitgelegd hoe u uw PHP-web-apps, mobiele back-ends en API apps in Azure App Service kunt configureren. Deze hand leiding is van toepassing op apps die worden gehost op het Windows-platform. Zie [Configure a Linux PHP app for Azure app service](containers/configure-language-php.md)(Engelstalig) voor meer informatie over Linux-apps.

Deze hand leiding bevat belang rijke concepten en instructies voor PHP-ontwikkel aars die apps implementeren op App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [php Quick](app-service-web-get-started-php.md) start en [php met MySQL zelf studie](app-service-web-tutorial-php-mysql.md) .

## <a name="show-php-version"></a>PHP-versie weer geven

Als u de huidige PHP-versie wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Als u alle ondersteunde PHP-versies wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>PHP-versie instellen

Voer de volgende opdracht uit in het [Cloud shell](https://shell.azure.com) om de PHP-versie in te stellen op 7,4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

## <a name="run-composer"></a>Componist uitvoeren

Als u wilt dat App Service [Composer](https://getcomposer.org/) uitvoert tijdens de implementatie, is de eenvoudigste manier om de Composer in uw opslag plaats te gebruiken.

Ga vanuit een lokaal Terminal venster naar de hoofdmap van uw opslag plaats en volg de instructies bij [down load Composer](https://getcomposer.org/download/) om *Composer. Phar* te downloaden naar de hoofdmap van de map.

Voer de volgende opdrachten uit (u hebt [NPM](https://www.npmjs.com/get-npm) geïnstalleerd):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

De hoofdmap van uw opslag plaats heeft nu twee extra bestanden: *. Deployment* en *Deploy.sh*.

Open *Deploy.sh* en zoek de `Deployment` sectie, die er als volgt uitziet:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Voeg de sectie code toe die u nodig hebt om het vereiste hulp programma uit te voeren *aan het einde* van de `Deployment` sectie:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Leg al uw wijzigingen door en implementeer uw code met git of zip-implementatie met Build Automation ingeschakeld. Componist moet nu worden uitgevoerd als onderdeel van implementatie automatisering.

## <a name="run-gruntbowergulp"></a>Voer grunt/Bower/Gulp uit

Als u wilt dat App Service populaire Automation-hulpprogram ma's uitvoert op het moment van implementatie, zoals grunt, Bower of Gulp, moet u een [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)opgeven. App Service voert dit script uit wanneer u implementeert met git of met een [zip-implementatie](deploy-zip.md) waarbij build Automation is ingeschakeld. 

Als u de opslag plaats wilt inschakelen om deze hulpprogram ma's uit te voeren, moet u deze toevoegen aan de afhankelijkheden in *package.jsop.* Bijvoorbeeld:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Wijzig vanuit een lokaal Terminal venster de map in de hoofdmap van uw opslag plaats en voer de volgende opdrachten uit (u hebt [NPM](https://www.npmjs.com/get-npm) geïnstalleerd):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

De hoofdmap van uw opslag plaats heeft nu twee extra bestanden: *. Deployment* en *Deploy.sh*.

Open *Deploy.sh* en zoek de `Deployment` sectie, die er als volgt uitziet:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Deze sectie eindigt met het uitvoeren van `npm install --production` . Voeg de sectie code toe die u nodig hebt om het vereiste hulp programma uit te voeren *aan het einde* van de `Deployment` sectie:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Bekijk een [voor beeld in het MEAN.js-voor beeld](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), waarbij het implementatie script ook een aangepaste `npm install` opdracht uitvoert.

### <a name="bower"></a>Bower

Dit code fragment wordt uitgevoerd `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Dit code fragment wordt uitgevoerd `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Dit code fragment wordt uitgevoerd `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md#configure-app-settings) buiten uw app-code. Vervolgens kunt u ze openen met het standaard [getenv ()-](https://secure.php.net/manual/function.getenv.php) patroon. Voor toegang tot bijvoorbeeld de app-instelling `DB_HOST` gebruikt u de volgende code:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Hoofdmap van site wijzigen

Het webframework van uw keuze kan een submap gebruiken als hoofdmap van de site. Zo gebruikt [Laravel](https://laravel.com/)de *open bare/* submap als de hoofdmap van de site.

Als u de hoofdmap van de site wilt aanpassen, stelt u het pad van de virtuele toepassing voor de app in met behulp van de [`az resource update`](/cli/azure/resource#az-resource-update) opdracht. In het volgende voor beeld wordt de hoofdmap van de site ingesteld op de *open bare/* submap in uw opslag plaats. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Azure App Service wijst standaard het pad naar de virtuele hoofdmap _/_ van de toepassing () naar de hoofdmap van de geïmplementeerde toepassings bestanden (_sites\wwwroot_).

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

Als u PHP_INI_USER-, PHP_INI_PERDIR-en PHP_INI_ALL-instructies wilt aanpassen (Zie [php.ini-instructies](https://www.php.net/manual/ini.list.php)), voegt u een `.user.ini` bestand toe aan de hoofdmap van uw app.

Voeg configuratie-instellingen toe aan het `.user.ini` bestand met dezelfde syntaxis als in een `php.ini` bestand. Als u bijvoorbeeld de `display_errors` instelling wilt inschakelen en instellen `upload_max_filesize` op 10 miljoen, bevat het bestand de volgende `.user.ini` tekst:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Implementeer uw app opnieuw met de wijzigingen en start deze opnieuw.

Als alternatief voor het gebruik `.user.ini` van een bestand kunt u [ini_set ()](https://www.php.net/manual/function.ini-set.php) in uw app gebruiken om deze niet-PHP_INI_SYSTEM-instructies aan te passen.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM-instructies aanpassen

Als u PHP_INI_SYSTEM-instructies wilt aanpassen (Zie [php.ini-instructies](https://www.php.net/manual/ini.list.php)), kunt u de methode *. htaccess* niet gebruiken. App Service biedt een afzonderlijk mechanisme voor het gebruik van de `PHP_INI_SCAN_DIR` app-instelling.

Voer eerst de volgende opdracht uit in het [Cloud shell](https://shell.azure.com) om een toepassings instelling toe te voegen `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Ga naar de kudu-console ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) en navigeer naar `d:\home\site` .

Maak een map in `d:\home\site` `ini` de naam en maak vervolgens een *. ini* -bestand in de `d:\home\site\ini` map (bijvoorbeeld *settings.ini)* met de instructies die u wilt aanpassen. Gebruik de syntaxis die u in een *php.ini* -bestand zou gebruiken. 

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

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

Start de app opnieuw op om de wijzigingen van kracht te laten worden.

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

Gebruik het standaard hulp programma [error_log ()](https://php.net/manual/function.error-log.php) om ervoor te zorgen dat uw Diagnostische logboeken in azure app service worden weer gegeven.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werkende PHP-app zich op een andere manier gedraagt in App Service of fouten bevat, kunt u het volgende proberen:

- [Open de logboekstream](#access-diagnostic-logs).
- Test de app lokaal in de productie modus. App Service wordt uw app in productie modus uitgevoerd. u moet er dus voor zorgen dat uw project in de productie modus lokaal werkt zoals verwacht. Bijvoorbeeld:
    - Bepaalde web Frameworks kunnen statische bestanden in de productie modus anders implementeren.
    - Bepaalde web Frameworks kunnen aangepaste opstart scripts gebruiken wanneer ze in de productie modus worden uitgevoerd.
- Voer uw app uit in App Service in de foutopsporingsmodus. In [Laravel](https://meanjs.org/)kunt u bijvoorbeeld uw app configureren voor het uitvoeren van debug-berichten in de productie door [de `APP_DEBUG` app-instelling `true` in te stellen op ](configure-common.md#configure-app-settings).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: PHP-app met MySQL](app-service-web-tutorial-php-mysql.md)

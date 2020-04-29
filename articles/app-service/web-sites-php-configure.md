---
title: PHP-runtime configureren
description: Meer informatie over het configureren van de standaard PHP-installatie of het toevoegen van een aangepaste PHP-installatie voor Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272471"
---
# <a name="configure-php-in-azure-app-service"></a>PHP configureren in Azure App Service

## <a name="introduction"></a>Inleiding

In deze hand leiding wordt beschreven hoe u de ingebouwde PHP-runtime voor web-apps en API apps in [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714)kunt configureren, een aangepaste PHP-runtime kunt opgeven en uitbrei dingen kunt inschakelen. Als u App Service wilt gebruiken, meldt u zich aan voor de [gratis proef versie]. Als u optimaal wilt profiteren van deze hand leiding, moet u eerst een PHP-app maken in App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Procedure: de ingebouwde PHP-versie wijzigen

Wanneer u een web-app maakt, kunt u kiezen welke versie van PHP moet worden geconfigureerd. Raadpleeg [php on app service](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) voor actuele informatie over momenteel ondersteunde versies.

Als u de bestaande runtime versie van uw app wilt controleren, kunt u een script implementeren dat de functie [phpinfo ()] aanroept.

Ga op een van de volgende manieren te werk om de PHP-versie bij te werken:

### <a name="azure-portal"></a>Azure Portal

1. Blader naar uw app in de [Azure Portal](https://portal.azure.com) en schuif naar de pagina **configuratie** .

2. In **configuratie**selecteert u **algemene instellingen** en kiest u de nieuwe PHP-versie.

3. Klik boven aan de Blade **algemene instellingen** op de knop **Opslaan** .

### <a name="azure-cli"></a>Azure CLI 

Als u de Azure-opdracht regel interface wilt gebruiken, moet u [de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) op uw computer installeren.

1. Open Terminal en meld u aan bij uw account.

        az login

1. Controleer de lijst met ondersteunde Runtimes.

        az webapp list-runtimes | grep php

2. Stel de PHP-versie voor de app in.

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. De PHP-versie is nu ingesteld. U kunt deze instellingen bevestigen:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Procedure: de ingebouwde PHP-configuraties wijzigen

Voor elke ingebouwde PHP-runtime kunt u de configuratie opties wijzigen door de volgende stappen uit te voeren. (Zie de [lijst met PHP. ini]-instructies voor meer informatie over PHP. ini-instructies.)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Wijzigen van\_PHP\_ini User,\_PHP\_ini PERDIR,\_PHP\_ini alle configuratie-instellingen

1. Voeg een [. User. ini] -bestand toe aan de hoofdmap.
1. Voeg configuratie-instellingen toe `.user.ini` aan het bestand met dezelfde syntaxis als in een `php.ini` bestand. Als u bijvoorbeeld de `display_errors` instelling wilt inschakelen en instellen `upload_max_filesize` op 10 miljoen, bevat het bestand de `.user.ini` volgende tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implementeer uw app.
3. De app opnieuw starten. (Opnieuw opstarten is nood zakelijk omdat de frequentie waarmee PHP bestanden leest `.user.ini` , wordt bepaald door de `user_ini.cache_ttl` instelling, een systeem niveau-instelling en 300 seconden (5 minuten). Het opnieuw starten van de app dwingt PHP de nieuwe instellingen in het `.user.ini` bestand te lezen.)

Als alternatief voor het gebruik van `.user.ini` een bestand kunt u de functie [ini_set ()] gebruiken in scripts voor het instellen van configuratie opties die geen instructies op systeem niveau zijn.

### <a name="changing-php_ini_system-configuration-settings"></a>Configuratie-\_instellingen\_voor PHP ini-systeem wijzigen

1. Een app-instelling toevoegen aan uw app met de `PHP_INI_SCAN_DIR` sleutel en waarde`d:\home\site\ini`
1. Maak een `settings.ini` bestand met behulp van de&lt;Kudu-console&gt;(http://site-name `d:\home\site\ini` . scm.azurewebsite.net) in de Directory.
1. Voeg configuratie-instellingen toe `settings.ini` aan het bestand met dezelfde syntaxis als in een `php.ini` bestand. Als u bijvoorbeeld de instelling wilt laten wijzen naar `curl.cainfo` een `*.crt` bestand en de instelling ' wincache. MaxFileSize ' wilt instellen op 512 K, bevat `settings.ini` uw bestand deze tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Start uw app opnieuw op om de wijzigingen opnieuw te laden.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Procedure: uitbrei dingen inschakelen in de standaard PHP-runtime

Zoals u in de vorige sectie hebt genoteerd, is de beste manier om de standaard PHP-versie, de standaard configuratie en de ingeschakelde uitbrei dingen te bekijken om een script te implementeren dat [phpinfo ()]aanroept. Als u extra uitbrei dingen wilt inschakelen, volgt u deze stappen:

### <a name="configure-via-ini-settings"></a>Configureren via ini-instellingen

1. Voeg een `ext` map toe aan `d:\home\site` de Directory.
1. Extensie `.dll` bestanden in de `ext` map plaatsen (bijvoorbeeld `php_xdebug.dll`). Zorg ervoor dat de uitbrei dingen compatibel zijn met de standaard versie van PHP en dat deze compatibel zijn met VC9 en niet-thread-safe (NTS).
1. Een app-instelling toevoegen aan uw app met de `PHP_INI_SCAN_DIR` sleutel en waarde`d:\home\site\ini`
1. Maak een `ini` bestand met `d:\home\site\ini` de `extensions.ini`naam.
1. Voeg configuratie-instellingen toe `extensions.ini` aan het bestand met dezelfde syntaxis als in een `php.ini` bestand. Als u bijvoorbeeld de uitbrei dingen MongoDB en XDebug wilt inschakelen, bevat het `extensions.ini` bestand deze tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Start uw app opnieuw om de wijzigingen te laden.

### <a name="configure-via-app-setting"></a>Configureren via app-instelling

1. Voeg een `bin` map toe aan de hoofdmap.
2. Extensie `.dll` bestanden in de `bin` map plaatsen (bijvoorbeeld `php_xdebug.dll`). Zorg ervoor dat de uitbrei dingen compatibel zijn met de standaard versie van PHP en dat deze compatibel zijn met VC9 en niet-thread-safe (NTS).
3. Implementeer uw app.
4. Blader naar uw app in de Azure Portal en klik op de **configuratie** onder **instellingen** hieronder.
5. Selecteer **Toepassings instellingen**op de Blade **configuratie** .
6. Klik in de sectie **Toepassings instellingen** op **+ nieuwe toepassings instelling** en maak een **php_extensions** sleutel. De waarde voor deze sleutel is een pad dat relatief is ten opzichte van de hoofdmap van de website: **bin\your-ext-file**.
7. Klik op de knop **bijwerken** aan de onderkant en klik vervolgens op **Opslaan** boven het tabblad **Toepassings instellingen** .

Zend uitbreidingen worden ook ondersteund met behulp van een **PHP_ZENDEXTENSIONS** sleutel. Als u meerdere uitbrei dingen wilt inschakelen, voegt u een `.dll` door komma's gescheiden lijst met bestanden toe voor de waarde van de app-instelling.

## <a name="how-to-use-a-custom-php-runtime"></a>Procedure: een aangepaste PHP-runtime gebruiken

In plaats van de standaard PHP-runtime kunt App Service een PHP-runtime gebruiken die u biedt om PHP-scripts uit te voeren. De runtime die u opgeeft, kan worden geconfigureerd door `php.ini` een bestand dat u ook opgeeft. Als u een aangepaste PHP-runtime met App Service wilt gebruiken, volgt u deze stappen.

1. Verkrijg een niet-thread-safe, VC9 of VC11 compatibele versie van PHP voor Windows. Recente releases van PHP voor Windows vindt u hier: [https://windows.php.net/download/]. Oudere releases vindt u in het archief hier: [https://windows.php.net/downloads/releases/archives/].
2. Wijzig het `php.ini` bestand voor uw runtime. Configuratie-instellingen die alleen op systeem niveau zijn ingesteld, worden door App Service genegeerd. (Zie de [lijst met PHP. ini-instructies]) voor informatie over de instructies op systeem niveau.
3. Voeg eventueel uitbrei dingen toe aan uw PHP-runtime en schakel ze `php.ini` in het bestand in.
4. Voeg een `bin` map toe aan uw hoofdmap en plaats de map met daarin uw PHP-runtime (bijvoorbeeld `bin\php`).
5. Implementeer uw app.
6. Blader naar uw app in de Azure Portal en klik op de Blade **configuratie** .
8. Selecteer **paden toewijzen**op de Blade **configuratie** . 
9. Klik op **+ nieuwe handler** en `*.php` Voeg toe aan het veld extensie en voeg het pad `php-cgi.exe` naar het uitvoer bare bestand toe aan de **script processor**. Als u de PHP-runtime in de `bin` map in de hoofdmap van uw toepassing plaatst, is `D:\home\site\wwwroot\bin\php\php-cgi.exe`het pad.
10. Klik onderaan op **bijwerken** om het toevoegen van de handlertoewijzing te volt ooien.
11. Klik op **Opslaan** om de wijzigingen op te slaan.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Procedure: Composer automatisering in azure inschakelen

App Service doen niets met Composer. json, als u er een hebt in uw PHP-project. Als u [Git-implementatie](deploy-local-git.md)gebruikt, kunt u Composer. json- `git push` verwerking inschakelen tijdens door de componist-extensie in te scha kelen.

> [!NOTE]
> U kunt [stemmen voor de eerste klasse Composer ondersteuning in app service hier](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Klik in de Blade van de PHP-app in het [Azure Portal](https://portal.azure.com)op **extra** > **extensies**.

    ![Blade met Azure Portal instellingen voor het inschakelen van Composer Automation in azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klik op **toevoegen**en vervolgens op **Composer**.

    ![Composer-extensie toevoegen om Composer functie in Azure in te scha kelen](./media/web-sites-php-configure/composer-extension-add.png)
3. Klik op **OK** om de juridische voor waarden te accepteren. Klik nogmaals op **OK** om de extensie toe te voegen.

    Op de Blade **geïnstalleerde extensies** wordt de componist extensie weer gegeven.
    ![Juridische voor waarden accepteren voor het inschakelen van Composer-automatisering in azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Nu kunt u in een Terminal venster op uw lokale computer, `git add`, `git commit`en `git push` voor uw app uitvoeren. U ziet dat componist afhankelijkheden installeert die zijn gedefinieerd in Composer. json.

    ![Git-implementatie met Composer Automation in azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Volgende stappen

Zie het [PHP-ontwikkelaars centrum](https://azure.microsoft.com/develop/php/)voor meer informatie.

[gratis proefversie]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lijst met PHP. ini-instructies]: https://www.php.net/manual/en/ini.list.php
[. User. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png

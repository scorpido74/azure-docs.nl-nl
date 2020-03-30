---
title: PHP-runtime configureren
description: Meer informatie over het configureren van de standaard PHP-installatie of het toevoegen van een aangepaste PHP-installatie voor Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016796"
---
# <a name="configure-php-in-azure-app-service"></a>PHP configureren in Azure App Service

## <a name="introduction"></a>Inleiding

In deze handleiding ziet u hoe u de ingebouwde PHP-runtime configureert voor web-apps, mobiele back-ends en API-apps in [Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)een aangepaste PHP-runtime bieden en extensies inschakelen. Als u App Service wilt gebruiken, meldt u zich aan voor de [gratis proefversie.] Om het meeste uit deze handleiding te halen, moet u eerst een PHP-app maken in App Service.

## <a name="how-to-change-the-built-in-php-version"></a>How to: De ingebouwde PHP-versie wijzigen

Standaard is PHP 5.6 geïnstalleerd en direct beschikbaar voor gebruik bij het maken van een App Service-app. De beste manier om de beschikbare releaserevisie, de standaardconfiguratie en de ingeschakelde extensies te bekijken, is door een script te implementeren dat de functie [phpinfo()] aanroept.

PHP 7.0- en PHP 7.2-versies zijn ook beschikbaar, maar niet standaard ingeschakeld. Als u de PHP-versie wilt bijwerken, volgt u een van de volgende methoden:

### <a name="azure-portal"></a>Azure Portal

1. Blader naar uw app in de [Azure-portal](https://portal.azure.com) en blader naar de **configuratiepagina.**

2. Selecteer algemene **instellingen** in **Configuratie**en kies de nieuwe PHP-versie.

3. Klik op de knop **Opslaan** boven aan het blad **Algemene instellingen.**

### <a name="azure-cli"></a>Azure-CLI 

Als u de Azure Command Line-interface wilt gebruiken, moet u de Azure CLI op uw computer [installeren.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

1. Open Terminal en log in op uw account.

        az login

1. Controleer of de lijst met ondersteunde runtimes wordt weergegeven.

        az webapp list-runtimes | grep php

2. Stel de PHP-versie in voor de app.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. De PHP-versie is nu ingesteld. U deze instellingen bevestigen:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>How to: De ingebouwde PHP-configuraties wijzigen

Voor elke ingebouwde PHP-runtime u een van de configuratieopties wijzigen door deze stappen te volgen. (Voor informatie over php.ini richtlijnen, zie [Lijst van php.ini richtlijnen].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Wijzigen\_van\_\_PHP INI-GEBRUIKER, PHP INI\_PERDIR, PHP\_INI\_ALLE configuratie-instellingen

1. Voeg een [.user.ini-bestand] toe aan uw hoofdmap.
1. Voeg configuratie-instellingen `.user.ini` toe aan het bestand met `php.ini` dezelfde syntaxis die u in een bestand zou gebruiken. Als u bijvoorbeeld de instelling `display_errors` wilt inschakelen `upload_max_filesize` en instellen op `.user.ini` 10M, bevat uw bestand de tekst:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implementeer uw app.
3. De app opnieuw starten. (Opnieuw opstarten is noodzakelijk omdat de `.user.ini` frequentie waarmee PHP `user_ini.cache_ttl` bestanden leest wordt bepaald door de instelling, dat is een systeemniveau instelling en is 300 seconden (5 minuten) standaard. Door de app opnieuw op te starten, wordt PHP gesommeerd om de nieuwe instellingen in het `.user.ini` bestand te lezen.)

Als alternatief voor `.user.ini` het gebruik van een bestand u de functie [ini_set()] in scripts gebruiken om configuratieopties in te stellen die geen richtlijnen op systeemniveau zijn.

### <a name="changing-php_ini_system-configuration-settings"></a>Instellingen\_voor\_PHP INI-systeemconfiguratie wijzigen

1. Een app-instelling toevoegen aan `PHP_INI_SCAN_DIR` uw app met de sleutel en waarde`d:\home\site\ini`
1. Maak `settings.ini` een bestand met Kudu Console (http://&lt;sitenaam&gt;.scm.azurewebsite.net) in de `d:\home\site\ini` map.
1. Voeg configuratie-instellingen `settings.ini` toe aan het bestand met `php.ini` dezelfde syntaxis die u in een bestand zou gebruiken. Als u bijvoorbeeld de `curl.cainfo` instelling naar `*.crt` een bestand wilt richten en de instelling 'wincache.maxfilesize' op 512K wilt instellen, bevat uw `settings.ini` bestand de tekst:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Als u de wijzigingen opnieuw wilt laden, start u de app opnieuw.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>How to: Extensies inschakelen in de standaard PHP-runtime

Zoals in de vorige sectie is opgemerkt, de beste manier om de standaard PHP-versie, de standaardconfiguratie en de ingeschakelde extensies te bekijken, is het implementeren van een script dat [phpinfo aanroept()]. Als u extra extensies wilt inschakelen, volgt u de volgende stappen:

### <a name="configure-via-ini-settings"></a>Configureren via ini-instellingen

1. Voeg `ext` een map `d:\home\site` toe aan de map.
1. Zet `.dll` extensiebestanden `ext` in de map `php_xdebug.dll`(bijvoorbeeld). Zorg ervoor dat de extensies compatibel zijn met de standaardversie van PHP en VC9 en niet-thread-safe (nts) compatibel zijn.
1. Een app-instelling toevoegen aan `PHP_INI_SCAN_DIR` uw app met de sleutel en waarde`d:\home\site\ini`
1. Maak `ini` een `d:\home\site\ini` bestand `extensions.ini`met de naam .
1. Voeg configuratie-instellingen `extensions.ini` toe aan het bestand met `php.ini` dezelfde syntaxis die u in een bestand zou gebruiken. Als u bijvoorbeeld de MongoDB- en XDebug-extensies wilt inschakelen, bevat uw `extensions.ini` bestand de tekst:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Start uw app opnieuw op om de wijzigingen te laden.

### <a name="configure-via-app-setting"></a>Configureren via app-instelling

1. Voeg `bin` een map toe aan de hoofdmap.
2. Zet `.dll` extensiebestanden `bin` in de map `php_xdebug.dll`(bijvoorbeeld). Zorg ervoor dat de extensies compatibel zijn met de standaardversie van PHP en VC9 en niet-thread-safe (nts) compatibel zijn.
3. Implementeer uw app.
4. Blader naar uw app in de Azure-portal en klik op de **sectie Configuratie** onder **instellingen.**
5. Selecteer **toepassingsinstellingen**in het **configuratieblad** .
6. Klik in de sectie **Toepassingsinstellingen** op **+ Nieuwe toepassingsinstelling** en maak een **PHP_EXTENSIONS** sleutel. De waarde voor deze sleutel zou een pad ten opzichte van website root: **bin\your-ext-file**.
7. Klik op de knop **Bijwerken** onderin en klik boven het tabblad **Toepassingsinstellingen** op **Opslaan.**

Zend-extensies worden ook ondersteund door een **PHP_ZENDEXTENSIONS-toets** te gebruiken. Als u meerdere extensies wilt inschakelen, `.dll` neemt u een door komma's gescheiden lijst met bestanden op voor de waarde van de app-instelling.

## <a name="how-to-use-a-custom-php-runtime"></a>How to: Gebruik een aangepaste PHP runtime

In plaats van de standaard PHP-runtime kan App Service een PHP-runtime gebruiken die u verstrekt om PHP-scripts uit te voeren. De runtime die u opgeeft, `php.ini` kan worden geconfigureerd door een bestand dat u ook opgeeft. Als u een aangepaste PHP-runtime met App-service wilt gebruiken, volgt u deze stappen.

1. Een niet-thread-safe, VC9- of VC11-compatibele versie van PHP voor Windows verkrijgen. Recente releases van PHP voor Windows [https://windows.php.net/download/]zijn hier te vinden: . Oudere releases zijn hier te [https://windows.php.net/downloads/releases/archives/]vinden in het archief: .
2. Wijzig `php.ini` het bestand voor uw runtime. Alle configuratie-instellingen die alleen op systeemniveau zijn, worden genegeerd door App Service. (Zie [Lijst met php.ini-richtlijnen]voor informatie over richtlijnen op systeemniveau).
3. Voeg optioneel extensies toe aan uw PHP-runtime en schakel ze in het `php.ini` bestand in.
4. Voeg `bin` een map toe aan uw hoofdmap en plaats de map met `bin\php`daarin uw PHP-runtime (bijvoorbeeld).
5. Implementeer uw app.
6. Blader naar uw app in de Azure-portal en klik op het **configuratieblad.**
8. Selecteer **padtoewijzingen**in het **configuratieblad** . 
9. Klik **op + Nieuwe handler** en voeg toe `*.php` `php-cgi.exe` aan het veld Extensie en voeg het pad toe aan de uitvoerbare **scriptprocessor**. Als u uw PHP-runtime in de `bin` map in de `D:\home\site\wwwroot\bin\php\php-cgi.exe`hoofdmap van uw toepassing plaatst, is het pad .
10. Klik onderaan op **Bijwerken** om de handlertoewijzing toe te voegen.
11. Klik op **Opslaan** om de wijzigingen op te slaan.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>How to: Composer automation inschakelen in Azure

App Service doet standaard niets met composer.json, als je er een hebt in je PHP-project. Als u [Git-implementatie](deploy-local-git.md)gebruikt, u `git push` composer.json-verwerking inschakelen door de extensie Componist in te schakelen.

> [!NOTE]
> U [stemmen voor eersteklas Composer ondersteuning in App Service hier!](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)
>

1. Klik in het blad van uw PHP-app in de [Azure-portal](https://portal.azure.com)op > **Extra-extensies**. **Tools**

    ![Het blad voor azure-portalinstellingen om componistautomatisering in Azure in te schakelen](./media/web-sites-php-configure/composer-extension-settings.png)
2. Klik **op Toevoegen**en klik vervolgens op **Componist**.

    ![Extensie Composer toevoegen om componistautomatisering in Azure in te schakelen](./media/web-sites-php-configure/composer-extension-add.png)
3. Klik op **OK** om wettelijke voorwaarden te accepteren. Klik nogmaals op **OK** om de extensie toe te voegen.

    Het **blad Geïnstalleerde extensies** toont de extensie Composer.
    ![Juridische voorwaarden accepteren om componistautomatisering in Azure in te schakelen](./media/web-sites-php-configure/composer-extension-view.png)
4. Voer nu in een terminalvenster op `git add` `git commit`uw `git push` lokale machine uit en naar uw app. Merk op dat Composer afhankelijkheden installeert die zijn gedefinieerd in composer.json.

    ![Git-implementatie met composer-automatisering in Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Volgende stappen

Zie het PHP [Developer Center](https://azure.microsoft.com/develop/php/)voor meer informatie.

[gratis proefversie]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lijst van php.ini richtlijnen]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
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

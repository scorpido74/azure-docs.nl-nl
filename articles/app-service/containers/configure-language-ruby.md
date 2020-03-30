---
title: Ruby-apps configureren - Azure App Service
description: Meer informatie over het configureren van een vooraf gebouwde Ruby-container voor uw app. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046331"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Een Linux Ruby-app configureren voor Azure App Service

In dit artikel wordt beschreven hoe [Azure App Service](app-service-linux-intro.md) Ruby-apps uitvoert en hoe u het gedrag van App Service aanpassen wanneer dat nodig is. Ruby apps moeten worden ingezet met alle benodigde [edelstenen.](https://rubygems.org/gems)

Deze handleiding biedt belangrijke concepten en instructies voor Ruby-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, moet u eerst de [Ruby quickstart](quickstart-ruby.md) en [Ruby volgen met De Zelfstudie van PostgreSQL.](tutorial-ruby-postgres-app.md)

## <a name="show-ruby-version"></a>Ruby-versie weergeven

Als u de huidige Ruby-versie wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com)om alle ondersteunde Ruby-versies weer te geven:

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

U een niet-ondersteunde versie van Ruby uitvoeren door in plaats daarvan uw eigen containerafbeelding te bouwen. Voor meer informatie raadpleegt u [Een aangepaste Docker-installatiekopie gebruiken](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ruby-versie instellen

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com) om de Ruby-versie in te stellen op 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Als u fouten ziet die vergelijkbaar zijn met de volgende fouten tijdens de implementatietijd:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> of
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Dit betekent dat de Ruby-versie die in uw project is geconfigureerd, anders is`2.3.3` dan de versie die is geïnstalleerd in de container die u uitvoert (in het bovenstaande voorbeeld). Controleer in het bovenstaande voorbeeld zowel *gemfile* als *.ruby-versie* en controleer of de Ruby-versie niet is ingesteld of`2.3.3` is ingesteld op de versie die is geïnstalleerd in de container die u uitvoert (in het bovenstaande voorbeeld).

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App-service u [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Vervolgens u ze openen met behulp van het standaard ENV-patroon>.Then you can access them using the standard [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) pattern. Voor toegang tot bijvoorbeeld de app-instelling `WEBSITE_SITE_NAME` gebruikt u de volgende code:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Implementatie aanpassen

Wanneer u een [Git-repository](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)of een [Zip-pakket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) met buildprocessen inzet, voert de implementatieengine (Kudu) standaard automatisch de volgende stappen na de implementatie uit:

1. Controleer of er een *Gemfile* bestaat.
1. Voer `bundle clean` uit. 
1. Voer `bundle install --path "vendor/bundle"` uit.
1. Voer `bundle package` aan om edelstenen in de map vendor/cache te verpakken.

### <a name="use---without-flag"></a>Gebruik --zonder vlag

Als `bundle install` u wilt uitvoeren met de `BUNDLE_WITHOUT` [--zonder](https://bundler.io/man/bundle-install.1.html) vlag, stelt u de [app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op een door komma's gescheiden lijst met groepen. Met de volgende opdracht wordt `development,test`deze bijvoorbeeld ingesteld op .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Als deze instelling is gedefinieerd, wordt `bundle install` `--without $BUNDLE_WITHOUT`de implementatieengine uitgevoerd met .

### <a name="precompile-assets"></a>Activa vooraf compileren

De stappen na de implementatie maken niet standaard de grootst.The post-deployment steps don't precompile assets by default. Als u de voorcompilatie `ASSETS_PRECOMPILE` van activa `true`wilt inschakelen, stelt u de [app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op . Vervolgens wordt `bundle exec rake --trace assets:precompile` de opdracht uitgevoerd aan het einde van de stappen na de implementatie. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Zie [Statische activa dienen](#serve-static-assets)voor meer informatie .

## <a name="customize-start-up"></a>Opstarten aanpassen

Standaard start de Ruby-container de server Rails in de volgende reeks (zie voor meer informatie het [opstartscript):](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)

1. Genereer een [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) waarde, als deze nog niet bestaat. Deze waarde is vereist om de app in de productiemodus uit te voeren.
1. Stel `RAILS_ENV` de omgevingsvariabele in op `production`.
1. Verwijder een *.pid-bestand* in de *tmp/pids-map* die is achtergelaten door een eerder draaiende Rails-server.
1. Controleer of alle afhankelijkheden zijn geïnstalleerd. Zo niet, probeer dan het installeren van edelstenen uit de lokale *leverancier / cache* directory.
1. Voer `rails server -e $RAILS_ENV` uit.

U het opstartproces op de volgende manieren aanpassen:

- [Statische activa weergeven](#serve-static-assets)
- [Uitvoeren in niet-productiemodus](#run-in-non-production-mode)
- [Secret_key_base handmatig instellen](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statische activa weergeven

De rails-server in de Ruby-container wordt standaard in de productiemodus uitgevoerd en [gaat ervan uit dat assets vooraf zijn gecompileerd en worden bediend door uw webserver.](https://guides.rubyonrails.org/asset_pipeline.html#in-production) Als u statische assets van de railsserver wilt weergeven, moet u twee dingen doen:

- **Precompileer de assets** - [Precompileer de statische assets lokaal](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) en implementeer ze handmatig. Of laat de implementatieengine deze in plaats daarvan afhandelen (zie [Vooropstellen van activa](#precompile-assets).
- **Statische bestanden weergeven inschakelen** - Als u statische elementen `true`uit de Ruby-container wilt weergeven, stelt u [ `RAILS_SERVE_STATIC_FILES` de app-instelling in op](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) . Bijvoorbeeld:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uitvoeren in niet-productiemodus

De rails-server wordt standaard in de productiemodus uitgevoerd. Als u bijvoorbeeld wilt werken in `RAILS_ENV` de `development`ontwikkelingsmodus, stelt u de [app-instelling in](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) op .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Deze instelling alleen zorgt er echter alleen voor dat de railsserver wordt gestart in de ontwikkelingsmodus, die alleen lokverzoeken voor localhost accepteert en niet toegankelijk is buiten de container. Als u externe clientverzoeken `APP_COMMAND_LINE` wilt `rails server -b 0.0.0.0`accepteren, stelt u de [app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op . Met deze app-instelling u een aangepaste opdracht uitvoeren in de Ruby-container. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Secret_key_base handmatig instellen

Als u `secret_key_base` uw eigen waarde wilt gebruiken in plaats `SECRET_KEY_BASE` van App Service er een voor u te laten genereren, stelt u de [app-instelling in](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) met de gewenste waarde. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in de browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Rails-app met PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)

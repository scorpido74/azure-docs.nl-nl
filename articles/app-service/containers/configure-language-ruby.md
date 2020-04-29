---
title: Ruby-apps configureren-Azure App Service
description: Meer informatie over het configureren van een vooraf ontwikkelde ruby-container voor uw app. In dit artikel vindt u de meest voorkomende configuratie taken.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80046331"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Een Linux ruby-app voor Azure App Service configureren

In dit artikel wordt beschreven hoe [Azure app service](app-service-linux-intro.md) ruby-apps uitvoert en hoe u het gedrag van app service kunt aanpassen wanneer dit nodig is. Ruby-apps moeten met alle vereiste [edelstenen](https://rubygems.org/gems)worden geïmplementeerd.

Deze hand leiding bevat belang rijke concepten en instructies voor ruby-ontwikkel aars die een ingebouwde Linux-container gebruiken in App Service. Als u Azure App Service nog nooit hebt gebruikt, moet u eerst de [ruby-Snelstartgids](quickstart-ruby.md) en [ruby met postgresql zelf studie](tutorial-ruby-postgres-app.md) volgen.

## <a name="show-ruby-version"></a>Ruby-versie weer geven

Als u de huidige ruby-versie wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle ondersteunde ruby-versies wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

U kunt een niet-ondersteunde versie van Ruby uitvoeren door in plaats daarvan uw eigen container installatie kopie te maken. Voor meer informatie raadpleegt u [Een aangepaste Docker-installatiekopie gebruiken](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ruby-versie instellen

Voer de volgende opdracht uit in het [Cloud shell](https://shell.azure.com) om de Ruby-versie in te stellen op 2,3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Als er tijdens de implementatie fouten worden weer gegeven die vergelijkbaar zijn met de volgende:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> of
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Dit betekent dat de Ruby-versie die in uw project is geconfigureerd, afwijkt van de versie die is geïnstalleerd in de`2.3.3` container die u gebruikt (in het bovenstaande voor beeld). Controleer in het bovenstaande voor beeld zowel *Gemfile* als *Ruby-versie* en controleer of de Ruby-versie niet is ingesteld, of is ingesteld op de versie die is geïnstalleerd in de container die u gebruikt (`2.3.3` in het bovenstaande voor beeld).

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u de [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Vervolgens kunt u ze openen met het standaard [env ['\<pad-naam> ']](https://ruby-doc.org/core-2.3.3/ENV.html) -patroon. Voor toegang tot bijvoorbeeld de app-instelling `WEBSITE_SITE_NAME` gebruikt u de volgende code:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Implementatie aanpassen

Wanneer u een [Git-opslag plaats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)implementeert, of een [zip-pakket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) met Build-processen is ingeschakeld, voert de implementatie-Engine (kudu) automatisch de volgende stappen na de implementatie uit:

1. Controleer of er een *Gemfile* bestaat.
1. Voer `bundle clean` uit. 
1. Voer `bundle install --path "vendor/bundle"` uit.
1. Voer `bundle package` uit om edelstenen te verpakken in de map leverancier/cachemap.

### <a name="use---without-flag"></a>Gebruik--zonder vlag

Als u `bundle install` wilt uitvoeren met de [--zonder-](https://bundler.io/man/bundle-install.1.html) vlag `BUNDLE_WITHOUT` , stelt u de [app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op een door komma's gescheiden lijst met groepen. Met de volgende opdracht wordt deze bijvoorbeeld ingesteld op `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Als deze instelling is gedefinieerd, wordt de implementatie-engine `bundle install` uitgevoerd `--without $BUNDLE_WITHOUT`met.

### <a name="precompile-assets"></a>Activa precompileren

Bij de stappen na de implementatie worden assets niet standaard vooraf gecompileerd. Als u de voor-compilatie van activa wilt `ASSETS_PRECOMPILE` inschakelen, stelt `true`u de app- [instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op. Vervolgens wordt de `bundle exec rake --trace assets:precompile` opdracht uitgevoerd aan het einde van de stappen na de implementatie. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Zie voor meer informatie [statische assets aanbieden](#serve-static-assets).

## <a name="customize-start-up"></a>Opstarten aanpassen

Standaard start de ruby-container de rails-server in de volgende volg orde (Zie voor meer informatie het [opstart script](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Genereer een [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) waarde als er nog geen bestaat. Deze waarde is vereist voor het uitvoeren van de app in de productie modus.
1. Stel de `RAILS_ENV` omgevings variabele `production`in op.
1. Verwijder een wille keurig *PID* -bestand in de map *tmp/pid's* die wordt weer gegeven door een server met een eerder uitgevoerde rails.
1. Controleer of alle afhankelijkheden zijn geïnstalleerd. Als dat niet het geval is, probeert u edelsteen te installeren vanuit de lokale map *leverancier/cache* .
1. Voer `rails server -e $RAILS_ENV` uit.

U kunt het opstart proces op de volgende manieren aanpassen:

- [Statische activa leveren](#serve-static-assets)
- [Uitvoeren in niet-productie modus](#run-in-non-production-mode)
- [Secret_key_base hand matig instellen](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Statische activa leveren

De rails-server in de ruby-container wordt standaard uitgevoerd in de productie modus en er [wordt van uitgegaan dat activa vooraf worden gecompileerd en door de webserver worden verwerkt](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Als u statische activa van de rails-server wilt gebruiken, moet u twee dingen doen:

- **De activa** - precompileren precompileren[de statische activa voor het](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) eerst samen te stellen en hand matig te implementeren. U kunt de implementatie-Engine ook laten afhandelen (Zie [assets voor precompileren](#precompile-assets).
- **Inschakelen van statische bestanden** : als u statische assets van de ruby-container wilt gebruiken, [stelt u `RAILS_SERVE_STATIC_FILES` de app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op. `true` Bijvoorbeeld:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uitvoeren in niet-productie modus

De rails-server wordt standaard uitgevoerd in de productie modus. Als u wilt uitvoeren in de ontwikkel modus, stelt u `RAILS_ENV` bijvoorbeeld de app `development`- [instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Met deze instelling wordt echter alleen de rails-server gestart in de ontwikkelings modus, die alleen localhost-aanvragen accepteert en niet toegankelijk is buiten de container. Als u aanvragen voor externe clients wilt accepteren `APP_COMMAND_LINE` , stelt u `rails server -b 0.0.0.0`de app- [instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in op. Met deze app-instelling kunt u een aangepaste opdracht uitvoeren in de ruby-container. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Secret_key_base hand matig instellen

Als u uw eigen `secret_key_base` waarde wilt gebruiken in plaats van app service een voor u te laten `SECRET_KEY_BASE` genereren, stelt u de [app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in met de gewenste waarde. Bijvoorbeeld:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Rails-app met PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)

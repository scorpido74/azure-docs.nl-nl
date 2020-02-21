---
title: 'Snelstartgids: een Linux ruby-app maken'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste ruby-app te implementeren in een Linux-container in App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 2fcbc40bf78357700e59551b77b7e4d2da1bd82d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524050"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>Een Ruby on Rails-app maken met App Service op Linux

[App Service onder Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. In deze Snelstartgids ziet u hoe u een Ruby on Rails-app implementeert om te Azure App Service op Linux met behulp van de [Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview).

> [!NOTE]
> De Ruby-ontwikkelstack ondersteunt momenteel alleen Ruby on Rails. Als u een ander platform wilt gebruiken, zoals Sinatra, of als u een [niet-ondersteunde ruby-versie](app-service-linux-intro.md)wilt gebruiken, moet u [deze uitvoeren in een aangepaste container](quickstart-docker-go.md).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2,6 of hoger installeren</a>
* <a href="https://git-scm.com/" target="_blank">Git installeren</a>

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Voer in een terminalvenster de volgende opdracht uit om de opslagplaats van de voorbeeld-app te klonen op uw lokale computer:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>De toepassing lokaal uitvoeren

Voer de toepassing lokaal uit zodat u kunt zien hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure. Open een terminalvenster, ga naar de map `hello-world` en gebruik de opdracht `rails server` om de server te starten.

De eerste stap is het installeren van de vereiste gems. Er is een `Gemfile` opgenomen in het voor beeld, dus u hoeft alleen de volgende opdracht uit te voeren:

```bash
bundle install
```

Nadat de gems zijn geïnstalleerd, gebruiken we bundler om de app te starten:

```bash
bundle exec rails server
```

Navigeer naar `http://localhost:3000` met uw webbrowser om de app lokaal te testen.

![Hello World geconfigureerd](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Blader naar de app om uw nieuwe web-app met de ingebouwde installatie kopie weer te geven. Vervang _&lt;app-naam>_ door de naam van uw web-app.

```bash
http://<app_name>.azurewebsites.net
```

Zo zou uw nieuwe web-app er moeten uitzien:

![Welkomstpagina](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Uw toepassing implementeren

Voer de volgende opdrachten uit om de lokale toepassing in uw Azure-web-app te implementeren:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

Controleer of de externe implementatiebewerkingen met succes voltooid zijn. De uitvoer van de opdrachten moet er ongeveer uitzien als de volgende tekst:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

Zodra de implementatie is voltooid, wacht u ongeveer tien seconden totdat de web-app opnieuw is opgestart en navigeert u vervolgens naar de web-app en controleert u de resultaten.

```bash
http://<app-name>.azurewebsites.net
```

![bijgewerkte web-app](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Terwijl de app opnieuw wordt gestart, kunt u de HTTP-status code `Error 503 Server unavailable` in de browser of de `Hey, Ruby developers!` standaard pagina bekijken. Het kan enkele minuten duren voordat de app volledig opnieuw is opgestart.
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: Ruby on Rails met post gres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby-app configureren](configure-language-ruby.md)

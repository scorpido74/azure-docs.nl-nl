---
title: Windows Node.js-apps configureren
description: Meer informatie over het configureren van een Node.js-app in de systeem eigen Windows-exemplaren van App Service. In dit artikel worden de meest algemene configuratietaken beschreven.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908090"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Een Windows Node.js-app configureren voor Azure App Service

Node.js-apps moeten worden geïmplementeerd met alle vereiste NPM-afhankelijkheden. De App Service-implementatie-engine wordt automatisch uitgevoerd `npm install --production` Wanneer u een [Git-opslag plaats](deploy-local-git.md)implementeert of een [zip-pakket](deploy-zip.md) met Build Automation is ingeschakeld. Als u uw bestanden implementeert met [FTP/S](deploy-ftp.md), moet u de vereiste pakketten echter hand matig uploaden. Zie [Configure a Linux PHP app for Azure app service](containers/configure-language-nodejs.md)(Engelstalig) voor meer informatie over Linux-apps.

Deze hand leiding bevat belang rijke concepten en instructies voor Node.js ontwikkel aars die op App Service zijn geïmplementeerd. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [Node.js Snelstartgids](app-service-web-get-started-nodejs.md) en [Node.js met MongoDb zelf studie](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Node.js versie weer geven

Als u de huidige versie van Node.js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Als u alle ondersteunde versies van Node.js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Node.js versie instellen

Als u uw app wilt instellen op een [ondersteunde Node.js versie](#show-nodejs-version), voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com) om in `WEBSITE_NODE_DEFAULT_VERSION` te stellen op een ondersteunde versie:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Met deze instelling geeft u op welke Node.js versie moet worden gebruikt, zowel tijdens runtime als tijdens het automatisch herstellen van pakketten tijdens het App Service bouwen van Automation.

> [!NOTE]
> Stel de Node.js versie in het project in `package.json` . De implementatie-engine wordt uitgevoerd in een afzonderlijk proces dat alle ondersteunde Node.js versies bevat.

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u [app-instellingen configureren](configure-common.md) buiten uw app-code. Vervolgens kunt u ze openen met behulp van het standaard Node.js patroon. Voor toegang tot bijvoorbeeld de app-instelling `NODE_ENV` gebruikt u de volgende code:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Voer grunt/Bower/Gulp uit

App Service build Automation wordt standaard uitgevoerd `npm install --production` Wanneer een Node.js-app wordt herkend via git (of zip-implementatie waarbij build Automation is ingeschakeld). Als uw app een van de populaire automatiserings hulpprogramma's vereist, zoals grunt, Bower of Gulp, moet u een [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) opgeven om het uit te voeren.

Als u de opslag plaats wilt inschakelen om deze hulpprogram ma's uit te voeren, moet u deze toevoegen aan de afhankelijkheden in *package.jsop.* Bijvoorbeeld:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Wijzig vanuit een lokaal Terminal venster de map in de hoofdmap van de opslag plaats en voer de volgende opdrachten uit:

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

## <a name="detect-https-session"></a>HTTPS-sessie detecteren

In App Service vindt [SSL-beëindiging](https://wikipedia.org/wiki/TLS_termination_proxy) plaats in de load balancers voor het netwerk, zodat alle HTTPS-aanvragen uw app bereiken als niet-versleutelde HTTP-aanvragen. Inspecteer de header `X-Forwarded-Proto` als de app-logica moet controleren of de aanvragen van gebruikers al dan niet zijn versleuteld.

Populaire webframeworks bieden toegang tot de `X-Forwarded-*`-informatie in het patroon van de standaard-app. In [Express](https://expressjs.com/)kunt u [vertrouwens proxy's](https://expressjs.com/guide/behind-proxies.html)gebruiken. Bijvoorbeeld:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werk Node.js-app anders werkt in App Service of fouten bevat, kunt u het volgende proberen:

- [Open de logboekstream](#access-diagnostic-logs).
- Test de app lokaal in de productie modus. App Service worden uw Node.js-apps uitgevoerd in de productie modus. u moet er dus voor zorgen dat uw project in de productie modus lokaal werkt zoals verwacht. Bijvoorbeeld:
    - Afhankelijk van uw *package.jsop*kunnen verschillende pakketten worden geïnstalleerd voor de productie modus ( `dependencies` VS. `devDependencies` ).
    - Bepaalde web Frameworks kunnen statische bestanden in de productie modus anders implementeren.
    - Bepaalde web Frameworks kunnen aangepaste opstart scripts gebruiken wanneer ze in de productie modus worden uitgevoerd.
- Voer uw app uit in de App Service in de ontwikkelings modus. In [MEAN.js](https://meanjs.org/)kunt u bijvoorbeeld uw app in runtime instellen op de ontwikkelings modus door [de app- `NODE_ENV` instelling](configure-common.md)in te stellen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Node.js-app met MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)


---
title: Node. js-apps configureren
description: Meer informatie over het configureren van een vooraf gemaakte node. js-container voor uw app. In dit artikel vindt u de meest voorkomende configuratie taken.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79252725"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Een Linux node. js-app configureren voor Azure App Service

Node. js-apps moeten worden geïmplementeerd met alle vereiste NPM-afhankelijkheden. De App Service Deployment Engine (kudu) wordt automatisch `npm install --production` uitgevoerd wanneer u een Git- [opslag plaats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)implementeert of een [zip-pakket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) met Build-processen is ingeschakeld. Als u uw bestanden implementeert met [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), moet u de vereiste pakketten echter hand matig uploaden.

Deze hand leiding bevat belang rijke concepten en instructies voor node. js-ontwikkel aars die een ingebouwde Linux-container gebruiken in App Service. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [Snelstartgids node. js](quickstart-nodejs.md) en [node. js met MongoDb zelf studie](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Node. js-versie weer geven

Als u de huidige versie van node. js wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Als u alle ondersteunde node. js-versies wilt weer geven, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Versie van node. js instellen

Als u uw app wilt instellen op een [ondersteunde node. js-versie](#show-nodejs-version), voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Met deze instelling geeft u de versie van node. js op die moet worden gebruikt tijdens runtime en tijdens automatische pakket herstel in kudu.

> [!NOTE]
> U moet de versie van node. js instellen in het project `package.json`. De implementatie-engine wordt uitgevoerd in een afzonderlijke container die alle ondersteunde node. js-versies bevat.

## <a name="customize-build-automation"></a>Bouw automatisering aanpassen

Als u uw app implementeert met git-of ZIP-pakketten waarvoor build Automation is ingeschakeld, wordt de App Service stapsgewijs door de volgende reeks gemaakt:

1. Voer een aangepast script uit, `PRE_BUILD_SCRIPT_PATH`indien opgegeven door.
1. Zonder `npm install` vlaggen worden uitgevoerd, waaronder NPM `preinstall` en `postinstall` scripts en wordt ook geïnstalleerd `devDependencies`.
1. Voer `npm run build` uit als een build-script is opgegeven in de *package. json*.
1. Uitvoeren `npm run build:azure` als een build: Azure script is opgegeven in de *package. json*.
1. Voer een aangepast script uit, `POST_BUILD_SCRIPT_PATH`indien opgegeven door.

> [!NOTE]
> Zoals beschreven in [NPM-documenten](https://docs.npmjs.com/misc/scripts), scripts `prebuild` met `postbuild` de namen en uitvoeren `build`vóór en na, respectievelijk, indien opgegeven. `preinstall`en `postinstall` vóór en na `install`, respectievelijk worden uitgevoerd.

`PRE_BUILD_COMMAND`en `POST_BUILD_COMMAND` zijn omgevings variabelen die standaard leeg zijn. Voor het uitvoeren van opdrachten die vooraf zijn `PRE_BUILD_COMMAND`gebouwd, definieert u. Als u opdrachten na het bouwen wilt uitvoeren `POST_BUILD_COMMAND`, definieert u.

In het volgende voor beeld worden de twee variabelen opgegeven voor een reeks opdrachten, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)voor meer omgevings variabelen voor het aanpassen van het bouwen van Automation.

Zie [Oryx-documentatie](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)voor meer informatie over hoe app service worden uitgevoerd en bouwt node. js-apps in Linux.

## <a name="configure-nodejs-server"></a>Node. js-server configureren

De node. js-containers worden geleverd met [PM2](https://pm2.keymetrics.io/), een productie proces Manager. U kunt uw app configureren om te beginnen met PM2, of met NPM, of met een aangepaste opdracht.

- [Aangepaste opdracht uitvoeren](#run-custom-command)
- [NPM starten](#run-npm-start)
- [Uitvoeren met PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Aangepaste opdracht uitvoeren

App Service kunt uw app starten met een aangepaste opdracht, zoals een uitvoerbaar bestand zoals *Run.sh*. Als u bijvoorbeeld wilt uitvoeren `npm run start:prod`, voert u de volgende opdracht uit in het [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>NPM starten

Als u uw app wilt `npm start`starten met, controleert u `start` of er een script is in het bestand *package. json* . Bijvoorbeeld:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Als u een aangepaste *package. json* in uw project wilt gebruiken, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Uitvoeren met PM2

De container start automatisch uw app met PM2 wanneer een van de gemeen schappelijke node. js-bestanden in uw project is gevonden:

- *bin/www*
- *server.js*
- *app. js*
- *index. js*
- *hostingstart. js*
- Een van de volgende [PM2-bestanden](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process. json* en *ecosysteem. config. js*

U kunt ook een aangepast start bestand met de volgende extensies configureren:

- Een *js* -bestand
- Een [PM2-bestand](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) met de extensie *. json*, *. config. js*, *. yaml*of *. yml*

Als u een aangepast start bestand wilt toevoegen, voert u de volgende opdracht uit in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Foutopsporing op afstand

> [!NOTE]
> Externe fout opsporing is momenteel beschikbaar als preview-versie.

U kunt de node. js-app op afstand fouten opsporen in [Visual Studio code](https://code.visualstudio.com/) als u deze configureert voor [uitvoering met PM2](#run-with-pm2), behalve wanneer u deze uitvoert met een *. config. js, *. yml of *. yaml*.

In de meeste gevallen is er geen extra configuratie vereist voor uw app. Als uw app wordt uitgevoerd met een *proces. json* -bestand (standaard of aangepast), moet het een `script` eigenschap hebben in het JSON-basis programma. Bijvoorbeeld:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Als u Visual Studio code voor externe fout opsporing wilt instellen, installeert u de [app service extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Volg de instructies op de pagina extensie en meld u aan bij Azure in Visual Studio code.

Zoek in de Azure Verkenner de app waarvoor u fouten wilt opsporen, klik er met de rechter muisknop op en selecteer **externe fout opsporing starten**. Klik op **Ja** om het voor uw app in te scha kelen. App Service start een tunnel proxy voor u en koppelt de debugger. U kunt vervolgens aanvragen indienen bij de app en zien hoe het fout opsporingsprogramma op onderbrekings punten pauzeert.

Als u klaar bent met het oplossen van fouten, stopt u het fout opsporingsprogramma door de **verbinding te verbreken** Wanneer u hierom wordt gevraagd, klikt u op **Ja** om externe fout opsporing uit te scha kelen. Als u dit later wilt uitschakelen, klikt u opnieuw met de rechter muisknop op uw app in azure Verkenner en selecteert u **externe fout opsporing uitschakelen**.

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App Service kunt u de [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Vervolgens kunt u ze openen met het standaard patroon node. js. Voor toegang tot bijvoorbeeld de app-instelling `NODE_ENV` gebruikt u de volgende code:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Voer grunt/Bower/Gulp uit

Kudu wordt standaard uitgevoerd `npm install --production` wanneer wordt gedetecteerd dat een node. js-app wordt geïmplementeerd. Als uw app een van de populaire automatiserings hulpprogramma's vereist, zoals grunt, Bower of Gulp, moet u een [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) opgeven om het uit te voeren.

Als u uw opslag plaats wilt inschakelen om deze hulpprogram ma's uit te voeren, moet u deze toevoegen aan de afhankelijkheden in *package. json.* Bijvoorbeeld:

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

Deze sectie eindigt met het `npm install --production`uitvoeren van. Voeg de sectie code toe die u nodig hebt om het vereiste hulp programma uit te `Deployment` voeren *aan het einde* van de sectie:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Bekijk een [voor beeld in het voor beeld mean. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), waarbij het implementatie script ook een `npm install` aangepaste opdracht uitvoert.

### <a name="bower"></a>Bower

Dit code fragment `bower install`wordt uitgevoerd.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Dit code fragment `gulp imagemin`wordt uitgevoerd.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Dit code fragment `grunt`wordt uitgevoerd.

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

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werk knooppunt. js-app zich anders gedraagt in App Service of fouten bevat, kunt u het volgende proberen:

- [Open de logboek stroom](#access-diagnostic-logs).
- Test de app lokaal in de productie modus. App Service worden uw node. js-apps in de productie modus uitgevoerd. u moet er dus voor zorgen dat uw project in de productie modus lokaal werkt zoals verwacht. Bijvoorbeeld:
    - Afhankelijk van uw *package. json*kunnen verschillende pakketten worden geïnstalleerd voor de productie modus (`dependencies` vs. `devDependencies`).
    - Bepaalde web Frameworks kunnen statische bestanden in de productie modus anders implementeren.
    - Bepaalde web Frameworks kunnen aangepaste opstart scripts gebruiken wanneer ze in de productie modus worden uitgevoerd.
- Voer uw app uit in de App Service in de ontwikkelings modus. U kunt bijvoorbeeld in [mean. js](https://meanjs.org/)uw app in runtime instellen op de ontwikkelings modus door [ `NODE_ENV` de app-instelling](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)in te stellen.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelf studie: node. js-app met MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)

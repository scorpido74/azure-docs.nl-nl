---
title: Apps voor Node.js configureren
description: Meer informatie over het configureren van een vooraf gebouwde Node.js-container voor uw app. In dit artikel worden de meest voorkomende configuratietaken weergegeven.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252725"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Een Linux Node.js-app configureren voor Azure App Service

Node.js-apps moeten worden geïmplementeerd met alle vereiste NPM-afhankelijkheden. De App Service deployment engine (Kudu) wordt automatisch voor u uitgevoerd `npm install --production` wanneer u een [Git-repository](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)of een [Zip-pakket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) implementeert waarbij buildprocessen zijn ingeschakeld. Als u uw bestanden implementeert met [FTP/S,](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)moet u de vereiste pakketten echter handmatig uploaden.

Deze handleiding biedt belangrijke concepten en instructies voor Node.js-ontwikkelaars die een ingebouwde Linux-container in App Service gebruiken. Als u Azure App Service nog nooit hebt gebruikt, volgt u eerst de [instructie Node.js quickstart](quickstart-nodejs.md) en [Node.js met de mongoDB-zelfstudie.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Node.js-versie weergeven

Als u de huidige Node.js-versie wilt weergeven, voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com)om alle ondersteunde Node.js-versies weer te geven:

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node.js-versie instellen

Als u uw app wilt instellen op een [ondersteunde Node.js-versie,](#show-nodejs-version)voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Met deze instelling geeft u de Node.js-versie op die moet worden gebruikt, zowel tijdens runtime als tijdens geautomatiseerde pakketherstel in Kudu.

> [!NOTE]
> U moet de Node.js-versie instellen `package.json`in die van uw project. De implementatie-engine wordt uitgevoerd in een aparte container die alle ondersteunde Node.js-versies bevat.

## <a name="customize-build-automation"></a>Build-automatisering aanpassen

Als u uw app implementeert met Git- of zip-pakketten waarbij buildautomatisering is ingeschakeld, worden de automatiseringsstappen van de App-service door de volgende reeks doorlopen:

1. Aangepaste script uitvoeren `PRE_BUILD_SCRIPT_PATH`als dit is opgegeven door .
1. Run `npm install` zonder vlaggen, die `preinstall` npm en `postinstall` scripts `devDependencies`omvat en installeert ook.
1. Uitvoeren `npm run build` als een buildscript is opgegeven in uw *package.json*.
1. Uitvoeren `npm run build:azure` als een build:azure script is opgegeven in uw *package.json*.
1. Aangepaste script uitvoeren `POST_BUILD_SCRIPT_PATH`als dit is opgegeven door .

> [!NOTE]
> Zoals beschreven in [npm-documenten,](https://docs.npmjs.com/misc/scripts)scripts `build`die voor en na worden genoemd `prebuild` en `postbuild` uitgevoerd, respectievelijk, indien opgegeven. `preinstall`en `postinstall` lopen voor `install`en na, respectievelijk.

`PRE_BUILD_COMMAND`en `POST_BUILD_COMMAND` zijn standaard lege omgevingsvariabelen. Als u pre-build-opdrachten `PRE_BUILD_COMMAND`wilt uitvoeren, definieert u . Als u opdrachten na het `POST_BUILD_COMMAND`bouwen wilt uitvoeren, definieert u .

In het volgende voorbeeld worden de twee variabelen opgeeft aan een reeks opdrachten, gescheiden door komma's.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Zie [Oryx-configuratie](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)voor extra omgevingsvariabelen om de buildautomatisering aan te passen.

Zie [Oryx-documentatie: Hoe Node.js-apps worden gedetecteerd en gebouwd voor](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md)meer informatie over hoe App Service Node.js-apps in Linux draait en bouwt.

## <a name="configure-nodejs-server"></a>Node.js-server configureren

De Node.js containers worden geleverd met [PM2](https://pm2.keymetrics.io/), een productieproces manager. U uw app zo configureren dat deze begint met PM2, of met NPM of met een aangepaste opdracht.

- [Aangepaste opdracht uitvoeren](#run-custom-command)
- [Run npm start](#run-npm-start)
- [Uitvoeren met PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Aangepaste opdracht uitvoeren

App Service kan uw app starten met een aangepaste opdracht, zoals een uitvoerbare *run.sh*. Als u bijvoorbeeld `npm run start:prod`wilt uitvoeren, voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Run npm start

Als u uw `npm start`app wilt `start` starten met behulp van , moet u ervoor zorgen dat er een script in het *bestand package.json* staat. Bijvoorbeeld:

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

Voer de volgende opdracht uit in de [Cloud Shell](https://shell.azure.com)als u een aangepast *pakket.json* in uw project wilt gebruiken:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Uitvoeren met PM2

De container start uw app automatisch met PM2 wanneer een van de algemene Node.js-bestanden in uw project wordt gevonden:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Een van de volgende [PM2-bestanden](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* en *ecosystem.config.js*

U ook een aangepast startbestand configureren met de volgende extensies:

- Een *.js-bestand*
- Een [PM2-bestand](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) met de extensie *.json*, *.config.js*, *.yaml*of *.yml*

Als u een aangepast startbestand wilt toevoegen, voert u de volgende opdracht uit in de [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Foutopsporing op afstand

> [!NOTE]
> Externe foutopsporing is momenteel in Preview.

U uw Node.js-app op afstand debuggen in [Visual Studio Code](https://code.visualstudio.com/) als u deze configureert om met PM2 uit te [voeren,](#run-with-pm2)behalve wanneer u deze uitvoert met een *.config.js, *.yml of *.yaml*.

In de meeste gevallen is er geen extra configuratie vereist voor uw app. Als uw app wordt uitgevoerd met een *process.json-bestand* `script` (standaard of aangepast), moet deze een eigenschap in de JSON-root hebben. Bijvoorbeeld:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Als u Visual Studio Code wilt instellen voor foutopsporing op afstand, installeert u de [app-serviceextensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Volg de instructies op de extensiepagina en meld u aan bij Azure in Visual Studio Code.

Zoek in de Azure Explorer de app die u wilt opsporen, klik er met de rechtermuisknop op en selecteer **Extern opsporen starten**. Klik **op Ja** om deze in te schakelen voor uw app. App Service start een tunnelproxy voor u en koppelt de foutopsporing. U vervolgens verzoeken indienen bij de app en de foutopsporing zien pauzeren bij breekpunten.

Zodra u klaar bent met foutopsporing, stopt u de foutopsporing door **Verbreken te**selecteren. Wanneer u daarom wordt gevraagd, moet u op **Ja** klikken om foutopsporing op afstand uit te schakelen. Als u deze later wilt uitschakelen, klikt u met de rechtermuisknop nogmaals op uw app in de Azure Explorer en selecteert u **Extern foutopsporing uitschakelen**.

## <a name="access-environment-variables"></a>Toegang tot omgevingsvariabelen

In App-service u [app-instellingen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) buiten uw app-code instellen. Dan u ze openen met behulp van de standaard Node.js patroon. Voor toegang tot bijvoorbeeld de app-instelling `NODE_ENV` gebruikt u de volgende code:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Run Grunt/Bower/Gulp

Standaard wordt Kudu `npm install --production` uitgevoerd wanneer wordt herkend dat een Node.js-app wordt geïmplementeerd. Als uw app een van de populaire automatiseringstools vereist, zoals Grunt, Bower of Gulp, moet u een [aangepast implementatiescript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) leveren om het uit te voeren.

Als u uw repository wilt inschakelen om deze hulpprogramma's uit te voeren, moet u ze toevoegen aan de afhankelijkheden in *package.json.* Bijvoorbeeld:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Wijzig vanuit een lokaal terminalvenster de map in uw opslagplaatsroot en voer de volgende opdrachten uit:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Uw repository root heeft nu twee extra bestanden: *.deployment* en *deploy.sh*.

Open *deploy.sh* en `Deployment` vind de sectie, die er als volgt uitziet:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Deze sectie eindigt `npm install --production`met hardlopen . Voeg de codesectie toe die u nodig hebt `Deployment` om het vereiste gereedschap aan het einde van de sectie *uit* te voeren:

- [Bower](#bower)
- [Gulp Gulp](#gulp)
- [Grunt](#grunt)

Zie een [voorbeeld in het voorbeeld MEAN.js,](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)waarbij `npm install` het implementatiescript ook een aangepaste opdracht uitvoert.

### <a name="bower"></a>Bower

Dit fragment `bower install`wordt uitgevoerd .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp Gulp

Dit fragment `gulp imagemin`wordt uitgevoerd .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Dit fragment `grunt`wordt uitgevoerd .

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

Populaire webframeworks bieden toegang tot de `X-Forwarded-*`-informatie in het patroon van de standaard-app. In [Express](https://expressjs.com/)u gebruik maken [van trust proxy's](https://expressjs.com/guide/behind-proxies.html). Bijvoorbeeld:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in de browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer een werkende Node.js-app zich anders gedraagt in App-service of fouten heeft, probeert u het volgende:

- [Toegang tot de logstream](#access-diagnostic-logs).
- Test de app lokaal in de productiemodus. App Service voert uw Node.js-apps uit in de productiemodus, dus u moet ervoor zorgen dat uw project werkt zoals verwacht in de productiemodus lokaal. Bijvoorbeeld:
    - Afhankelijk van uw *package.json*kunnen verschillende pakketten worden`dependencies` geïnstalleerd `devDependencies`voor de productiemodus (vs. ).
    - Bepaalde webframeworks kunnen statische bestanden anders implementeren in de productiemodus.
    - Bepaalde webframeworks kunnen aangepaste opstartscripts gebruiken wanneer ze in de productiemodus worden uitgevoerd.
- Voer uw app uit in appservice in de ontwikkelingsmodus. In [MEAN.js](https://meanjs.org/)u uw app bijvoorbeeld in runtime instellen op de ontwikkelingsmodus door [ `NODE_ENV` de app-instelling in te stellen.](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Node.js-app met MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen over App Service Linux](app-service-linux-faq.md)

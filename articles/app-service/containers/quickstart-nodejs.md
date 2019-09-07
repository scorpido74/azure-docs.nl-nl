---
title: Een node. js-web-app maken-Azure App Service
description: Een node. js-app implementeren in Azure App Service
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390975"
---
# <a name="create-a-nodejs-app-in-azure"></a>Een node. js-app maken in azure

Azure App Service biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze snelstart ziet u hoe u een Node.js-app naar Azure App Service implementeert.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-account hebt, [meldt u zich vandaag](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) aan voor een gratis account met $200 in azure-tegoed om een wille keurige combi natie van services uit te proberen.

U moet [Visual Studio code](https://code.visualstudio.com/) installeren, samen met [node. js en NPM](https://nodejs.org/en/download), het node. js-pakket beheer.

U moet ook de [Azure app service-extensie](vscode:extension/ms-azuretools.vscode-azureappservice)installeren, die u kunt gebruiken voor het maken, beheren en implementeren van Linux-web apps op het Azure-Platform as a Service (PaaS).

### <a name="sign-in"></a>Aanmelden

Nadat de extensie is geïnstalleerd, meldt u zich aan bij uw Azure-account. Klik in de activiteiten balk op het Azure-logo om de **Azure app service** Explorer weer te geven. Klik op **Aanmelden bij Azure...** en volg de instructies.

![aanmelden bij Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout melding **' kan geen abonnement vinden met de naam [abonnements-id] '** ziet, kan het zijn dat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer `HTTP_PROXY` `export`en `HTTPS_PROXY` omgevings variabelen met uw proxy gegevens in uw Terminal met behulp van.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het probleem niet wordt opgelost door de omgevings variabelen in te stellen, kunt u contact met ons opnemen door hieronder op de knop met **een probleem** te klikken.

### <a name="prerequisite-check"></a>Controle van vereisten

Voordat u doorgaat, moet u ervoor zorgen dat alle vereiste onderdelen zijn geïnstalleerd en geconfigureerd.

In VS code ziet u uw Azure-e-mail adres in de status balk en uw abonnement in **Azure app service** Explorer.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Uw node. js-toepassing maken

Maak vervolgens een node. js-toepassing die in de cloud kan worden geïmplementeerd. In deze Snelstartgids wordt een toepassings generator gebruikt om de toepassing snel uit te steigeren vanuit een Terminal.

> [!TIP]
> Als u de [node. js-zelf studie](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)al hebt voltooid, kunt u verdergaan met [het implementeren van de website](#deploy-the-website).

### <a name="install-the-express-generator"></a>De Express-Generator installeren

[Express](https://www.expressjs.com) is een populair Framework voor het bouwen en uitvoeren van node. js-toepassingen. U kunt een nieuwe Express-toepassing maken met behulp van het hulp programma [Express Generator](https://expressjs.com/en/starter/generator.html) . De Express-generator wordt geleverd als een NPM-module en geïnstalleerd met behulp van het opdracht `npm`regel programma NPM.

```bash
npm install -g express-generator
```

Met `-g` de switch wordt de Express-Generator globaal op uw computer geïnstalleerd, zodat u deze overal kunt uitvoeren.

### <a name="scaffold-a-new-application"></a>Een nieuwe toepassing in de steiger

Vervolgens wordt een nieuwe Express-toepassing met `myExpressApp` de naam gestart:

```bash
express myExpressApp --view pug --git
```

De `--view pug --git` para meters geven de generator de opdracht om de [Pug](https://pugjs.org/api/getting-started.html) -sjabloon engine `jade`(voorheen bekend als) `.gitignore` te gebruiken en om een bestand te maken.

Als u alle afhankelijkheden van de toepassing wilt installeren, gaat u naar de nieuwe `npm install`map en voert u uit.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Controleer vervolgens of de toepassing wordt uitgevoerd. Start vanuit de Terminal de toepassing met behulp `npm start` van de opdracht om de server te starten.

```bash
npm start
```

Open nu uw browser en navigeer naar [http://localhost:3000](http://localhost:3000), waar u er ongeveer als volgt zou moeten uitzien:

![Een Express-toepassing uitvoeren](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>De website implementeren

In deze sectie implementeert u de node. js-website met behulp van VS code en de uitbrei ding Azure App Service. Deze Snelstartgids maakt gebruik van het meest eenvoudige implementatie model waarin uw app is ingepakt en geïmplementeerd op een Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Implementeren met behulp van Azure App Service

Open eerst de toepassingsmap in VS code.

```bash
code .
```

Klik in **Azure app service** Explorer op het pictogram met de blauwe pijl-omhoog om uw app te implementeren in Azure.

![Implementeren naar web-app](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> U kunt ook implementeren vanuit het **opdracht palet** (CTRL + SHIFT + P) door ' implementeren naar web-app ' te typen en **de Azure app service uit te voeren: Implementeren in web app** -opdracht.

1. Selecteer de map die u momenteel hebt geopend, `myExpressApp`.

2. Kies **nieuwe web-app maken**.

3. Typ een wereld wijd unieke naam voor de web-app en druk op ENTER. Geldige tekens voor de naam van een app zijn ' a-z ', ' 0-9 ' en '-'.

4. Kies uw **versie van node. js**, LTS wordt aanbevolen.

    Het meldings kanaal toont de Azure-resources die worden gemaakt voor uw app.

Klik op **Ja** wanneer u wordt gevraagd uw configuratie bij `npm install` te werken en op de doel server uit te voeren. De app wordt vervolgens geïmplementeerd.

![Geconfigureerde implementatie](./media/quickstart-nodejs/server-build.png)

Wanneer de implementatie wordt gestart, wordt u gevraagd om uw werk ruimte bij te werken zodat latere implementaties automatisch worden gericht op dezelfde App Service web-app. Kies **Ja** om te controleren of uw wijzigingen zijn geïmplementeerd naar de juiste app.

![Geconfigureerde implementatie](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Zorg ervoor dat uw toepassing luistert op de poort die is verschaft door de variabele poort `process.env.PORT`omgeving:.

### <a name="browse-the-website"></a>Door de website bladeren

Zodra de implementatie is voltooid, klikt u op **website bladeren** in de prompt om uw nieuwe website te bekijken.

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout **' u bent niet gemachtigd om deze map of pagina weer te geven '** ziet, kan de toepassing waarschijnlijk niet correct worden gestart. Kop naar de volgende sectie en bekijkt u de logboek uitvoer om de fout te vinden en op te lossen. Als u het niet kunt oplossen, neemt u contact met ons op door op de onderstaande knop met **een probleem** te klikken. We helpen u graag!

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>De website bijwerken

U kunt wijzigingen in deze app implementeren door hetzelfde proces te gebruiken en de bestaande app te kiezen in plaats van een nieuwe te maken.

## <a name="viewing-logs"></a>Logboeken weer geven

In deze sectie leert u hoe u de logboeken van de lopende website kunt weer geven (of ' staart '). Alle aanroepen `console.log` naar op de site worden weer gegeven in het uitvoer venster in Visual Studio code.

Zoek de app in de **Azure app service** Explorer, klik met de rechter muisknop op de app en kies **streaming-logboeken weer geven**.

Wanneer u hierom wordt gevraagd, kiest u logboek registratie inschakelen en start u de toepassing opnieuw. Zodra de app opnieuw is opgestart, wordt het venster VS code uitvoer geopend met een verbinding met de logboek stroom.

![Streaming-logboeken weer geven](./media/quickstart-nodejs/view-logs.png)

![Logboek registratie inschakelen en opnieuw starten](./media/quickstart-nodejs/enable-restart.png)

Na een paar seconden ziet u een bericht met de mede deling dat u bent verbonden met de service voor logboek registratie.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Vernieuw de pagina enkele keren in de browser om de logboek uitvoer te bekijken.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze Snelstartgids voltooid.

Bekijk vervolgens de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook downloaden door het [node Pack voor Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack te installeren.

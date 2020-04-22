---
title: 'Quickstart: Maak een Linux Node.js app'
description: Ga aan de slag met Linux-apps in Azure App Service door uw eerste Node.js-app te implementeren in een Linux-container in App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 08a246b07effb8d5f3e2473a4d7959882cf43235
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687942"
---
# <a name="create-a-nodejs-app-in-azure"></a>Een Node.js-app maken in Azure

Azure App Service biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze snelstart ziet u hoe u een Node.js-app naar Azure App Service implementeert.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-account hebt, [meldt u zich vandaag nog aan](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) voor een gratis account met $ 200 aan Azure-tegoeden om een combinatie van services uit te proberen.

U moet [Visual Studio Code](https://code.visualstudio.com/) geïnstalleerd samen met [Node.js en npm](https://nodejs.org/en/download), de Node.js package manager.

U moet ook de [Azure App Service-extensie](vscode:extension/ms-azuretools.vscode-azureappservice)installeren, die u gebruiken om Linux Web Apps te maken, beheren en implementeren op het Azure Platform as a Service (PaaS).

### <a name="sign-in"></a>Aanmelden

Zodra de extensie is geïnstalleerd, meldt u zich aan bij uw Azure-account. Selecteer in de activiteitsbalk het Azure-logo om de **AZURE APP SERVICE-verkenner** weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies.

![aanmelden bij Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout **'Geen abonnement met naam [abonnements-ID]' vinden,** kan dit zijn omdat u achter een proxy zit en de Azure API niet bereiken. `HTTP_PROXY` Configureer `HTTPS_PROXY` en omgevingsvariabelen met uw proxy-informatie in uw terminal met behulp van `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het instellen van de omgevingsvariabelen het probleem niet corrigeert, neemt u contact met ons op door hieronder de knop **Ik ben tegengelopen.**

### <a name="prerequisite-check"></a>Vereiste controle

Voordat u verdergaat, moet u ervoor zorgen dat u alle vereisten hebt geïnstalleerd en geconfigureerd.

In VS-code ziet u uw Azure-e-mailadres in de statusbalk en uw abonnement in de **AZURE APP SERVICE** Explorer.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Uw Node.js-toepassing maken

Maak vervolgens een Node.js-toepassing die kan worden geïmplementeerd in de cloud. Deze quickstart maakt gebruik van een applicatiegenerator om de toepassing van een terminal snel uit te steigeren.

> [!TIP]
> Als u de [zelfstudie Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)al hebt voltooid, u doorgaan [naar Implementeren naar Azure.](#deploy-to-azure)

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Steiger een nieuwe toepassing met de Express Generator

[Express](https://www.expressjs.com) is een populair raamwerk voor het bouwen en uitvoeren van Node.js-toepassingen. U een nieuwe Express-toepassing (maak) schavot (maken) met behulp van het [gereedschap Express Generator.](https://expressjs.com/en/starter/generator.html) De Express Generator wordt verzonden als een npm-module en kan direct (zonder `npx`installatie) worden uitgevoerd met behulp van het npm-command-line-gereedschap.

```bash
npx express-generator myExpressApp --view pug --git
```

De `--view pug --git` parameters vertellen de generator om de [pug](https://pugjs.org/api/getting-started.html) `jade`template engine (voorheen bekend als) te gebruiken en om een `.gitignore` bestand te maken.

Als u alle afhankelijkheden van de toepassing wilt installeren, gaat u naar de nieuwe map en voert u . `npm install`

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Zorg er vervolgens voor dat de toepassing wordt uitgevoerd. Start de toepassing vanaf de `npm start` terminal met de opdracht om de server te starten.

```bash
npm start
```

Open nu uw browser [http://localhost:3000](http://localhost:3000)en navigeer naar , waar u zoiets als dit zou moeten zien:

![Express-toepassing uitvoeren](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementeren in Azure

In deze sectie implementeert u uw Node.js-app met VS Code en de Azure App Service-extensie. Deze quickstart maakt gebruik van het meest elementaire implementatiemodel waarbij uw app wordt gezipt en geïmplementeerd in een Azure Web App op Linux.

### <a name="deploy-using-azure-app-service"></a>Implementeren met Azure App Service

Open eerst de toepassingsmap in VS Code.

```bash
code .
```

Selecteer in de **AZURE APP SERVICE** Explorer het blauwe pijl-omhoog-pictogram om uw app te implementeren in Azure.

![Implementeren in web-app](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> U ook implementeren vanuit het **opdrachtpalet** (Ctrl + SHIFT + P) door 'Implementeren naar web-app' te typen en de opdracht **Azure App Service: Deploy to Web App** uit te voeren.

1. Kies de map die u `myExpressApp`momenteel hebt geopend.

1. Kies **Nieuwe web-app maken,** die standaard wordt geïmplementeerd op App-service op Linux.

1. Typ een wereldwijd unieke naam voor uw web-app en druk op ENTER. Geldige tekens voor een app-naam zijn 'a-z', '0-9' en '-'.

1. Kies uw **Node.js-versie**, LTS wordt aanbevolen.

    Het meldingskanaal toont de Azure-resources die voor uw app worden gemaakt.

1. Selecteer **Ja** wanneer u wordt gevraagd `npm install` uw configuratie bij te werken om op de doelserver uit te voeren. Uw app wordt vervolgens geïmplementeerd.

    ![Geconfigureerde implementatie](./media/quickstart-nodejs/server-build.png)

1. Wanneer de implementatie wordt gestart, wordt u gevraagd uw werkruimte bij te werken, zodat latere implementaties automatisch op dezelfde Web App-app van appservice worden gericht. Kies **Ja** om ervoor te zorgen dat uw wijzigingen worden geïmplementeerd in de juiste app.

    ![Geconfigureerde implementatie](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Zorg ervoor dat uw toepassing luistert op de poort `process.env.PORT`die wordt geleverd door de PORT-omgevingsvariabele: .

### <a name="browse-the-app-in-azure"></a>Blader door de app in Azure

Zodra de implementatie is voltooid, selecteert u **Website bladeren** in de prompt om uw nieuw geïmplementeerde web-app te bekijken.

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout **'U hebt geen toestemming hebt om deze map of pagina te bekijken'** ziet, kan de toepassing waarschijnlijk niet correct worden gestart. Ga naar de volgende sectie en bekijk de logboekuitvoer om de fout te vinden en op te lossen. Als u niet in staat bent om het te repareren, neem dan contact met ons op door het selecteren van de **ik liep in een probleem** knop hieronder. Wij helpen u graag!

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>De app bijwerken

U wijzigingen in deze app implementeren door hetzelfde proces te gebruiken en de bestaande app te kiezen in plaats van een nieuwe app te maken.

## <a name="viewing-logs"></a>Logboeken weergeven

In deze sectie leert u hoe u de logboeken weergeven (of volgen) vanuit de app app.This section, you learn how to view (or tail) the logs from the running App Service app. Alle oproepen `console.log` naar de app worden weergegeven in het uitvoervenster in Visual Studio Code.

Zoek de app in de **AZURE APP SERVICE** Explorer, klik met de rechtermuisknop op de app en kies **Streaminglogboeken weergeven**.

Kies desgevraagd om logboekregistratie in te schakelen en de toepassing opnieuw te starten. Zodra de app opnieuw is opgestart, wordt het uitvoervenster VS-code geopend met een verbinding met de logboekstream.

![Streaminglogboeken weergeven](./media/quickstart-nodejs/view-logs.png)

![Logboekregistratie inschakelen en opnieuw opstarten](./media/quickstart-nodejs/enable-restart.png)

Na een paar seconden zie je een bericht dat je verbonden bent met de log-streaming service. Vernieuw de pagina een paar keer om meer activiteit te zien.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, je hebt deze quickstart met succes voltooid!

Bekijk vervolgens de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Hulpprogramma's voor Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Of haal ze allemaal op door het [Node Pack voor](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Azure-uitbreidingspakket te installeren.

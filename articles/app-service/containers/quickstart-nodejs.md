---
title: 'Snelstartgids: een Linux node. js-app maken'
description: Ga aan de slag met Linux-apps op Azure App Service door uw eerste node. js-app te implementeren in een Linux-container in App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 52466bac083f78002a8208ba52ca7d1b951c4064
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801481"
---
# <a name="create-a-nodejs-app-in-azure"></a>Een node. js-app maken in azure

Azure App Service biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze snelstart ziet u hoe u een Node.js-app naar Azure App Service implementeert.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-account hebt, [meldt u zich vandaag](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) aan voor een gratis account met $200 in azure-tegoed om een wille keurige combi natie van services uit te proberen.

U moet [Visual Studio code](https://code.visualstudio.com/) installeren, samen met [node. js en NPM](https://nodejs.org/en/download), het node. js-pakket beheer.

U moet ook de [Azure app service-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)installeren, die u kunt gebruiken voor het maken, beheren en implementeren van Linux-web apps op het Azure-Platform as a Service (PaaS).

### <a name="sign-in"></a>Hier

Nadat de extensie is geïnstalleerd, meldt u zich aan bij uw Azure-account. Selecteer in de activiteiten balk het Azure-logo om de **Azure app service** Explorer weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies.

![aanmelden bij Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Probleemoplossing

Als u de fout melding **' kan geen abonnement vinden met de naam [abonnements-id] '** ziet, kan het zijn dat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer `HTTP_PROXY` en `HTTPS_PROXY` omgevings variabelen met uw proxy gegevens in uw Terminal `export`met behulp van.

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
> Als u de [node. js-zelf studie](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)al hebt voltooid, kunt u verder naar [Azure implementeren](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Steiger een nieuwe toepassing met de Express-generator

[Express](https://www.expressjs.com) is een populair Framework voor het bouwen en uitvoeren van node. js-toepassingen. U kunt een nieuwe Express-toepassing maken met behulp van het hulp programma [Express Generator](https://expressjs.com/en/starter/generator.html) . De Express-generator wordt geleverd als een NPM-module en kan rechtstreeks worden uitgevoerd (zonder installatie) met behulp van het opdracht `npx`regel programma NPM.

```bash
npx express-generator myExpressApp --view pug --git
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

Open nu uw browser en navigeer naar `http://localhost:3000`, waar u er ongeveer als volgt zou moeten uitzien:

![Een Express-toepassing uitvoeren](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementeren in Azure

In deze sectie implementeert u uw node. js-app met behulp van VS code en de uitbrei ding Azure App Service. Deze Snelstartgids maakt gebruik van het meest eenvoudige implementatie model waarin uw app is ingepakt en geïmplementeerd op een Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Implementeren met behulp van Azure App Service

Open eerst de toepassingsmap in VS code.

```bash
code .
```

Selecteer in de Verkenner van **Azure app service** het pictogram met de blauwe pijl-omhoog om uw app te implementeren in Azure.

![Implementeren naar web-app](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> U kunt ook implementeren vanuit het **opdracht palet** (CTRL + SHIFT + P) door ' implementeren naar web-app ' te typen en de opdracht **Azure app service: implementeren op Web app** uit te voeren.

1. Selecteer de map die u momenteel hebt geopend, `myExpressApp`.

1. Kies **nieuwe web-app maken**die standaard wordt geïmplementeerd in app service op Linux.

1. Typ een wereld wijd unieke naam voor de web-app en druk op ENTER. Geldige tekens voor de naam van een app zijn ' a-z ', ' 0-9 ' en '-'.

1. Kies uw **versie van node. js**, LTS wordt aanbevolen.

    Het meldings kanaal toont de Azure-resources die worden gemaakt voor uw app.

1. Selecteer **Ja** wanneer u wordt gevraagd uw configuratie bij te `npm install` werken en op de doel server uit te voeren. De app wordt vervolgens geïmplementeerd.

    ![Geconfigureerde implementatie](./media/quickstart-nodejs/server-build.png)

1. Wanneer de implementatie wordt gestart, wordt u gevraagd om uw werk ruimte bij te werken zodat latere implementaties automatisch worden gericht op dezelfde App Service web-app. Kies **Ja** om te controleren of uw wijzigingen zijn geïmplementeerd naar de juiste app.

    ![Geconfigureerde implementatie](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Zorg ervoor dat uw toepassing luistert op de poort die is verschaft door de variabele poort `process.env.PORT`omgeving:.

### <a name="browse-the-app-in-azure"></a>Door de app in azure bladeren

Zodra de implementatie is voltooid, selecteert u **Bladeren website** in de prompt om uw nieuw geïmplementeerde web-app weer te geven.

### <a name="troubleshooting"></a>Probleemoplossing

Als u de fout **' u bent niet gemachtigd om deze map of pagina weer te geven '** ziet, kan de toepassing waarschijnlijk niet correct worden gestart. Kop naar de volgende sectie en bekijkt u de logboek uitvoer om de fout te vinden en op te lossen. Als u het niet kunt oplossen, neemt u contact met ons op door de onderstaande knop met **een probleem** te selecteren. We helpen u graag!

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>De app bijwerken

U kunt wijzigingen in deze app implementeren door hetzelfde proces te gebruiken en de bestaande app te kiezen in plaats van een nieuwe te maken.

## <a name="viewing-logs"></a>Logboeken weer geven

In deze sectie leert u hoe u de logboeken van de actieve App Service-app kunt weer geven (of ' staart '). Alle aanroepen `console.log` naar in de app worden weer gegeven in het venster uitvoer in Visual Studio code.

Zoek de app in de **Azure app service** Explorer, klik met de rechter muisknop op de app en kies **streaming-logboeken weer geven**.

Het VS code-uitvoer venster wordt geopend met een verbinding met de logboek stroom.

![Streaming-logboeken weer geven](./media/quickstart-nodejs/view-logs.png)

![Logboek registratie inschakelen en opnieuw starten](./media/quickstart-nodejs/enable-restart.png)

Na een paar seconden ziet u een bericht met de mede deling dat u bent verbonden met de service voor logboek registratie. Vernieuw de pagina enkele keren om meer activiteit weer te geven.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze Snelstartgids voltooid.

Bekijk vervolgens de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook downloaden door het [node Pack voor Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack te installeren.

---
title: 'Snelstartgids: een node. js-web-app maken'
description: Implementeer in enkele minuten uw eerste node. js-Hallo wereld naar Azure App Service. U implementeert met behulp van Visual Studio code, een van de vele manieren waarop u kunt implementeren in App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/30/2019
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 91494cc9c1e3a1fc159702bdbb7f68a4423b604c
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671369"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure 

Azure App Service biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In deze snelstart ziet u hoe u een Node.js-app naar Azure App Service implementeert.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-account hebt, [meldt u zich vandaag](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) aan voor een gratis account met $200 in azure-tegoed om een wille keurige combi natie van services uit te proberen.

U moet [Visual Studio code](https://code.visualstudio.com/) installeren, samen met [node. js en NPM](https://nodejs.org/en/download), het node. js-pakket beheer.

U moet ook de [Azure app service-extensie](vscode:extension/ms-azuretools.vscode-azureappservice)installeren, die u kunt gebruiken voor het maken, beheren en implementeren van Linux-web apps op het Azure-Platform as a Service (PaaS).

### <a name="sign-in"></a>Aanmelden

Nadat de extensie is geïnstalleerd, meldt u zich aan bij uw Azure-account. Selecteer op de activiteiten balk in het Azure-logo om de **Azure app service** Explorer weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies.

![aanmelden bij Azure](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout melding **' kan geen abonnement vinden met de naam [abonnements-id] '** ziet, kan het zijn dat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer `HTTP_PROXY`-en `HTTPS_PROXY` omgevings variabelen met uw proxy gegevens in uw Terminal met behulp van `export`.

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

[Express](https://www.expressjs.com) is een populair Framework voor het bouwen en uitvoeren van node. js-toepassingen. U kunt een nieuwe Express-toepassing maken met behulp van het hulp programma [Express Generator](https://expressjs.com/en/starter/generator.html) . De Express-generator wordt geleverd als een NPM-module en kan rechtstreeks worden uitgevoerd (zonder installatie) met behulp van het NPM-opdracht regel programma `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

De `--view pug --git`-para meters geven de generator de [Pug](https://pugjs.org/api/getting-started.html) -sjabloon Engine (voorheen bekend als `jade`) en om een `.gitignore` bestand te maken.

Als u alle afhankelijkheden van de toepassing wilt installeren, gaat u naar de nieuwe map en voert u `npm install`uit.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Controleer vervolgens of de toepassing wordt uitgevoerd. Start vanuit de Terminal de toepassing met behulp van de `npm start` opdracht om de server te starten.

```bash
npm start
```

Open nu uw browser en navigeer naar [http://localhost:3000](http://localhost:3000), waar u er ongeveer als volgt zou moeten uitzien:

![Een Express-toepassing uitvoeren](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implementatie in Azure

In deze sectie implementeert u uw node. js-app met behulp van VS code en de uitbrei ding Azure App Service. Deze Snelstartgids maakt gebruik van het meest eenvoudige implementatie model waarin uw app is ingepakt en geïmplementeerd op een Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Implementeren met behulp van Azure App Service

Open eerst de toepassingsmap in VS code.

```bash
code .
```

Selecteer in de Verkenner van **Azure app service** het pictogram met de blauwe pijl-omhoog om uw app te implementeren in Azure.

![Implementeren naar web-app](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> U kunt ook implementeren vanuit het **opdracht palet** (CTRL + SHIFT + P) door ' implementeren naar web-app ' te typen en de opdracht **Azure app service: implementeren op Web app** uit te voeren.

1. Kies de map die momenteel is geopend, `myExpressApp`.

1. Kies een optie voor het maken op basis van het besturings systeem dat u wilt implementeren:

    - Linux: Kies **nieuwe web-app maken**.
    - Windows: Kies **nieuwe web-app maken** en selecteer de optie **Geavanceerd** .

1. Typ een wereld wijd unieke naam voor de web-app en druk op ENTER. Geldige tekens voor de naam van een app zijn ' a-z ', ' 0-9 ' en '-'.

1. Als u een Linux-doel groep hebt, selecteert u een node. js-versie wanneer u hierom wordt gevraagd. Een **LTS** -versie wordt aanbevolen.

1. Als u de optie voor het maken van Vensters wilt gebruiken, volgt **u de extra** prompts:
    1. Selecteer **een nieuwe resource groep maken**en voer een naam in voor de resource groep.
    1. Selecteer **Windows** voor het besturings systeem.
    1. Selecteer een bestaand App Service plan of maak een nieuw abonnement. U kunt een prijs categorie selecteren bij het maken van een nieuw plan.
    1. Kies overs **Laan nu** wanneer u wordt gevraagd over Application Insights.
    1. Kies een regio bij u in de buurt of in de buurt van de resources die u wilt gebruiken.

1. Nadat u op alle prompts hebt gereageerd, worden de Azure-resources die voor uw app worden gemaakt, weer gegeven in het meldings kanaal.

1. Selecteer **Ja** wanneer u wordt gevraagd uw configuratie bij te werken om `npm install` op de doel server uit te voeren. De app wordt vervolgens geïmplementeerd.

    ![Geconfigureerde implementatie](containers/media/quickstart-nodejs/server-build.png)

1. Wanneer de implementatie wordt gestart, wordt u gevraagd om uw werk ruimte bij te werken zodat latere implementaties automatisch worden gericht op dezelfde App Service web-app. Kies **Ja** om te controleren of uw wijzigingen zijn geïmplementeerd naar de juiste app.

    ![Geconfigureerde implementatie](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Zorg ervoor dat uw toepassing luistert op de poort die is verschaft door de variabele poort omgeving: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Door de app in azure bladeren

Zodra de implementatie is voltooid, selecteert u **Bladeren website** in de prompt om uw nieuw geïmplementeerde web-app weer te geven.

### <a name="troubleshooting"></a>Problemen oplossen

Als u de fout **' u bent niet gemachtigd om deze map of pagina weer te geven '** ziet, kan de toepassing waarschijnlijk niet correct worden gestart. Kop naar de volgende sectie en bekijkt u de logboek uitvoer om de fout te vinden en op te lossen. Als u het niet kunt oplossen, neemt u contact met ons op door de onderstaande knop met **een probleem** te selecteren. We helpen u graag!

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>De app bijwerken

U kunt wijzigingen in deze app implementeren door hetzelfde proces te gebruiken en de bestaande app te kiezen in plaats van een nieuwe te maken.

## <a name="viewing-logs"></a>Logboeken weer geven

In deze sectie leert u hoe u de logboeken van de actieve App Service-app kunt weer geven (of ' staart '). Alle aanroepen naar `console.log` in de app worden weer gegeven in het venster uitvoer in Visual Studio code.

Zoek de app in de **Azure app service** Explorer, klik met de rechter muisknop op de app en kies **streaming-logboeken weer geven**.

Wanneer u hierom wordt gevraagd, kiest u logboek registratie inschakelen en start u de toepassing opnieuw. Zodra de app opnieuw is opgestart, wordt het venster VS code uitvoer geopend met een verbinding met de logboek stroom.

![Streaming-logboeken weer geven](containers/media/quickstart-nodejs/view-logs.png)

![Logboek registratie inschakelen en opnieuw starten](containers/media/quickstart-nodejs/enable-restart.png)

Na een paar seconden ziet u een bericht met de mede deling dat u bent verbonden met de service voor logboek registratie. Vernieuw de pagina enkele keren om meer activiteit weer te geven.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
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

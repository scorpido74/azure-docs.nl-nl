---
title: 'Quickstart: Een Node.js-web-app maken'
description: Implementeer in enkele minuten uw eerste Node.js Hallo wereld naar Azure App Service. U implementeert met Visual Studio Code, een van de vele manieren waarop u kunt implementeren naar App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18, devx-track-javascript
ms.openlocfilehash: 0e72c17ab20d092a710bb21b1ff6d3d6418e452f
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170267"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure 

Ga aan de slag met Azure App Service door een lokale Node.js/Express-app te maken met Visual Studio Code en de app vervolgens te implementeren in de cloud. Omdat u een gratis servicelaag van App Service gebruikt, zijn er geen kosten verbonden aan het voltooien van deze quickstart.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- [Node.js en NPM](https://nodejs.org). Voer de opdracht `node --version` uit om te controleren of Node.js is geïnstalleerd.
- [Visual Studio Code](https://code.visualstudio.com/).
- De [Azure App Service-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) voor Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Een lokale Node.js-toepassing klonen en uitvoeren

1. Open een terminal op uw lokale computer en kloon de voorbeeldopslagplaats:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigeer naar de map voor de nieuwe app:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Start de app om deze lokaal te testen:

    ```bash
    npm start
    ```
    
1. Open uw browser en ga naar `http://localhost:1337`. In de browser moet ‘Hallo wereld!’ worden weergegeven.

1. Druk op **Ctrl**+**C** in de terminal om de server te stoppen.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>De app implementeren in Azure

In deze sectie implementeert u de Node.js-app in Azure met behulp van VS Code en de Azure App Service-extensie.

1. Controleer in de terminal of u zich in de map *nodejs-docs-hello-world* bevindt en start Visual Studio Code met de volgende opdracht:

    ```bash
    code .
    ```

1. Selecteer op de activiteitenbalk van VS Code het Azure-logo om de verkenner van **AZURE APP SERVICE** weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies. (Zie [Probleem oplossen met aanmelden bij Azure](#troubleshooting-azure-sign-in) hieronder als er fouten optreden.) Als u bent aangemeld, moet de verkenner de naam van uw Azure-abonnement weergeven.

    ![Aanmelden bij Azure](containers/media/quickstart-nodejs/sign-in.png)

1. Selecteer in de verkenner van **AZURE APP SERVICE** binnen VS Code het pictogram met de blauwe pijl omhoog om uw app te implementeren in Azure. (U kunt dezelfde opdracht ook aanroepen vanuit het **Opdrachtpalet** (**Ctrl**+**Shift**+**P**) door 'implementeren naar web-app' te typen en **Azure App Service: Implementeren naar web-app** te kiezen).

    ![Implementeren naar web-app](containers/media/quickstart-nodejs/deploy.png)
        
1. Kies de map *nodejs-docs-hello-world*.

1. Kies een optie voor het maken van een web-app op basis van het besturingssysteem waarnaar u wilt implementeren:

    - Linux: Kies **Nieuwe web-app maken**
    - Windows: Kies **Nieuwe web-app maken... Geavanceerd**

1. Typ een unieke naam voor uw web-app en druk op **Enter**. De naam moet uniek zijn binnen Azure en mag alleen alfanumerieke tekens (‘A-Z’, ‘a-z’ en ‘0-9’) en afbreekstreepjes (‘-’) bevatten.

1. Als u Linux gebruikt, selecteert u een node.js-versie wanneer hierom wordt gevraagd. Een **LTS**-versie wordt aanbevolen.

1. Als u Windows gebruikt, volgt u de extra prompts:
    1. Selecteer **Een nieuwe resourcegroep maken** en voer een naam in voor de resourcegroep, zoals `AppServiceQS-rg`.
    1. Selecteer **Windows** als uw besturingssysteem.
    1. Selecteer **Nieuw App Service-plan maken**, voer een naam in voor het plan (bijvoorbeeld `AppServiceQS-plan`) en selecteer vervolgens **F1 Gratis** als prijscategorie.
    1. Kies **Voorlopig overslaan** wanneer u een vraag krijgt over Application Insights.
    1. Kies een regio bij u in de buurt of in de buurt van de resources die u wilt gebruiken.

1. Nadat u alle prompts hebt doorlopen, worden de Azure-resources die met VS Code worden gemaakt voor uw app weergegeven in het pop-upvenster.

    Wanneer u implementeert in Linux, selecteert u **Ja** wanneer u wordt gevraagd uw configuratie bij te werken om `npm install` uit te voeren op de Linux-doelserver.

    ![Prompt om de configuratie van de Linux-doelserver bij te werken](containers/media/quickstart-nodejs/server-build.png)

1. Selecteer **Ja** wanneer u de melding **De werkruimte ‘nodejs-docs-hello-world’ altijd implementeren naar (app-naam)** krijgt. Als u **Ja** selecteert, gebruikt VS Code automatisch dezelfde App Service-web-app bij volgende implementaties.

1. Als u implementeert in Linux, selecteert u **Bladeren door website** in de prompt om uw zojuist geïmplementeerde web-app weer te geven nadat de implementatie is voltooid. In de browser moet ‘Hallo wereld!’ worden weergegeven.

1. Als u implementeert in Windows, moet u eerst het Node.js-versienummer van de web-app instellen.

    1. Vouw in VS Code het knooppunt voor de nieuwe app-service uit, klik met de rechtermuisknop op **Toepassingsinstellingen** en selecteer **Nieuwe instelling toevoegen...** :

        ![Opdracht App-instelling toevoegen](containers/media/quickstart-nodejs/add-setting.png)

    1. Voer `WEBSITE_NODE_DEFAULT_VERSION` in bij de instellingssleutel.
    1. Voer `10.15.2` in bij de instellingswaarde.
    1. Klik met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Opnieuw opstarten**

        ![Opdracht App-service opnieuw opstarten](containers/media/quickstart-nodejs/restart.png)

    1. Klik met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Bladeren door website**

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Probleemoplossing voor inloggen bij Azure

Als de fout **‘Kan geen abonnement vinden met de naam [abonnements-id]’** wordt weergegeven als u zich aanmeldt bij Azure, kan dit zijn omdat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer de omgevingsvariabelen `HTTP_PROXY` en `HTTPS_PROXY` met uw proxygegevens in uw terminal met behulp van `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het probleem niet wordt opgelost door de omgevingsvariabelen in te stellen, kunt u contact met ons opnemen door de knop **Er is een fout opgetreden** hierboven te selecteren.

### <a name="update-the-app"></a>De app bijwerken

U kunt wijzigingen in deze app implementeren door wijzigingen in VS Code aan te brengen, uw bestanden op te slaan en vervolgens hetzelfde proces te gebruiken als hierboven, alleen selecteert u nu de bestaande app en maakt u geen nieuwe.

## <a name="viewing-logs"></a>Logboeken weergeven

U kunt de uitvoer van logboeken (aanroepen naar `console.log`) van de app rechtstreeks bekijken in het uitvoervenster van VS Code.

1. Klik in de verkenner van **AZURE APP SERVICE** met de rechtermuisknop op het app-knooppunt en kies **Streaminglogboeken starten**.

    ![Streaminglogboeken starten](containers/media/quickstart-nodejs/view-logs.png)

1. Wanneer u hierom wordt gevraagd, kiest u ervoor om logboekregistratie in te schakelen en start u de toepassing opnieuw. Zodra de app opnieuw is opgestart, wordt het uitvoervenster van VS Code geopend met een verbinding met de logboekstroom. 

    ![Logboekregistratie inschakelen en opnieuw opstarten](containers/media/quickstart-nodejs/enable-restart.png)

1. Na enkele seconden wordt in het uitvoervenster een bericht weergegeven dat aangeeft dat u bent verbonden met de streamservice voor logboekregistratie. U kunt meer uitvoeractiviteiten genereren door de pagina in de browser te vernieuwen.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt deze quickstart voltooid.

> [!div class="nextstepaction"]
> [Zelfstudie: Node.js-app met MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Node.js-app configureren](configure-language-nodejs.md)

Bekijk de andere Azure-extensies.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager-hulpprogramma’s](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

U kunt ze ook allemaal downloaden door het extensiepakket [Node-pakket voor Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren.


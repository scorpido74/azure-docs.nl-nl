---
title: 'Snelstart: een web-app Node.js maken'
description: Implementeer uw eerste Node.js Hello World binnen enkele minuten naar Azure App Service. U implementeert met Behulp van Visual Studio Code, een van de vele manieren om te implementeren in App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047123"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure 

Ga aan de slag met Azure App Service door een Node.js/Express-app lokaal te maken met Visual Studio Code en vervolgens de app in de cloud te implementeren. Omdat u een gratis appservicelaag gebruikt, hoeft u geen kosten te maken om deze quickstart te voltooien.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)
- [Node.js en npm](https://nodejs.org). Voer de `node --version` opdracht uit om te controleren of Node.js is geïnstalleerd.
- [Visual Studio Code](https://code.visualstudio.com/).
- De [Azure App Service-extensie](vscode:extension/ms-azuretools.vscode-azureappservice) voor Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Een lokale node.js-toepassing klonen en uitvoeren

1. Open op uw lokale computer een terminal en kloon de voorbeeldopslagplaats:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigeer naar de nieuwe app-map:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Start de app om deze lokaal te testen:

    ```bash
    npm start
    ```
    
1. Open uw browser [http://localhost:1337](http://localhost:1337)en navigeer naar . De browser moet "Hello World!" weergeven.

1. Druk **op Ctrl**+**C** in de terminal om de server te stoppen.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>De app implementeren in Azure

In deze sectie implementeert u uw Node.js-app naar Azure met VS-code en de Azure App Service-extensie.

1. Zorg er in de terminal voor dat u zich in de map *nodejs-docs-hello-world* bevindt en start Visual Studio Code met de volgende opdracht:

    ```bash
    code .
    ```

1. Selecteer in de activiteitenbalk VS Code het Azure-logo om de **AZURE APP SERVICE** Explorer weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies. (Zie [Azure-aanmelding](#troubleshooting-azure-sign-in) hieronder oplossen als u fouten tegenkomt.) Nadat de verkenner is aangemeld, moet de naam van uw Azure-abonnement worden weergegeven.

    ![Aanmelden bij Azure](containers/media/quickstart-nodejs/sign-in.png)

1. Selecteer in de **AZURE APP SERVICE** Explorer van VS Code het blauwe pijl-uppictogram om uw app te implementeren in Azure. (U dezelfde opdracht ook aanroepen in het **opdrachtpalet** **(Ctrl**+**Shift**+**P)** door 'Deploy to web app' te typen en **Azure App Service: Deploy to Web App te**kiezen).

    ![Implementeren in web-app](containers/media/quickstart-nodejs/deploy.png)
        
1. Kies de map *nodejs-docs-hello-world.*

1. Kies een aanmaakoptie op basis van het besturingssysteem waarvoor u wilt implementeren:

    - Linux: kies **Nieuwe Web App maken**
    - Windows: Kies **Nieuwe Web App maken... Geavanceerd**

1. Typ een wereldwijd unieke naam voor uw web-app en druk op **Enter**. De naam moet uniek zijn voor heel Azure en alleen alfanumerieke tekens gebruiken ('A-Z', 'a-z' en '0-9') en koppeltekens ('-').

1. Als u Linux target, selecteert u een Node.js-versie wanneer deze wordt gevraagd. Een **LTS-versie** wordt aanbevolen.

1. Volg de aanvullende aanwijzingen als u Windows target:
    1. Selecteer **Een nieuwe resourcegroep maken**en voer vervolgens een `AppServiceQS-rg`naam in voor de resourcegroep, zoals .
    1. Selecteer **Windows** voor het besturingssysteem.
    1. Selecteer **Nieuw App Service-abonnement maken**en voer vervolgens `AppServiceQS-plan`een naam in voor het abonnement (zoals) en selecteer **F1 Gratis** voor de prijscategorie.
    1. Kies **Overslaan voor nu** wanneer u wordt gevraagd over Toepassingsinzichten.
    1. Kies een regio bij u in de buurt of in de buurt van bronnen die u wilt openen.

1. Nadat u op alle aanwijzingen hebt gereageerd, toont VS Code de Azure-bronnen die voor uw app worden gemaakt in de pop-up van de melding.

    Wanneer u wordt geïmplementeerd **Yes** op Linux, selecteert u `npm install` Ja wanneer u wordt gevraagd uw configuratie bij te werken om op de doel-Linux-server te worden uitgevoerd.

    ![Prompt om de configuratie op de doel-Linux-server bij te werken](containers/media/quickstart-nodejs/server-build.png)

1. Selecteer **Ja** wanneer u wordt gevraagd met **Altijd de werkruimte 'nodejs-docs-hello-world' implementeren voor (app-naam)"**. Als **u Ja** selecteert, moet VS-code automatisch dezelfde App Service Web App targeten met volgende implementaties.

1. Als u wordt geïmplementeerd op Linux, selecteert u **Website bladeren** in de prompt om uw nieuw geïmplementeerde web-app te bekijken zodra de implementatie is voltooid. De browser moet "Hello World!" weergeven.

1. Als u wordt geïmplementeerd in Windows, moet u eerst het versienummer van Node.js instellen voor de web-app:

    1. Vouw in VS-code het knooppunt voor de nieuwe app-service uit, klik met de rechtermuisknop op **Toepassingsinstellingen**en selecteer **Nieuwe instelling toevoegen...**:

        ![Opdracht App-instelling toevoegen](containers/media/quickstart-nodejs/add-setting.png)

    1. Voer `WEBSITE_NODE_DEFAULT_VERSION` de instellingssleutel in.
    1. Voer `10.15.2` de instellingswaarde in.
    1. Klik met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Opnieuw starten**

        ![Opdracht App-service opnieuw starten](containers/media/quickstart-nodejs/restart.png)

    1. Klik nogmaals met de rechtermuisknop op het knooppunt voor de app-service en selecteer **Website bladeren**.

> [!div class="nextstepaction"]
> [Ik liep in een probleem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure-aanmelding oplossen

Als u de fout **'Geen abonnement met naam [abonnements-ID]] vinden** bij het aanmelden bij Azure, kan dit zijn omdat u achter een proxy zit en de Azure API niet bereiken. `HTTP_PROXY` Configureer `HTTPS_PROXY` en omgevingsvariabelen met uw proxy-informatie in uw terminal met behulp van `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het instellen van de omgevingsvariabelen het probleem niet corrigeert, neemt u contact met ons op door de bovenstaande **knop Ik ben tegen gelopen.**

### <a name="update-the-app"></a>De app bijwerken

U wijzigingen in deze app implementeren door bewerkingen in VS-code uit te voeren, uw bestanden op te slaan en vervolgens hetzelfde proces te gebruiken als voordat u alleen de bestaande app kiest in plaats van een nieuwe app te maken.

## <a name="viewing-logs"></a>Logboeken weergeven

U logboekuitvoer (aanroepen naar) `console.log`rechtstreeks vanuit de app bekijken in het uitvoervenster VS-code.

1. Klik in de **AZURE APP SERVICE** Explorer met de rechtermuisknop op het app-knooppunt en kies **Streaminglogboeken starten**.

    ![Streaminglogboeken starten](containers/media/quickstart-nodejs/view-logs.png)

1. Kies desgevraagd om logboekregistratie in te schakelen en de toepassing opnieuw te starten. Zodra de app opnieuw is opgestart, wordt het uitvoervenster VS-code geopend met een verbinding met de logboekstream. 

    ![Logboekregistratie inschakelen en opnieuw opstarten](containers/media/quickstart-nodejs/enable-restart.png)

1. Na een paar seconden wordt in het uitvoervenster een bericht weergegeven dat u bent verbonden met de aanmeldservice. U meer uitvoeractiviteit genereren door de pagina in de browser te vernieuwen.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
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

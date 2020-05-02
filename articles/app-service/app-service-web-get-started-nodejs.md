---
title: 'Snelstartgids: een node. js-web-app maken'
description: Implementeer in enkele minuten uw eerste node. js-Hallo wereld naar Azure App Service. U implementeert met behulp van Visual Studio code, een van de vele manieren waarop u kunt implementeren in App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: df1e0809c0122eae593543cd193a6477b03d0938
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690799"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Een Node.js-web-app maken in Azure 

Ga aan de slag met Azure App Service door een node. js/Express-app lokaal te maken met Visual Studio code en vervolgens de app te implementeren in de Cloud. Omdat u een gratis App Service laag gebruikt, zijn er geen kosten om deze Snelstartgids te volt ooien.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node. js en NPM](https://nodejs.org). Voer de opdracht `node --version` uit om te controleren of node. js is geïnstalleerd.
- [Visual Studio code](https://code.visualstudio.com/).
- De [uitbrei ding](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) van de Azure app service voor Visual Studio code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Een lokale node. js-toepassing klonen en uitvoeren

1. Open een Terminal op uw lokale computer en kloon de voor beeld-opslag plaats:

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
    
1. Open uw browser en ga naar `http://localhost:1337`. In de browser moet Hallo wereld! worden weer gegeven.

1. Druk op **CTRL**+**C** in de terminal om de server te stoppen.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>De app implementeren in Azure

In deze sectie implementeert u de node. js-app in azure met behulp van VS code en de uitbrei ding Azure App Service.

1. Zorg ervoor dat u zich in de map *nodejs-docs-Hallo-World* bevindt in de Terminal en Start Visual Studio code met de volgende opdracht:

    ```bash
    code .
    ```

1. Selecteer op de activiteiten balk VS-code het Azure-logo om de **Azure app service** Explorer weer te geven. Selecteer **Aanmelden bij Azure...** en volg de instructies. (Zie [probleem oplossing voor Azure-aanmelding](#troubleshooting-azure-sign-in) hieronder als er fouten optreden.) Als u bent aangemeld, moet de Explorer de naam van uw Azure-abonnement weer geven.

    ![Aanmelden bij Azure](containers/media/quickstart-nodejs/sign-in.png)

1. Selecteer in **Azure app service** Explorer van VS code het pictogram met de blauwe pijl-omhoog om uw app te implementeren in Azure. (U kunt dezelfde opdracht ook aanroepen vanuit het **opdracht palet** (**CTRL**+**SHIFT**+**P**) door ' implementeren naar web-app ' te typen en **Azure app service: implementeren naar web-app te**kiezen.

    ![Implementeren naar web-app](containers/media/quickstart-nodejs/deploy.png)
        
1. Kies de map *nodejs-docs-Hello-World* .

1. Kies een optie voor het maken op basis van het besturings systeem dat u wilt implementeren:

    - Linux: Kies **nieuwe web-app maken**
    - Windows: Kies **nieuwe web-app maken... Geavanceerd**

1. Typ een wereld wijd unieke naam voor de web-app en druk op **Enter**. De naam moet uniek zijn voor alle Azure en alleen alfanumerieke tekens (A-Z, a-z en ' 0-9 ') en afbreek streepjes ('-') gebruiken.

1. Als u een Linux-doel groep hebt, selecteert u een node. js-versie wanneer u hierom wordt gevraagd. Een **LTS** -versie wordt aanbevolen.

1. Als u het doel van Windows hebt, volgt u de extra prompts:
    1. Selecteer **een nieuwe resource groep maken**en voer een naam in voor de resource groep, zoals `AppServiceQS-rg`.
    1. Selecteer **Windows** voor het besturings systeem.
    1. Selecteer **nieuw app service plan maken**, voer een naam in voor het plan (bijvoorbeeld `AppServiceQS-plan`) en selecteer vervolgens **F1 gratis** voor de prijs categorie.
    1. Kies overs **Laan nu** wanneer u wordt gevraagd over Application Insights.
    1. Kies een regio bij u in de buurt of in de buurt van de resources die u wilt gebruiken.

1. Nadat u op alle prompts hebt gereageerd, worden de Azure-resources die worden gemaakt voor uw app weer gegeven in het pop-upvenster.

    Wanneer u implementeert in Linux, selecteert u **Ja** wanneer u wordt gevraagd uw `npm install` configuratie bij te werken voor uitvoering op de Linux-doel server.

    ![Vragen om configuratie van de Linux-doel server bij te werken](containers/media/quickstart-nodejs/server-build.png)

1. Selecteer **Ja** wanneer u wordt gevraagd **om de werk ruimte "nodejs-docs-Hallo-World" to (app name) te implementeren**. Als u **Ja** selecteert, wordt de code vergeleken met het automatisch richten op dezelfde app service Web-app met de volgende implementaties.

1. Als u implementeert in Linux, selecteert u **website bladeren** in de prompt om uw nieuwste geïmplementeerde web-app weer te geven nadat de implementatie is voltooid. In de browser moet Hallo wereld! worden weer gegeven.

1. Als u naar Windows implementeert, moet u eerst het versie nummer van node. js instellen voor de web-app:

    1. Vouw in VS code het knoop punt voor de nieuwe app service uit, klik met de rechter muisknop op **Toepassings instellingen**en selecteer **nieuwe instelling toevoegen...**:

        ![App-instelling toevoegen opdracht](containers/media/quickstart-nodejs/add-setting.png)

    1. Voer `WEBSITE_NODE_DEFAULT_VERSION` in voor de instellings sleutel.
    1. Voer `10.15.2` in voor de instellings waarde.
    1. Klik met de rechter muisknop op het knoop punt voor de app service en selecteer **opnieuw starten** .

        ![Opdracht app service opnieuw starten](containers/media/quickstart-nodejs/restart.png)

    1. Klik nog een keer met de rechter muisknop op het knoop punt voor de app-service en selecteer **Bladeren website**.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Problemen met Azure-aanmelding oplossen

Als u de fout melding **' kan geen abonnement vinden met de naam [abonnement-id] '** ziet als u zich aanmeldt bij Azure, kan dat zijn omdat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer `HTTP_PROXY` en `HTTPS_PROXY` omgevings variabelen met uw proxy gegevens in uw Terminal `export`met behulp van.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Als het probleem niet wordt opgelost door de omgevings variabelen in te stellen, kunt u contact met ons opnemen door de hierboven vermelde **probleem** knop te selecteren.

### <a name="update-the-app"></a>De app bijwerken

U kunt wijzigingen in deze app implementeren door bewerkingen in VS code aan te brengen, uw bestanden op te slaan en vervolgens hetzelfde proces te gebruiken alsof u alleen de bestaande app selecteert in plaats van een nieuwe te maken.

## <a name="viewing-logs"></a>Logboeken weer geven

U kunt de logboek uitvoer (aanroepen `console.log`naar) rechtstreeks vanuit de app weer geven in het VS code-uitvoer venster.

1. Klik in **Azure app service** Explorer met de rechter muisknop op het app-knoop punt en kies **streaming-Logboeken starten**.

    ![Streaming-Logboeken starten](containers/media/quickstart-nodejs/view-logs.png)

1. Wanneer u hierom wordt gevraagd, kiest u logboek registratie inschakelen en start u de toepassing opnieuw. Zodra de app opnieuw is opgestart, wordt het venster VS code uitvoer geopend met een verbinding met de logboek stroom. 

    ![Logboek registratie inschakelen en opnieuw starten](containers/media/quickstart-nodejs/enable-restart.png)

1. Na enkele seconden wordt in het uitvoer venster een bericht weer gegeven dat aangeeft dat u bent verbonden met de service voor logboek registratie. U kunt meer uitvoer activiteiten genereren door de pagina in de browser te vernieuwen.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
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

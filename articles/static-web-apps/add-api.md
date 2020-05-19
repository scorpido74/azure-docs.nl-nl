---
title: Een API toevoegen aan statische Azure-Web Apps met Azure Functions
description: Ga aan de slag met statische Azure-Web Apps door een Serverloze API toe te voegen aan uw statische web-app met behulp van Azure Functions.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598456"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Een API toevoegen aan de preview-versie van statische Web Apps van Azure met Azure Functions

U kunt serverloze Api's toevoegen aan statische Azure-Web Apps via integratie met Azure Functions. In dit artikel wordt beschreven hoe u een API toevoegt en implementeert op een statische Web Apps-site van Azure.

Raadpleeg de [routerings handleiding](routes.md)voor informatie over het beveiligen van API-routes.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free).
- [Visual Studio code](https://code.visualstudio.com/)
- [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code
- [Live server Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) -extensie.

## <a name="create-a-git-repository"></a>Een Git-opslag plaats maken 

De volgende stappen laten zien hoe u een nieuwe opslag plaats maakt en de bestanden naar uw computer kloont.

1. Ga naar https://github.com/staticwebdev/vanilla-basic/generate om een nieuwe opslag plaats te maken.
1. Voer in het vak _naam van opslag plaats_ **mijn-vanille-API**in.
1. Klik op **opslagplaats maken op basis van sjabloon**.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Een nieuwe opslag plaats maken op basis van vanille-Basic":::

Wanneer het project is gemaakt, kunt u Visual Studio code gebruiken om de Git-opslag plaats te klonen.

1. Druk op **F1** om de opdracht in het opdracht palet te openen.
1. Plak de URL in de modus _git: Clone_ en druk op **Enter**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Een GitHub-project klonen met Visual Studio code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Een GitHub-project klonen met Visual Studio code":::


## <a name="create-your-local-project"></a>Uw lokale project maken

In deze sectie gebruikt u Visual Studio code om een lokaal Azure Functions project te maken. Later publiceert u de functions-app naar Azure.

1. Maak in het _vanille-API-_ project een submap met de naam **API**.

   > [!NOTE]
   > U kunt deze map een wille keurige naam geven. In dit voor beeld wordt gebruikt `api` . 

2. Druk op **F1** om het opdracht palet te openen
3. Typ **Azure functions: nieuw project maken...**
4. Druk op **Enter**
5. Klik op **Bladeren**
6. Selecteer de map **API** als de Directory voor uw project werkruimte
7. Kies **selecteren**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Een nieuwe Azure Functions maken met Visual Studio code":::

8. Geef de volgende informatie op bij de prompts:

    - _Selecteer een taal voor uw functie project_: **Java script** kiezen
    - _Selecteer een sjabloon voor de eerste functie van uw project_: **http-trigger** kiezen
    - _Geef een functie naam_op: type **GetMessage**
    - _Autorisatie niveau_: Kies **anoniem**, waarmee iedereen uw functie-eind punt kan aanroepen.
        - Zie [autorisatie sleutels](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie over autorisatie niveaus.

9. Met behulp van deze informatie wordt met Visual Studio code een Azure Functions project gegenereerd met een HTTP-trigger.
    - U kunt de lokale project bestanden weer geven in het Explorer-venster van Visual Studio code.
    - Zie [gegenereerde project bestanden](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files)voor meer informatie over bestanden die worden gemaakt.

10. Uw app moet nu een project structuur hebben die vergelijkbaar is met dit voor beeld.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Werk vervolgens de `GetMessage` functie onder _API/GetMessage/index. js_ uit met de volgende code.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Werk de `GetMessage` configuratie onder `api/GetMessage/function.json` met de volgende instellingen.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
Met de bovenstaande instellingen is het API-eind punt:

- Er wordt een HTTP-aanvraag geactiveerd met de functie
- Beschikbaar voor alle aanvragen ongeacht de verificatie status
- Beschikbaar via de _/API/Message_ -route

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio code integreert met [Azure functions core tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) zodat u dit project kunt uitvoeren op uw lokale ontwikkel computer voordat u naar Azure publiceert.

1. Voer de functie uit door op **F5** te drukken om de functions-app te starten en de kern hulpprogramma's uitvoer wordt weer gegeven in het deel venster _Terminal_ .

2. Als Azure Functions Core Tools nog niet is geïnstalleerd, selecteert u **installeren** bij de prompt.

    Wanneer de kern Hulpprogramma's zijn geïnstalleerd, wordt uw app gestart in het deel venster _Terminal_ . Als onderdeel van de uitvoer ziet u het URL-eind punt van de HTTP-geactiveerde functie die lokaal wordt uitgevoerd.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Een nieuwe Azure Functions maken met Visual Studio code":::

3. Als de kern Hulpprogramma's worden uitgevoerd, gaat u naar de volgende URL om een aanvraag uit te voeren `GET` .

   <http://localhost:7071/api/message>

   Er wordt een antwoord geretourneerd dat in de browser er als volgt uitziet:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Een nieuwe Azure Functions maken met Visual Studio code":::

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd, kunt u de API aanroepen vanuit de Java script-toepassing.

### <a name="call-the-api-from-the-application"></a>De API aanroepen vanuit de toepassing

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Bestanden bijwerken voor toegang tot de API

1. Werk vervolgens de inhoud van het bestand _index. html_ bij met de volgende code om de tekst van de API-functie op te halen en weer te geven op het scherm:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Gebruik de Visual Studio code-extensie van [Live server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) voor het uitvoeren van het bestand _index. html_ en open deze in een browser. 

2. Druk op **F1** en kies **Live server: openen met live server**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Een nieuwe Azure Functions maken met Visual Studio code":::

   > [!NOTE]
   > U kunt andere HTTP-servers of-proxy's gebruiken om het `index.html` bestand te verwerken. Het `index.html` is niet mogelijk om toegang te krijgen tot de van `file:///` .

### <a name="commit-and-push-your-changes-to-github"></a>Uw wijzigingen door voeren en pushen naar GitHub

Gebruik Visual Studio code om uw wijzigingen door te voeren en te pushen naar de externe Git-opslag plaats.

1. Druk op **F1** om het opdracht palet te openen
1. Type **git: alles door voeren**
1. Een commit-bericht toevoegen
1. Type in **git: push**

## <a name="create-a-static-web-app"></a>Een statische web-app maken

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Statische app maken op het Azure Portal scherm 1":::

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Klik op **een resource maken**
1. Zoeken naar **statische web apps**
1. Klik op **statische web apps (preview-versie)**
1. Klik op **maken**
1. Uw _Azure-abonnement_ selecteren
1. Een nieuwe _resource groep_ selecteren of maken
1. Geef de app de naam **My-vanille-API**.
1. Selecteer de _regio_ die het dichtst bij u ligt
1. Selecteer de **gratis** _SKU_
1. Klik op de knop **Aanmelden met github** en verificatie met github
1. Selecteer uw voorkeurs _organisatie_
1. Selecteer **mijn-vanille-API** in de vervolg keuzelijst voor de _opslag plaats_
1. Selecteer **model** in de vervolg keuzelijst _vertakking_
1. Klik op de **volgende: >knop maken** om de opbouw configuratie te bewerken

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Statische app maken op het Azure Portal scherm 2":::

Voeg vervolgens de volgende details van de build toe.

1. Voer **./** in voor de locatie van de _app_.

1. Voer de **API** in het vak _API-locatie_ in.

   Dit is de naam van de API-map die in de vorige stap is gemaakt.
   
1. Wis de standaard waarde uit de locatie van het _app-artefact_en laat het vak leeg.

1. Klik op **Controleren + maken**.

| Instelling | Beschrijving             | Vereist |
| -------- | ----------------------- |
|  App-locatie | De locatie van de statische bron code van de toepassing | Ja |
|  API-locatie | De locatie van de API-back-end. Dit verwijst naar de hoofdmap van het Azure Functions-app-project | Nee |
|  Locatie van app-artefact | De locatie van de uitvoermap van de build. Sommige front-end Java script-Frameworks hebben een build-stap waarmee productie bestanden in een map worden geplaatst. Deze instelling verwijst naar de map build-uitvoer. | Nee |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Statische app maken op het Azure Portal scherm 3":::

1. Klik op **maken**
1. Wacht tot de implementatie is voltooid (dit kan een minuut duren)
1. Ga naar`https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Zorg ervoor dat de build is geslaagd

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub-werk stroom":::

De geïmplementeerde API is beschikbaar op `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` .

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub-werk stroom":::

U kunt de URL van de toepassing ook vinden in de Azure Portal:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Toegang tot de statische app-URL vanuit de Azure Portal":::

U kunt ook rechtstreeks toegang krijgen tot uw statische Azure-web-app op `https://<STATIC_APP_NAME>.azurestaticapps.net` en het resultaat controleren in de browser.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken voor verder gebruik, kunt u de volgende stappen gebruiken om de statische Azure-web-app en de bijbehorende resources te verwijderen.

1. Ga naar [Azure Portal](https://portal.azure.com)
1. Typ in de bovenste zoek balk **resource groepen**
1. Klik op **resource groepen** 
1. **MyResourceGroup** selecteren
1. Controleer op de pagina _myResourceGroup_ of de vermelde bronnen de resources zijn die u wilt verwijderen.
1. Selecteer **verwijderen**
1. Typ **myResourceGroup** in het tekstvak
1. Selecteer **verwijderen**.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-instellingen configureren](./application-settings.md)

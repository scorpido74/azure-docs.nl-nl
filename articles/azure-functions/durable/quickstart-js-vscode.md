---
title: Uw eerste duurzame functie in Azure maken met behulp van JavaScript
description: Maak en publiceer een duurzame Azure-functie met behulp van Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80257645"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Uw eerste duurzame functie maken in JavaScript

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

In dit artikel leert u hoe u de Azure Functions-extensie van Visual Studio Code kunt gebruiken om lokaal een duurzame ‘Hallo wereld’-functie te maken en te testen.  Met deze functie worden aanroepen naar andere functies ingedeeld en aan elkaar gekoppeld. Vervolgens publiceert u de functiecode op Azure.

![Durable Function uitvoeren in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* Installeer [Visual Studio Code](https://code.visualstudio.com/download).

* De [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS code-extensie installeren

* Zorg ervoor dat u de nieuwste versie van de [Azure functions core tools](../functions-run-local.md)hebt.

* Durable Functions een Azure-opslag account vereist. U hebt een Azure-abonnement nodig.

* Zorg ervoor dat versie 10. x of 12. x van [node. js](https://nodejs.org/) is geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio code om een lokaal Azure Functions project te maken. 

1. Druk in Visual Studio code op F1 (of Ctrl/Cmd + Shift + P) om het opdracht palet te openen. In het opdracht palet zoekt en selecteert u `Azure Functions: Create New Project...`.

    ![Functie maken](media/quickstart-js-vscode/functions-create-project.png)

1. Kies een lege maplocatie voor het project en kies **selecteren**.

1. Voer de volgende informatie in om de aanwijzingen te volgen:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een taal voor uw functie-app-project | Javascript | Een lokaal node. js-functie project maken. |
    | Een versie selecteren | Azure Functions v3 | U ziet deze optie alleen wanneer de kern Hulpprogramma's niet al zijn geïnstalleerd. In dit geval worden de kern Hulpprogramma's geïnstalleerd wanneer u de app voor het eerst uitvoert. |
    | Selecteer een sjabloon voor de eerste functie van uw project | Nu overs Laan | |
    | Selecteer hoe u uw project wilt openen | In het huidige venster openen | Opent de VS code in de map die u hebt geselecteerd. |

Met Visual Studio code worden de Azure Functions Core Tools, indien nodig, geïnstalleerd. Er wordt ook een functie-app-project gemaakt in een map. Dit project bevat de configuratie bestanden [host. json](../functions-host-json.md) en [Local. settings. json](../functions-run-local.md#local-settings-file) .

Er wordt ook een package. JSON-bestand gemaakt in de hoofdmap.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Compatibiliteits modus voor Azure Functions v2 inschakelen

Java script Durable Functions vereisen dat de Azure Functions v2-compatibiliteits modus is ingeschakeld.

1. Open *Local. settings. json* om de instellingen te bewerken die worden gebruikt wanneer de app lokaal wordt uitgevoerd.

1. Voeg een instelling toe `FUNCTIONS_V2_COMPATIBILITY_MODE` met de naam met `true`een waarde van.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureWebJobsStorage": "",
            "FUNCTIONS_WORKER_RUNTIME": "node",
            "FUNCTIONS_V2_COMPATIBILITY_MODE": "true"
        }
    }
    ```

## <a name="install-the-durable-functions-npm-package"></a>Het NPM-pakket voor Durable Functions installeren

Als u wilt werken met Durable Functions in een node. js-functie-app, gebruikt `durable-functions`u een bibliotheek met de naam.

1. Gebruik het menu *weer geven* of CTRL + SHIFT + om een nieuwe terminal te openen in VS code.

1. Installeer het `durable-functions` NPM-pakket met duurzame functies door `npm install durable-functions` uit te voeren in de hoofdmap van de functie-app.

## <a name="creating-your-functions"></a>Uw functies maken

De meest eenvoudige Durable Functions-app bevat drie functies:

* *Orchestrator-functie* : beschrijft een werk stroom die andere functies organiseert.
* *Activiteit functie* : wordt aangeroepen door de Orchestrator-functie, voert het werk uit en retourneert optioneel een waarde.
* *Client functie* : een reguliere Azure-functie waarmee een Orchestrator-functie wordt gestart. In dit voor beeld wordt een door HTTP geactiveerde functie gebruikt.

### <a name="orchestrator-function"></a>Orchestrator-functie

U gebruikt een sjabloon om de duurzame functie code in uw project te maken.

1. In het opdracht palet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Voer de volgende informatie in om de aanwijzingen te volgen:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor de functie | Durable Functions Orchestrator | Een Durable Functions indeling maken |
    | Een functie naam opgeven | HelloOrchestrator | Naam van uw duurzame functie |

U hebt een Orchestrator toegevoegd om de activiteit functies te coördineren. Open *HelloOrchestrator/index. js* om de Orchestrator-functie weer te geven. Elke aanroep voor `context.df.callActivity` het aanroepen van een activiteit `Hello`functie met de naam.

Vervolgens voegt u de functie voor de `Hello` activiteit waarnaar wordt verwezen toe.

### <a name="activity-function"></a>Functie activity

1. In het opdracht palet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Voer de volgende informatie in om de aanwijzingen te volgen:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor de functie | Durable Functions activiteit | Een activiteit functie maken |
    | Een functie naam opgeven | Hello | De naam van de functie van de activiteit |

U hebt de `Hello` activiteit functie toegevoegd die wordt aangeroepen door de Orchestrator. Open *Hello/index. js* om te zien dat het een naam krijgt als invoer en een begroeting retourneert. Met een activiteit functie kunt u acties uitvoeren zoals het maken van een database oproep of het uitvoeren van een berekening.

Ten slotte voegt u een door HTTP geactiveerde functie toe waarmee de indeling wordt gestart.

### <a name="client-function-http-starter"></a>Client functie (HTTP-starter)

1. In het opdracht palet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Voer de volgende informatie in om de aanwijzingen te volgen:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor de functie | Durable Functions HTTP-starter | Een HTTP-starter-functie maken |
    | Een functie naam opgeven | DurableFunctionsHttpStart | De naam van de functie van de activiteit |
    | Autorisatieniveau | Anoniem | Toestaan dat de functie wordt aangeroepen zonder verificatie voor demonstratie doeleinden |

U hebt een door HTTP geactiveerde functie toegevoegd waarmee een indeling wordt gestart. Open *DurableFunctionsHttpStart/index. js* om te zien dat dit `client.startNew` wordt gebruikt om een nieuwe indeling te starten. Vervolgens wordt gebruikt `client.createCheckStatusResponse` om een HTTP-antwoord met url's te retour neren dat kan worden gebruikt om de nieuwe indeling te controleren en te beheren.

U hebt nu een Durable Functions-app die lokaal kan worden uitgevoerd en geïmplementeerd in Azure.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Als u uw functie wilt testen, stelt u een `Hello` onderbrekings punt in voor de functie code van de activiteit (*Hallo/index. js*). Druk op F5 of `Debug: Start Debugging` Selecteer een optie in het opdracht palet om het project van de functie-app te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

    > [!NOTE]
    > Raadpleeg het [Durable functions diagnostische](durable-functions-diagnostics.md#debugging) gegevens voor meer informatie over fout opsporing.

1. Durable Functions moet een Azure Storage account worden uitgevoerd. Wanneer VS-code u vraagt een opslag account te selecteren, kiest u **opslag account selecteren**.

    ![Een opslagaccount maken](media/quickstart-js-vscode/functions-select-storage.png)

1. Volg de aanwijzingen om de volgende informatie op te geven voor het maken van een nieuw opslag account in Azure.

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Abonnement selecteren | *de naam van uw abonnement* | selecteer uw Azure-abonnement |
    | Een opslag account selecteren | Een nieuw opslagaccount maken |  |
    | Voer de naam van het nieuwe opslag account in | *unieke naam* | De naam van het opslag account dat moet worden gemaakt |
    | Een resourcegroep selecteren | *unieke naam* | De naam van de resource groep die u wilt maken |
    | Een locatie selecteren | *deel* | Selecteer een regio die dicht bij u ligt |

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](media/quickstart-js-vscode/functions-f5.png)

1. Verzend met een hulp programma zoals [postman](https://www.getpostman.com/) of [krul](https://curl.haxx.se/)een HTTP POST-aanvraag naar het URL-eind punt. Vervang het laatste segment door de naam van de Orchestrator-functie (`HelloOrchestrator`). De URL moet vergelijkbaar zijn met `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   Het antwoord is het eerste resultaat van de HTTP-functie zodat u weet dat de duurzame indeling is gestart. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

1. Kopieer de URL-waarde `statusQueryGetUri` voor en plak deze in de adres balk van de browser en voer de aanvraag uit. U kunt postman ook blijven gebruiken om de GET-aanvraag uit te geven.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eindige reactie krijgen, waarin wordt weer gegeven dat het exemplaar is voltooid en dat de uitvoer of resultaten van de duurzame functie bevat. Het ziet er als volgt uit: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Als u het fout opsporingsprogramma wilt stoppen, drukt u op **SHIFT + F5** in VS code.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Compatibiliteits modus voor Azure Functions v2 inschakelen

Dezelfde Azure Functions v2-compatibiliteit die u lokaal hebt ingeschakeld, moet worden ingeschakeld in de app in Azure.

1. Zoek en selecteer `Azure Functions: Edit Setting...`met behulp van het opdracht palet.

1. Volg de aanwijzingen om uw functie-app in uw Azure-abonnement te vinden.

1. Selecteer `Create new App Setting...`.

1. Voer een nieuwe instellings sleutel `FUNCTIONS_V2_COMPATIBILITY_MODE`van in.

1. Voer een waarde in voor `true`de instelling.

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio Code gebruikt om een duurzame JavaScript-functie-app te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
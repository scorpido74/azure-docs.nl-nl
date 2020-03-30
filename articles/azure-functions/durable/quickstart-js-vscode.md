---
title: Uw eerste duurzame functie in Azure maken met behulp van JavaScript
description: Maak en publiceer een duurzame Azure-functie met behulp van Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
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

* De [azure-functie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs-code-extensie installeren

* Zorg ervoor dat u de nieuwste versie van de [Azure Functions Core Tools hebt.](../functions-run-local.md)

* Voor duurzame functies is een Azure-opslagaccount vereist. U hebt een Azure-abonnement nodig.

* Zorg ervoor dat versie 10.x of 12.x van [Node.js](https://nodejs.org/) is geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure-functieproject te maken. 

1. Druk in Visual Studio Code op F1 (of Ctrl/Cmd+Shift+P) om het opdrachtpalet te openen. Zoek en selecteer `Azure Functions: Create New Project...`in het opdrachtpalet .

    ![Functie maken](media/quickstart-js-vscode/functions-create-project.png)

1. Kies een lege maplocatie voor uw project en kies **Selecteren**.

1. Geef de volgende aanwijzingen op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Een taal selecteren voor uw functie-app-project | JavaScript | Maak een lokaal project Node.js Functions. |
    | Een versie selecteren | Azure-functies v3 | U ziet deze optie alleen als de Core Tools nog niet zijn geïnstalleerd. In dit geval worden Core Tools geïnstalleerd wanneer u de app voor het eerst uitvoert. |
    | Een sjabloon selecteren voor de eerste functie van uw project | Sla voor nu | |
    | Selecteer hoe u uw project wilt openen | Openen in het huidige venster | Hiermee opent u VS-code opnieuw in de door u geselecteerde map. |

Visual Studio Code installeert indien nodig de Core Tools voor Azure Functions Core. Het maakt ook een functie-app project in een map. Dit project bevat de configuratiebestanden [host.json](../functions-host-json.md) en [local.settings.json.](../functions-run-local.md#local-settings-file)

Er wordt ook een bestand package.json gemaakt in de hoofdmap.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Compatibiliteitsmodus Azure Functions V2 inschakelen

JavaScript-duurzame functies vereisen momenteel dat de compatibiliteitsmodus Azure Functions V2 is ingeschakeld.

1. Open *local.settings.json* om de instellingen te bewerken die worden gebruikt bij het lokaal uitvoeren van de app.

1. Voeg een `FUNCTIONS_V2_COMPATIBILITY_MODE` instelling toe `true`met de naam waarde van .

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

Als u wilt werken met duurzame functies in een functie-app Node.js, gebruikt u een bibliotheek met de naam `durable-functions`.

1. Gebruik het menu *Weergave* of Ctrl+Shift+' om een nieuwe terminal in VS-code te openen.

1. Installeer het `durable-functions` NPM-pakket met duurzame functies door `npm install durable-functions` uit te voeren in de hoofdmap van de functie-app.

## <a name="creating-your-functions"></a>Uw functies maken

De meest elementaire app voor duurzame functies bevat drie functies:

* *Orchestrator-functie* - beschrijft een werkstroom die andere functies orkestreert.
* *Activiteitsfunctie* - aangeroepen door de orchestrator-functie, voert werk uit en retourneert optioneel een waarde.
* *Clientfunctie* - een reguliere Azure-functie waarmee een orchestrator-functie wordt gestart. In dit voorbeeld wordt een HTTP-geactiveerde functie gebruikt.

### <a name="orchestrator-function"></a>Orchestrator, functie

U gebruikt een sjabloon om de duurzame functiecode in uw project te maken.

1. Zoek en selecteer `Azure Functions: Create Function...`in het opdrachtpalet .

1. Geef de volgende aanwijzingen op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Een sjabloon voor uw functie selecteren | Duurzame functies orchestrator | Een orkestratie met duurzame functies maken |
    | Een functienaam opgeven | HelloOrchestrator HelloOrchestrator | Naam van uw duurzame functie |

U hebt een orchestrator toegevoegd om activiteitsfuncties te coördineren. Open *HelloOrchestrator/index.js* om de functie orchestrator te bekijken. Elke aanroep om een `Hello`activiteitsfunctie met de naam aan te `context.df.callActivity` roepen .

Vervolgens voegt u de activiteitsfunctie waarnaar wordt verwezen. `Hello`

### <a name="activity-function"></a>Activiteit, functie

1. Zoek en selecteer `Azure Functions: Create Function...`in het opdrachtpalet .

1. Geef de volgende aanwijzingen op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Een sjabloon voor uw functie selecteren | Activiteit duurzame functies | Een activiteitsfunctie maken |
    | Een functienaam opgeven | Hello | Naam van uw activiteitsfunctie |

U hebt de `Hello` activiteitsfunctie toegevoegd die door de orchestrator wordt aangeroepen. Open *Hello/index.js* om te zien dat het een naam als invoer neemt en een begroeting teruggeeft. Een activiteitsfunctie is de plaats waar u acties uitvoert, zoals het maken van een databaseaanroep of het uitvoeren van een berekening.

Ten slotte voegt u een HTTP-geactiveerde functie toe waarmee de orkestratie wordt gestart.

### <a name="client-function-http-starter"></a>Clientfunctie (HTTP starter)

1. Zoek en selecteer `Azure Functions: Create Function...`in het opdrachtpalet .

1. Geef de volgende aanwijzingen op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Een sjabloon voor uw functie selecteren | Duurzame functies HTTP starter | Een HTTP-starterfunctie maken |
    | Een functienaam opgeven | Duurzame functieshttpStart | Naam van uw activiteitsfunctie |
    | Autorisatieniveau | Anoniem | Voor demodoeleinden u toestaan dat de functie zonder verificatie wordt aangeroepen |

U hebt een HTTP-geactiveerde functie toegevoegd waarmee een orkestratie wordt gestart. Open *Duurzame functiesHttpStart/index.js* om `client.startNew` te zien dat het wordt gebruikt om een nieuwe orkestratie te starten. Vervolgens wordt `client.createCheckStatusResponse` gebruikt om een HTTP-antwoord met URL's terug te sturen die kunnen worden gebruikt om de nieuwe orkestratie te controleren en te beheren.

U hebt nu een app Duurzame functies die lokaal kan worden uitgevoerd en kan worden geïmplementeerd in Azure.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Als u de functie wilt `Hello` testen, stelt u een breekpunt in in de code van de activiteitsfunctie (*Hello/index.js*). Druk op F5 of selecteer `Debug: Start Debugging` in het opdrachtpalet om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

    > [!NOTE]
    > Raadpleeg de [Sustainable Functions Diagnostics](durable-functions-diagnostics.md#debugging) voor meer informatie over foutopsporing.

1. Voor duurzame functies moet een Azure Storage-account worden uitgevoerd. Wanneer VS Code u vraagt een opslagaccount te selecteren, kiest **u Opslagaccount selecteren**.

    ![Een opslagaccount maken](media/quickstart-js-vscode/functions-select-storage.png)

1. Geef de volgende informatie naar aanleiding van de volgende aanwijzingen om een nieuw opslagaccount in Azure te maken.

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Abonnement selecteren | *naam van uw abonnement* | selecteer uw Azure-abonnement |
    | Een opslagaccount selecteren | Een nieuw opslagaccount maken |  |
    | Voer de naam in van het nieuwe opslagaccount | *unieke naam* | Naam van het opslagaccount dat u wilt maken |
    | Een resourcegroep selecteren | *unieke naam* | Naam van de resourcegroep die moet worden gemaakt |
    | Een locatie selecteren | *regio* | Een gebied bij u in de buurt selecteren |

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](media/quickstart-js-vscode/functions-f5.png)

1. Met behulp van een tool zoals [Postman](https://www.getpostman.com/) of [cURL,](https://curl.haxx.se/)stuur je een HTTP POST-verzoek naar het URL-eindpunt. Vervang het laatste segment door de naam`HelloOrchestrator`van de orchestrator-functie ( ). De URL moet `http://localhost:7071/api/orchestrators/HelloOrchestrator`vergelijkbaar zijn met .

   Het antwoord is het eerste resultaat van de HTTP-functie om u te laten weten dat de duurzame orkestratie is begonnen. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

1. Kopieer de URL-waarde voor `statusQueryGetUri` en plak deze in de adresbalk van de browser en voer de aanvraag uit. U ook Postman blijven gebruiken om de GET-aanvraag uit te geven.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eventueel antwoord krijgen, waaruit blijkt dat de instantie is voltooid, en omvat de uitgangen of resultaten van de duurzame functie. Het lijkt erop dat: 

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

1. Als u foutopsporing wilt stoppen, drukt u op **Shift + F5** in VS-code.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Compatibiliteitsmodus Azure Functions V2 inschakelen

Dezelfde Azure Functions V2-compatibiliteit die u lokaal hebt ingeschakeld, moet worden ingeschakeld in de app in Azure.

1. Zoek en selecteer `Azure Functions: Edit Setting...`met het opdrachtpalet .

1. Volg de aanwijzingen om uw functie-app in uw Azure-abonnement te vinden.

1. Selecteer `Create new App Setting...`.

1. Voer een nieuwe `FUNCTIONS_V2_COMPATIBILITY_MODE`instellingstoets in van .

1. Voer een instellingswaarde in van `true`.

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet in deze indeling staan:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio Code gebruikt om een duurzame JavaScript-functie-app te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
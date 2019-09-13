---
title: Uw eerste duurzame functie in Azure maken met behulp van JavaScript
description: Maak en publiceer een duurzame Azure-functie met behulp van Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure-functies, functies, gebeurtenisverwerking, berekenen, architectuur zonder server
ms.service: azure-functions
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: glenga
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: b47e828f3b8d760594cb04ba40ceaa7248050c52
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933476"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Uw eerste duurzame functie maken in JavaScript

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

In dit artikel leert u hoe u de Azure Functions-extensie van Visual Studio Code kunt gebruiken om lokaal een duurzame ‘Hallo wereld’-functie te maken en te testen.  Met deze functie worden aanroepen naar andere functies ingedeeld en aan elkaar gekoppeld. Vervolgens publiceert u de functiecode op Azure.

![Duurzame functie uitvoeren in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* Installeer [Visual Studio Code](https://code.visualstudio.com/download).

* Zorg ervoor dat u de nieuwste versie van de [Azure functions core tools](../functions-run-local.md)hebt.

* Op een Windows-computer controleert u of de [Azure Storage-emulator](../../storage/common/storage-use-emulator.md) is geïnstalleerd en wordt uitgevoerd. Op een Mac- of Linux-computer moet u daadwerkelijk een Azure-opslagaccount gebruiken.

* Zorg ervoor dat versie 8.0 of een latere versie van [Node.js](https://nodejs.org/) is geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Het NPM-pakket voor Durable Functions installeren

1. Installeer het `durable-functions` NPM-pakket met duurzame functies door `npm install durable-functions` uit te voeren in de hoofdmap van de functie-app.

## <a name="creating-your-functions"></a>Uw functies maken

We gaan nu de drie functies maken die u nodig hebt om aan de slag te gaan met Durable Functions: een HTTP-starter, een Orchestrator en een activiteit functie. De HTTP-starter initieert uw volledige oplossing en de Orchestrator verzendt werk naar verschillende activiteit functies.

### <a name="http-starter"></a>HTTP-starter

Maak eerst een HTTP-geactiveerde functie waarmee de indeling voor een duurzame functie wordt gestart.

1. In *Azure: Functions*, kiest u het pictogram **functie maken** .

    ![Een functie maken](./media/quickstart-js-vscode/create-function.png)

2. Selecteer de map met uw functie-app-project en selecteer de sjabloon **Durable functions HTTP-starter-** functie.

    ![De HTTP-starter-sjabloon kiezen](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Laat de standaardnaam als `DurableFunctionsHttpStart` en druk op ** **Enter**, selecteer vervolgens **anoniem** verificatie.

    ![Anonieme verificatie kiezen](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

U hebt nu een ingangspunt gemaakt in de duurzame functie. Nu gaan we een orchestrator toevoegen.

### <a name="orchestrator"></a>Orchestrator

Nu gaan we een Orchestrator maken om de activiteit functies te coördineren.

1. In *Azure: Functions*, kiest u het pictogram **functie maken** .

    ![Een functie maken](./media/quickstart-js-vscode/create-function.png)

2. Selecteer de map met uw functie-app-project en selecteer de sjabloon **Durable functions Orchestrator** -functie. De naam behouden als de standaard waarde voor DurableFunctionsOrchestrator

    ![De Orchestrator-sjabloon kiezen](./media/quickstart-js-vscode/create-function-choose-template.png)

Er is een orchestrator toegevoegd om de activiteitsfuncties te coördineren. Nu gaan we de activiteitsfunctie toevoegen waarnaar wordt verwezen.

### <a name="activity"></a>Activiteit

Nu gaan we een activiteit functie maken om het werk van de oplossing daad werkelijk uit te voeren.

1. In *Azure: Functions*, kiest u het pictogram **functie maken** .

    ![Een functie maken](./media/quickstart-js-vscode/create-function.png)

2. Selecteer de map met uw functie-app-project en selecteer de functie sjabloon **Durable functions activiteit** . Wijzig de naam als de standaard Hello.

    ![De activiteiten sjabloon kiezen](./media/quickstart-js-vscode/create-function-choose-template.png)

Nu zijn alle benodigde onderdelen toegevoegd voor het starten van een indeling en om activiteitsfuncties te koppelen.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Start op een Windows-computer de Azure Storage-emulator en zorg ervoor dat de eigenschap **AzureWebJobsStorage** van *Local. settings. json* is ingesteld `UseDevelopmentStorage=true`op.

    Zorg ervoor dat de eigenschap **AzureWebJobsSecretStorageType** van local. settings. json is ingesteld op `files`voor Storage emulator 5,8. Op een Mac-of Linux-computer moet u de eigenschap **AzureWebJobsStorage** instellen op de Connection String van een bestaand Azure Storage-account. Later in dit artikel maakt u een opslagaccount.

2. U kunt de functie testen door een onderbrekingspunt in de functiecode in te stellen en op F5 te drukken om het functie-appproject te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**. Als dit de eerste keer is dat u Durable Functions gebruikt, wordt de Durable Functions-extensie geïnstalleerd. Het bouwen kan enkele seconden duren.

    > [!NOTE]
    > Voor JavaScript Durable Functions is versie **1.7.0** of hoger van de extensie **Microsoft.Azure.WebJobs.Extensions.DurableTask** vereist. Voer de volgende opdracht uit vanuit de hoofdmap van uw Azure Functions-app om de Durable Functions-extensie te installeren`func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Vervang `{functionName}` door `DurableFunctionsOrchestrator`.

5. Verzend met een hulp programma zoals [postman](https://www.getpostman.com/) of [krul](https://curl.haxx.se/)een HTTP POST-aanvraag naar het URL-eind punt.

   De reactie is het eerste resultaat van de HTTP-functie waarmee wordt aangegeven dat de orchestrator is gestart. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

6. Kopieer de URL-waarde `statusQueryGetUri` voor en plak deze in de adres balk van de browser en voer de aanvraag uit. U kunt postman ook blijven gebruiken om de GET-aanvraag uit te geven.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eindige reactie krijgen, waarin wordt weer gegeven dat het exemplaar is voltooid en dat de uitvoer of resultaten van de duurzame functie bevat. Het ziet er als volgt uit: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. Als u het fout opsporingsprogramma wilt stoppen, drukt u op **SHIFT + F5** in VS code.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio Code gebruikt om een duurzame JavaScript-functie-app te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
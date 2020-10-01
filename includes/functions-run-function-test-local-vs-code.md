---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408539"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio Code integreert met [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) om u een project te laten uitvoeren vanaf uw lokale ontwikkelaarscomputer voordat u in Azure publiceert.

1. Druk op <kbd>F5</kbd> om het functie-app-project te starten en uw functie aan te roepen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Als u Azure Functions Core Tools nog niet hebt geïnstalleerd, selecteert u **Installeren** als u daar om wordt gevraagd. Wanneer Core Tools zijn geïnstalleerd, wordt u app gestart in het deelvenster **Terminal**. Kopieer het URL-eindpunt van uw functie die lokaal wordt uitgevoerd en door HTTP is geactiveerd.

    ![Lokale functie versus code-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Als Core Tools wordt uitgevoerd, navigeert u naar de volgende URL om een GET-aanvraag uit te voeren die de querytekenreeks `?name=Functions` bevat.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Er wordt een antwoord geretourneerd die er in een browser als volgt uitziet:

    ![Browser - voorbeelduitvoer van localhost](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal**.

    ![Taakhost starten - VS Code-terminaluitvoer](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op <kbd>CTRL + C</kbd> om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.

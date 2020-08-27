---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 052e0c93732b99efa37b029cad29dc2efded78ee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703788"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio Code integreert met [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) om u een project te laten uitvoeren vanaf uw lokale ontwikkelaarscomputer voordat u in Azure publiceert.

1. Druk op F5 om het functie-app-project te starten en uw functie aan te roepen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Als u Azure Functions Core Tools nog niet hebt geïnstalleerd, selecteert u **Installeren** als u daar om wordt gevraagd. Wanneer Core Tools zijn geïnstalleerd, wordt u app gestart in het deelvenster **Terminal**. Kopieer het URL-eindpunt van uw functie die lokaal wordt uitgevoerd en door HTTP is geactiveerd. 

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Als Core Tools wordt uitgevoerd, navigeert u naar de volgende URL om een GET-aanvraag uit te voeren die de querytekenreeks `?name=Functions` bevat.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Er wordt een antwoord geretourneerd die er in een browser als volgt uitziet:

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal**.

    ![Functie-uitvoering in het deelvenster Terminal](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op CTRL + C om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.

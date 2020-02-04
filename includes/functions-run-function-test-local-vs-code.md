---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964086"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio code integreert met [Azure functions core tools](../articles/azure-functions/functions-run-local.md) zodat u dit project kunt uitvoeren op uw lokale ontwikkel computer voordat u naar Azure publiceert.

1. Als u de functie wilt aanroepen, drukt u op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Als u Azure Functions Core Tools nog niet hebt geïnstalleerd, selecteert u **installeren** bij de prompt. Wanneer de kern Hulpprogramma's zijn geïnstalleerd, wordt uw app gestart in het deel venster **Terminal** . U ziet het URL-eind punt van de HTTP-geactiveerde functie die lokaal wordt uitgevoerd. 

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Als er kern Hulpprogramma's worden uitgevoerd, gaat u naar de volgende URL om een GET-aanvraag uit te voeren, die `?name=Functions` query teken reeks bevat.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Er wordt een antwoord geretourneerd dat lijkt op het volgende in een browser:

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informatie over de aanvraag wordt weer gegeven in het deel venster **Terminal** .

    ![Uitvoering van functies in het Terminal paneel](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op CTRL + C om de kern Hulpprogramma's te stoppen en de debugger te verbreken.

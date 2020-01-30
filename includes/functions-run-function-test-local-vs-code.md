---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842135"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio code integreert met [Azure functions core tools](../articles/azure-functions/functions-run-local.md) zodat u dit project kunt uitvoeren op uw lokale ontwikkel computer voordat u naar Azure publiceert.

1. Als u de functie wilt aanroepen, drukt u op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Als u Azure Functions Core Tools nog niet hebt geïnstalleerd, selecteert u **installeren** bij de prompt. Wanneer de kern Hulpprogramma's zijn geïnstalleerd, wordt uw app gestart in het deel venster **Terminal** .

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**. 

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser. Voeg de query reeks `?name=<yourname>` toe aan deze URL en voer de GET-aanvraag uit. 

1. Er wordt een antwoord geretourneerd dat lijkt op het volgende in een browser:

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Druk op SHIFT + F5 om de kern Hulpprogramma's te stoppen en de debugger te verbreken.

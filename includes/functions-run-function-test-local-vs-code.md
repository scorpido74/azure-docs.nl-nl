---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76964086"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio Code integreert met [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) om u dit project op uw lokale ontwikkelingscomputer te laten uitvoeren voordat u publiceert naar Azure.

1. Als u uw functie wilt aanroepen, drukt u op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Als u Azure Functions Core Tools nog niet hebt geïnstalleerd, selecteert u **Installeren** bij de prompt. Wanneer de Core Tools zijn geïnstalleerd, wordt uw app gestart in het **deelvenster Terminal.** U het URL-eindpunt van uw http-geactiveerde functie lokaal weergeven. 

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Als Kernhulpprogramma's worden uitgevoerd, navigeert u naar `?name=Functions` de volgende URL om een GET-aanvraag uit te voeren, die querytekenreeksbevat.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Een antwoord wordt geretourneerd, dat er als volgt uitziet in een browser:

    ![De reactie van de lokale host van de functie in de browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal.**

    ![Functie-uitvoering in deelvenster Terminal](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op Ctrl + C om Kerngereedschappen te stoppen en de foutopsporing los te koppelen.

---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191014"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Azure Functions Core Tools integreert met Visual Studio Code om u een Azure Functions-project lokaal uit te laten voeren en te debuggen. Zie [PowerShell Azure-functies van Debuging PowerShell Lokaal voor](../articles/azure-functions/functions-debug-powershell-local.md)meer informatie over het opsporen van foutopsporing in Visual Studio-code. 

1. Druk op F5 om het functie-app-project te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Voeg de querytekenreeks `?name=<yourname>` toe aan deze `Invoke-RestMethod` URL en gebruik vervolgens in een tweede PowerShell-opdrachtprompt om het verzoek als volgt uit te voeren:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    U het GET-verzoek ook vanuit een browser uitvoeren vanuit de volgende URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Wanneer u het HttpTrigger-eindpunt `name` aanroept zonder een parameter als queryparameter of `BadRequest` in de hoofdtekst door te geven, geeft de functie een fout als resultaat. Wanneer u de code in run.ps1 bekijkt, ziet u dat deze fout optreedt door het ontwerp.

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal.**

    ![Functie-uitvoering in deelvenster Terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Druk op **Ctrl + C** om Kerngereedschappen te stoppen als u klaar bent.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.
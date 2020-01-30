---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842145"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Azure Functions Core Tools integreert met Visual Studio code zodat u een Azure Functions-project lokaal kunt uitvoeren en debuggen.  

1. Als u fouten wilt opsporen in uw functie, voegt u een aanroep naar de cmdlet [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) in de functie code in voordat u de debugger kunt koppelen. Druk vervolgens op F5 om het functie-app-project te starten en de debugger te koppelen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Voeg de query reeks `?name=<yourname>` toe aan deze URL en gebruik `Invoke-RestMethod` in een tweede Power shell-opdracht prompt om de aanvraag uit te voeren, als volgt:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    U kunt ook de GET-aanvraag uitvoeren vanuit een browser.

    Wanneer u het http trigger-eind punt aanroept zonder een `name` para meter door te geven als query parameter of in de hoofd tekst, retourneert de functie een [http status code]:: onjuiste aanvraag-fout. Wanneer u de code in run. ps1 bekijkt, ziet u dat deze fout wordt veroorzaakt door het ontwerp.

1. Als u wilt stoppen met fouten opsporen, drukt u op Shift + F5.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

> [!NOTE]
> Vergeet niet alle aanroepen naar `Wait-Debugger` te verwijderen voordat u uw functies naar Azure publiceert. 
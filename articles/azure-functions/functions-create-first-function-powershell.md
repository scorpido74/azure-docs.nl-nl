---
title: Uw eerste Power shell-functie maken in azure
description: Meer informatie over het maken van uw eerste Power shell-functie in azure met Visual Studio code.
author: joeyaiello
manager: gwallace
ms.author: jaiello
ms.reviewer: glenga
ms.date: 04/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.openlocfilehash: 1d6d641e141862b12fed40b800589aad70af2789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469404"
---
# <a name="create-your-first-powershell-function-in-azure"></a>Uw eerste Power shell-functie maken in azure

Dit Quick Start-artikel helpt u bij het maken van uw eerste [serverloze](https://azure.com/serverless) Power shell-functie met Visual Studio code.

![Azure Functions code in een Visual Studio code-project](./media/functions-create-first-function-powershell/powershell-project-first-function.png)

U gebruikt de [Azure Functions extension for Visual Studio Code] om een Power shell-functie lokaal te maken en deze vervolgens te implementeren in een nieuwe functie-app in Azure. De extensie is momenteel beschikbaar als preview-product. Zie de uitbreidingspagina [Azure Functions extension for Visual Studio Code] (Azure Functions-extensie voor Visual Studio Code) voor meer informatie.

> [!NOTE]  
> Power Shell-ondersteuning voor de uitbrei ding[Azure functions extensie voor Visual Studio code] voor de [Azure functions extensie]is momenteel standaard uitgeschakeld. Het inschakelen van Power Shell-ondersteuning is een van de stappen in dit artikel.

De volgende stappen worden ondersteund op macOS-, Windows-en Linux-besturings systemen.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

* [Power shell core](/powershell/scripting/install/installing-powershell-core-on-windows) installeren

* Installeer [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 

* Installeer de [Power shell-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

* Installeer [.net core SDK 2.2 +](https://www.microsoft.com/net/download) (vereist voor Azure functions core tools en beschikbaar op alle ondersteunde platformen).

* Installeer versie 2. x van de [Azure functions core tools](functions-run-local.md#v2).

* U hebt ook een actief Azure-abonnement nodig.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)] 

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

De Azure Functions-projectsjabloon in Visual Studio Code maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources kunt beheren, implementeren en delen. 

1. Selecteer in Visual Studio Code het Azure-logo om het gebied **Azure: Functions** weer te geven. Selecteer vervolgens het pictogram Create New Project (Nieuw project maken).

    ![Een functie-appproject maken](./media/functions-create-first-function-powershell/create-function-app-project.png)

1. Kies een locatie voor uw functions project-werk ruimte en kies **selecteren**.

    > [!NOTE]
    > Dit artikel is bedoeld om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Selecteer de **Power shell** als de taal voor uw functie-app-project en klik vervolgens **Azure functions v2**.

1. Kies **http trigger** als de sjabloon voor uw eerste functie, gebruik `HTTPTrigger` als functie naam en kies een autorisatie **functie**niveau.

    > [!NOTE]
    > Het **functie** autorisatie niveau vereist een [functie sleutel](functions-bindings-http-webhook.md#authorization-keys) waarde bij het aanroepen van het functie-eind punt in Azure. Dit maakt het moeilijker voor iedereen om uw functie aan te roepen.

1. Kies **Add to workspace** (Aan werkruimte toevoegen) als daarom wordt gevraagd.

Visual Studio code maakt het Power shell-functie-app-project in een nieuwe werk ruimte. Dit project bevat de configuratie bestanden [host. json](functions-host-json.md) en [Local. settings. json](functions-run-local.md#local-settings-file) , die van toepassing zijn op alle functies in het project. Dit [Power Shell-project](functions-reference-powershell.md#folder-structure) is hetzelfde als een functie-app die wordt uitgevoerd in Azure.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Azure Functions Core Tools integreert met Visual Studio code zodat u een Azure Functions-project lokaal kunt uitvoeren en debuggen.  

1. Als u fouten wilt opsporen in uw functie, voegt u een aanroep naar de cmdlet [`Wait-Debugger`] in de functie code in voordat u de debugger kunt koppelen. Druk vervolgens op F5 om het functie-app-project te starten en de debugger te koppelen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](./media/functions-create-first-function-powershell/functions-vscode-f5.png)

1. Voeg de query reeks `?name=<yourname>` toe aan deze URL en gebruik `Invoke-RestMethod` om de aanvraag uit te voeren, als volgt:

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
>
> Het maken van een functie-app in azure vraagt alleen om de naam van uw functie-app. Andere waarden worden voor u gedefinieerd.
> Stel `azureFunctions.advancedCreation` in op `true` moet worden gevraagd om alle andere waarden.

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test"></a>De functie uitvoeren in azure

Als u wilt controleren of uw gepubliceerde functie wordt uitgevoerd in azure, voert u de volgende Power shell-opdracht uit en vervangt u de `Uri` para meter door de URL van de functie http trigger uit de vorige stap. Voeg, net als voorheen, de query reeks `&name=<yourname>` toe aan de URL, zoals in het volgende voor beeld:

```powershell
PS > Invoke-WebRequest -Method Get -Uri "https://glengatest-vscode-powershell.azurewebsites.net/api/HttpTrigger?code=nrY05eZutfPqLo0som...&name=PowerShell"

StatusCode        : 200
StatusDescription : OK
Content           : Hello PowerShell
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 16
                    Content-Type: text/plain; charset=utf-8
                    Date: Thu, 25 Apr 2019 16:01:22 GMT

                    Hello PowerShell
Forms             : {}
Headers           : {[Content-Length, 16], [Content-Type, text/plain; charset=utf-8], [Date, Thu, 25 Apr 2019 16:01:22 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 16
```

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio code gebruikt voor het maken van een Power shell-functie-app met een eenvoudige, door HTTP geactiveerde functie. Mogelijk wilt u ook meer informatie over [fout opsporing van een Power shell-functie](functions-debug-powershell-local.md) met behulp van de Azure functions core tools. Bekijk de [Azure functions Power shell-ontwikkelaars handleiding](functions-reference-powershell.md).

> [!div class="nextstepaction"]
> [Application Insights-integratie inschakelen](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure portal]: https://portal.azure.com
[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions (Azure Functions-extensie voor Visual Studio Code)
[' Wacht fout opsporingsprogramma ']: /powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6

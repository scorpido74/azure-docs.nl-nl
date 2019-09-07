---
title: Uw eerste functie maken in Azure met behulp van Visual Studio Code
description: Maak een eenvoudige HTTP-geactiveerde functie en publiceer deze op Azure met behulp van de Azure Functions-extensie in Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-functies, functies, gebeurtenisverwerking, berekenen, architectuur zonder server
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 52f682f7c7f06056be122b33d27592a55a01be94
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70744079"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Uw eerste functie maken met Visual Studio Code

Met Azure Functions kunt u uw code in een [serverloze](https://azure.microsoft.com/solutions/serverless/) omgeving uitvoeren zonder dat u eerst een virtuele machine moet maken of een webtoepassing publiceren.

In dit artikel leert u hoe u de [Azure Functions extension for Visual Studio Code] (Azure Functions-extensie voor Visual Studio Code) gebruikt om met Microsoft Visual Studio Code een 'hello world'-functie op uw lokale computer te maken en te testen. Vervolgens publiceert u de functiecode vanuit Visual Studio Code op Azure.

![Azure-functiecode in een Visual Studio-project](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

De uitbrei ding C#biedt momenteel ondersteuning voor Java script-, Java-en python-functies. De stappen in dit artikel en het artikel die volgen alleen Java script en C# functions ondersteunen. Zie [python implementeren in azure functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions)voor meer informatie over het gebruik van Visual Studio code voor het maken en publiceren van python-functies. Zie [uw eerste Power shell-functie maken in azure](functions-create-first-function-powershell.md)voor meer informatie over het gebruik van Visual Studio code voor het maken en publiceren van Power shell-functies. 

De extensie is momenteel beschikbaar als preview-product. Zie de uitbreidingspagina [Azure Functions extension for Visual Studio Code] (Azure Functions-extensie voor Visual Studio Code) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* Installeer [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Installeer versie 2. x van de [Azure functions core tools](functions-run-local.md#v2).

* Installeer de specifieke vereisten voor de taal van uw keuze:

    | Taal | Vereiste |
    | -------- | --------- |
    | **C#** | [C#switch](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Actieve LTS en onderhoud LTS-versies (8.11.1 en 10.14.1 aanbevolen).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>De functie uitvoeren in azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). Deze URL bevat de functie sleutel, die wordt door gegeven aan `code` de query parameter. Zorg ervoor dat u net als eerder de queryreeks `?name=<yourname>` toevoegt aan het eind van deze URL en de aanvraag uitvoert.

    De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. Hieronder ziet u het antwoord op de externe GET-aanvraag dat door de functie wordt geretourneerd, weergegeven in de browser: 

    ![Het antwoord van de functie in de browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Volgende stappen

U hebt een functie-app met een eenvoudige HTTP-geactiveerde functie gemaakt in Visual Studio Code. In het volgende artikel vouwt u die functie uit door een uitvoer binding toe te voegen. Deze binding schrijft de teken reeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij. In het volgende artikel ziet u ook hoe u deze nieuwe Azure-resources kunt opschonen door de resource groep die u hebt gemaakt, te verwijderen.

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij binding aan uw functie toevoegen](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions (Azure Functions-extensie voor Visual Studio Code)

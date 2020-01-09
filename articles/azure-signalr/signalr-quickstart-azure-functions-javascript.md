---
title: Serverloze Quick Start van Azure signalering-java script
description: Een snelstart waarin u leert hoe u Azure SignalR Service en Azure Functions gebruikt om een chatruimte te maken.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.openlocfilehash: eadeb0f0203868c2a1a37190fdd46e47bf26e8f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450268"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Quickstart: Een chatruimte maken met Azure Functions en de service SignalR met behulp van JavaScript

Met Azure SignalR Service kunt u eenvoudig realtime functionaliteit toevoegen aan uw toepassing. Azure Functions is een serverloos platform waarmee u code kunt uitvoeren zonder een infrastructuur te beheren. In deze snelstart leert u hoe u SignalR Service en Functions gebruikt om een serverloze, realtime chattoepassing te bouwen.

## <a name="prerequisites"></a>Vereisten

Deze snelstartgids kan worden uitgevoerd op macOS, Windows of Linux.

Zorg ervoor dat u een code-editor hebt geïnstalleerd, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/).

Installeer de [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools#installing) (versie 2 of hoger) om Azure-functie-apps lokaal uit te voeren.

Deze Snelstartgids maakt gebruik van [node. js](https://nodejs.org/en/download/) 10. x, maar zou moeten werken met andere versies. Raadpleeg de documentatie voor de [Azure functions runtime versie](../azure-functions/functions-versions.md#languages) voor meer informatie over ondersteunde node. js-versies.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>De Azure Functions-app uitvoeren

1. Controleer in de browser waarin de Azure Portal is geopend of het SignalR Service-exemplaar dat u eerder hebt geïmplementeerd, is gemaakt. Daarvoor typt u de naam ervan in het zoekvak bovenaan de portal. Selecteer het exemplaar om het te openen.

    ![Het service-exemplaar van SignalR zoeken](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

    ![SignalR Service maken](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Open in de code-editor de map *src/chat/java script* in de gekloonde opslag plaats.

1. Wijzig de naam *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json** plakt u de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. JavaScript-functies zijn georganiseerd in mappen. In elke map zitten twee bestanden: *function.json* definieert de bindingen die worden gebruikt in de functie, en *index.js* is de hoofdtekst van de functie. Deze functie-app bevat twee HTTP-geactiveerde functies:

    - **negotiate**: gebruikt de invoerbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **messages**: ontvangt een chatbericht in de hoofdtekst van de aanvraag en gebruikt de uitvoerbinding *SignalR* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Controleer in de terminal of u zich in de map *src/chat/java script* bevindt. Voer de functie-app uit.

    ```bash
    func start
    ```

    ![SignalR Service maken](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een realtime serverloze toepassing gemaakt en uitgevoerd in VS code. Nu volgt meer informatie over het implementeren van Azure Functions vanuit VS Code.

> [!div class="nextstepaction"]
> [Azure Functions met VS Code implementeren](/azure/javascript/tutorial-vscode-serverless-node-01)

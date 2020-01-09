---
title: Windows signalerings service serverloze Snelstartgids-python
description: Een snelstart waarin u leert hoe u Azure SignalR Service en Azure Functions gebruikt om een chatruimte te maken.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.openlocfilehash: 0cf8705cf2567a60129681c2db41b0868f8fe182
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392153"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Snelstartgids: een chat ruimte met Azure Functions-en signalerings service maken met behulp van python

Met Azure SignalR Service kunt u eenvoudig realtime functionaliteit toevoegen aan uw toepassing. Azure Functions is een serverloos platform waarmee u code kunt uitvoeren zonder een infrastructuur te beheren. In deze snelstart leert u hoe u SignalR Service en Functions gebruikt om een serverloze, realtime chattoepassing te bouwen.

## <a name="prerequisites"></a>Vereisten

Deze snelstartgids kan worden uitgevoerd op macOS, Windows of Linux.

Zorg ervoor dat u een code-editor hebt geïnstalleerd, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/).

Installeer de [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools#installing) (versie 2.7.1505 of hoger) om python Azure-functie-apps lokaal uit te voeren.

Azure Functions vereist [Python 3,6 of 3,7](https://www.python.org/downloads/).

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

1. Open in de code-editor de map *src/chat/python* in de gekloonde opslag plaats.

1. Als u python-functies lokaal wilt ontwikkelen en testen, moet u in een python 3,6-of 3,7-omgeving werken. Voer de volgende opdrachten uit om een virtuele omgeving met de naam `.venv` te maken.

    **Linux of macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Wijzig de naam *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json** plakt u de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. Python-functies worden in mappen ingedeeld. In elke map zijn twee bestanden: *Function. json* definieert de bindingen die worden gebruikt in de functie en *\_\_init\_\_. py* de hoofd tekst van de functie is. Deze functie-app bevat twee HTTP-geactiveerde functies:

    - **negotiate**: gebruikt de invoerbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **messages**: ontvangt een chatbericht in de hoofdtekst van de aanvraag en gebruikt de uitvoerbinding *SignalR* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Zorg ervoor dat u zich in de map *src/chat/python* bevindt in de Terminal waarbij de virtuele omgeving is geactiveerd. Installeer de benodigde Python-pakketten met PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Voer de functie-app uit.

    ```bash
    func start
    ```

    ![Functie-app uitvoeren](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een realtime serverloze toepassing gemaakt en uitgevoerd in VS code. Nu volgt meer informatie over het implementeren van Azure Functions vanuit VS Code.

> [!div class="nextstepaction"]
> [Azure Functions met VS Code implementeren](/azure/javascript/tutorial-vscode-serverless-node-01)

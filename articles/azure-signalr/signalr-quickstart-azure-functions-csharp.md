---
title: 'Azure SignalR Service serverless quickstart - C #'
description: Een snelstart waarin u leert hoe u Azure SignalR Service en Azure Functions gebruikt om een chatruimte te maken.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 75d9977546c2a085765310a5654897f739a271ae
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "65595391"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Snelstart: een chatroom maken met Azure-functies en SignalR-service met C\#

Met Azure SignalR Service kunt u eenvoudig realtime functionaliteit toevoegen aan uw toepassing. Azure Functions is een serverloos platform waarmee u code kunt uitvoeren zonder een infrastructuur te beheren. In deze snelstart leert u hoe u SignalR Service en Functions gebruikt om een serverloze, realtime chattoepassing te bouwen.

## <a name="prerequisites"></a>Vereisten

Als je Visual Studio 2019 nog niet hebt geïnstalleerd, kun je de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

U deze zelfstudie ook uitvoeren op de opdrachtregel (macOS, Windows of Linux) met behulp van de [Azure Functions Core Tools (v2),](https://github.com/Azure/azure-functions-core-tools#installing)de [.NET Core SDK](https://dotnet.microsoft.com/download)en uw favoriete codeeditor.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>De Azure Functions-app uitvoeren

1. Start Visual Studio (of een andere codeeditor) en open de oplossing in de *src/chat/csharp* map van de gekloonde repository.

1. Controleer in de browser waarin de Azure Portal is geopend of het SignalR Service-exemplaar dat u eerder hebt geïmplementeerd, is gemaakt. Daarvoor typt u de naam ervan in het zoekvak bovenaan de portal. Selecteer het exemplaar om het te openen.

    ![Het service-exemplaar van SignalR zoeken](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecteer **Sleutels** om de verbindingsreeksen voor het service-exemplaar van SignalR weer te geven.

1. Selecteer en kopieer de primaire verbindingsreeks.

1. Terug in Visual Studio wijzigt u in Solution Explorer de naam van *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json** plakt u de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. Open **Functions.cs**. Deze functie-app bevat twee HTTP-geactiveerde functies:

    - **GetSignalRInfo**: gebruikt de invoergegevensbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **SendMessage**: ontvangt een chatbericht in de aanvraagbody en gebruikt de uitvoergegevensbinding *SignalR* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Gebruik een van de volgende opties om de Azure Function-app lokaal te starten.

    - **Visual Studio:** Selecteer in het menu *Foutopsporing* starten de optie *Foutopsporing starten* om de toepassing uit te voeren.

        ![Fouten in de toepassing opsporen](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Opdrachtregel:** Voer de volgende opdracht uit om de functiehost te starten.

        ```bash
        func start
        ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een real-time serverloze toepassing gebouwd en uitgevoerd in Visual Studio. Nu volgt meer informatie over het ontwikkelen en implementeren van Azure Functions met behulp van Visual Studio Code.

> [!div class="nextstepaction"]
> [Azure Functions ontwikkelen met Visual Studio](../azure-functions/functions-develop-vs.md)
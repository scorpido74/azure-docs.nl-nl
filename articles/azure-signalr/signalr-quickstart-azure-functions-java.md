---
title: Java gebruiken om een chatroom te maken met Azure-functies en SignalR-service
description: Een snelstart waarin u leert hoe u Azure SignalR Service en Azure Functions gebruikt om een chatruimte te maken.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083201"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snelstart: Java gebruiken om een chatroom te maken met Azure-functies en SignalR-service

Met Azure SignalR Service u eenvoudig realtime functionaliteit toevoegen aan uw toepassing en Azure Functions is een serverloos platform waarmee u uw code uitvoeren zonder infrastructuur te beheren. In deze quickstart gebruik je Java om een serverloze, real-time chatapplicatie te bouwen met behulp van SignalR Service en Functions.

## <a name="prerequisites"></a>Vereisten

- Een codeeditor, zoals [Visual Studio Code](https://code.visualstudio.com/)
- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Core-hulpprogramma's voor Azure-functies](https://github.com/Azure/azure-functions-core-tools#installing). Wordt gebruikt om Azure Function-apps lokaal uit te voeren.

   > [!NOTE]
   > De vereiste SignalR Service-bindingen in Java worden alleen ondersteund in Azure Function Core Tools versie 2.4.419 (hostversie 2.0.12332) of hoger.

   > [!NOTE]
   > Voor het installeren van extensies vereist Azure Functions Core Tools de [.NET Core SDK](https://www.microsoft.com/net/download) geïnstalleerd. Er is echter geen kennis van .NET vereist om JavaScript Azure-functie-apps te bouwen.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), versie 8
- [Apache Maven](https://maven.apache.org), versie 3.0 of hoger

> [!NOTE]
> Deze snelstartgids kan worden uitgevoerd op macOS, Windows of Linux.

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

1. Open in uw code-editor de map *src/chat/java* in de gekloonde opslagplaats.

1. Wijzig de naam *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json** plakt u de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. Het hoofdbestand dat de functies bevat zijn in *src/chat/java/src/main/java/com/function/Functions.java:*

    - **negotiate**: gebruikt de invoerbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **sendMessage** - Ontvangt een chatbericht in de aanvraaginstantie en gebruikt de *SignalR-uitvoerbinding* om het bericht uit te zenden naar alle verbonden clienttoepassingen.

1. Zorg er in de terminal voor dat u zich in de *src/chat/java-map* bevindt. Bouw de functie-app.

    ```bash
    mvn clean package
    ```

1. Voer de functie-app lokaal uit.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart bouwde en runde je een real-time serverless applicatie met Maven. Lees vervolgens hoe u Java Azure-functies helemaal opnieuw maken.

> [!div class="nextstepaction"]
> [Uw eerste functie maken met Java en Maven](../azure-functions/functions-create-first-java-maven.md)
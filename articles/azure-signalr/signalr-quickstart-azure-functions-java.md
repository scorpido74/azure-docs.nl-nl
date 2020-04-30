---
title: Java gebruiken om een chat ruimte te maken met Azure Functions en de signaal service
description: Een snelstart waarin u leert hoe u Azure SignalR Service en Azure Functions gebruikt om een chatruimte te maken.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77083201"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Snelstartgids: Java gebruiken om een chat ruimte te maken met Azure Functions en de signaal service

Met de Azure signalerings service kunt u eenvoudig real-time functionaliteit toevoegen aan uw toepassing en Azure Functions een serverloos platform is waarmee u uw code kunt uitvoeren zonder een infra structuur te beheren. In deze Quick Start gebruikt u Java voor het bouwen van een serverloze, realtime chat toepassing met behulp van de seingevings service en-functies.

## <a name="prerequisites"></a>Vereisten

- Een code-editor, zoals [Visual Studio code](https://code.visualstudio.com/)
- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools#installing). Wordt gebruikt om Azure function-apps lokaal uit te voeren.

   > [!NOTE]
   > De vereiste bindingen van de service signalering in Java worden alleen ondersteund in azure function core tools versie 2.4.419 (host versie 2.0.12332) of hoger.

   > [!NOTE]
   > Als u uitbrei dingen wilt installeren, moet Azure Functions Core Tools de [.net core SDK](https://www.microsoft.com/net/download) zijn geïnstalleerd. Er is echter geen kennis van .NET vereist om JavaScript Azure-functie-apps te bouwen.

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

1. Open in de code-editor de map *src/chat/Java* in de gekloonde opslag plaats.

1. Wijzig de naam *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json** plakt u de verbindingsreeks in de waarde van de instelling **AzureSignalRConnectionString**. Sla het bestand op.

1. Het hoofd bestand met de functies bevindt zich in *src/chat/Java/src/main/Java/function. java*:

    - **negotiate**: gebruikt de invoerbinding *SignalRConnectionInfo* om geldige verbindingsgegevens te genereren en te retourneren.
    - **SendMessage** : er wordt een chat bericht in de aanvraag tekst ontvangen en de *signaal* uitvoer binding wordt gebruikt om het bericht uit te zenden naar alle verbonden client toepassingen.

1. Controleer in de terminal of u zich in de map *src/chat/Java* bevindt. De functie-app bouwen.

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

In deze Quick Start hebt u een realtime serverloze toepassing gemaakt en uitgevoerd met behulp van Maven. Meer informatie over het maken van een volledig nieuwe Java-Azure Functions.

> [!div class="nextstepaction"]
> [Uw eerste functie maken met Java en Maven](../azure-functions/functions-create-first-java-maven.md)
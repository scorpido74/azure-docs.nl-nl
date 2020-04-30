---
title: bestand opnemen
description: bestand opnemen
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67176406"
---
## <a name="run-the-web-application"></a>De webtoepassing uitvoeren

1. Voor uw gemak wordt een voorbeeldwebtoepassing van één pagina in GitHub gehost. Open uw browser in [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/).

    > [!NOTE]
    > De bron van het HTML-bestand bevindt zich op [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Wanneer u om de basis-URL van de functie-app wordt gevraagd, voert u `http://localhost:7071` in.

1. Voer een gebruikersnaam in wanneer hierom wordt gevraagd.

1. De webtoepassing roept de functie *GetSignalRInfo* in de functie-app aan om de verbindingsgegevens voor het verbinden met Azure SignalR Service op te halen. Wanneer de verbinding is voltooid, wordt het invoervak voor het chat-bericht weergegeven.

1. Typ een bericht en druk op Enter. Het bericht wordt verzonden naar de functie *SendMessage* in de Azure-functie-app die vervolgens de SignalR-uitvoerbinding gebruikt om het bericht naar alle verbonden clients te verzenden. Als alles goed werkt, wordt het bericht weergegeven in de toepassing.

    ![De toepassing uitvoeren](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Open een ander exemplaar van de webtoepassing in een ander browservenster. U ziet dat alle verzonden berichten in alle exemplaren van de toepassing worden weergegeven.

> [!IMPORTANT]
> Omdat de HTML-pagina wordt geleverd met HTTPS, maar de lokale Azure Functions runtime gebruikt standaard HTTP, kan uw browser (zoals Firefox) een gemengd beleid afdwingen waarmee de aanvragen van de webpagina worden geblokkeerd voor uw functies. U kunt dit oplossen door een browser te gebruiken die deze beperking niet heeft of een lokale HTTP-server zoals [http-server](https://www.npmjs.com/package/http-server) in de */docs/demo/chat-v2* -map te starten. Zorg ervoor dat de oorsprong is toegevoegd `CORS` aan de instelling in *Local. settings. json*.
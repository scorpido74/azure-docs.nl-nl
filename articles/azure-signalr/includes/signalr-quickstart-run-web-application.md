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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67176406"
---
## <a name="run-the-web-application"></a>De webtoepassing uitvoeren

1. Voor uw gemak wordt een voorbeeldwebtoepassing van één pagina in GitHub gehost. Open uw [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)browser voor .

    > [!NOTE]
    > De bron van het HTML-bestand bevindt zich op [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Wanneer u om de basis-URL van de functie-app wordt gevraagd, voert u `http://localhost:7071` in.

1. Voer een gebruikersnaam in wanneer hierom wordt gevraagd.

1. De webtoepassing roept de functie *GetSignalRInfo* in de functie-app aan om de verbindingsgegevens voor het verbinden met Azure SignalR Service op te halen. Wanneer de verbinding is voltooid, wordt het invoervak voor het chat-bericht weergegeven.

1. Typ een bericht en druk op Enter. Het bericht wordt verzonden naar de functie *SendMessage* in de Azure-functie-app die vervolgens de SignalR-uitvoerbinding gebruikt om het bericht naar alle verbonden clients te verzenden. Als alles goed werkt, wordt het bericht weergegeven in de toepassing.

    ![De toepassing uitvoeren](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Open een ander exemplaar van de webtoepassing in een ander browservenster. U ziet dat alle verzonden berichten in alle exemplaren van de toepassing worden weergegeven.

> [!IMPORTANT]
> Omdat de HTML-pagina wordt weergegeven met HTTPS, maar de lokale runtime van Azure-functies standaard HTTP gebruikt, kan uw browser (zoals Firefox) een beleid voor gemengde inhoud afdwingen dat de aanvragen van de webpagina naar uw functies blokkeert. Gebruik hiervoor een browser die deze beperking niet heeft of start een lokale [HTTP-server zoals http-server](https://www.npmjs.com/package/http-server) in de map */docs/demo/chat-v2.* Zorg ervoor dat de `CORS` oorsprong wordt toegevoegd aan de instelling in *local.settings.json*.
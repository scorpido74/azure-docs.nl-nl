---
title: Hybride verbindingen voor Azure Relay - WebSockets in knooppunt
description: Een Node.js consoletoepassing schrijven voor websockets voor hybride verbindingen in Azure Relay
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 818db4db082a441877b573fd52361e63becce374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75352674"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Aan de slag met WebSockets voor hybride verbindingen in Azure Relay in Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In deze snelstart maakt u Node.js-toepassingen voor afzenders en ontvangers waarmee berichten worden verzonden en ontvangen met behulp van WebSockets voor hybride verbindingen in Azure Relay. Zie [Azure Relay](relay-what-is-it.md) voor meer informatie over Azure Relay in het algemeen. 

In deze snelstart voert u de volgende stappen uit: 

1. Een Relay-naamruimte maken met behulp van Azure Portal.
2. Een hybride verbinding in die naamruimte maken met behulp van Azure Portal.
3. Een serverconsoletoepassing (listener) schrijven om berichten te ontvangen.
4. Een clientconsoletoepassing (afzender) schrijven om berichten te verzenden.
5. Toepassingen uitvoeren. 

## <a name="prerequisites"></a>Vereisten

- [Knooppunt.js.](https://nodejs.org/en/)
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-namespace"></a>Een naamruimte maken
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Een hybride verbinding maken
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Een servertoepassing (listener) maken
Maak een Node.js-consoletoepassing om berichten van de Relay te beluisteren en te ontvangen.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Een clienttoepassing maken (afzender)
Maak een Node.js-consoletoepassing om berichten naar de Relay te verzenden.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>De toepassingen uitvoeren

1. Voer de servertoepassing uit: via een Node.js-opdrachtprompt van het type `node listener.js`.
2. Voer de clienttoepassing uit: via een Node.js-opdrachtprompt van het type `node sender.js`, en voer tekst in.
3. Zorg ervoor dat de servertoepassingsconsole de tekst uitvoert die in de clienttoepassing is ingevoerd.

    ![actieve-toepassingen](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Gefeliciteerd, u hebt een end-to-endtoepassing met hybride verbindingen gemaakt met behulp van Node.js!

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u Node.js-toepassingen gemaakt voor clients en servers waarmee berichten worden verzonden en ontvangen met behulp van WebSockets. De hybride verbindingsfunctie van Azure Relay ondersteunt tevens HTTP voor het verzenden en ontvangen van berichten. Zie de [snelstart over Node.js HTTP](relay-hybrid-connections-http-requests-node-get-started.md) voor informatie over het gebruik van HTTP met hybride verbindingen van Azure Relay.

In deze snelstart hebt u Node.js gebruikt om client- en servertoepassingen te maken. Zie de [snelstart over WebSockets in .NET](relay-hybrid-connections-dotnet-get-started.md) of de [snelstart over HTTP in .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md) voor informatie over het schrijven van client- en servertoepassingen in .Net Framework.



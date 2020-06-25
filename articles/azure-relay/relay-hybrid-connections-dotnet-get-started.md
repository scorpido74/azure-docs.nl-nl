---
title: Azure Relay Hybride verbindingen-websockets in .NET
description: Een C#-console toepassing schrijven voor Azure Relay Hybride verbindingen websockets.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: a6e759b8cda7515faf63834ef15c013e2f075687
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85317074"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Aan de slag met websockets voor hybride verbindingen in Azure Relay in .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In deze snelstart maakt u .NET maakt u toepassingen voor afzenders en ontvangers waarmee berichten worden verzonden en ontvangen met behulp van websockets voor hybride verbindingen in Azure Relay. Zie [Azure Relay](relay-what-is-it.md) voor meer informatie over Azure Relay in het algemeen. 

In deze snelstart voert u de volgende stappen uit:

1. Een Relay-naamruimte maken met behulp van Azure Portal.
2. Een hybride verbinding in die naamruimte maken met behulp van Azure Portal.
3. Een serverconsoletoepassing (listener) schrijven om berichten te ontvangen.
4. Een clientconsoletoepassing (afzender) schrijven om berichten te verzenden.
5. Toepassingen uitvoeren. 

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

* [Visual Studio 2015 of hoger](https://www.visualstudio.com). In de voorbeelden in deze zelfstudie wordt Visual Studio 2017 gebruikt.
* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-namespace"></a>Een naamruimte maken
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Een hybride verbinding maken
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Een servertoepassing (listener) maken
Maak in Visual Studio een C#-consoletoepassing om berichten van de Relay te beluisteren en te ontvangen.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Een clienttoepassing maken (afzender)
Maak in Visual Studio een C#-consoletoepassing om berichten naar de Relay te sturen.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>De toepassingen uitvoeren
1. Voer de servertoepassing uit.
2. Voer de clienttoepassing uit en voer wat tekst in.
3. Zorg ervoor dat de servertoepassingsconsole de tekst weergeeft die in de clienttoepassing is ingevoerd.

    ![actieve-toepassingen](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Gefeliciteerd, u hebt een complete Hybride verbindingen-toepassing gemaakt.

## <a name="next-steps"></a>Volgende stappen
In deze snelstart hebt u .NET toepassingen gemaakt voor afzenders en ontvangers waarmee berichten worden verzonden en ontvangen met behulp van websockets. De hybride verbindingsfunctie van Azure Relay ondersteunt tevens HTTP voor het verzenden en ontvangen van berichten. Zie de [snelstart over HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md) voor informatie over het gebruik van HTTP met hybride verbindingen van Azure Relay.

In deze snelstart hebt u .NET Framework gebruikt om client- en servertoepassingen te maken. Zie de [snelstart over WebSockets in Node.js](relay-hybrid-connections-node-get-started.md) of de [snelstart over HTTP in Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md) voor informatie over het schrijven van client- en servertoepassingen in Node.js.


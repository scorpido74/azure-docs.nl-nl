---
title: Interne werking van Azure SignalR Service
description: Meer informatie over de interne service van Azure signalering, de architectuur, de verbindingen en hoe gegevens worden verzonden.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 62afa5ee6993aa1bb3c7b5926e5320ab1fa510a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157594"
---
# <a name="azure-signalr-service-internals"></a>Interne werking van Azure SignalR Service

Azure SignalR Service is gebouwd op basis van het ASP.NET Core SignalR-framework. Azure SignalR Service ondersteunt ook ASP.NET SignalR als preview-functie.

> Voor de ondersteuning van ASP.NET SignalR wordt het gegevensprotocol van ASP.NET SignalR opnieuw geïmplementeerd op basis van het ASP.NET Core-framework door Azure SignalR Service

U kunt eenvoudig een lokale ASP.NET Core SignalR-toepassing migreren voor werking met SignalR Service door slechts enkele regels code te wijzigen.

In het volgende diagram wordt de typische architectuur beschreven voor gebruik van SignalR Service met uw toepassingsserver.

De verschillen met een zelf-hostende ASP.NET Core SignalR-toepassing worden ook behandeld.

![Architectuur](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Serververbindingen

Een zelf-hostende ASP.NET Core SignalR-toepassingsserver luistert rechtstreeks naar en maakt rechtstreeks verbinding met clients.

Met SignalR Service accepteert de toepassingsserver geen permanente clientverbindingen meer, maar gebeurt in plaats daarvan het volgende:

1. Voor elke hub wordt een `negotiate`-eindpunt beschikbaar gesteld door Azure SignalR Service SDK.
1. Dit eindpunt reageert op onderhandelingsaanvragen van clients en leidt clients om naar SignalR Service.
1. Uiteindelijk worden clients verbonden met SignalR Service.

Zie [Clientverbindingen](#client-connections) voor meer informatie.

Zodra de toepassingsserver is gestart, gebeurt het volgende: 
- Voor ASP.NET Core SignalR opent Azure SignalR Service SDK per hub vijf WebSocket-verbindingen met SignalR Service. 
- Voor ASP.NET SignalR opent Azure SignalR Service SDK per hub vijf WebSocket-verbindingen met SignalR Service en één exemplaar per WebSocket-toepassingsverbinding.

Vijf WebSocket-verbindingen is de standaardwaarde. Deze kan worden gewijzigd in [Configuratie](https://github.com/Azure/azure-signalr/blob/dev/docs/use-signalr-service.md#connectioncount).

Berichten naar en van clients worden gemultiplext in deze verbindingen.

Deze verbindingen blijven altijd verbonden met SignalR Service. Als er een serververbinding wordt verbroken door een netwerkprobleem, gebeurt het volgende:
- de verbinding met alle clients van deze serververbinding wordt verbroken (zie [Gegevensoverdracht tussen client en server](#data-transmit-between-client-and-server) voor meer informatie);
- de serververbinding wordt automatisch hersteld.

## <a name="client-connections"></a>Clientverbindingen

Wanneer u SignalR Service gebruikt, maken clients verbinding met SignalR Service in plaats van de toepassingsserver.
Er zijn twee stappen om permanente verbindingen tot stand te brengen tussen de client en SignalR Service.

1. De client stuurt een onderhandelingsaanvraag naar de toepassingsserver. Met Azure SignalR Service SDK retourneert de toepassingsserver een omleidingsantwoord met de URL en het toegangstoken van SignalR Service.

- Voor ASP.NET Core SignalR ziet een typisch omleidingsantwoord er als volgt uit:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- Voor ASP.NET SignalR ziet een typisch omleidingsantwoord er als volgt uit:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Na ontvangst van het omleidingsantwoord maakt de client gebruik van de nieuwe URL en het toegangstoken om het gebruikelijke verbindingsproces met SignalR Service te starten.

Meer informatie over [transportprotocollen](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md) van ASP.NET Core SignalR.

## <a name="data-transmit-between-client-and-server"></a>Gegevensoverdracht tussen client en server

Wanneer een client is verbonden met SignalR Service, zoekt serviceruntime een serververbinding voor deze client
- Deze stap vindt eenmalig plaats, op basis van een-op-een toewijzing tussen de client en serververbindingen.
- De toewijzing wordt bijgehouden in SignalR Service totdat de verbinding met de client of server wordt verbroken.

Op dat moment ontvangt de toepassingsserver een gebeurtenis met informatie van de nieuwe client. In de toepassingsserver wordt een logische verbinding met de client tot stand gebracht. Via SignalR-Service wordt het gegevenskanaal van de client naar de toepassingsserver gerealiseerd.

SignalR Service draagt gegevens over van de client naar de koppelende toepassingsserver. Gegevens van de toepassingsserver worden daarnaast verzonden naar de toegewezen clients.

Zoals u ziet, is Azure SignalR Service in wezen een logische transportlaag tussen de toepassingsserver en clients. Alle permanente verbindingen worden geoffload naar SignalR Service.
De toepassingsserver hoeft alleen de bedrijfslogica in de hubklasse af te handelen, zonder zich bezig te houden met clientverbindingen.
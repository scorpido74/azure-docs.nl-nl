---
title: Wat is Azure SignalR-service?
description: Beter begrijpen welke typische gebruiksscenario's Azure SignalR moeten gebruiken en leer de belangrijkste voordelen van Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157590"
---
# <a name="what-is-azure-signalr-service"></a>Wat is Azure SignalR-service?

Met de service Azure SignalR wordt het proces van het toevoegen van realtimewebfunctionaliteit aan toepassingen via HTTP vereenvoudigd. Dankzij deze realtimefunctionaliteit kunnen via de service inhoudsupdates worden gepusht naar verbonden clients, zoals een enkele webpagina of mobiele toepassing. Zo worden clients bijgewerkt zonder de server te hoeven pollen of nieuwe HTTP-bijwerkaanvragen te hoeven verzenden.


In dit artikel vindt u een overzicht van de service Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>Waarvoor wordt de service Azure SignalR gebruikt?

Elk scenario waarvoor gegevens in realtime van server naar client moeten worden geduwd, kan Azure SignalR Service gebruiken.

Traditionele realtime functies waarvoor vaak polling van de server nodig zijn, kunnen ook azure SignalR-service gebruiken.

Azure SignalR Service is gebruikt in een breed scala van industrieën, voor elk toepassingstype dat realtime inhoudsupdates vereist. We zetten enkele voorbeelden op een rijtje die goed zijn om Azure SignalR-service te gebruiken:

* **Hoogfrequente data-updates:** gaming, stemmen, polling, veiling.
* **Dashboards en monitoring:** bedrijfsdashboard, gegevens over de financiële markten, instant sales update, multi-player game leader board en IoT monitoring.
* **Chat:** live chat room, chat bot, on-line customer support, real-time shopping assistant, messenger, in-game chat, enzovoort.
* **Real-time locatie op de kaart:** logistieke tracking, delivery status tracking, transport status updates, GPS apps.
* **Real-time gerichte advertenties:** gepersonaliseerde real-time push advertenties en aanbiedingen, interactieve advertenties.
* **Collaboratieve apps:** cocreatie, whiteboard-apps en teammeetingsoftware.
* **Pushmeldingen:** sociaal netwerk, e-mail, game, reiswaarschuwing.
* **Real-time broadcasting:** live audio/video broadcasting, live captioning, vertalen, events/news broadcasting.
* **IoT en verbonden apparaten:** realtime IoT-statistieken, afstandsbediening, real-time status en locatietracking.
* **Automatisering:** real-time trigger van upstream-gebeurtenissen.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Wat zijn de voordelen van Azure SignalR Service?

**Standaard gebaseerd:**

SignalR biedt een aantal verschillende technieken voor het bouwen van realtimewebtoepassingen. [WebSockets](https://wikipedia.org/wiki/WebSocket) is de optimale transportmethode, maar als bepaalde opties niet beschikbaar zijn, worden ook andere methoden zoals [SSE (Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) en Long Polling gebruikt. In SignalR wordt automatisch de juiste transportmethode gedetecteerd en geïnitialiseerd, afhankelijk van welke functies op de server en de client worden ondersteund.

**Ondersteuning voor Native ASP.NET Core:**

SignalR Service biedt native programmeerervaring met ASP.NET Core en ASP.NET. Het ontwikkelen van nieuwe SignalR-toepassing met SignalR Service, of het migreren van bestaande SignalR-gebaseerde toepassing naar SignalR Service vereist minimale inspanningen.
SignalR Service ondersteunt ook de nieuwe functie van ASP.NET Core, Blazor aan de Server-kant.

**Brede klantenondersteuning:**

SignalR Service werkt met een breed scala aan clients, zoals web- en mobiele browsers, desktop-apps, mobiele apps, serverproces, IoT-apparaten en gameconsoles. SignalR Service biedt SDK's in verschillende talen. Naast native ASP.NET Core of ASP.NET C# SDK's biedt SignalR Service ook JavaScript-client SDK, om webclients en veel JavaScript-frameworks in te schakelen. Java client SDK wordt ook ondersteund voor Java-toepassingen, waaronder Android native apps. SignalR Service ondersteunt REST API en serverless via integraties met Azure Functions en Event Grid.

**Omgaan met grootschalige clientverbindingen:**

SignalR Service is ontworpen voor grootschalige real-time toepassingen. Met SignalR Service kunnen meerdere instanties samenwerken om te schalen naar miljoenen clientverbindingen. De service ondersteunt ook meerdere globale regio's voor sharding, hoge beschikbaarheid of disaster recovery-doeleinden.

**Verwijder de last voor self-host SignalR:**

In vergelijking met zelf gehoste SignalR-toepassingen, zal het overschakelen naar SignalR Service de noodzaak wegnemen om back-vliegtuigen te beheren die de weegschalen en clientverbindingen verwerken. De volledig beheerde service vereenvoudigt ook webapplicaties en bespaart hostingkosten. SignalR Service biedt wereldwijd bereik en datacenter en netwerk van wereldklasse, schaalt naar miljoenen verbindingen, garandeert SLA en biedt alle compliance en beveiliging bij Azure-standaard.

![Beheerde SignalR-service](./media/signalr-overview/managed-signalr-service.png)

**Bied uitgebreide API's voor verschillende berichtenpatronen:**

Met SignalR Service kan de server berichten verzenden naar een bepaalde verbinding, alle verbindingen of een subset van verbindingen die van een specifieke gebruiker zijn of in een willekeurige groep zijn geplaatst.

## <a name="how-to-use-azure-signalr-service"></a>De service Azure SignalR gebruiken

Er zijn veel verschillende manieren om te programmeren met Azure SignalR Service, zoals sommige van de voorbeelden hier worden vermeld:

- **[Een ASP.NET Core SignalR-app schalen](signalr-concept-scale-aspnet-core.md)**: integreer de service Azure SignalR met een ASP.NET Core SignalR-toepassing om uit te schalen naar duizenden verbindingen.
- **[Serverloze realtimeapps bouwen](signalr-concept-azure-functions.md)**: gebruik de integratie van Azure Functions met de service Azure SignalR om serverloze realtimetoepassingen te bouwen in talen zoals JavaScript, C# en Java.
- **[Berichten van de server verzenden naar clients via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)**: de service Azure SignalR biedt REST API waarmee via toepassingen berichten kunnen worden verzonden naar clients die zijn verbonden met de service SignalR, in elke programmeertaal die compatibel is met REST.

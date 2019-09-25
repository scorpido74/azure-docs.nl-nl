---
title: Wat is de Azure signalerings service?
description: Een overzicht van de Azure signalerings service.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 06/20/2019
ms.author: zhshang
ms.openlocfilehash: e7bdc62f7fa46bbacce7f264d8f331ea64b05430
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273697"
---
# <a name="what-is-azure-signalr-service"></a>Wat is de Azure signalerings service?

Met de service Azure SignalR wordt het proces van het toevoegen van realtimewebfunctionaliteit aan toepassingen via HTTP vereenvoudigd. Dankzij deze realtimefunctionaliteit kunnen via de service inhoudsupdates worden gepusht naar verbonden clients, zoals een enkele webpagina of mobiele toepassing. Zo worden clients bijgewerkt zonder de server te hoeven pollen of nieuwe HTTP-bijwerkaanvragen te hoeven verzenden.


In dit artikel vindt u een overzicht van de service Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>Waarvoor wordt de service Azure SignalR gebruikt?

Elk scenario waarvoor het pushen van gegevens van de server naar de client in realtime is vereist, kan de Azure signalerings service gebruiken.

Traditionele realtime-functies waarvoor vaak polling van de server nodig is, kunnen ook gebruikmaken van de service Azure signalering.

De Azure Signalr-service is in een groot aantal branches gebruikt voor elk toepassings type waarvoor realtime-inhouds updates zijn vereist. We vermelden enkele voor beelden die geschikt zijn voor het gebruik van de Azure signalerings service:

* **Gegevens updates met hoge frequentie:** gaming, stem, polling, veiling.
* **Dash boards en bewaking:** bedrijfs dashboard, financiële markt gegevens, direct verkoop update, Game leider van het spel voor meerdere spelers en IOT-bewaking.
* **Chat:** live chat room, chat bot, on line klant ondersteuning, realtime boodschappen assistentie, Mess enger, chatten in games, enzovoort.
* **Real-time locatie op kaart:** logistiek volgen, bezorgings status bijhouden, transport status updates, GPS-Apps.
* **Real-time gerichte advertenties:** gepersonaliseerde realtime push advertenties en aanbiedingen, interactieve advertenties.
* **Samenwerkings toepassingen:** cocreatie, White Board-apps en software voor team vergaderingen.
* **Push meldingen:** sociaal netwerk, e-mail, Game, reis waarschuwing.
* **Realtime-uitzending:** live audio/video-uitzending, Live ondertiteling, trans acties/nieuws uitzending.
* **IOT en verbonden apparaten:** realtime IOT-metrische gegevens, beheer op afstand, realtime status en bijhouden van locaties.
* **Automation:** realtime activeren vanuit upstream-gebeurtenissen.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Wat zijn de voor delen van het gebruik van de Azure signalerings service?

**Standaard gebaseerd:**

SignalR biedt een aantal verschillende technieken voor het bouwen van realtimewebtoepassingen. [WebSockets](https://wikipedia.org/wiki/WebSocket) is de optimale transportmethode, maar als bepaalde opties niet beschikbaar zijn, worden ook andere methoden zoals [SSE (Server-Sent Events)](https://wikipedia.org/wiki/Server-sent_events) en Long Polling gebruikt. In SignalR wordt automatisch de juiste transportmethode gedetecteerd en geïnitialiseerd, afhankelijk van welke functies op de server en de client worden ondersteund.

**Ondersteuning voor systeem eigen ASP.NET Core:**

De Signa lering-service biedt systeem eigen programmerings ervaring met ASP.NET Core en ASP.NET. Het ontwikkelen van een nieuwe seingevings toepassing met de seingevings service of het migreren van een bestaande toepassing op basis van een Signa lering naar de signaal service vereist minimale inspanningen.
De Signa lering-service biedt ook ondersteuning voor de nieuwe functie van ASP.NET Core, aan de server zijde.

**Uitgebreide ondersteuning voor clients:**

De signalerings service werkt met een breed scala aan clients, zoals web-en mobiele browsers, desktop-apps, mobiele apps, Server proces, IoT-apparaten en spel consoles. De Signa lering-service biedt Sdk's in verschillende talen. Naast systeem eigen ASP.NET Core-of ASP.NET C# Sdk's biedt signalr service ook Java script-client-SDK, voor het inschakelen van webclients en vele java script-frameworks. De Java-client-SDK wordt ook ondersteund voor Java-toepassingen, waaronder systeem eigen Android-apps. De Signa lering-service ondersteunt REST API en serverloos via integraties met Azure Functions en Event Grid.

**Grootschalige client verbindingen verwerken:**

De Signa lering-service is ontworpen voor grootschalige realtime toepassingen. Met de seingevings service kunnen meerdere instanties samen werken om te schalen naar miljoenen client verbindingen. De service ondersteunt ook meerdere globale regio's voor sharding, hoge Beschik baarheid of herstel na nood gevallen.

**Verwijder de last-to-host-Signa lering:**

Vergeleken met zelf-hostende seingevings toepassingen, wordt door het overschakelen naar de signaal service de nood zaak voor het beheren van back-upabonnementen voor het verwerken van schalen en client verbindingen verwijderd. De volledig beheerde service vereenvoudigt ook webtoepassingen en bespaart kosten voor hosting. De Signa lering-service biedt wereld wijd bereik en hoogwaardige Data Center en netwerk, schaalt naar miljoenen verbindingen, garandeert de SLA en biedt alle compatibiliteit en beveiliging van Azure Standard.

![Beheerde Signa lering-service](./media/signalr-overview/managed-signalr-service.png)

**Bieden uitgebreide Api's voor verschillende bericht patronen:**

Met de seingevings service kan de server berichten verzenden naar een bepaalde verbinding, alle verbindingen of een subset van verbindingen die tot een specifieke gebruiker behoren of in een wille keurige groep zijn geplaatst.

## <a name="how-to-use-azure-signalr-service"></a>De service Azure SignalR gebruiken

Er zijn veel verschillende manieren om te Program meren met de Azure signalerings service, op enkele van de voor beelden die hier worden weer gegeven:

- **[Een ASP.NET Core SignalR-app schalen](signalr-concept-scale-aspnet-core.md)** : integreer de service Azure SignalR met een ASP.NET Core SignalR-toepassing om uit te schalen naar duizenden verbindingen.
- **[Serverloze realtimeapps bouwen](signalr-concept-azure-functions.md)** : gebruik de integratie van Azure Functions met de service Azure SignalR om serverloze realtimetoepassingen te bouwen in talen zoals JavaScript, C# en Java.
- **[Berichten van de server verzenden naar clients via REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** : de service Azure SignalR biedt REST API waarmee via toepassingen berichten kunnen worden verzonden naar clients die zijn verbonden met de service SignalR, in elke programmeertaal die compatibel is met REST.

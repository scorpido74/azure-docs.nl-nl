---
title: Gebeurtenisraster vergelijken, routering voor IoT Hub | Microsoft Documenten
description: IoT Hub biedt een eigen message routing-service, maar integreert ook met Event Grid voor het publiceren van evenementen. Vergelijk de twee functies.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 877467b65d346c871dd93f4b3f96b2c1664fa4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906787"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Berichtroutering en gebeurtenisraster voor IoT-hub vergelijken

Azure IoT Hub biedt de mogelijkheid om gegevens van uw verbonden apparaten te streamen en die gegevens te integreren in uw bedrijfstoepassingen. IoT Hub biedt twee methoden voor het integreren van IoT-gebeurtenissen in andere Azure-services of bedrijfstoepassingen. In dit artikel worden de twee functies besproken die deze mogelijkheid bieden, zodat u kiezen welke optie het beste is voor uw scenario.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub-berichtroutering:](iot-hub-devguide-messages-d2c.md)** met deze IoT Hub-functie kunnen gebruikers apparaat-naar-cloudberichten routeren naar eindpunten van services zoals Azure Storage-containers, Gebeurtenishubs, Wachtrijen servicebus en servicebus-onderwerpen. Routering biedt ook een querymogelijkheid om de gegevens te filteren voordat deze naar de eindpunten worden geleid. Naast apparaattelemetriegegevens u ook [niet-telemetriegebeurtenissen](iot-hub-devguide-messages-d2c.md#non-telemetry-events) verzenden die kunnen worden gebruikt om acties te activeren. 

**IoT Hub-integratie met Gebeurtenisraster:** Azure Event Grid is een volledig beheerde gebeurtenisrouteringsservice die gebruikmaakt van een model voor het abonneren op een abonnement op een publiceren. IoT Hub en Event Grid werken samen om IoT Hub-gebeurtenissen in bijna realtime te [integreren in Azure- en niet-Azure-services.](iot-hub-event-grid.md) IoT Hub publiceert zowel [apparaatgebeurtenissen](iot-hub-event-grid.md#event-types) als telemetriegebeurtenissen.

## <a name="differences"></a>Verschillen

Hoewel zowel berichtroutering als gebeurtenisraster waarschuwingsconfiguratie inschakelen, zijn er enkele belangrijke verschillen tussen de twee. Raadpleeg de volgende tabel voor meer informatie:

| Functie | Routering van IoT-hub-berichten | IoT Hub-integratie met Event Grid |
| ------- | --------------- | ---------- |
| **Apparaatberichten en -gebeurtenissen** | Ja, berichtroutering kan worden gebruikt voor telemetriegegevens, dubbele wijzigingen van het apparaat, gebeurtenissen in de levenscyclus van apparaten en gebeurtenissen voor digitale tweelingwijzigingen (onderdeel van de openbare preview van [IoT Plug and Play).](../iot-pnp/overview-iot-plug-and-play.md) | Ja, gebeurtenisraster kan worden gebruikt voor telemetriegegevens, maar kan ook rapporteren wanneer apparaten worden gemaakt, verwijderd, verbonden en losgekoppeld van IoT Hub |
| **Ordenen** | Ja, het bestellen van evenementen wordt gehandhaafd.  | Nee, volgorde van de gebeurtenissen is niet gegarandeerd. | 
| **Filteren** | Uitgebreide filtering op de eigenschappen van berichttoepassingen, eigenschappen van het berichtensysteem, de berichttekst, de dubbele tags van het apparaat en de dubbele eigenschappen van het apparaat. Filteren wordt niet toegepast op gebeurtenissen voor digitale tweelingwijzigingen. Zie voor voorbeelden [syntaxis van berichtrouteringsquery .](iot-hub-devguide-routing-query-syntax.md) | Filteren op basis van gebeurtenistype, onderwerptype en kenmerken in elke gebeurtenis. Zie [Filtergebeurtenissen in gebeurtenisrasterabonnementen begrijpen](../event-grid/event-filtering.md)voor voorbeelden . Wanneer u zich abonneert op telemetriegebeurtenissen, u extra filters op de gegevens toepassen om te filteren op berichteigenschappen, berichttekst en apparaattweeling in uw IoT-hub, voordat u publiceert naar Gebeurtenisraster. Bekijk [hoe u gebeurtenissen filtert](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Eindpunten** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus-wachtrij</li> <li>Service Bus-onderwerpen</li></ul><br>Betaalde IoT Hub SKU's (S1, S2 en S3) zijn beperkt tot 10 aangepaste eindpunten. Er kunnen 100 routes worden gemaakt per IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Aangepaste onderwerpen</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>Services van derden via WebHooks</li></ul><br>Er worden 500 eindpunten per IoT Hub ondersteund. Zie [Gebeurtenisrastergebeurtenishandlers](../event-grid/overview.md#event-handlers)voor de meest actuele lijst met eindpunten . |
| **Kosten** | Er zijn geen afzonderlijke kosten verbonden aan het routeren van berichten. Alleen het binnendringen van telemetrie in IoT Hub wordt opgeladen. Als u bijvoorbeeld een bericht naar drie verschillende eindpunten hebt doorgestuurd, wordt slechts één bericht in rekening gebracht. | Er worden geen kosten in rekening gebracht via IoT Hub. Event Grid biedt de eerste 100.000 bewerkingen per maand gratis, en daarna $ 0,60 per miljoen bewerkingen. |

## <a name="similarities"></a>Overeenkomsten

IoT Hub-berichtroutering en gebeurtenisraster hebben ook overeenkomsten, waarvan sommige in de volgende tabel worden beschreven:

| Functie | Routering van IoT-hub-berichten | IoT Hub-integratie met Event Grid |
| ------- | --------------- | ---------- |
| **Maximale berichtgrootte** | 256 KB, device-to-cloud | 256 KB, device-to-cloud |
| **Betrouwbaarheid** | Hoog: levert elk bericht ten minste één keer voor elke route naar het eindpunt. Verloopt alle berichten die niet binnen een uur worden bezorgd. | Hoog: Levert elk bericht ten minste één keer voor elk abonnement aan de webhook. Verloopt alle gebeurtenissen die niet binnen 24 uur worden geleverd. | 
| **Schaalbaarheid** | Hoog: geoptimaliseerd om miljoenen tegelijk verbonden apparaten te ondersteunen die miljarden berichten verzenden. | Hoog: geschikt voor het routeren van 10.000.000 gebeurtenissen per seconde per regio. |
| **Latentie** | Laag: Bijna real-time. | Laag: Bijna real-time. |
| **Verzenden naar meerdere eindpunten** | Ja, stuur één bericht naar meerdere eindpunten. | Ja, stuur één bericht naar meerdere eindpunten.  
| **Beveiliging** | Iot Hub biedt identiteit per apparaat en herroepbare toegangscontrole. Zie het [toegangsbesturingselement voor iot-hub](iot-hub-devguide-security.md)voor meer informatie . | Event Grid biedt validatie op drie punten: gebeurtenisabonnementen, evenementpublicatie en webhook-gebeurtenislevering. Zie [Beveiliging en verificatie van gebeurtenisraster](../event-grid/security-authentication.md)voor meer informatie. |

## <a name="how-to-choose"></a>Een keuze maken

IoT Hub-berichtroutering en de IoT Hub-integratie met Event Grid voeren verschillende acties uit om vergelijkbare resultaten te bereiken. Ze nemen beide informatie uit uw IoT Hub-oplossing en geven deze door, zodat andere services kunnen reageren. Dus hoe beslis je welke te gebruiken? Houd rekening met de volgende vragen om uw beslissing te begeleiden: 

* **Wat voor gegevens stuurt u naar de eindpunten?**

   Gebruik IoT Hub-berichtroutering wanneer u telemetriegegevens naar andere services moet verzenden. Berichtroutering maakt het ook mogelijk om berichttoepassing- en systeemeigenschappen, berichttekst, dubbele tags van het apparaat en dubbele eigenschappen van het apparaat op te vragen.

   De IoT Hub-integratie met Event Grid werkt met gebeurtenissen die plaatsvinden in de IoT Hub-service. Deze IoT Hub-gebeurtenissen omvatten telemetriegegevens, apparaat dat is gemaakt, verwijderd, verbonden en losgekoppeld. Wanneer u zich abonneert op telemetriegebeurtenissen, u extra filters op de gegevens toepassen om te filteren op berichteigenschappen, berichttekst en apparaattweeling in uw IoT-hub, voordat u publiceert naar Gebeurtenisraster. Bekijk [hoe u gebeurtenissen filtert](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Welke eindpunten hebben nodig om deze informatie te ontvangen?**

   IoT Hub-berichtroutering ondersteunt een beperkt aantal unieke eindpunten en eindpunttypen, maar u connectors maken om de gegevens en gebeurtenissen om te leiden naar extra eindpunten. Zie de tabel in de vorige sectie voor een volledige lijst met ondersteunde eindpunten. 

   De IoT Hub-integratie met Event Grid ondersteunt 500 eindpunten per IoT-hub en een grotere verscheidenheid aan eindpunttypen. Het integreert native met Azure Functions, Logic Apps, Storage and Service Bus queues, en werkt ook met webhooks om het verzenden van gegevens uit te breiden buiten het Azure-service-ecosysteem en naar zakelijke toepassingen van derden.

* **Maakt het uit of uw gegevens in orde komen?**

   IoT Hub-berichtroutering behoudt de volgorde waarin berichten worden verzonden, zodat ze op dezelfde manier aankomen.

   Event Grid garandeert niet dat eindpunten gebeurtenissen ontvangen in dezelfde volgorde als ze hebben plaatsgevonden. Voor die gevallen waarin de absolute volgorde van berichten aanzienlijk is en/of waarin een consument een betrouwbare unieke id voor berichten nodig heeft, raden we u aan berichtroutering te gebruiken. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [De routevan iot-hubberichten](iot-hub-devguide-messages-d2c.md) en de [eindpunten van de IoT-hub](iot-hub-devguide-endpoints.md).
* Meer informatie over [Azure Event Grid](../event-grid/overview.md).
* Zie de voortgang van [IoT Hub-berichten over het maken van Berichtenroutes voor](../iot-hub/tutorial-routing.md) meer informatie over het maken van berichtenroutes.
* Probeer de integratie van gebeurtenisrasters door [e-mailmeldingen over Azure IoT Hub-gebeurtenissen te verzenden met Behulp van Logische Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

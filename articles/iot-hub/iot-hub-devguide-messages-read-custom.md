---
title: Aangepaste eindpunten van Azure IoT Hub begrijpen | Microsoft Documenten
description: Handleiding voor ontwikkelaars - met behulp van routeringsquery's om berichten van apparaat naar cloud te routeren naar aangepaste eindpunten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244341"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Berichtroutes en aangepaste eindpunten gebruiken voor device-to-cloudberichten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met IoT Hub [Message Routing](iot-hub-devguide-routing-query-syntax.md) kunnen gebruikers device-to-cloud berichten doorsturen naar servicegerichte eindpunten. Routering biedt ook een querymogelijkheid om de gegevens te filteren voordat deze naar de eindpunten worden geleid. Elke routeringsquery die u configureert, heeft de volgende eigenschappen:

| Eigenschap      | Beschrijving |
| ------------- | ----------- |
| **Naam**      | De unieke naam die de query identificeert. |
| **Bron**    | De oorsprong van de te handelen gegevensstroom. Bijvoorbeeld apparaattelemetrie. |
| **Voorwaarde** | De queryexpressie voor de routeringsquery die wordt uitgevoerd tegen de eigenschappen van de berichttoepassing, systeemeigenschappen, berichttekst, dubbele tags van het apparaat en twee eigenschappen van het apparaat om te bepalen of het een overeenkomst voor het eindpunt is. Zie de [syntaxis](iot-hub-devguide-routing-query-syntax.md) van de query see Message Routing |
| **Eindpunt**  | De naam van het eindpunt waar IoT Hub berichten verzendt die overeenkomen met de query. We raden u aan een eindpunt te kiezen in dezelfde regio als uw IoT-hub. |

Eén bericht kan overeenkomen met de voorwaarde voor meerdere routeringsquery's, in welk geval IoT Hub het bericht levert aan het eindpunt dat aan elke overeenkomende query is gekoppeld. IoT Hub dedupeert ook automatisch de bezorging van berichten, dus als een bericht overeenkomt met meerdere query's die dezelfde bestemming hebben, wordt het slechts één keer naar die bestemming geschreven.

## <a name="endpoints-and-routing"></a>Eindpunten en routering

Een IoT-hub heeft een standaard [ingebouwd eindpunt.](iot-hub-devguide-messages-read-builtin.md) U aangepaste eindpunten maken om berichten naar te leiden door andere services in uw abonnement aan de hub te koppelen. IoT Hub ondersteunt momenteel Azure Storage-containers, Gebeurtenishubs, ServiceBus-wachtrijen en servicebus-onderwerpen als aangepaste eindpunten.

Wanneer u routerings- en aangepaste eindpunten gebruikt, worden berichten alleen geleverd aan het ingebouwde eindpunt als ze niet overeenkomen met een query. Als u berichten wilt leveren aan het ingebouwde eindpunt en aan een aangepast eindpunt, voegt u een route toe die berichten naar het eindpunt van de ingebouwde **gebeurtenissen** verzendt.

> [!NOTE]
> * IoT Hub ondersteunt alleen het schrijven van gegevens naar Azure Storage-containers als blobs.
> * Wachtrijen voor servicebussen en onderwerpen met **Sessies** of **Dubbele detectie** ingeschakeld, worden niet ondersteund als aangepaste eindpunten.

Zie [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)voor meer informatie over het maken van aangepaste eindpunten in IoT Hub.

Zie voor meer informatie over het lezen van aangepaste eindpunten:

* Lezen uit [Azure Storage-containers](../storage/blobs/storage-blobs-introduction.md).

* Lezen van [Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Lezen van [Service Bus wachtrijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Lezen van [Service Bus onderwerpen](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)voor meer informatie over eindpunten van IoT Hub.

* Zie Syntaxis van de opdracht [voor berichtroutering](iot-hub-devguide-routing-query-syntax.md)voor meer informatie over de querytaal die u gebruikt om routeringsquery's te definiëren.

* In [de zelfstudie van Process IoT Hub device-to-cloud-berichten met routes](tutorial-routing.md) ziet u hoe u routeringsquery's en aangepaste eindpunten gebruiken.
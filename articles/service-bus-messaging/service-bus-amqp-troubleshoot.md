---
title: AMQP-fouten in Azure Service Bus oplossen | Microsoft Documenten
description: Biedt een lijst met AMQP-fouten die u ontvangen bij het gebruik van Azure Service Bus en de oorzaak van die fouten.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402782"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP-fouten in Azure Service Bus
In dit artikel worden enkele fouten vermeld die u ontvangt bij het gebruik van AMQP met Azure Service Bus. Ze zijn allemaal standaard gedrag van de dienst. U ze vermijden door te bellen/ontvangen via de verbinding/koppeling, waardoor de verbinding/koppeling automatisch wordt gemaakt.

## <a name="link-is-closed"></a>Koppeling is gesloten 
U ziet de volgende fout wanneer de AMQP-verbinding en koppeling actief zijn, maar er gedurende 10 minuten geen oproepen (bijvoorbeeld verzenden of ontvangen) worden uitgevoerd via de koppeling. Dus, de link is gesloten. De verbinding is nog open.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Verbinding is gesloten
U ziet de volgende fout op de AMQP-verbinding wanneer alle koppelingen in de verbinding zijn gesloten omdat er geen activiteit was (niet actief) en er in 5 minuten geen nieuwe koppeling is gemaakt.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Koppeling wordt niet gemaakt 
U ziet deze fout wanneer een nieuwe AMQP-verbinding wordt gemaakt, maar een koppeling wordt niet gemaakt binnen 1 minuut na het maken van de AMQP-verbinding.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over AMQP en Service Bus naar de volgende links:

* [Overzicht servicebus AMQP]
* [AMQP 1.0-protocolhandleiding]
* [AMQP in servicebus voor Windows Server]

[Overzicht servicebus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protocolhandleiding]: service-bus-amqp-protocol-guide.md
[AMQP in servicebus voor Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)

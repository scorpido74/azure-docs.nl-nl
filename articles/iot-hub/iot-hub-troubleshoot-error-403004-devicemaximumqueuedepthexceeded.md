---
title: Problemen met Azure IoT Hub-fout 403004 DeviceMaximumQueueDepthExceeded
description: Meer informatie over het oplossen van fout 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960904"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

In dit artikel worden de oorzaken en oplossingen voor **403004 DeviceMaximumQueueDepthExceeded** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u een Cloud-naar-apparaat-bericht probeert te verzenden, mislukt de aanvraag met de fout **403004** of **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Oorzaak

De onderliggende oorzaak is dat het aantal berichten in de wachtrij voor het apparaat groter is dan de limiet voor de [wacht rijen (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

De meest waarschijnlijke reden hiervoor is dat u HTTPS gebruikt voor het ontvangen van het bericht, dat leidt tot continue polling met behulp van `ReceiveAsync`, wat resulteert in IoT Hub beperking van de aanvraag.

## <a name="solution"></a>Oplossing

Het ondersteunde patroon voor Cloud-naar-apparaat-berichten met HTTPS is op een regel matig verbonden apparaten die op berichten te controleren zijn (minder dan elke 25 minuten). Als u de kans op het uitvoeren van de wachtrij limiet wilt beperken, schakelt u over naar AMQP of MQTT voor Cloud-naar-apparaat-berichten.

U kunt de logica van een apparaat ook verbeteren om snel een bericht in de wachtrij te plaatsen, te weigeren of te annuleren, de tijd te verkorten of te overwegen om minder berichten te verzenden. Zie [C2D message time to Live](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live) (TTL van C2D-bericht) voor meer informatie.

U kunt eventueel de API voor het leegmaken van de [wachtrij](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) gebruiken om regel matig wachtende berichten op te schonen voordat de limiet is bereikt.
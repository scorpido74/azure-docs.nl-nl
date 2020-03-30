---
title: Azure IoT Hub-fout oplossen 403004 DeviceMaximumQueueDepthExceeded
description: Informatie over het oplossen van fout 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497494"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

In dit artikel worden de oorzaken en oplossingen voor **403004 DeviceMaximumQueueDepthExceeded-fouten beschreven.**

## <a name="symptoms"></a>Symptomen

Wanneer u een cloud-naar-apparaatbericht probeert te verzenden, mislukt de aanvraag met de fout **403004** of **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Oorzaak

De onderliggende oorzaak is dat het aantal berichten dat voor het apparaat in de wachtrij is geplaatst, de wachtrijlimiet overschrijdt [(50).](./iot-hub-devguide-quotas-throttling.md#other-limits)

De meest waarschijnlijke reden dat u tegen deze limiet aanloopt, is omdat u HTTPS `ReceiveAsync`gebruikt om het bericht te ontvangen, wat leidt tot continue polling met behulp van , wat resulteert in het beperken van de aanvraag door IoT Hub.

## <a name="solution"></a>Oplossing

Het ondersteunde patroon voor cloud-to-device-berichten met HTTPS is met tussenpozen verbonden apparaten die zelden controleren op berichten (minder dan elke 25 minuten). Als u de kans op het tegenkomen van de wachtrijlimiet wilt verkleinen, schakelt u over naar AMQP of MQTT voor berichten van cloud naar apparaat.

U ook de logica aan de apparaatzijde verbeteren om berichten in de wachtrij snel te voltooien, af te wijzen of te verlaten, de live-tijd in te korten of te overwegen minder berichten te verzenden. Zie [C2D message time to Live](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live) (TTL van C2D-bericht) voor meer informatie.

Tot slot u overwegen de [API voor zuiveringswachtrij](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) te gebruiken om in behandeling zijnde berichten periodiek op te schonen voordat de limiet is bereikt.
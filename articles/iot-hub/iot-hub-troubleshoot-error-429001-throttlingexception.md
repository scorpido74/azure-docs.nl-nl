---
title: Problemen met Azure IoT Hub-fout 429001 ThrottlingException
description: Meer informatie over het oplossen van fout 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960696"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

In dit artikel worden de oorzaken en oplossingen voor **429001 ThrottlingException** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Uw aanvragen voor IoT Hub mislukken met de fout **429001 ThrottlingException**.

## <a name="cause"></a>Oorzaak

De [limieten](./iot-hub-devguide-quotas-throttling.md) voor IOT hub beperking zijn voor de aangevraagde bewerking overschreden.

## <a name="solution"></a>Oplossing

Controleer of u de beperkings limiet hebt bereikt door uw *telemetrie-bericht verzendings* waarden te vergelijken met de hierboven opgegeven limieten. U kunt ook het aantal metrieke *beperkings fouten* controleren. Zie voor meer informatie over deze en andere metrische gegevens die beschikbaar zijn voor IoT Hub [IOT hub metrische gegevens en hoe u deze kunt gebruiken](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub retourneert 429 ThrottlingException nadat de limiet te lang is geschonden. Dit wordt gedaan zodat uw berichten niet worden verwijderd als uw IoT-hub burst-verkeer ontvangt. In de tussentijd worden de berichten in IoT Hub verwerkt volgens de vertragingsfactor voor bewerkingen. Als er te veel achterstallig verkeer is, kan dit traag zijn. Zie [IoT traffic shaping](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U kunt [uw IOT hub opschalen](./iot-hub-scaling.md) als u quotum-of beperkings limieten wilt uitvoeren.
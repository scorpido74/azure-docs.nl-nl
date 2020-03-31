---
title: Azure IoT Hub-fout oplossen 429001 ThrottlingException
description: Begrijpen hoe u fout 429001 ThrottlingException oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960696"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

In dit artikel worden de oorzaken en oplossingen beschreven voor **429001 ThrottlingException-fouten.**

## <a name="symptoms"></a>Symptomen

Uw aanvragen voor IoT Hub mislukken met de fout **429001 ThrottlingException**.

## <a name="cause"></a>Oorzaak

IoT [Hub-beperkingslimieten](./iot-hub-devguide-quotas-throttling.md) zijn overschreden voor de gevraagde bewerking.

## <a name="solution"></a>Oplossing

Controleer of u de beperkingslimiet bereikt door uw *telemetriebericht te* vergelijken met de hierboven opgegeven limieten. U ook de statistiek *Aantal beperkingsfouten* controleren. Zie [IoT Hub-statistieken en hoe u deze](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)gebruiken voor meer informatie over deze en andere statistieken die beschikbaar zijn voor IoT Hub.

IoT Hub retourneert 429 ThrottlingException pas nadat de limiet te lang is geschonden. Dit wordt zo gedaan dat uw berichten niet worden verwijderd als uw IoT-hub burst-verkeer krijgt. In de tussentijd worden de berichten in IoT Hub verwerkt volgens de vertragingsfactor voor bewerkingen. Als er te veel achterstallig verkeer is, kan dit traag zijn. Zie [IoT traffic shaping](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Overweeg [uw IoT-hub op te schalen](./iot-hub-scaling.md) als u quota- of beperkingslimieten tegenkomt.
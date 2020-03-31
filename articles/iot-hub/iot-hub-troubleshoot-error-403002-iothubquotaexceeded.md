---
title: Azure IoT Hub-fout oplossen 403002 IoTHubQuotaoverschreden
description: Begrijpen hoe u fout 403002 IoTHubQuotaOverschreed oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961112"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

In dit artikel worden de oorzaken en oplossingen voor **403002 IoTHubQuotaOverschreed-fouten beschreven.**

## <a name="symptoms"></a>Symptomen

Alle aanvragen voor IoT Hub mislukken met de fout **403002 IoTHubQuotaExceeded**. In Azure-portal wordt de lijst met IoT-hub-apparaten niet geladen.

## <a name="cause"></a>Oorzaak

Het dagelijkse berichtquotum voor de IoT-hub wordt overschreden. 

## <a name="solution"></a>Oplossing

[Upgrade of verhoog het aantal eenheden op de IoT-hub](iot-hub-upgrade.md) of wacht tot de volgende UTC-dag tot het dagelijkse quotum wordt vernieuwd.

## <a name="next-steps"></a>Volgende stappen

* Zie [IoT Hub-prijzen begrijpen](iot-hub-devguide-pricing.md#charges-per-operation) als u wilt begrijpen hoe bewerkingen worden meegeteld voor het quotum, zoals dubbele query's en directe methoden.
* Als u bewaking wilt instellen voor dagelijks quotumgebruik, stelt u een waarschuwing in met het metrische *totaal aantal gebruikte berichten*. Zie [Statistieken en waarschuwingen instellen met IoT](tutorial-use-metrics-and-diags.md#set-up-metrics) Hub voor stapsgewijze instructies
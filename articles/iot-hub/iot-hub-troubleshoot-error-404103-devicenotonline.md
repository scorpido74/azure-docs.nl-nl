---
title: Problemen met Azure IoT Hub-fout 404103 DeviceNotOnline
description: Meer informatie over het oplossen van fout 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960813"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

In dit artikel worden de oorzaken en oplossingen voor **404103 DeviceNotOnline** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Een directe methode op een apparaat mislukt met de fout **404103 DeviceNotOnline** zelfs als het apparaat online is. 

## <a name="cause"></a>Oorzaak

Als u weet dat het apparaat online is en de fout nog steeds krijgt, is het waarschijnlijk dat de call back van de methode direct wordt niet geregistreerd op het apparaat.

## <a name="solution"></a>Oplossing

Zie [een directe methode op een apparaat afhandelen](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)om uw apparaat op de juiste wijze te configureren voor direct-methode-aanroepen.
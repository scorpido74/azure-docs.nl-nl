---
title: Azure IoT Hub-fout oplossen 404103 DeviceNotOnline
description: Begrijpen hoe u fout 404103 DeviceNotOnline oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960813"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

In dit artikel worden de oorzaken en oplossingen voor **404103 DeviceNotOnline-fouten** beschreven.

## <a name="symptoms"></a>Symptomen

Een directe methode om een apparaat mislukt met de fout **404103 DeviceNotOnline,** zelfs als het apparaat online is. 

## <a name="cause"></a>Oorzaak

Als u weet dat het apparaat online is en nog steeds de fout krijgt, is dit waarschijnlijk omdat de directe methode callback niet is geregistreerd op het apparaat.

## <a name="solution"></a>Oplossing

Zie [Een directe methode op een apparaat afhandelen](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)als u uw apparaat goed wilt configureren voor oproepen met directe methoden.
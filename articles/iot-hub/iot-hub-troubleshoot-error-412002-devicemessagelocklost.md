---
title: Problemen met Azure IoT Hub-fout 412002 DeviceMessageLockLost oplossen
description: Begrijpen hoe u fout 412002 DeviceMessageLockLost oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960761"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

In dit artikel worden de oorzaken en oplossingen voor **412002 DeviceMessageLockLost-fouten** beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u een cloud-naar-apparaatbericht probeert te verzenden, mislukt het verzoek met de fout **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Oorzaak

Wanneer een apparaat een cloud-to-device-bericht ontvangt vanuit [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)de wachtrij (bijvoorbeeld met behulp van) wordt het bericht vergrendeld door IoT Hub voor een time-outduur van één minuut. Als het apparaat het bericht probeert te voltooien nadat de vergrendelingstime-out is verlopen, gooit IoT Hub deze uitzondering.

## <a name="solution"></a>Oplossing

Als IoT Hub de melding niet binnen de tijdsduur van een vergrendeling van één minuut ontvangt, wordt het bericht teruggezet naar de status *Enqueued.* Het apparaat kan proberen het bericht opnieuw te ontvangen. Als u wilt voorkomen dat de fout zich in de toekomst voordoet, implementeert u de logica aan de apparaatzijde om het bericht binnen een minuut na ontvangst van het bericht te voltooien. Deze time-out van één minuut kan niet worden gewijzigd.
---
title: Problemen met Azure IoT Hub-fout 504101 GatewayTimeout oplossen
description: Begrijpen hoe u fout 504101 GatewayTimeout oplossen
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960670"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

In dit artikel worden de oorzaken en oplossingen voor **504101 GatewayTimeout-fouten** beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u een directe methode van IoT Hub naar een apparaat probeert aan te roepen, mislukt de aanvraag met de fout **504101 GatewayTimeout**.

## <a name="cause"></a>Oorzaak

### <a name="cause-1"></a>Oorzaak 1

IoT Hub heeft een fout opgetreden en kon niet bevestigen of de directe methode is voltooid voordat de timing uitging.

### <a name="cause-2"></a>Oorzaak 2

Bij het gebruik van een eerdere versie van de Azure IoT C# SDK (<1.19.0) kan de AMQP-koppeling tussen het apparaat en IoT Hub in stilte worden verwijderd vanwege een bug.

## <a name="solution"></a>Oplossing

### <a name="solution-1"></a>Oplossing 1

Geef een nieuwe poging.

### <a name="solution-2"></a>Oplossing 2

Upgrade naar de nieuwste versie van de Azure IOT C# SDK.
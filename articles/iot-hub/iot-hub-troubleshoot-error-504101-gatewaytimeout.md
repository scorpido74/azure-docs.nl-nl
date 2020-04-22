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
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759550"
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
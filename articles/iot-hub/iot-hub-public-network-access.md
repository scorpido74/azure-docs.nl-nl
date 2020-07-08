---
title: Open bare netwerk toegang voor Azure IoT hub beheren
description: Documentatie over het uitschakelen en inschakelen van open bare netwerk toegang voor IoT hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937522"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Open bare netwerk toegang beheren voor uw IoT-hub

Als u de toegang tot alleen [het persoonlijke eind punt voor uw IOT-hub in uw VNet](virtual-network-support.md)wilt beperken, schakelt u open bare netwerk toegang uit. Gebruik hiervoor Azure Portal of de `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Open bare netwerk toegang via Azure Portal uitschakelen

1. Ga naar [Azure Portal](https://portal.azure.com)
2. Ga naar uw IoT-hub.
3. Selecteer **netwerken** in het menu aan de linkerkant.
4. Selecteer onder ' open bare netwerk toegang toestaan ' de optie **uitgeschakeld**
5. Selecteer **Opslaan**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Afbeelding met Azure Portal waar open bare netwerk toegang wordt uitgeschakeld" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Als u open bare netwerk toegang wilt inschakelen, selecteert u **ingeschakeld**en vervolgens **Opslaan**.

## <a name="ip-filter"></a>IP-filter 

Als open bare netwerk toegang is uitgeschakeld, worden alle [IP-filter](iot-hub-ip-filtering.md) regels genegeerd. Dit komt doordat alle IP-adressen van het open bare Internet worden geblokkeerd. Als u IP-filter wilt gebruiken, gebruikt u de optie **geselecteerde IP-bereiken** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Fout oplossing met ingebouwd eind punt dat compatibel is met Event hub

Er is een bug met IoT Hub waarbij het [ingebouwde Event hub-compatibele eind punt](iot-hub-devguide-messages-read-builtin.md) toegankelijk blijft via het open bare Internet wanneer open bare netwerk toegang tot de IOT hub is uitgeschakeld. Zie toegang [tot het ingebouwde Event hub-eind punt uitschakelen IOT hub voor](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)meer informatie en neem contact met ons op over deze fout.
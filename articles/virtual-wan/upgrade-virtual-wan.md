---
title: Een Azure Virtual WAN upgraden van Basic naar Standard - Azure portal | Microsoft Documenten
description: U uw virtuele WAN-type upgraden voor meer functionaliteit.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515809"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Een virtuele WAN upgraden van Basic naar Standard

Met dit artikel u een Basic WAN upgraden naar een standaard WAN. Een 'Basic' WAN-type maakt alle hubs erin als Basic SKU-hubs. In een Basic-hub bent u beperkt tot alleen site-to-site VPN-functionaliteit. Een 'Standard' WAN-type maakt alle hubs erin als Standaard SKU-hubs. Wanneer u Standaardhubs gebruikt, u ExpressRoute, User (Point-to-site) VPN, een volledige mesh-hub en VNet-naar-VNet-doorvoer via de Azure-hubs inschakelen.

In de volgende tabel worden de configuraties weergegeven die beschikbaar zijn voor elk WAN-type:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Het virtuele WAN-type wijzigen

1. Selecteer Op de pagina voor uw virtuele WAN de optie **Configuratie** om de pagina Configuratie te openen.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/1.png)
2. Selecteer **Standaard** in de vervolgkeuzelijst voor het type Virtueel WAN.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/2.png)
3. Begrijp dat als u een upgrade uitvoert naar een standaard virtueel WAN, u niet terugkeren naar een virtuele basisWAN. Selecteer **Bevestigen** als u wilt upgraden.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/4.png)
4. Zodra de wijziging is opgeslagen, lijkt uw virtuele WAN-pagina op dit voorbeeld.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
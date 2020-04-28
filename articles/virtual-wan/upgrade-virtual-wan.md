---
title: Een virtuele WAN van Azure upgraden van Basic naar Standard-Azure Portal | Microsoft Docs
description: U kunt uw virtuele WAN-type upgraden voor meer functionaliteit.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515809"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Een virtueel WAN upgraden van Basic naar Standard

Dit artikel helpt u bij het upgraden van een basis WAN naar een standaard WAN. Een ' standaard ' WAN-type maakt alle hubs erin als basis-SKU-hubs. In een Basic-hub bent u beperkt tot alleen de site-naar-site VPN-functionaliteit. Met een ' standaard ' WAN-type worden alle hubs erin gemaakt als standaard-SKU-hubs. Wanneer u standaard hubs gebruikt, kunt u ExpressRoute, gebruiker (punt-naar-site) VPN inschakelen, een volledige mesh-hub en VNet-naar-VNet-transit via de Azure-hubs.

De volgende tabel bevat de configuraties die beschikbaar zijn voor elk WAN-type:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Het virtuele WAN-type wijzigen

1. Selecteer op de pagina voor uw virtuele WAN **configuratie** om de configuratie pagina te openen.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/1.png)
2. Selecteer voor het virtuele WAN-type de optie **standaard** in de vervolg keuzelijst.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/2.png)
3. Als u een upgrade uitvoert naar een standaard virtueel WAN, kunt u niet terugkeren naar een virtueel WAN. Selecteer **bevestigen** als u een upgrade wilt uitvoeren.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/4.png)
4. Zodra de wijziging is opgeslagen, ziet uw virtuele WAN-pagina er ongeveer uit in dit voor beeld.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN de pagina [Overzicht van Virtual WAN](virtual-wan-about.md).
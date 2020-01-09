---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436998"
---
|  | **Persoonlijke Peering** | **Microsoft-Peering** |  **Openbare Peering** (afgeschaft voor nieuwe circuits) |
| --- | --- | --- | --- |
| **Max. # voorvoegsels per peering ondersteund** |4000 standaard 10.000 met ExpressRoute Premium |200 |200 |
| **IP-adresbereiken ondersteund** |Een geldig IP-adres binnen uw WAN. |Openbare IP-adressen die eigendom zijn door u of uw connectiviteitsprovider. |Openbare IP-adressen die eigendom zijn door u of uw connectiviteitsprovider. |
| **Aantal vereisten** |Persoonlijke en openbare AS-nummers. U moet de eigenaar bent van de openbare AS-nummer als u ervoor kiest een te gebruiken. |Persoonlijke en openbare AS-nummers. U moet echter bewijzen eigendom van openbare IP-adressen. |Persoonlijke en openbare AS-nummers. U moet echter bewijzen eigendom van openbare IP-adressen. |
| **IP-protocollen die worden ondersteund**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Routering Interface IP-adressen** |RFC1918 en openbare IP-adressen |Openbare IP-adressen in routeringsregisters voor u geregistreerd. |Openbare IP-adressen in routeringsregisters voor u geregistreerd. |
| **MD5-Hash-ondersteuning** |Ja |Ja |Ja |
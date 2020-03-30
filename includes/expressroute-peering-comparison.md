---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75436998"
---
|  | **Persoonlijke peering** | **Microsoft-peering** |  **Openbare Peering** (afgeschaft voor nieuwe circuits) |
| --- | --- | --- | --- |
| **Max. # voorvoegsels ondersteund per peering** |Standaard 4000, 10.000 met ExpressRoute Premium |200 |200 |
| **Ondersteunde IP-adresbereiken** |Elk geldig IP-adres binnen uw WAN. |Openbare IP-adressen die eigendom zijn van u of uw connectiviteitsprovider. |Openbare IP-adressen die eigendom zijn van u of uw connectiviteitsprovider. |
| **AS-nummervereisten** |Private en publieke AS-nummers. U moet eigenaar zijn van het openbare AS-nummer als u ervoor kiest om er een te gebruiken. |Private en publieke AS-nummers. U moet echter bewijzen dat u eigenaar is van openbare IP-adressen. |Private en publieke AS-nummers. U moet echter bewijzen dat u eigenaar is van openbare IP-adressen. |
| **ONDERSTEUNDE IP-protocollen**| IPv4 |  IPv4, IPv6 | IPv4 |
| **IP-adressen routeringinterface** |RFC1918 en openbare IP-adressen |Openbare IP-adressen die bij u zijn geregistreerd in routeringsregisters. |Openbare IP-adressen die bij u zijn geregistreerd in routeringsregisters. |
| **Ondersteuning voor MD5 Hash** |Ja |Ja |Ja |
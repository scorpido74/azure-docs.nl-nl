---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75436998"
---
|  | **Persoonlijke peering** | **Microsoft-peering** |  **Openbare peering** (afgeschaft voor nieuwe circuits) |
| --- | --- | --- | --- |
| **Maximumaantal voorvoegsels ondersteund per peering** |4000 standaard, 10.000 met ExpressRoute Premium |200 |200 |
| **Ondersteunde IP-adresbereiken** |Elk geldig IP-adres in uw WAN. |Openbare IP-adressen waarvan u of uw connectiviteitsprovider eigenaar is. |Openbare IP-adressen waarvan u of uw connectiviteitsprovider eigenaar is. |
| **Vereisten voor AS-nummers** |Persoonlijke en openbare AS-nummers. Als u ervoor kiest een openbaar AS-nummer te gebruiken, moet u er eigenaar van zijn. |Persoonlijke en openbare AS-nummers. U moet echter bewijzen dat u eigenaar bent van openbare IP-adressen. |Persoonlijke en openbare AS-nummers. U moet echter bewijzen dat u eigenaar bent van openbare IP-adressen. |
| **Ondersteunde IP-protocollen**| IPv4 |  IPv4, IPv6 | IPv4 |
| **IP-adressen met routeringsinterface** |RFC1918- en openbare IP-adressen |Openbare IP-adressen die bij u zijn geregistreerd in routeringsregisters. |Openbare IP-adressen die bij u zijn geregistreerd in routeringsregisters. |
| **Ondersteuning voor MD5-hashes** |Ja |Ja |Ja |
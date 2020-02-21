---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: b674f8e31eb61328f60bb24866f73d02653b655f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77494939"
---
**Open bare DNS-zones**

| Resource | Standaardlimiet |
| --- | --- |
| Open bare DNS-zones per abonnement |250 <sup>1</sup> |
| Record sets per open bare DNS-zone |10.000 <sup>1</sup> |
| Records per recordset die in open bare DNS-zone is ingesteld |20 |
| Aantal alias records voor één Azure-resource |20|
| Privé-DNS zones per abonnement |1000|
| Record sets per privé-DNS-zone |25000|
| Records per recordset voor privé-DNS-zones |20|
| Virtual Network koppelingen per privé-DNS-zone |1000|
| Virtuele netwerken koppelingen per privé-DNS-zone met automatische registratie ingeschakeld |100|
| Aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld met automatische registratie ingeschakeld |1|
| Aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld |1000|
| Aantal DNS-query's dat een virtuele machine kan verzenden naar Azure DNS resolver per seconde |500 <sup>2</sup> |
| Maximum aantal DNS-query's in wachtrij (reactie in behandeling) per virtuele machine |200 <sup>2</sup> |

<sup>1</sup> Als u deze limieten wilt verhogen, neemt u contact op met de ondersteuning van Azure.

<sup>2</sup> Deze limieten worden toegepast op elke afzonderlijke virtuele machine en niet op het niveau van het virtuele netwerk. DNS-query's die deze limieten overschrijden, worden verwijderd.
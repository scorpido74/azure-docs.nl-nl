---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 3fbbb9d491eef202309c9e64f9a5a839a5d4516d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334788"
---
**Openbare DNS-zones**

| Resource | Limiet |
| --- | --- |
| Openbare DNS-zones per abonnement |250 <sup>1</sup> |
| Recordsets per openbare DNS-zone |10.000 <sup>1</sup> |
| Records per record in openbare DNS-zone |20 |
| Aantal aliasrecords voor één Azure-bron |20|
| Private DNS-zones per abonnement |1000|
| Recordsets per privé-DNS-zone |25.000|
| Records per record voor privé-DNS-zones |20|
| Virtuele netwerkkoppelingen per privé-DNS-zone |1000|
| Koppelingen naar virtuele netwerken per privé-DNS-zones met automatische registratie ingeschakeld |100|
| Aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld met automatische registratie ingeschakeld |1|
| Aantal privé-DNS-zones die een virtueel netwerk kan koppelen |1000|
| Aantal DNS-query's dat een virtuele machine per seconde naar Azure DNS-resolver kan verzenden |500 <sup>2</sup> |
| Maximaal aantal DNS-query's in de wachtrij (in afwachting van antwoord) per virtuele machine |200 <sup>2</sup> |

<sup>1.</sup> Als u deze limieten wilt verhogen, neemt u contact op met Azure Support.

<sup>2.</sup> Deze limieten worden toegepast op elke afzonderlijke virtuele machine en niet op virtueel netwerkniveau. DNS-query's die deze limieten overschrijden, worden verwijderd.
---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334626"
---
| Resource | Limiet | Opmerking |
| --- | --- | --- |
| Azure Application Gateway |1.000 per abonnement | |
| Front-end IP-configuraties |2 |1 openbaar en 1 privé |
| Front-end poorten |100<sup>1</sup> | |
| Back-endadresgroepen |100<sup>1</sup> | |
| Back-endservers per groep |1200 | |
| HTTP-listeners |200<sup>1</sup> |Beperkt tot 100 actieve luisteraars die verkeer routeren. Actieve luisteraars = totaal aantal luisteraars - luisteraars niet actief.<br>Als een standaardconfiguratie binnen een routeringsregel is ingesteld op routeverkeer (bijvoorbeeld een listener, een backendpool en HTTP-instellingen), telt dat ook als listener.|
| HTTP-regels voor het balanceren van de taakverdeling |100<sup>1</sup> | |
| Back-end HTTP-instellingen |100<sup>1</sup> | |
| Exemplaren per gateway |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL-certificaten |100<sup>1</sup> |1 per HTTP-listener |
| Maximale grootte ssl-certificaat |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certificaten voor verificatie |100 | |
| Vertrouwde basiscertificaten |100 | |
| Minimaal een time-out aanvragen |1 seconde | |
| Maximum time-out aanvragen |24 uur | |
| Aantal sites |100<sup>1</sup> |1 per HTTP-listener |
| URL-kaarten per listener |1 | |
| Maximale regels op basis van paden per URL-kaart|100||
| Configuraties omleiden |100<sup>1</sup>| |
| Gelijktijdige WebSocket-verbindingen |Middelgrote gateways 20k<br> Grote gateways 50k| |
| Maximale URL-lengte|32KB| |
| Maximale kopverhouding voor HTTP/2 |4KB| |
| Maximale bestandsuploadgrootte, Standaard |2 GB | |
| Maximale bestandsuploadgrootte WAF |V1 Medium WAF-gateways, 100 MB<br>V1 Grote WAF-gateways, 500 MB<br>V2 WAF, 750 MB| |
| WAF-lichaamsgroottelimiet, zonder bestanden|128 kB||
| Maximale WAF aangepaste regels|100||
| Maximale WAF-uitsluitingen|100||

<sup>1</sup> In het geval van WAF-enabled SKU's raden we u aan het aantal resources te beperken tot 40 voor optimale prestaties.

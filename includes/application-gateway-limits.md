---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334626"
---
| Resource | Limiet | Opmerking |
| --- | --- | --- |
| Azure Application Gateway |1.000 per abonnement | |
| Front-end-IP-configuraties |2 |1 openbaar en 1 privé |
| Front-end-poorten |100<sup>1</sup> | |
| Back-end-adres groepen |100<sup>1</sup> | |
| Back-endservers per pool |1200 | |
| HTTP-listeners |200<sup>1</sup> |Beperkt tot 100 actieve listeners die routerings verkeer zijn. Actieve listeners = totaal aantal listeners-Listeners niet actief.<br>Als er een standaard configuratie binnen een routerings regel is ingesteld om verkeer te routeren (bijvoorbeeld een listener, een back-end-pool en HTTP-instellingen), wordt die ook meegeteld als listener.|
| Regels voor HTTP-taak verdeling |100<sup>1</sup> | |
| Back-end-HTTP-instellingen |100<sup>1</sup> | |
| Exemplaren per gateway |V1 SKU-32<br>V2-SKU-125 | |
| SSL-certificaten |100<sup>1</sup> |1 per HTTP-listener |
| Maximale grootte van SSL-certificaat |V1-SKU-10 KB<br>V2 SKU-16 KB| |
| Certificaten voor verificatie |100 | |
| Vertrouwde basiscertificaten |100 | |
| Minimale aanvraag time-out |1 seconde | |
| Maximum time-out van aanvraag |24 uur | |
| Aantal sites |100<sup>1</sup> |1 per HTTP-listener |
| URL-kaarten per listener |1 | |
| Maximum aantal op pad gebaseerde regels per URL-toewijzing|100||
| Configuraties omleiden |100<sup>1</sup>| |
| Gelijktijdige WebSocket-verbindingen |Gemiddelde gateways 20.000<br> Grote gateways 50.000| |
| Maximale URL-lengte|32 KB| |
| Maximale header grootte voor HTTP/2 |4KB| |
| Maximale grootte voor het uploaden van bestanden, standaard |2 GB | |
| Maximale grootte voor het uploaden van bestanden WAF |V1-middel grote WAF-gateways, 100 MB<br>V1 grote WAF-gateways, 500 MB<br>V2 WAF, 750 MB| |
| Maximale grootte van WAF, zonder bestanden|128 kB||
| Aangepaste regels voor WAF maximum|100||
| Maxi maal aantal WAF uitsluitingen|100||

<sup>1</sup> in het geval van WAF sku's wordt u aangeraden het aantal resources te beperken tot 40 voor optimale prestaties.

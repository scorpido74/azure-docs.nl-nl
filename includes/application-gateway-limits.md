---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 6ab6c4c2051ccd2fbb22c383b9ca0af53ceb13d3
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054892"
---
| Resource | Standaard/maximum limiet | Opmerking |
| --- | --- | --- |
| Azure Application Gateway |1\.000 per abonnement | |
| Front-end-IP-configuraties |2 |1 openbaar en 1 privé |
| Front-end-poorten |100<sup>1</sup> | |
| Back-end-adres groepen |100<sup>1</sup> | |
| Back-endservers per pool |1,200 | |
| HTTP-listeners |100<sup>1</sup> | |
| Regels voor HTTP-taak verdeling |100<sup>1</sup> | |
| Back-end-HTTP-instellingen |100<sup>1</sup> | |
| Exemplaren per gateway |V1 SKU-32<br>V2-SKU-125 | |
| SSL-certificaten |100<sup>1</sup> |1 per HTTP-listener |
| Maximale grootte van SSL-certificaat |V1-SKU-10 KB<br>V2 SKU-16 KB| |
| Certificaten voor verificatie |100 | |
| Vertrouwde basis certificaten |100 | |
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
| Maximale grootte voor het uploaden van bestanden WAF |v1-middel grote WAF-gateways, 100 MB<br>v1 grote WAF-gateways, 500 MB<br>v2 WAF, 750 MB| |
| Maximale grootte van WAF, zonder bestanden|128 KB||
| Aangepaste regels voor WAF maximum|100||
| Maxi maal aantal WAF uitsluitingen|100||

<sup>1</sup> in het geval van WAF sku's wordt u aangeraden het aantal resources te beperken tot 40 voor optimale prestaties.

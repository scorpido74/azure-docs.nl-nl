---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 3b692ed697d69deca4c50a0595cc54251bac4990
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74174941"
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
| Exemplaren per gateway |32 | |
| SSL-certificaten |100<sup>1</sup> |1 per HTTP-listeners |
| Maximale grootte van SSL-certificaat |V1-SKU-10 KB<br>V2 SKU-16 KB| |
| Certificaten voor verificatie |100 | |
| Vertrouwde basis certificaten |100 | |
| Minimale aanvraag time-out |1 seconde | |
| Maximum time-out van aanvraag |24 uur | |
| Aantal sites |100<sup>1</sup> |1 per HTTP-listeners |
| URL-kaarten per listener |1 | |
| Maximum aantal op pad gebaseerde regels per URL-toewijzing|100||
| Configuraties omleiden |100<sup>1</sup>| |
| Gelijktijdige WebSocket-verbindingen |Gemiddelde gateways 20.000<br> Grote gateways 50.000| |
| Maximale URL-lengte|32 KB| |
| Maximale header grootte voor HTTP/2 |4KB| |
| Maximale grootte voor het uploaden van bestanden, standaard |2 GB | |
| Maximale grootte voor het uploaden van bestanden WAF |Gemiddeld aantal WAF-gateways, 100 MB<br>Grote WAF-gateways, 500 MB| |
| Maximale grootte van WAF, zonder bestanden|128 KB||
| Aangepaste regels voor WAF maximum|100||
| Maxi maal aantal WAF uitsluitingen|100||

<sup>1</sup> in het geval van WAF sku's wordt u aangeraden het aantal resources te beperken tot 40 voor optimale prestaties.

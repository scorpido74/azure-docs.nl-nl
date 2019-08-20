---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623714"
---
Wanneer u de configuratie kloont vanuit een andere implementatie site, kan de gekloonde configuratie bewerkbaar zijn. Sommige configuratie-elementen volgen de inhoud in een wissel (niet sleuf specifiek), terwijl andere configuratie-elementen zich in dezelfde sleuf bevinden na een wissel (sleuf specifiek). In de volgende lijsten ziet u de instellingen die veranderen wanneer u sleuven verwisselt.

**Instellingen die worden omgewisseld**:

* Algemene instellingen, zoals Framework versie, 32/64 bits, Web Sockets
* App-instellingen (kan worden geconfigureerd om naar een sleuf te worden gestickt)
* Verbindings reeksen (kan zodanig worden geconfigureerd dat ze naar een sleuf worden gestickt)
* Handlertoewijzingen
* Bewakings-en diagnostische instellingen
* Open bare certificaten
* Inhoud van webjobs
* Hybride verbindingen *
* Integratie van virtueel netwerk *
* Service-eind punten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een sterretje (*) zijn gepland om te worden toegevoegd aan de sleuf. 

**Instellingen die niet zijn gewisseld**:

* Eind punten publiceren
* Namen van aangepaste domeinen
* Persoonlijke certificaten en SSL-bindingen
* Schaalinstellingen
* Webjobs-planners
* IP-beperkingen
* Altijd aan
* Protocol instellingen (HTTPS, TLS-versie, client certificaten)
* Instellingen voor Diagnostische logboeken
* Cross-Origin-resource delen (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->
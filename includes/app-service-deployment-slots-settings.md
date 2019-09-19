---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129695"
---
Wanneer u de configuratie kloont vanuit een andere implementatie site, kan de gekloonde configuratie bewerkbaar zijn. Sommige configuratie-elementen volgen de inhoud in een wissel (niet sleuf specifiek), terwijl andere configuratie-elementen zich in dezelfde sleuf bevinden na een wissel (sleuf specifiek). In de volgende lijsten ziet u de instellingen die veranderen wanneer u sleuven verwisselt.

**Instellingen die worden omgewisseld**:

* Algemene instellingen, zoals Framework versie, 32/64 bits, Web Sockets
* App-instellingen (kan worden geconfigureerd om naar een sleuf te worden gestickt)
* Verbindings reeksen (kan zodanig worden geconfigureerd dat ze naar een sleuf worden gestickt)
* Handlertoewijzingen
* Open bare certificaten
* Inhoud van webjobs
* Hybride verbindingen *
* Integratie van virtueel netwerk *
* Service-eind punten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een sterretje (*) zijn gepland om ongewisseld te worden. 

**Instellingen die niet zijn gewisseld**:

* Eind punten publiceren
* Namen van aangepaste domeinen
* Niet-open bare certificaten en TLS/SSL-instellingen
* Schaalinstellingen
* Webjobs-planners
* IP-beperkingen
* Altijd aan
* Instellingen voor Diagnostische logboeken
* Cross-Origin-resource delen (CORS)

> [!NOTE]
> Bepaalde app-instellingen die van toepassing zijn op niet-Verwissel bare instellingen, worden ook niet omgewisseld. Omdat de instellingen voor Diagnostische logboeken niet zijn omgewisseld, worden gerelateerde app `WEBSITE_HTTPLOGGING_RETENTION_DAYS` - `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` instellingen zoals en ook niet omgewisseld, zelfs niet als ze niet als sleuf instellingen worden weer gegeven.
>

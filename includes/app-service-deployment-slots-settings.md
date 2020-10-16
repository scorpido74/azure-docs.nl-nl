---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82132212"
---
Wanneer u de configuratie kloont vanuit een andere implementatie site, kan de gekloonde configuratie bewerkbaar zijn. Sommige configuratie-elementen volgen de inhoud in een wissel (niet sleuf specifiek), terwijl andere configuratie-elementen zich in dezelfde sleuf bevinden na een wissel (sleuf specifiek). In de volgende lijsten ziet u de instellingen die veranderen wanneer u sleuven verwisselt.

**Instellingen die worden omgewisseld**:

* Algemene instellingen, zoals Framework versie, 32/64 bits, Web Sockets
* App-instellingen (kan worden geconfigureerd om naar een sleuf te worden gestickt)
* Verbindings reeksen (kan zodanig worden geconfigureerd dat ze naar een sleuf worden gestickt)
* Handlertoewijzing
* Openbare certificaten
* Inhoud van webjobs
* Hybride verbindingen *
* Integratie van virtueel netwerk *
* Service-eind punten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een sterretje (*) zijn gepland om ongewisseld te worden. 

**Instellingen die niet zijn gewisseld**:

* Eind punten publiceren
* Aangepaste domeinnamen
* Niet-open bare certificaten en TLS/SSL-instellingen
* Schaal instellingen
* Webjobs-planners
* IP-beperkingen
* AlwaysOn
* Diagnostische instellingen
* Cross-Origin-resource delen (CORS)

> [!NOTE]
> Bepaalde app-instellingen die van toepassing zijn op niet-Verwissel bare instellingen, worden ook niet omgewisseld. Omdat de diagnostische instellingen bijvoorbeeld niet worden gewisseld, worden gerelateerde app-instellingen, zoals `WEBSITE_HTTPLOGGING_RETENTION_DAYS` en `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ook niet omgewisseld, zelfs niet weer gegeven als sleuf instellingen.
>

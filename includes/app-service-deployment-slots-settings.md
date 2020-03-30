---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129695"
---
Wanneer u de configuratie vanuit een andere implementatiesleuf kloont, is de gekloonde configuratie bewerkbaar. Sommige configuratie-elementen volgen de inhoud in een swap (niet specifiek voor sleuf), terwijl andere configuratie-elementen na een swap (sleufspecifiek) in dezelfde sleuf blijven. In de volgende lijsten worden de instellingen weergegeven die veranderen wanneer u van sleuven wisselt.

**Instellingen die worden verwisseld:**

* Algemene instellingen, zoals framework-versie, 32/64-bits, websockets
* App-instellingen (kan worden geconfigureerd om aan een sleuf te blijven)
* Verbindingstekenreeksen (kan worden geconfigureerd om aan een sleuf te blijven plakken)
* Handlertoewijzingen
* Openbare certificaten
* WebJobs-inhoud
* Hybride verbindingen *
* Virtuele netwerkintegratie *
* Serviceeindpunten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een sterretje (*) zijn gepland om niet te worden geruild. 

**Instellingen die niet worden verwisseld:**

* Eindpunten publiceren
* Aangepaste domeinnamen
* Niet-openbare certificaten en TLS/SSL-instellingen
* Schaalinstellingen
* WebJobs-planners
* IP-beperkingen
* AlwaysOn
* Instellingen voor diagnostisch logboek
* Cross-origin resource sharing (CORS)

> [!NOTE]
> Bepaalde app-instellingen die van toepassing zijn op niet-geruilde instellingen worden ook niet verwisseld. Omdat diagnostische logboekinstellingen bijvoorbeeld niet worden verwisseld, `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` worden gerelateerde app-instellingen zoals en ook niet verwisseld, zelfs als ze niet worden weergegeven als sleufinstellingen.
>

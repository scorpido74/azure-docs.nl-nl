---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711273"
---
| Dimensie naam | Beschrijving |
| ------------------- | ----------------- |
| **Geotype** | Trans actie van het primaire of secundaire cluster. De beschik bare waarden zijn onder andere **primaire** en **secundaire**. Dit is van toepassing op lees toegang geografisch redundante opslag (RA-GRS) bij het lezen van objecten van een secundaire Tenant. |
| **ResponseType** | Antwoord type voor de trans actie. De beschikbare waarden zijn onder meer: <br/><br/> <li>**ServerOtherError**: alle overige serverfouten, behalve diegene die zijn beschreven </li> <li>**ServerBusyError**: geverifieerde aanvraag waardoor een HTTP 503-statuscode is geretourneerd. </li> <li>**ServerTimeoutError**: geverifieerde aanvraag met time-out waardoor een HTTP 500-statuscode is geretourneerd. De time-out is opgetreden vanwege een serverfout. </li> <li>**AuthorizationError**: geverifieerde aanvraag die is mislukt vanwege niet-geautoriseerde toegang tot gegevens of een autorisatiefout. </li> <li>**NetworkError**: geverifieerde aanvraag die is mislukt vanwege netwerkfouten. Treedt meestal op als dooreen client voortijdig een verbinding wordt verbroken voordat de time-out voorbij is. </li><li>**ClientAccountBandwidthThrottlingError**: de aanvraag wordt beperkt over de band breedte voor het overschrijden van de [schaalbaarheids limieten van het opslag account](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError**: de aanvraag wordt beperkt op aanvraag snelheid voor overschrijding van de [schaalbaarheids limieten van het opslag account](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: andere fout met beperking aan client zijde. ClientAccountBandwidthThrottlingError en ClientAccountRequestThrottlingError zijn uitgesloten.</li> <li>**ClientTimeoutError**: geverifieerde aanvraag met time-out waardoor een HTTP 500-statuscode is geretourneerd. Als de time-out van het clientnetwerk of van de aanvraag is ingesteld op een lagere waarde dan door de opslagservice wordt verwacht, is er sprake van een verwachte time-out. Anders wordt deze als ServerTimeoutError gerapporteerd. </li> <li>**ClientOtherError**: alle overige fouten aan de clientzijde, behalve diegene die zijn beschreven. </li> <li>**Geslaagd**: succesvolle aanvraag</li> <li> **SuccessWithThrottling**: de aanvraag is voltooid wanneer een SMB-client wordt beperkt tijdens de eerste poging (en), maar na nieuwe pogingen slaagt.</li> |
| **ApiName** | De naam van de bewerking. 
| **Verificatie** | Het verificatie type dat wordt gebruikt in trans acties. De beschikbare waarden zijn onder meer: <br/> <li>**AccountKey**: de trans actie is geverifieerd met de sleutel van het opslag account.</li> <li>**SAS**: de trans actie wordt geverifieerd met hand tekeningen voor gedeelde toegang.</li> <li>**OAuth**: de trans actie wordt geverifieerd met OAuth-toegangs tokens.</li> <li>**Anoniem**: de trans actie is anoniem aangevraagd. Het bevat geen Preflight-aanvragen.</li> <li>**AnonymousPreflight**: de trans actie is een Preflight-aanvraag.</li> |
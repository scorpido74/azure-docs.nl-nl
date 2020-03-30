---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78305057"
---
| Resource | Gratis | Gedeeld | Basic | Standard | Premie (v2) | Geïsoleerd </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-, mobiele of API-apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service-abonnement](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Onbeperkt<sup>2</sup> |Onbeperkt<sup>2</sup> |Onbeperkt<sup>2</sup> |Onbeperkt<sup>2</sup>|
| [App-serviceplan](../articles/app-service/overview-hosting-plans.md) |10 per regio |10 per resourcegroep |100 per resourcegroep |100 per resourcegroep |100 per resourcegroep |100 per resourcegroep|
| Instantietype berekenen |Gedeeld |Gedeeld |Speciaal<sup>3</sup> |Speciaal<sup>3</sup> |Speciaal<sup>3</sup></p> |Speciaal<sup>3</sup>|
| [Uitschalen](../articles/app-service/manage-scale-up.md) (maximale exemplaren) |1 gedeeld |1 gedeeld |3 dedicated<sup>3</sup> |10 dedicated<sup>3</sup> |30 dedicated<sup>3</sup>|100 dedicated<sup>4</sup>|
| Opslag<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-tijd (5 minuten)<sup>6</sup> |3 minuten |3 minuten |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-tijd (dag)<sup>6</sup> |60 minuten |240 minuten |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, betalen tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Geheugen (1 uur) |1.024 MB per App-serviceabonnement |1.024 MB per app |N.v.t. |N.v.t. |N.v.t. |N.v.t. |
| Bandbreedte |165 MB |Onbeperkt, [dataoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) zijn van toepassing |Onbeperkt, [dataoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) zijn van toepassing |Onbeperkt, [dataoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) zijn van toepassing |Onbeperkt, [dataoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) zijn van toepassing |Onbeperkt, [dataoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) zijn van toepassing |
| Toepassingsarchitectuur |32-bits |32-bits |32-bits/64-bits |32-bits/64-bits |32-bits/64-bits |32-bits/64-bits |
| Websockets per exemplaar<sup>7</sup> |5 |35 |350 |Onbeperkt |Onbeperkt |Onbeperkt |
| IP-verbindingen | 600 | 600 | Afhankelijk van instantiegrootte<sup>8</sup> | Afhankelijk van instantiegrootte<sup>8</sup> | Afhankelijk van instantiegrootte<sup>8</sup> | 16.000 |
| Gelijktijdige [foutopsporingsverbindingen](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per toepassing |1 |1 |1 |5 |5 |5 |
| App-servicecertificaten per abonnement<sup>9</sup>| Niet ondersteund | Niet ondersteund |10 |10 |10 |10 |
| Aangepaste domeinen per app</a> |0 (alleen azurewebsites.net subdomein)|500 |500 |500 |500 |500 |
| [Ssl-ondersteuning](../articles/app-service/configure-ssl-certificate.md) voor aangepaste domeinen |Niet ondersteund, wildcardcertificaat voor *.azurewebsites.net standaard beschikbaar|Niet ondersteund, wildcardcertificaat voor *.azurewebsites.net standaard beschikbaar|Onbeperkte SNI SSL-verbindingen |Onbeperkte SNI SSL en 1 IP SSL-verbindingen inbegrepen |Onbeperkte SNI SSL en 1 IP SSL-verbindingen inbegrepen | Onbeperkte SNI SSL en 1 IP SSL-verbindingen inbegrepen|
| Hybride verbindingen per abonnement | | | 5 | 25 | 200 | 200 |
| Geïntegreerde load balancer | |X |X |X |X |X<sup>10</sup> |
| [AlwaysOn](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Geplande back-ups](../articles/app-service/manage-backup.md) | | | | Geplande back-ups om de 2 uur, maximaal 12 back-ups per dag (handleiding + gepland) | Geplande back-ups elk uur, een maximum van 50 back-ups per dag (handleiding + gepland) | Geplande back-ups elk uur, een maximum van 50 back-ups per dag (handleiding + gepland) |
| [Automatisch schalen](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Endpoint-bewaking](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Tijdelijke sleuven](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1.</sup> Apps en opslagquota zijn per App Service-abonnement, tenzij anders vermeld.  
<sup>2.</sup> Het werkelijke aantal apps dat u op deze machines hosten, is afhankelijk van de activiteit van de apps, de grootte van de machine-exemplaren en het bijbehorende resourcegebruik.  
<sup>3.</sup> Speciale exemplaren kunnen van verschillende grootte zijn. Zie [App Service-prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor meer informatie .  
<sup>4.</sup> Meer zijn toegestaan op verzoek.  
<sup>5.</sup> De opslaglimiet is de totale inhoudsgrootte voor alle apps in hetzelfde app-serviceabonnement. De totale inhoudsgrootte van alle apps in alle App-serviceplannen in één resourcegroep en regio mag niet hoger zijn dan 500 GB.  
<sup>6.</sup> Deze resources worden beperkt door fysieke resources op de specifieke instanties (de instantiegrootte en het aantal exemplaren).  
<sup>7.</sup> Als u een app in de basislaag schaalt naar twee instanties, hebt u 350 gelijktijdige verbindingen voor elk van de twee instanties. Voor standaardlagen en hoger zijn er geen theoretische beperkingen voor websockets, maar andere factoren kunnen het aantal websockets beperken. De maximaal toegestane gelijktijdige aanvragen (gedefinieerd `maxConcurrentRequestsPerCpu`door ) zijn bijvoorbeeld: 7.500 per kleine VM, 15.000 per vm per medium (7.500 x 2 cores) en 75.000 per grote VM (18.750 x 4 cores).  
<sup>8.</sup> De maximale IP-verbindingen zijn per instantie en zijn afhankelijk van de instantiegrootte: 1.920 per B1/S1/P1V2-exemplaar, 3.968 per B2/S2/P2V2-instantie, 8.064 per B3/S3/P3V2-instantie.  
<sup>9.</sup> De quotumlimiet voor app-servicecertificaten per abonnement kan via een ondersteuningsverzoek worden verhoogd tot een maximum van 200.  
<sup>10.</sup> App Service Geïsoleerde SKU's kunnen intern worden belast balanced (ILB) met Azure Load Balancer, dus er is geen openbare connectiviteit van het internet. Als gevolg hiervan moeten sommige functies van een ILB-geïsoleerde app-service worden gebruikt vanaf machines die directe toegang hebben tot het eindpunt van het ILB-netwerk.  
<sup>11.</sup> Voer aangepaste uitvoerbare bestanden en/of scripts op aanvraag, volgens een planning of continu uit als achtergrondtaak binnen uw App Service-instantie. Always On is vereist voor continue WebJobs-uitvoering. Er is geen vooraf gedefinieerde limiet voor het aantal WebJobs dat kan worden uitgevoerd in een app-service-instantie. Er zijn praktische limieten die afhangen van wat de toepassingscode probeert te doen.  

---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906707"
---


| Functie |[Verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan)|[Toegewezen plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Binnenkomende IP-beperkingen en toegang tot persoonlijke sites](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integratie van virtueel netwerk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Yes| ✅Yes|
|[Virtuele netwerk triggers (niet-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybride verbindingen](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (alleen Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Uitgaande IP-beperkingen](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Klikt|
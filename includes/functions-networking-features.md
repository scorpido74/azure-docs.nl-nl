---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906707"
---


| Functie |[Verbruiks abonnement](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan)|[Toegewezen plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Binnenkomende IP-beperkingen en toegang tot persoonlijke sites](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integratie van virtueel netwerk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nee|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Ja| ✅Ja|
|[Virtuele netwerk triggers (niet-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nee| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybride verbindingen](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (alleen Windows)|❌Nee|✅Ja|✅Ja|✅Ja|✅Ja|
|[Uitgaande IP-beperkingen](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nee| ✅Ja|✅Ja|✅Ja|✅Klikt|
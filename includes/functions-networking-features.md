---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578825"
---


| Functie |[Verbruiksabonnement](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan)|[Toegewezen abonnement](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Binnenkomende IP-beperkingen en toegang tot persoonlijke sites](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integratie van virtueel netwerk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nee|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Ja| ✅Ja|
|[Virtuele netwerk triggers (niet-HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nee| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybride verbindingen](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (alleen Windows)|❌Nee|✅Ja|✅Ja|✅Ja|✅Ja|
|[Uitgaande IP-beperkingen](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nee| ✅Ja|✅Ja|✅Ja|✅Ja|
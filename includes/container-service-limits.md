---
title: bestand opnemen
description: bestand opnemen
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ac928d9087ba5db312540b8ec542d7a2a29e2a99
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179063"
---
| Resource | Standaardlimiet |
| --- | :--- |
| Maximum aantal clusters per abonnement | 100 |
| Maximum aantal knoop punten per cluster met beschikbaarheids sets voor virtuele machines en Basic Load Balancer SKU  | 100 |
| Maximum aantal knoop punten per cluster met Virtual Machine Scale Sets en [Standard load BALANCER SKU][standard-load-balancer] | 1000 (100 knoop punten per [knooppunt groep][node-pool]) |
| Maximum aantal peulen per knoop punt: [basis netwerken][basic-networking] met Kubenet | 110 |
| Maximum aantal per knoop punt: [Geavanceerd netwerken][advanced-networking] met Azure container Network-Interface | Implementatie van Azure CLI: 30<sup>1</sup><br />Azure Resource Manager sjabloon: 30<sup>1</sup><br />Portal-implementatie: 30 |

<sup>1</sup> Wanneer u een Azure Kubernetes service-cluster (AKS) implementeert met de Azure CLI of een resource manager-sjabloon, kan deze waarde worden geconfigureerd tot 250 per knoop punt. U kunt het maximum aantal per knoop punt niet configureren nadat u al een AKS-cluster hebt geïmplementeerd of als u een cluster implementeert met behulp van de Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

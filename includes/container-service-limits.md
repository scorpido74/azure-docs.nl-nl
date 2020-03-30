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
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335057"
---
| Resource | Limiet |
| --- | :--- |
| Maximale clusters per abonnement | 100 |
| Maximale knooppunten per cluster met virtual machine availability sets en Basic Load Balancer SKU  | 100 |
| Maximale knooppunten per cluster met virtuele machineschaalsets en [Standard Load Balancer SKU][standard-load-balancer] | 1000 (100 knooppunten per [knooppuntgroep][node-pool]) |
| Maximale pods per knooppunt: [Basisnetwerken][basic-networking] met Kubenet | 110 |
| Maximale pods per knooppunt: [geavanceerde netwerken][advanced-networking] met Azure Container Networking Interface | Implementatie van Azure CLI: 30<sup>1</sup><br />Azure Resource Manager-sjabloon: 30<sup>1</sup><br />Portal-implementatie: 30 |

<sup>1.</sup> Wanneer u een AKS-cluster (Azure Kubernetes Service) implementeert met de Azure CLI- of Een Resource Manager-sjabloon, kan deze waarde tot 250 pods per knooppunt worden geconfigureerd. U geen maximale pods per knooppunt configureren nadat u al een AKS-cluster hebt geïmplementeerd of als u een cluster implementeert met behulp van de Azure-portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

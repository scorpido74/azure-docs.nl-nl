---
title: bestand opnemen
description: bestand opnemen
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67176398"
---
| Resource | Standaardlimiet |
| --- | :--- |
| Maximum aantal clusters per abonnement | 100 |
| Maximum aantal knoop punten per cluster | 100 |
| Maximum aantal per knoop punt: [Basis netwerken][basic-networking] met Kubenet | 110 |
| Maximum aantal per knoop punt: [Geavanceerde netwerken][advanced-networking] met Azure container Networking-interface | Implementatie van Azure CLI: 30<sup>1</sup><br />Azure Resource Manager sjabloon: 30<sup>1</sup><br />Portal-implementatie: 30 |

<sup>1</sup> Wanneer u een Azure Kubernetes service-cluster (AKS) implementeert met de Azure CLI of een resource manager-sjabloon, kan deze waarde worden geconfigureerd tot 250 per knoop punt. U kunt het maximum aantal per knoop punt niet configureren nadat u al een AKS-cluster hebt geïmplementeerd of als u een cluster implementeert met behulp van de Azure Portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

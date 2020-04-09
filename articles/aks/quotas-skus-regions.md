---
title: Limieten voor resources, SKU's, regio's
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de standaardquota, de VM-sKU-formaten met beperkte node en de beschikbaarheid van de Azure Kubernetes-service (AKS) in de regio.
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 054d6ff4fc105d84192ac81feda97515f6cfae49
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886769"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quota, beperkingen voor de grootte van virtuele machines en regiobeschikbaarheid in Azure Kubernetes Service (AKS)

Alle Azure-services stellen standaardlimieten en quota in voor resources en functies. Bepaalde virtuele machine (VM) SKU's zijn ook beperkt voor gebruik.

In dit artikel worden de standaardbronlimieten voor AKS-bronnen (Azure Kubernetes Service) en de beschikbaarheid van AKS in Azure-regio's beschreven.

## <a name="service-quotas-and-limits"></a>Servicequota en -limieten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Ingerichte infrastructuur

Alle andere beperkingen voor netwerk, berekening en opslag zijn van toepassing op de ingerichte infrastructuur. Zie [Azure-abonnements- en servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor de relevante limieten .

> [!IMPORTANT]
> Wanneer u een AKS-cluster upgradet, worden er tijdelijk extra resources verbruikt. Deze bronnen omvatten beschikbare IP-adressen in een virtueel netwerksubnet of vCPU-quotum voor virtuele machines. Als u Windows Server-containers gebruikt (momenteel in preview in AKS), is de enige goedgekeurde benadering om de nieuwste updates op de knooppunten toe te passen het uitvoeren van een upgradebewerking. Een mislukt clusterupgradeproces kan erop wijzen dat u niet over de beschikbare IP-adresruimte of vCPU-quotum beschikt om deze tijdelijke resources te verwerken. Zie [Een knooppuntgroep in AKS upgraden][nodepool-upgrade]voor meer informatie over het upgradeproces voor het knooppunt van Windows Server.

## <a name="restricted-vm-sizes"></a>Beperkte VM-formaten

Elk knooppunt in een AKS-cluster bevat een vaste hoeveelheid rekenbronnen zoals vCPU en geheugen. Als een AKS-knooppunt onvoldoende rekenbronnen bevat, kunnen pods mogelijk niet correct worden uitgevoerd. Gebruik de **volgende VM SKU's in AKS**om ervoor te zorgen dat de vereiste *kube-systeempods* en uw toepassingen betrouwbaar kunnen worden gepland:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Zie Grootte voor virtuele machines in [Azure voor][vm-skus]meer informatie over VM-typen en hun rekenbronnen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Zie de beschikbaarheid van [AKS-regio's][region-availability]voor de meest recente lijst met waar u clusters implementeren en uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als uw resource een verhoging ondersteunt, vraagt u de verhoging aan via een [Azure-ondersteuningsaanvraag][azure-support] (selecteer Quotum voor **probleemtype).** **Quota**

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

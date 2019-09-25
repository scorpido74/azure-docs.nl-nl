---
title: Aanbevolen procedures voor Azure Service Fabric-netwerken | Microsoft Docs
description: Aanbevolen procedures voor het beheren van Service Fabric netwerk.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 317977af9d41163013545a6e5f60bee887da596c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262252"
---
# <a name="networking"></a>Netwerken

Wanneer u Azure Service Fabric-clusters maakt en beheert, biedt u netwerk connectiviteit voor uw knoop punten en toepassingen. De netwerk bronnen omvatten IP-adresbereiken, virtuele netwerken, load balancers en netwerk beveiligings groepen. In dit artikel vindt u aanbevolen procedures voor deze resources.

Bekijk Azure [service Fabric-netwerk patronen](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) voor meer informatie over het maken van clusters die gebruikmaken van de volgende functies: Bestaand virtueel netwerk of subnet, statisch openbaar IP-adres, intern alleen load balancer, of intern en extern load balancer.

## <a name="infrastructure-networking"></a>Infrastructuur netwerken
Maximaliseer de prestaties van uw virtuele machine met versneld netwerken door de eigenschap enableAcceleratedNetworking in uw Resource Manager-sjabloon te declareren, het volgende code fragment is van een Networkinterfaceconfigurations schaalset voor virtuele-machine schaal sets die versnelde netwerken inschakelen:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric cluster kan worden ingericht op [Linux met versneld netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)en [Windows met versneld netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

Versnelde netwerken worden ondersteund voor Sku's van de Azure-machine serie: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 en MS/MMS. Versneld netwerken zijn getest met behulp van de Standard_DS8_v3-SKU op 1/23/2019 voor een Service Fabric Windows-cluster, en met Standard_DS12_v2 op 01/29/2019 voor een Service Fabric Linux-cluster.

Als u versneld netwerken op een bestaand Service Fabric cluster wilt inschakelen, moet u eerst [een service Fabric cluster uitschalen door een schaalset voor virtuele machines toe te voegen](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), zodat u het volgende kunt doen:
1. Een NodeType inrichten met versneld netwerken ingeschakeld
2. Migreer uw services en hun status naar het ingerichte NodeType met versneld netwerken ingeschakeld

Er is een uitbrei ding van de infra structuur vereist om versnelde netwerken in te scha kelen op een bestaand cluster, omdat het inschakelen van versnelde netwerken op de juiste manier downtime zou veroorzaken, omdat alle virtuele machines in een beschikbaarheidsset moeten worden gestopt en de toewijzing wordt ongedaan gemaakt [voordat versnelde netwerken inschakelen op een bestaande NIC](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Cluster netwerken

* Service Fabric clusters kunnen worden geïmplementeerd in een bestaand virtueel netwerk door de stappen te volgen die worden beschreven in [service Fabric-netwerk patronen](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Netwerk beveiligings groepen (Nsg's) worden aanbevolen voor knooppunt typen die binnenkomend en uitgaand verkeer naar hun cluster beperken. Zorg ervoor dat de benodigde poorten zijn geopend in de NSG. Bijvoorbeeld: ![Service Fabric NSG-regels][NSGSetup]

* Het primaire knooppunt type, dat de Service Fabric systeem services bevat, hoeft niet te worden weer gegeven via de externe load balancer en kan worden weer gegeven door een [interne Load Balancer](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Gebruik een [statisch openbaar IP-adres](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) voor uw cluster.

## <a name="application-networking"></a>Toepassings netwerken

* Als u Windows-container werkbelastingen wilt uitvoeren, gebruikt u de [open-netwerk modus](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) om service-to-service-communicatie eenvoudiger te maken.

* Gebruik een omgekeerde proxy zoals [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) of de [service Fabric reverse proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) om algemene toepassings poorten weer te geven, zoals 80 of 443.

* Voor Windows-containers die worden gehost op gapped machines die geen basis lagen kunnen halen uit Azure-Cloud opslag, kunt u het gedrag van de afwijkende laag overschrijven met behulp van de vlag [--Allow-niet-distribueer bare artefacten](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) in de docker-daemon.

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op Vm's of computers met Windows Server: [Service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op Vm's of op computers met Linux: [Een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

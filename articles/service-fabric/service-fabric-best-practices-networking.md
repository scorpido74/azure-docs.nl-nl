---
title: Aanbevolen procedures voor Azure Service Fabric-netwerken
description: Aanbevolen procedures en ontwerpoverwegingen voor het beheren van netwerkconnectiviteit met Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551791"
---
# <a name="networking"></a>Netwerken

Terwijl u Azure Service Fabric-clusters maakt en beheert, biedt u netwerkconnectiviteit voor uw knooppunten en toepassingen. De netwerkbronnen omvatten IP-adresbereiken, virtuele netwerken, load balancers en netwerkbeveiligingsgroepen. In dit artikel leert u best practices voor deze bronnen.

Bekijk Azure [Service Fabric-netwerkpatronen](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) voor meer informatie over het maken van clusters die de volgende functies gebruiken: Bestaand virtueel netwerk of subnet, statisch openbaar IP-adres, interne lastenbalancer of interne en externe load balancer.

## <a name="infrastructure-networking"></a>Infrastructuurnetwerken
Maximaliseer de prestaties van uw virtuele machine met accelerated networking, door de eigenschap AcceleratedNetworking in uw Resource Manager-sjabloon te declareren, is het volgende fragment van een Virtual Machine Scale Set NetworkInterfaceConfigurations die maakt Accelerated Networking mogelijk:

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
Service Fabric-cluster kan worden ingericht op [Linux met Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)en Windows met [Accelerated Networking.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)

Versnelde netwerken wordt ondersteund voor Azure Virtual Machine Series SKU's: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 en Ms/Mms. Accelerated Networking is met succes getest met behulp van de Standard_DS8_v3 SKU op 1/23/2019 voor een Service Fabric Windows Cluster en het gebruik van Standard_DS12_v2 op 01/29/2019 voor een Service Fabric Linux Cluster.

Als u Versneld netwerken in een bestaand cluster van Servicefabric wilt inschakelen, moet u eerst [een cluster van servicestructuur schalen door een virtuele machineschaalset toe](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)te voegen om het volgende uit te voeren:
1. Een NodeType inrichten met accelerated networking ingeschakeld
2. Migreer uw services en hun status naar het ingerichte NodeType met accelerated networking ingeschakeld

Het uitschalen van de infrastructuur is vereist om Versnelde netwerken in te schakelen op een bestaand cluster, omdat het inschakelen van Versnelde netwerken downtime zou veroorzaken, omdat alle virtuele machines in een beschikbaarheidsset moeten [worden gestopt en detoewijzen voordat versnelde netwerken op een bestaande NIC](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)worden ingeschakeld.

## <a name="cluster-networking"></a>Clusternetwerken

* Service Fabric-clusters kunnen worden geïmplementeerd in een bestaand virtueel netwerk door de stappen te volgen die zijn beschreven in [netwerkpatronen van Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)

* Netwerkbeveiligingsgroepen (NSG's) worden aanbevolen voor knooppunttypen die binnenkomend en uitgaand verkeer beperken tot hun cluster. Zorg ervoor dat de benodigde poorten worden geopend in de NSG. Bijvoorbeeld: ![Service Fabric NSG Regels][NSGSetup]

* Het primaire knooppunttype, dat de servicefabric-systeemservices bevat, hoeft niet te worden blootgesteld via de externe load balancer en kan worden blootgesteld door een [interne load balancer](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)

* Gebruik een [statisch openbaar IP-adres](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) voor uw cluster.

## <a name="application-networking"></a>Netwerkvan toepassingen

* Als u Windows-containerworkloads wilt uitvoeren, gebruikt u [de open netwerkmodus](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) om service-to-service-communicatie eenvoudiger te maken.

* Gebruik een omgekeerde proxy zoals [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) of de [reverse proxy van](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) Service Fabric om algemene toepassingspoorten zoals 80 of 443 bloot te leggen.

* Voor Windows Containers die worden gehost op machines met luchtfoto's die basislagen niet uit Azure-cloudopslag kunnen halen, overschrijft u het gedrag van de vreemde laag door de vlag [--toestaan-niet-distribueerbare artefacten](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) in de Docker-daemon te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op VM's of computers met Windows Server: [clustercreatie van servicefabric voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op VM's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

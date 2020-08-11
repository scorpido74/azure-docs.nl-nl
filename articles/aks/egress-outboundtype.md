---
title: Door de gebruiker gedefinieerde routes (UDR) aanpassen in azure Kubernetes service (AKS)
description: Meer informatie over het definiëren van een aangepaste uitgangs route in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: 5fe674fa7ab6a6a3f222a215ebc6912549776fee
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067355"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Cluster uitgang aanpassen met een door de gebruiker gedefinieerde route

Een AKS-cluster kan worden aangepast aan specifieke scenario's. AKS zal standaard een standaard-SKU inrichten Load Balancer worden ingesteld en gebruikt voor uitgaand verkeer. Het is echter mogelijk dat de standaard instelling niet voldoet aan de vereisten van alle scenario's als open bare IP-adressen zijn niet toegestaan of extra hops zijn vereist voor het uitvallen van de gegevens.

In dit artikel wordt beschreven hoe u de uitgangs route van een cluster kunt aanpassen ter ondersteuning van aangepaste netwerk scenario's, zoals die voor het niet toestaan van open bare IP-adressen en het cluster vereist achter een virtueel netwerk apparaat (NVA).

## <a name="prerequisites"></a>Vereisten
* Azure CLI-versie 2.0.81 of hoger
* API-versie van `2020-01-01` of hoger


## <a name="limitations"></a>Beperkingen
* OutboundType kan alleen worden gedefinieerd tijdens het maken van het cluster en kan later niet worden bijgewerkt.
* Voor `outboundType` de instelling zijn AKS-clusters met een `vm-set-type` van `VirtualMachineScaleSets` en van vereist `load-balancer-sku` `Standard` .
* Voor `outboundType` het instellen van een waarde van `UDR` is een door de gebruiker gedefinieerde route met geldige uitgaande connectiviteit voor het cluster vereist.
* `outboundType`Als u de waarde instelt op ' `UDR` impliceert het binnenkomende bron-IP-adres dat naar de Load Balancer wordt doorgestuurd, mogelijk niet hetzelfde als het uitgaande **afwijkings** doel van het cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Overzicht van uitgaande typen in AKS

Een AKS-cluster kan worden aangepast met een uniek `outboundType` type Load Balancer of door de gebruiker gedefinieerde route ring.

> [!IMPORTANT]
> Uitgaand type is alleen van invloed op het uitgaande verkeer van uw cluster. Zie voor meer informatie over het [instellen van ingress-controllers](ingress-basic.md).

> [!NOTE]
> U kunt uw eigen [route tabel][byo-route-table] gebruiken met UDR-en kubenet-netwerken. Zorg ervoor dat de cluster-id (Service-Principal of beheerde identiteit) Inzender machtigingen heeft voor de aangepaste route tabel.

### <a name="outbound-type-of-loadbalancer"></a>Uitgaand type loadBalancer

Als `loadBalancer` is ingesteld, aks de volgende configuratie automatisch volt ooien. De load balancer wordt gebruikt voor uitgaand verkeer via een AKS toegewezen openbaar IP-adres. Een uitgaand type van `loadBalancer` ondersteunt Kubernetes services van `loadBalancer` het type, dat wordt verwacht uit het Load Balancer dat is gemaakt door de resource provider AKS.

De volgende configuratie wordt uitgevoerd door AKS.
   * Een openbaar IP-adres is ingericht voor het uituitgangs cluster.
   * Het open bare IP-adres wordt toegewezen aan de load balancer resource.
   * Back-endservers voor de load balancer worden ingesteld voor agent knooppunten in het cluster.

Hieronder vindt u een netwerk topologie die standaard wordt geïmplementeerd in AKS-clusters, waarbij een van wordt gebruikt `outboundType` `loadBalancer` .

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Uitgaand type userDefinedRouting

> [!NOTE]
> Het gebruik van een uitgaand type is een geavanceerd netwerk scenario en vereist een juiste netwerk configuratie.

Als `userDefinedRouting` is ingesteld, AKS niet automatisch uitzonderings paden configureren. De uitgebrachte installatie moet door u worden uitgevoerd.

Het AKS-cluster moet worden geïmplementeerd in een bestaand virtueel netwerk met een subnet dat eerder is geconfigureerd omdat als er geen standaard load balancer-architectuur (SLB) wordt gebruikt, moet u expliciete uitgang instellen. Daarom vereist deze architectuur expliciet uitgaand verkeer naar een apparaat als een firewall, gateway, proxy of om de NAT (Network Address Translation) toe te staan door een openbaar IP-adres dat is toegewezen aan de standaard load balancer of het apparaat.

De resource provider AKS implementeert een Standard-load balancer (SLB). De load balancer is niet geconfigureerd met regels en er [worden geen kosten in rekening gebracht tot er een regel wordt geplaatst](https://azure.microsoft.com/pricing/details/load-balancer/). AKS wordt niet automatisch een openbaar IP-adres voor de SLB-frontend ingericht en de load balancer back-end-groep **niet** automatisch geconfigureerd.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Een cluster met uitgaand type UDR en Azure Firewall implementeren

Ter illustratie van de toepassing van een cluster met een uitgaand type met behulp van een door de gebruiker gedefinieerde route kan een cluster worden geconfigureerd in een virtueel netwerk met een Azure Firewall op een eigen subnet. Zie dit voor beeld in het voor beeld uitgaand [verkeer beperken met Azure firewall](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Uitgaand type UDR vereist dat er een route voor 0.0.0.0/0 en de volgende hop-bestemming van NVA (virtueel netwerk apparaat) in de route tabel is.
> De route tabel heeft al een standaard 0.0.0.0/0 op internet, zonder een openbaar IP-adres voor SNAT. Als u deze route alleen wilt toevoegen, hoeft u niets te doen. AKS valideert dat u geen 0.0.0.0/0-route hebt gemaakt die verwijst naar het Internet, maar in plaats daarvan NVA of gateway, enzovoort.


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van Azure Networking UDR](../virtual-network/virtual-networks-udr-overview.md).

Zie [een route tabel maken, wijzigen of verwijderen](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet

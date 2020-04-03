---
title: Azure CNI-netwerken configureren in Azure Kubernetes Service (AKS)
description: Meer informatie over het configureren van Azure CNI (advanced) networking in Azure Kubernetes Service (AKS), inclusief het implementeren van een AKS-cluster in een bestaand virtueel netwerk en subnet.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 6f194cb97850fcb24e4789ac0ba39b6f03d99e6e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617384"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Azure CNI-netwerken configureren in Azure Kubernetes Service (AKS)

Standaard gebruiken AKS-clusters [kubenet][kubenet]en worden er een virtueel netwerk en subnet voor u gemaakt. Met *kubenet*krijgen knooppunten een IP-adres van een virtueel netwerksubnet. Netwerkadresvertaling (NAT) wordt vervolgens geconfigureerd op de knooppunten en pods ontvangen een IP-adres dat achter het IP-knooppunt wordt "verborgen". Deze aanpak vermindert het aantal IP-adressen dat u moet reserveren in uw netwerkruimte voor pods om te gebruiken.

Met [Azure Container Networking Interface (CNI)][cni-networking]krijgt elke pod een IP-adres van het subnet en kan deze rechtstreeks worden geopend. Deze IP-adressen moeten uniek zijn in uw netwerkruimte en moeten van tevoren worden gepland. Elk knooppunt heeft een configuratieparameter voor het maximum aantal pods dat het ondersteunt. Het equivalentaantal IP-adressen per knooppunt wordt dan vooraf gereserveerd voor dat knooppunt. Deze aanpak vereist meer planning en leidt vaak tot uitputting van IP-adressen of de noodzaak om clusters opnieuw op te bouwen in een groter subnet naarmate de vraag naar uw toepassing toont.

In dit artikel ziet u hoe u *Azure CNI-netwerken* gebruiken om een virtueel netwerksubnet te maken en te gebruiken voor een AKS-cluster. Zie [Netwerkconcepten voor Kubernetes en AKS voor][aks-network-concepts]meer informatie over netwerkopties en -overwegingen.

## <a name="prerequisites"></a>Vereisten

* Het virtuele netwerk voor het AKS-cluster moet uitgaande internetverbinding mogelijk maken.
* AKS-clusters mogen `169.254.0.0/16` `172.30.0.0/16`geen `172.31.0.0/16`gebruik `192.0.2.0/24` maken van , , of voor het bereik van de Kubernetes-serviceadres.
* De serviceprincipal die door het AKS-cluster wordt gebruikt, moet ten minste machtigingen voor [netwerkbijdragen in het](../role-based-access-control/built-in-roles.md#network-contributor) subnet binnen uw virtuele netwerk hebben. Als u een [aangepaste rol](../role-based-access-control/custom-roles.md) wilt definiëren in plaats van de ingebouwde rol netwerkbijdrager te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* In plaats van een serviceprincipal u de beheerde identiteit met het systeem gebruiken voor machtigingen. Zie [Beheerde identiteiten gebruiken voor](use-managed-identity.md)meer informatie .
* Het subnet dat is toegewezen aan de AKS-knooppuntgroep kan geen [gedelegeerd subnet](../virtual-network/subnet-delegation-overview.md)zijn.

## <a name="plan-ip-addressing-for-your-cluster"></a>IP-adressering plannen voor uw cluster

Clusters die zijn geconfigureerd met Azure CNI-netwerken vereisen extra planning. De grootte van uw virtuele netwerk en het subnet moeten rekening houden met het aantal pods dat u wilt uitvoeren en het aantal knooppunten voor het cluster.

IP-adressen voor de pods en de knooppunten van het cluster worden toegewezen vanuit het opgegeven subnet binnen het virtuele netwerk. Elk knooppunt is geconfigureerd met een primair IP-adres. Standaard zijn 30 extra IP-adressen vooraf geconfigureerd door Azure CNI die zijn toegewezen aan pods die op het knooppunt zijn gepland. Wanneer u uw cluster schaalt, wordt elk knooppunt op dezelfde manier geconfigureerd met IP-adressen van het subnet. U ook de [maximale pods per knooppunt](#maximum-pods-per-node)bekijken.

> [!IMPORTANT]
> Het aantal vereiste IP-adressen moet overwegingen bevatten voor upgrade- en schaalbewerkingen. Als u het IP-adresbereik instelt op alleen ondersteuning voor een vast aantal knooppunten, u uw cluster niet upgraden of schalen.
>
> - Wanneer u uw AKS-cluster **upgradet,** wordt een nieuw knooppunt in het cluster geïmplementeerd. Services en workloads beginnen te worden uitgevoerd op het nieuwe knooppunt en een ouder knooppunt wordt uit het cluster verwijderd. Voor dit voortschrijdend upgradeproces is minimaal één extra blok IP-adressen beschikbaar. Je knooppunt telt `n + 1`dan.
>   - Deze overweging is vooral belangrijk wanneer u windows serverknooppuntgroepen gebruikt (momenteel in preview in AKS). Windows Server-knooppunten in AKS passen windows-updates niet automatisch toe, maar voert u een upgrade uit op de knooppuntgroep. Met deze upgrade worden nieuwe knooppunten geïmplementeerd met de nieuwste Windows Server 2019-basisknooppuntafbeeldings- en beveiligingspatches. Zie [Een knooppuntgroep][nodepool-upgrade]in AKS upgraden voor meer informatie over het upgraden van een windows server-knooppuntgroep.
>
> - Wanneer u een AKS-cluster **schaalt,** wordt een nieuw knooppunt in het cluster geïmplementeerd. Services en workloads beginnen te worden uitgevoerd op het nieuwe knooppunt. Uw IP-adresbereik moet rekening houden met overwegingen hoe u het aantal knooppunten wilt opschalen en pods die uw cluster kan ondersteunen. Een extra knooppunt voor upgradebewerkingen moet ook worden opgenomen. Je knooppunt telt `n + number-of-additional-scaled-nodes-you-anticipate + 1`dan.

Als u verwacht dat uw knooppunten het maximum aantal pods uitvoeren en regelmatig pods vernietigen en implementeren, moet u ook enkele extra IP-adressen per knooppunt in calculeren. Deze extra IP-adressen houden er rekening mee dat het enkele seconden kan duren voordat een service is verwijderd en het IP-adres is vrijgegeven om een nieuwe service te implementeren en het adres te verkrijgen.

Het IP-adresplan voor een AKS-cluster bestaat uit een virtueel netwerk, ten minste één subnet voor knooppunten en pods en een Kubernetes-serviceadresbereik.

| Adresbereik / Azure-bron | Limieten en grootte |
| --------- | ------------- |
| Virtueel netwerk | Het virtuele Azure-netwerk kan zo groot zijn als /8, maar is beperkt tot 65.536 geconfigureerde IP-adressen. |
| Subnet | Moet groot genoeg zijn voor de knooppunten, pods en alle Kubernetes- en Azure-bronnen die in uw cluster kunnen worden ingericht. Als u bijvoorbeeld een interne Azure Load Balancer implementeert, worden de front-end IP's toegewezen vanuit het clustersubnet en niet van openbare IP's. Bij de subnetgrootte moet ook rekening worden gehouden met upgradebewerkingen of toekomstige schaalbehoeften.<p />Ga als het gaat om het berekenen van de *minimale* subnetgrootte, inclusief een extra knooppunt voor upgradebewerkingen:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Voorbeeld voor een cluster met `(51) + (51  * 30 (default)) = 1,581` 50 node: (/21 of groter)<p/>Voorbeeld voor een cluster met 50 knooppunten dat ook voorzieningen `(61) + (61 * 30 (default)) = 1,891` bevat om nog eens 10 knooppunten op te schalen: (/21 of groter)<p>Als u bij het maken van uw cluster geen maximumaantal pods per knooppunt opgeeft, wordt het maximum aantal pods per knooppunt ingesteld op *30*. Het minimum aantal vereiste IP-adressen is gebaseerd op die waarde. Als u uw minimale IP-adresvereisten op een andere maximumwaarde berekent, raadpleegt u hoe u [het maximumaantal pods per knooppunt configureert](#configure-maximum---new-clusters) om deze waarde in te stellen wanneer u uw cluster implementeert. |
| Adresbereik van Kubernetes Service | Dit bereik mag niet worden gebruikt door een netwerkelement op of verbonden met dit virtuele netwerk. Serviceadres CIDR moet kleiner zijn dan /12. U dit bereik opnieuw gebruiken in verschillende AKS-clusters. |
| IP-adres van DNS-service van Kubernetes | IP-adres binnen het adresbereik van de Kubernetes-service dat wordt gebruikt door clusterservicedetectie (kube-dns). Gebruik het eerste IP-adres niet in uw adresbereik, zoals .1. Het eerste adres in uw subnetbereik wordt gebruikt voor het *ip.default.svc.cluster.local-adres.* |
| Dockerbrugadres | Het netwerkadres van Docker Bridge vormt het standaardadres van het *docker0* bridge-netwerk dat in alle Docker-installaties aanwezig is. Hoewel *docker0-bridge* niet wordt gebruikt door AKS-clusters of de pods zelf, moet u dit adres zo instellen dat scenario's zoals *dockerbuild* binnen het AKS-cluster blijven worden ondersteund. Het is vereist om een CIDR te selecteren voor het Docker bridge netwerkadres, omdat Docker anders automatisch een subnet kiest dat in strijd zou kunnen zijn met andere CIDRs. U moet een adresruimte kiezen die niet botst met de rest van de CIDR's op uw netwerken, inclusief de cidr- en podCIDR van het cluster. Standaard van 172.17.0.1/16. U dit bereik opnieuw gebruiken in verschillende AKS-clusters. |

## <a name="maximum-pods-per-node"></a>Maximumpods per knooppunt

Het maximum aantal pods per knooppunt in een AKS-cluster is 250. Het *standaard* maximumaantal pods per knooppunt varieert tussen *kubenet* en *Azure CNI-netwerken* en de methode van clusterimplementatie.

| Implementatiemethode | Kubenet standaard | Azure CNI standaard | Configureerbaar bij implementatie |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ja (tot 250) |
| Resource Manager-sjabloon | 110 | 30 | Ja (tot 250) |
| Portal | 110 | 30 | Nee |

### <a name="configure-maximum---new-clusters"></a>Maximaal configureren - nieuwe clusters

U het maximum aantal pods per knooppunt alleen configureren *op de implementatietijd*van het cluster. Als u implementeert met de Azure CLI of met een Resource Manager-sjabloon, u de maximale pods per knooppuntwaarde instellen tot 250.

Er wordt een minimumwaarde voor maximale pods per knooppunt afgedwongen om ruimte te garanderen voor systeempods die essentieel zijn voor de clusterstatus. De minimumwaarde die kan worden ingesteld voor maximale pods per knooppunt is 10 als en alleen als de configuratie van elke knooppuntgroep ruimte heeft voor minimaal 30 pods. Als u bijvoorbeeld de maximale pods per knooppunt instelt op het minimum van 10, moet elke afzonderlijke knooppuntgroep minimaal 3 knooppunten hebben. Deze vereiste geldt ook voor elke nieuwe knooppuntgroep die is gemaakt, dus als 10 wordt gedefinieerd als maximale pods per knooppunt moet elke volgende knooppuntgroep die wordt toegevoegd ten minste 3 knooppunten hebben.

| Netwerken | Minimum | Maximum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet Kubenet | 10 | 110 |

> [!NOTE]
> De minimumwaarde in de bovenstaande tabel wordt strikt afgedwongen door de AKS-service. U geen maxPods-waarde instellen die lager is dan het minimum dat wordt weergegeven, zodat het cluster niet kan starten.

* **Azure CLI**: `--max-pods` geef het argument op wanneer u een cluster implementeert met de opdracht [az aks create.][az-aks-create] De maximale waarde is 250.
* **Resourcemanager-sjabloon**: `maxPods` Geef de eigenschap op in het object [ManagedClusterAgentPoolProfile] wanneer u een cluster implementeert met een resourcemanagersjabloon. De maximale waarde is 250.
* **Azure-portal**: u het maximum aantal pods per knooppunt niet wijzigen wanneer u een cluster implementeert met de Azure-portal. Azure CNI-netwerkclusters zijn beperkt tot 30 pods per knooppunt wanneer u implementeert met behulp van de Azure-portal.

### <a name="configure-maximum---existing-clusters"></a>Maximaal configureren - bestaande clusters

U de maximale pods per knooppunt op een bestaand AKS-cluster niet wijzigen. U het nummer alleen aanpassen wanneer u het cluster in eerste instantie implementeert.

## <a name="deployment-parameters"></a>Implementatieparameters

Wanneer u een AKS-cluster maakt, zijn de volgende parameters configureerbaar voor Azure CNI-netwerken:

**Virtueel netwerk**: het virtuele netwerk waarin u het Kubernetes-cluster wilt implementeren. Als u een nieuw virtueel netwerk voor uw cluster wilt maken, selecteert u *Nieuw maken* en volgt u de stappen in de sectie Virtueel *netwerk maken.* Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)voor informatie over de limieten en quota voor een virtueel Azure-netwerk.

**Subnet**: Het subnet binnen het virtuele netwerk waar u het cluster wilt implementeren. Als u een nieuw subnet wilt maken in het virtuele netwerk voor uw cluster, selecteert u *Nieuw maken* en volgt u de stappen in de sectie *Subnet maken.* Voor hybride connectiviteit mag het adresbereik niet overlappen met andere virtuele netwerken in uw omgeving.

**Kubernetes service adresbereik**: dit is de set virtuele IP's die Kubernetes toewijst aan interne [services][services] in uw cluster. U elk privéadresbereik gebruiken dat aan de volgende vereisten voldoet:

* Mag zich niet binnen het virtuele netwerk-IP-adresbereik van uw cluster bevinden
* Mag niet overlappen met andere virtuele netwerken waarmee de virtuele netwerkpeers van het cluster
* Mag niet overlappen met on-premises IP's
* Mag niet binnen `169.254.0.0/16`het `172.30.0.0/16` `172.31.0.0/16`bereik , , , of`192.0.2.0/24`

Hoewel het technisch mogelijk is om een serviceadresbereik op te geven binnen hetzelfde virtuele netwerk als uw cluster, wordt dit niet aanbevolen. Onvoorspelbaar gedrag kan het gevolg zijn als overlappende IP-bereiken worden gebruikt. Zie voor meer informatie het gedeelte [VEELgestelde vragen](#frequently-asked-questions) van dit artikel. Zie [Services][services] in de Kubernetes-documentatie voor meer informatie over Kubernetes-services.

**Ip-adres van de Kubernetes DNS-service:** het IP-adres voor de DNS-service van het cluster. Dit adres moet binnen het *adresbereik van Kubernetes Service* liggen. Gebruik het eerste IP-adres niet in uw adresbereik, zoals .1. Het eerste adres in uw subnetbereik wordt gebruikt voor het *ip.default.svc.cluster.local-adres.*

**Docker Bridge-adres**: Het dockerbrugnetwerkadres vertegenwoordigt het standaard *docker0-brugnetwerkadres* dat aanwezig is in alle Docker-installaties. Hoewel *docker0-bridge* niet wordt gebruikt door AKS-clusters of de pods zelf, moet u dit adres zo instellen dat scenario's zoals *dockerbuild* binnen het AKS-cluster blijven worden ondersteund. Het is vereist om een CIDR te selecteren voor het Docker bridge netwerkadres, omdat Docker anders automatisch een subnet kiest dat in strijd zou kunnen zijn met andere CIDRs. U moet een adresruimte kiezen die niet botst met de rest van de CIDR's op uw netwerken, inclusief de cidr- en podCIDR van het cluster.

## <a name="configure-networking---cli"></a>Netwerken configureren - CLI

Wanneer u een AKS-cluster maakt met de Azure CLI, u ook Azure CNI-netwerken configureren. Gebruik de volgende opdrachten om een nieuw AKS-cluster te maken met Azure CNI-netwerken ingeschakeld.

Download eerst de subnetbron-ID voor het bestaande subnet waarin het AKS-cluster wordt samengevoegd:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Gebruik de [opdracht az aks create][az-aks-create] met het `--network-plugin azure` argument om een cluster met geavanceerde netwerken te maken. Werk `--vnet-subnet-id` de waarde bij met de subnet-id die in de vorige stap is verzameld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Netwerken configureren - portal

De volgende schermafbeelding van de Azure-portal toont een voorbeeld van het configureren van deze instellingen tijdens het maken van AKS-cluster:

![Geavanceerde netwerkconfiguratie in de Azure-portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

De volgende vragen en antwoorden zijn van toepassing op de **Azure CNI-netwerkconfiguratie.**

* *Kan ik VM's implementeren in mijn clustersubnet?*

  Nee. Het implementeren van VM's in het subnet dat wordt gebruikt door uw Kubernetes-cluster wordt niet ondersteund. VM's kunnen worden geïmplementeerd in hetzelfde virtuele netwerk, maar in een ander subnet.

* *Kan ik netwerkbeleid per pod configureren?*

  Ja, het Kubernetes-netwerkbeleid is beschikbaar in AKS. Zie [Verkeer tussen pods beveiligen met behulp van netwerkbeleid in AKS][network-policy]om aan de slag te gaan.

* *Is het maximum aantal pods beschikbaar voor een knooppunt configureerbaar?*

  Ja, wanneer u een cluster implementeert met de sjabloon Azure CLI of Een Resource Manager. Zie [Maximumpods per knooppunt](#maximum-pods-per-node).

  U het maximum aantal pods per knooppunt op een bestaand cluster niet wijzigen.

* *Hoe configureer ik extra eigenschappen voor het subnet dat ik heb gemaakt tijdens het maken van AKS-cluster? Bijvoorbeeld serviceeindpunten.*

  De volledige lijst met eigenschappen voor het virtuele netwerk en subnetten die u maakt tijdens het maken van AKS-clusteren, kan worden geconfigureerd op de standaard pagina met virtuele netwerkconfiguratie in de Azure-portal.

* *Kan ik een ander subnet binnen mijn clustervirtuele netwerk gebruiken voor het* **Kubernetes-serviceadresbereik?**

  Het wordt niet aanbevolen, maar deze configuratie is mogelijk. Het serviceadresbereik is een set virtuele IP's (VIP's) die Kubernetes toewijst aan interne services in uw cluster. Azure Networking heeft geen inzicht in het e-segment van de service-IP-bereik van het Kubernetes-cluster. Vanwege het gebrek aan inzicht in het serviceadresbereik van het cluster, is het mogelijk om later een nieuw subnet te maken in het virtuele clusternetwerk dat overlapt met het serviceadresbereik. Als een dergelijke overlapping optreedt, kan Kubernetes een service toewijzen aan een IP die al wordt gebruikt door een andere bron in het subnet, waardoor onvoorspelbaar gedrag of fouten ontstaan. Door ervoor te zorgen dat u een adresbereik buiten het virtuele netwerk van het cluster gebruikt, u dit overlappingsrisico vermijden.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken in AKS in de volgende artikelen:

- [Een statisch IP-adres gebruiken met de Laadbalans (Azure Kubernetes Service)](static-ip.md)
- [Een interne load balancer gebruiken met Azure Container Service (AKS)](internal-lb.md)

- [Een basis-ingress-controller maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een invallende controller maken die een intern, privénetwerk en IP-adres gebruikt][aks-ingress-internal]
- [Maak een invallende controller met een dynamisch openbaar IP-adres en configureer Let's Encrypt om automatisch TLS-certificaten te genereren][aks-ingress-tls]
- [Een invallende controller maken met een statisch openbaar IP-adres en Let's Encrypt configureren om automatisch TLS-certificaten te genereren][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS-motor

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] is een open-sourceproject dat Azure Resource Manager-sjablonen genereert die u gebruiken voor het implementeren van Kubernetes-clusters op Azure.

Kubernetes-clusters die zijn gemaakt met AKS Engine ondersteunen zowel de [kubenet-][kubenet] als Azure CNI-plug-ins. [Azure CNI][cni-networking] Als zodanig worden beide netwerkscenario's ondersteund door AKS Engine.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfiel]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models

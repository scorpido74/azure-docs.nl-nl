---
title: Kubenet-netwerken configureren in Azure Kubernetes Service (AKS)
description: Meer informatie over het configureren van het kubenet-netwerk (basic) in Azure Kubernetes Service (AKS) om een AKS-cluster te implementeren in een bestaand virtueel netwerk en subnet.
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 3fe1d36b859884ab19a645e5693c7e7931fe5c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368465"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Kubenet-netwerken gebruiken met uw eigen IP-adresbereiken in Azure Kubernetes Service (AKS)

Standaard gebruiken AKS-clusters [kubenet][kubenet]en worden er een virtueel Azure-netwerk en subnet voor u gemaakt. Met *kubenet*krijgen knooppunten een IP-adres van het subnet azure virtual network. Pods krijgen een IP-adres van een logisch verschillende adresruimte van het subnet van het virtuele Azure-netwerk van de knooppunten. NAT (Network Address Translation) wordt vervolgens zo geconfigureerd dat de pods resources kunnen bereiken in het virtuele Azure-netwerk. Het bron-IP-adres van het verkeer is NAT'd naar het primaire IP-adres van het knooppunt. Deze aanpak vermindert het aantal IP-adressen dat u moet reserveren in uw netwerkruimte voor pods om te gebruiken.

Met [Azure Container Networking Interface (CNI)][cni-networking]krijgt elke pod een IP-adres van het subnet en kan deze rechtstreeks worden geopend. Deze IP-adressen moeten uniek zijn in uw netwerkruimte en moeten van tevoren worden gepland. Elk knooppunt heeft een configuratieparameter voor het maximum aantal pods dat het ondersteunt. Het equivalentaantal IP-adressen per knooppunt wordt dan vooraf gereserveerd voor dat knooppunt. Deze aanpak vereist meer planning en leidt vaak tot uitputting van IP-adressen of de noodzaak om clusters opnieuw op te bouwen in een groter subnet naarmate de vraag naar uw toepassing toont.

In dit artikel ziet u hoe u *kubenet-netwerken* gebruiken om een virtueel netwerksubnet te maken en te gebruiken voor een AKS-cluster. Zie [Netwerkconcepten voor Kubernetes en AKS voor][aks-network-concepts]meer informatie over netwerkopties en -overwegingen.

## <a name="prerequisites"></a>Vereisten

* Het virtuele netwerk voor het AKS-cluster moet uitgaande internetverbinding mogelijk maken.
* Maak niet meer dan één AKS-cluster in hetzelfde subnet.
* AKS-clusters mogen `169.254.0.0/16` `172.30.0.0/16`geen `172.31.0.0/16`gebruik `192.0.2.0/24` maken van , , of voor het bereik van de Kubernetes-serviceadres.
* De serviceprincipal die door het AKS-cluster wordt gebruikt, moet ten minste machtigingen voor [netwerkbijdragen in het](../role-based-access-control/built-in-roles.md#network-contributor) subnet binnen uw virtuele netwerk hebben. Als u een [aangepaste rol](../role-based-access-control/custom-roles.md) wilt definiëren in plaats van de ingebouwde rol netwerkbijdrager te gebruiken, zijn de volgende machtigingen vereist:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Als u groepen voor Windows Server-knooppunten wilt gebruiken (momenteel in preview in AKS), moet u Azure CNI gebruiken. Het gebruik van kubenet als netwerkmodel is niet beschikbaar voor Windows Server-containers.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.65 of hoger installeren en configureren. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Overzicht van kubenet netwerken met uw eigen subnet

In veel omgevingen hebt u virtuele netwerken en subnetten gedefinieerd met toegewezen IP-adresbereiken. Deze virtuele netwerkbronnen worden gebruikt om meerdere services en toepassingen te ondersteunen. Om netwerkconnectiviteit te bieden, kunnen AKS-clusters *kubenet* (basisnetwerken) of Azure CNI *(geavanceerde netwerken)* gebruiken.

Met *kubenet*ontvangen alleen de knooppunten een IP-adres in het virtuele netwerksubnet. Pods kunnen niet rechtstreeks met elkaar communiceren. In plaats daarvan wordt UDR (User Defined Routing) en IP forwarding gebruikt voor connectiviteit tussen pods tussen knooppunten. U ook pods implementeren achter een service die een toegewezen IP-adres ontvangt en verkeer voor de toepassing in balansbrengt. In het volgende diagram ziet u hoe de AKS-knooppunten een IP-adres ontvangen in het subnet van het virtuele netwerk, maar niet in de pods:

![Kubenet-netwerkmodel met een AKS-cluster](media/use-kubenet/kubenet-overview.png)

Azure ondersteunt maximaal 400 routes in een UDR, dus u geen AKS-cluster hebben dat groter is dan 400 knooppunten. Virtuele [KNOOPPUNTEN van][virtual-nodes] AKS en Azure-netwerkbeleid worden niet ondersteund met *kubenet.*  U [Calico Network Policies gebruiken,][calico-network-policies]omdat ze worden ondersteund met kubenet.

Met *Azure CNI*ontvangt elke pod een IP-adres in het IP-subnet en kan deze rechtstreeks communiceren met andere pods en services. Uw clusters kunnen net zo groot zijn als het IP-adresbereik dat u opgeeft. Het IP-adresbereik moet echter vooraf worden gepland en alle IP-adressen worden verbruikt door de AKS-knooppunten op basis van het maximum aantal pods dat ze kunnen ondersteunen. Geavanceerde netwerkfuncties en scenario's zoals [virtuele knooppunten][virtual-nodes] of netwerkbeleid (Azure of Calico) worden ondersteund met *Azure CNI.*

### <a name="ip-address-availability-and-exhaustion"></a>Beschikbaarheid en uitputting van IP-adres

Met *Azure CNI*is een veelvoorkomend probleem het toegewezen IP-adresbereik te klein om vervolgens extra knooppunten toe te voegen wanneer u een cluster schaalt of upgradet. Het netwerkteam kan ook niet in staat zijn om een groot genoeg IP-adresbereik uit te geven om uw verwachte toepassingsvereisten te ondersteunen.

Als compromis u een AKS-cluster maken dat *kubenet* gebruikt en verbinding maken met een bestaand virtueel netwerksubnet. Met deze benadering kunnen de knooppunten gedefinieerde IP-adressen ontvangen, zonder dat u vooraf een groot aantal IP-adressen hoeft te reserveren voor alle potentiële pods die in het cluster kunnen worden uitgevoerd.

Met *kubenet*u een veel kleiner IP-adresbereik gebruiken en grote clusters en toepassingsvereisten ondersteunen. Zelfs met een IP-adresbereik *van 27* u bijvoorbeeld een cluster met 20-25 knooppuntuitvoeren met voldoende ruimte om te schalen of te upgraden. Deze clustergrootte ondersteunt maximaal *2.200-2.750* pods (met een standaardmaximum van 110 pods per knooppunt). Het maximum aantal pods per knooppunt dat u configureren met *kubenet* in AKS is 110.

De volgende basisberekeningen vergelijken het verschil in netwerkmodellen:

- **kubenet** - een eenvoudig IP-adresbereik *van 24* kan maximaal *251* knooppunten in het cluster ondersteunen (elk subnet van het Azure-virtuele netwerk reserveert de eerste drie IP-adressen voor beheerbewerkingen)
  - Dit knooppunt telling kan tot *27.610* pods ondersteunen (met een standaard maximum van 110 pods per knooppunt met *kubenet*)
- **Azure CNI** - datzelfde basis */24-subnetbereik* kan maximaal *8* knooppunten in het cluster ondersteunen
  - Dit knooppunttelling kan slechts tot *240* pods ondersteunen (met een standaardmaximum van 30 pods per knooppunt met *Azure CNI)*

> [!NOTE]
> Deze maxima houden geen rekening met upgrade- of schaalbewerkingen. In de praktijk u het maximum aantal knooppunten dat het subnet-IP-adresbereik ondersteunt, niet uitvoeren. U moet bepaalde IP-adressen beschikbaar laten voor gebruik tijdens de schaal van upgradebewerkingen.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Virtuele netwerkpeering- en ExpressRoute-verbindingen

Om on-premises connectiviteit te bieden, kunnen zowel *kubenet-* als *Azure-CNI-netwerkbenaderingen* gebruikmaken [van Azure virtual network peering-][vnet-peering] of [ExpressRoute-verbindingen.][express-route] Plan uw IP-adresbereiken zorgvuldig om overlapping en onjuiste verkeersroutering te voorkomen. Veel on-premises netwerken gebruiken bijvoorbeeld een adresbereik *van 10.0.0.0/8* dat wordt geadverteerd via de ExpressRoute-verbinding. Het wordt aanbevolen om uw AKS-clusters te maken in virtuele netwerksubnetten van Azure buiten dit adresbereik, zoals *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Een netwerkmodel kiezen dat u wilt gebruiken

De keuze van welke netwerkplug-in te gebruiken voor uw AKS-cluster is meestal een evenwicht tussen flexibiliteit en geavanceerde configuratiebehoeften. De volgende overwegingen helpen bij het schetsen wanneer elk netwerkmodel het meest geschikt kan zijn.

Gebruik *kubenet* wanneer:

- U hebt beperkte IP-adresruimte.
- Het grootste deel van de podcommunicatie bevindt zich binnen het cluster.
- U hebt geen geavanceerde AKS-functies nodig, zoals virtuele knooppunten of Azure-netwerkbeleid.  Gebruik [calico-netwerkbeleid][calico-network-policies].

*Azure CNI gebruiken* wanneer:

- U beschikt over beschikbare IP-adresruimte.
- Het grootste deel van de podcommunicatie is naar bronnen buiten het cluster.
- U wilt de UDR's niet beheren.
- U hebt AKS-geavanceerde functies nodig, zoals virtuele knooppunten of Azure Network Policy.  Gebruik [calico-netwerkbeleid][calico-network-policies].

Zie [Netwerkmodellen vergelijken en hun ondersteuningsbereik][network-comparisons]voor meer informatie om u te helpen beslissen welk netwerkmodel u wilt gebruiken.

## <a name="create-a-virtual-network-and-subnet"></a>Een virtueel netwerk en een subnet maken

Om aan de slag te gaan met het gebruik van *kubenet* en je eigen virtuele netwerksubnet, maak je eerst een resourcegroep met de opdracht [AZ-groep maken.][az-group-create] In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Als u geen bestaand virtueel netwerk en subnet hebt om te gebruiken, maakt u deze netwerkbronnen met de opdracht [vnet create van het AZ-netwerk.][az-network-vnet-create] In het volgende voorbeeld wordt het virtuele netwerk *myVnet* genoemd met het adresvoorvoegsel van *192.168.0.0/16*. Er wordt een subnet gemaakt met de naam *myAKSSubnet* met het adresvoorvoegsel *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Een serviceprincipal maken en machtigingen toewijzen

Een AKS-cluster heeft een service-principal van Azure Active Directory nodig om met andere Azure-resources te kunnen communiceren. De serviceprincipal moet machtigingen hebben om het virtuele netwerk en subnet te beheren dat de AKS-knooppunten gebruiken. Als u een serviceprincipal wilt maken, gebruikt u de opdracht [AZ Ad SP create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

In de volgende voorbeelduitvoer worden de toepassings-id en het wachtwoord voor uw serviceprincipal weergegeven. Deze waarden worden gebruikt in aanvullende stappen om een rol toe te wijzen aan de serviceprincipal en vervolgens het AKS-cluster te maken:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Als u de juiste delegaties in de resterende stappen wilt toewijzen, gebruikt u de [vnet show-][az-network-vnet-show] en [vnet-subnetopdrachten van az-netwerk vnet][az-network-vnet-subnet-show] om de vereiste resource-id's te krijgen. Deze resource-geïdentificeerde gegevens worden opgeslagen als variabelen en worden verwezen in de resterende stappen:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Wijs nu de serviceprincipal toe voor de machtigingen *van* uw AKS-clusterbijdrager aan het virtuele netwerk met behulp van de opdracht [Voor het maken van az-rollen.][az-role-assignment-create] Geef uw eigen * \<appId->* zoals weergegeven in de uitvoer van de vorige opdracht om de serviceprincipal te maken:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Een AKS-cluster maken in het virtuele netwerk

U hebt nu een virtueel netwerk en subnet gemaakt en machtigingen gemaakt en toegewezen voor een serviceprincipal om deze netwerkbronnen te gebruiken. Maak nu een AKS-cluster in uw virtuele netwerk en subnet met behulp van de [opdracht az aks maken.][az-aks-create] Definieer uw eigen service hoofd * \<appId>* en * \<wachtwoord>, *zoals weergegeven in de uitvoer van de vorige opdracht om de service principal te maken.

De volgende IP-adresbereiken worden ook gedefinieerd als onderdeel van het proces voor het maken van cluster:

* De *--service-cidr* wordt gebruikt om interne services in het AKS-cluster een IP-adres toe te wijzen. Dit IP-adresbereik moet een adresruimte zijn die elders in uw netwerkomgeving niet wordt gebruikt. Dit bereik omvat alle on-premises netwerkbereiken als u verbinding maakt met uw virtuele Azure-netwerken of van plan bent verbinding te maken met uw Azure-netwerken via Express Route of een Site-to-Site VPN-verbinding.

* Het *IP-adres --dns-service-ip* moet het *.10-adres* van uw service-IP-adresbereik zijn.

* De *-pod-cidr* moet een grote adresruimte zijn die elders in uw netwerkomgeving niet wordt gebruikt. Dit bereik omvat alle on-premises netwerkbereiken als u verbinding maakt met uw virtuele Azure-netwerken of van plan bent verbinding te maken met uw Azure-netwerken via Express Route of een Site-to-Site VPN-verbinding.
    * Dit adresbereik moet groot genoeg zijn om het aantal knooppunten te kunnen verwerken waarop u verwacht op te schalen. U dit adresbereik niet wijzigen nadat het cluster is geïmplementeerd als u meer adressen nodig hebt voor extra knooppunten.
    * Het IP-adresbereik van de pod wordt gebruikt om een *adresruimte van /24* toe te wijzen aan elk knooppunt in het cluster. In het volgende voorbeeld wijst de *--pod-cidr* van *10.244.0.0/16* het eerste knooppunt *10.244.0.0/24*toe , het tweede knooppunt *10.244.1.0/24*, en het derde knooppunt *10.244.2.0/24*.
    * Terwijl het cluster schaalt of upgradet, blijft het Azure-platform een pod-IP-adresbereik toewijzen aan elk nieuw knooppunt.
    
* Met *het --docker-bridge-adres* kunnen de AKS-knooppunten communiceren met het onderliggende beheerplatform. Dit IP-adres mag niet binnen het ip-adresbereik van het virtuele netwerk van uw cluster vallen en mag niet overlappen met andere adresbereiken die op uw netwerk worden gebruikt.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Als u een AKS-cluster wilt inschakelen om een [Calico-netwerkbeleid][calico-network-policies] op te nemen, u de volgende opdracht gebruiken.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Wanneer u een AKS-cluster maakt, worden een netwerkbeveiligingsgroep en routetabel gemaakt. Deze netwerkbronnen worden beheerd door het AKS-controlevlak. De netwerkbeveiligingsgroep wordt automatisch gekoppeld aan de virtuele NIC's op uw knooppunten. De routetabel wordt automatisch gekoppeld aan het virtuele netwerksubnet. Regels voor netwerkbeveiliging en routetabellen en worden automatisch bijgewerkt wanneer u services maakt en blootstelt.

## <a name="next-steps"></a>Volgende stappen

Met een AKS-cluster geïmplementeerd in uw bestaande virtuele netwerksubnet, u het cluster nu normaal gebruiken. Ga aan de slag met [het bouwen van apps met Azure Dev Spaces][dev-spaces] of het gebruik van [Concept][use-draft]of het implementeren van apps [met Helm][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models

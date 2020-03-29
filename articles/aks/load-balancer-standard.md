---
title: Een Standaard SKU-loadbalancer gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het gebruik van een load balancer met een Standaard SKU om uw services bloot te leggen met Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252907"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Een Standaard SKU-loadbalancer gebruiken in Azure Kubernetes Service (AKS)

Als u toegang wilt bieden `LoadBalancer` tot toepassingen via Kubernetes-services van type in Azure Kubernetes Service (AKS), u een Azure Load Balancer gebruiken. Een load balancer die op AKS draait, kan worden gebruikt als interne of externe load balancer. Een interne load balancer maakt een Kubernetes-service alleen toegankelijk voor toepassingen die in hetzelfde virtuele netwerk worden uitgevoerd als het AKS-cluster. Een externe load balancer ontvangt een of meer openbare IP's voor binnendringen en maakt een Kubernetes-service extern toegankelijk met behulp van de openbare IP's.

Azure Load Balancer is beschikbaar in twee SKU's - *Basic* en *Standard*. Standaard wordt de *Standaard* SKU gebruikt wanneer u een AKS-cluster maakt. Het gebruik van een *Standaard* SKU-load balancer biedt extra functies en functionaliteit, zoals een grotere backendpoolgrootte en beschikbaarheidszones. Het is belangrijk dat u de verschillen tussen *standaard-* en basic-loadbalancers begrijpt voordat u kiest welke te gebruiken. *Basic* Zodra u een AKS-cluster hebt gemaakt, u de SKU voor de load balancer voor dat cluster niet meer wijzigen. Zie Azure [load balancer SKU-vergelijking][azure-lb-comparison]voor meer informatie over de *Basis-* en StandaardSKU's. *Standard*

In dit artikel wordt uitgegaan van een basiskennis van Kubernetes- en Azure Load Balancer-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] en [Wat is Azure Load Balancer voor][azure-lb]meer informatie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.81 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een AKS-cluster hebt met de *Standaard* SKU Azure Load Balancer. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

De AKS-clusterserviceprincipal heeft ook toestemming nodig om netwerkbronnen te beheren als u een bestaand subnet of resourcegroep gebruikt. Wijs de rol *Netwerkbijdrager toe* aan uw serviceprincipal aan de gedelegeerde resources. Zie [AKS-toegang tot andere Azure-bronnen voor][aks-sp]meer informatie over machtigingen.

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Overstappen van een Basic SKU Load Balancer naar Standard SKU

Als u een bestaand cluster hebt met de BasisSKU-loadbalancer, zijn er belangrijke gedragsverschillen om op te merken bij het migreren naar een cluster met de standaard SKU-loadbalancer.

Het maken van blauw/groene implementaties om clusters te `load-balancer-sku` migreren is bijvoorbeeld een gangbare praktijk, omdat het type cluster alleen kan worden gedefinieerd op de tijd voor het maken van clusters. Basic *SKU* Load Balancers gebruiken echter *Basic SKU* IP-adressen die niet compatibel zijn met *standaard SKU* Load Balancers omdat ze *standaard SKU* IP-adressen vereisen. Bij het migreren van clusters om Load Balancer SKU's te upgraden, is een nieuw IP-adres met een compatibele IP-adres SKU vereist.

Ga voor meer overwegingen over het migreren van clusters naar [onze documentatie over migratieoverwegingen](aks-migration.md) om een lijst met belangrijke onderwerpen te bekijken waarmee u rekening moet houden bij het migreren. De onderstaande beperkingen zijn ook belangrijke gedragsverschillen op te merken bij het gebruik van Standaard SKU Load Balancers in AKS.

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die een load balancer ondersteunen met de *Standaard* SKU:

* Er is ten minste één openbaar IP- of IP-voorvoegsel vereist voor het toestaan van uitgangsverkeer uit het AKS-cluster. Het openbare IP- of IP-voorvoegsel is ook vereist om de connectiviteit tussen het besturingsvlak en de agentknooppunten te behouden en om de compatibiliteit met eerdere versies van AKS te behouden. U hebt de volgende opties voor het opgeven van openbare IP-adressen of IP-voorvoegsels met een *standaard* SKU-loadbalancer:
    * Zorg voor uw eigen openbare IP's.
    * Geef uw eigen openbare IP-voorvoegsels op.
    * Geef een getal op tot 100 zodat het AKS-cluster zoveel standaard-SKU-openbare IP's kan maken in dezelfde resourcegroep die is gemaakt als het AKS-cluster, dat meestal wordt genoemd met *MC_* aan het begin. *Standard* AKS wijst het openbare IP toe aan de *Standaard* SKU load balancer. Standaard wordt er automatisch één openbaar IP-adres gemaakt in dezelfde resourcegroep als het AKS-cluster, als er geen openbaar IP-, openbaar IP-voorvoegsel of aantal IP-adressen is opgegeven. U moet ook openbare adressen toestaan en voorkomen dat u een Azure-beleid maakt dat het maken van INTELLECTUELE-eigendom verbiedt.
* Wanneer u de *Standaard* SKU voor een load balancer gebruikt, moet u Kubernetes-versie *1.13 of hoger*gebruiken.
* Het definiëren van de load balancer SKU kan alleen worden gedaan wanneer u een AKS-cluster maakt. U de SKU van de load balancer niet wijzigen nadat een AKS-cluster is gemaakt.
* U slechts één type load balancer SKU (Basic of Standard) in één cluster gebruiken.
* *Standaard* SKU Load Balancers ondersteunen alleen *Standaard* SKU IP-adressen.

## <a name="use-the-standard-sku-load-balancer"></a>De *standaard* SKU-loadbalancer gebruiken

Wanneer u een AKS-cluster maakt, wordt standaard de *Standaard* SKU-loadbalancer gebruikt wanneer u services uitvoert in dat cluster. Met [de quickstart met Azure CLI][aks-quickstart-cli] wordt bijvoorbeeld een voorbeeldtoepassing geïmplementeerd die de standaardSKU-loadbalancer gebruikt. *Standard*

> [!IMPORTANT]
> Openbare IP-adressen kunnen worden vermeden door een door de gebruiker gedefinieerde route (UDR) aan te bieden. Als u het uitgaande type van een AKS-cluster opgeeft, kan UDR ip-inrichting en backendpoolsetup overslaan voor de door AKS gemaakte Azure-load balancer. Zie [het instellen `outboundType` van een cluster op 'userDefinedRouting'.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>De load balancer configureren als intern

U de load balancer ook configureren als intern en geen openbaar IP.You can also configure the load balancer to be internal and not expose a public IP. Als u de load balancer `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` als intern wilt configureren, voegt u als annotatie toe aan de *loadbalancer-service.* U een voorbeeld yaml manifest evenals meer details over een interne load balancer [hier][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Het aantal beheerde openbare IP's schalen

Wanneer u een *Standaard* SKU-load balancer gebruikt met beheerde uitgaande openbare IP-adressen, die standaard worden gemaakt, u het aantal beheerde uitgaande openbare IP-adressen schalen met behulp van de parameter *load-balancer-managed-ip-count.*

Als u een bestaand cluster wilt bijwerken, voert u de volgende opdracht uit. Deze parameter kan ook worden ingesteld op cluster create-time om meerdere beheerde uitgaande openbare IP's te hebben.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

In het bovenstaande voorbeeld wordt het aantal beheerde uitgaande openbare IP-adressen ingesteld op *2* voor het *myAKSCluster-cluster* in *myResourceGroup.* 

You can also use the *load-balancer-managed-ip-count* parameter to set the initial number of managed outbound public IPs when creating your cluster by appending the `--load-balancer-managed-outbound-ip-count` parameter and setting it to your desired value. Het standaardaantal beheerde uitgaande openbare IP's is 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Geef uw eigen openbare IP's of voorvoegsels voor uitgaande

Wanneer u een *Standaard* SKU-loadbalancer gebruikt, maakt het AKS-cluster automatisch een openbaar IP-adres in dezelfde resourcegroep die is gemaakt voor het AKS-cluster en wijst het openbare IP toe aan de *standaard* SKU-loadbalancer. U ook uw eigen openbare IP toewijzen op het moment van het maken van een cluster of u de eigenschappen van de load balancer van een bestaand cluster bijwerken.

Door meerdere IP-adressen of voorvoegsels mee te nemen, u meerdere backservices definiëren bij het definiëren van het IP-adres achter één load balancer-object. Het uitgangseindpunt van specifieke knooppunten is afhankelijk van aan welke service ze zijn gekoppeld.

> [!IMPORTANT]
> U moet *standaard* IP-adressen van SKU-adressen gebruiken om met uw *Standaard* SKU uw load balancer te gebruiken. U de SKU van uw openbare IP-adressen verifiëren met de opdracht [public-ip show van het AZ-netwerk:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Gebruik de [opdracht public-ip show van het AZ-netwerk][az-network-public-ip-show] om de id's van uw openbare IP-adressen weer te geven.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

In de bovenstaande opdracht wordt de id voor het *ip-adres van myPublicIP* in de brongroep *myResourceGroup* weergegeven.

Gebruik de opdracht *az aks-update* met de parameter *load-balancer-outbound-ips* om uw cluster bij te werken met uw openbare IP's.

In het volgende voorbeeld wordt de parameter *load-balancer-outbound-ips* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

U ook openbare IP-voorvoegsels gebruiken om uit te gaan met uw *Standaard* SKU-loadbalancer. In het volgende voorbeeld wordt de [opdracht public-ip-voorvoegsel az-netwerk gebruikt][az-network-public-ip-prefix-show] om de i-adressen van uw openbare IP-voorvoegsels weer te geven:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

In de bovenstaande opdracht wordt de id voor het openbare IP-voorvoegsel *van myPublicIP-voorvoegsel* weergegeven in de brongroep *myResourceGroup.*

In het volgende voorbeeld wordt de parameter *load-balancer-outbound-ip-prefixes* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> De openbare IP-adressen en IP-voorvoegsels moeten zich in dezelfde regio bevinden en deel uitmaken van hetzelfde abonnement als uw AKS-cluster. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Uw eigen openbare IP- of voorvoegsels definiëren op de tijd voor het maken van clusteren

U uw eigen IP-adressen of IP-voorvoegsels meenemen voor het maken van het clusteromtewerk om scenario's zoals het whitelisten van uitgangspunten te ondersteunen. Voeg dezelfde parameters toe die hierboven worden weergegeven aan de stap voor het maken van een cluster om uw eigen openbare IP-voorvoegsels en IP-voorvoegsels aan het begin van de levenscyclus van een cluster te definiëren.

Gebruik de opdracht *az aks create* met de parameter *load-balancer-outbound-ips* om een nieuw cluster te maken met uw openbare IP's aan het begin.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Gebruik de opdracht *az aks create* met de parameter *load-balancer-outbound-ip-prefixes* om een nieuw cluster te maken met uw openbare IP-voorvoegsels aan het begin.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Uitgaande poorten en inactieve time-out configureren

> [!WARNING]
> De volgende sectie is bedoeld voor geavanceerde scenario's van grootschaligenetwerken op grotere schaal of voor het aanpakken van SNAT-uitputtingsproblemen met de standaardconfiguraties. U moet een nauwkeurige voorraad van beschikbare quota voor VM's en IP-adressen hebben voordat *u AltoegewezenOutboundPorts* of *IdleTimeoutInMinutes* wijzigt van de standaardwaarde om gezonde clusters te behouden.
> 
> Als u de waarden *voor AllocatedOutboundPorts* en *IdleTimeoutInMinutes wijzigt,* kan het gedrag van de uitgaande regel voor uw load balancer aanzienlijk worden gewijzigd. Bekijk de [uitgaande regels load balancer,][azure-lb-outbound-rules-overview] [de uitgaande regels voor load Balancer][azure-lb-outbound-rules]en [uitgaande verbindingen in Azure][azure-lb-outbound-connections] voordat u deze waarden bijwerkt om de impact van uw wijzigingen volledig te begrijpen.

Uitgaande toegewezen poorten en hun niet-actieve time-outs worden gebruikt voor [SNAT][azure-lb-outbound-connections]. Standaard gebruikt de *Standaard* SKU-load balancer [automatische toewijzing voor het aantal uitgaande poorten op basis van de grootte van de backendpool][azure-lb-outbound-preallocatedports] en een idle time-out van 30 minuten voor elke poort. Als u deze waarden wilt zien, gebruikt u [de lijst met outbound-regel][az-network-lb-outbound-rule-list] van AZ Network LB om de uitgaande regel voor de load balancer weer te geven:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

In de vorige opdrachten wordt de regel voor uitgaande regel voor uw load balancer weergegeven, bijvoorbeeld:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

De voorbeelduitvoer toont de standaardwaarde voor *AllocatedOutboundPorts* en *IdleTimeoutInMinutes*. Met een waarde van 0 voor *AllocatedOutboundPorts* wordt het aantal uitgaande poorten ingesteld met automatische toewijzing voor het aantal uitgaande poorten op basis van de grootte van de backendpool. Als het cluster bijvoorbeeld 50 of minder knooppunten heeft, worden 1024 poorten voor elk knooppunt toegewezen.

Overweeg de instelling van *toegewezenOutboundPorts* of *IdleTimeoutInMinutes* te wijzigen als u verwacht te worden geconfronteerd met SNAT-uitputting op basis van de bovenstaande standaardconfiguratie. Elk extra IP-adres maakt 64.000 extra poorten mogelijk voor toewijzing, maar de Azure Standard Load Balancer verhoogt de poorten per knooppunt niet automatisch wanneer er meer IP-adressen worden toegevoegd. U deze waarden wijzigen door de *load-balancer-outbound-ports* en *load-balancer-idle-timeout parameters in* te stellen. Bijvoorbeeld:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> U moet [het vereiste quotum berekenen][calculate-required-quota] voordat u toegewezen *OutboundPorts* aanwerkt om verbindings- of schalingsproblemen te voorkomen. De waarde die u opgeeft voor *toegewezenOutboundPorts* moet ook een veelvoud van 8 zijn.

U ook de *load-balancer-outbound-ports* en *load-balancer-idle-timeout parameters* gebruiken bij het maken van een cluster, maar u moet ook *load-balancer-managed-outbound-ip-count,* *load-balancer-outbound-ips*of *load-balancer-outr-outbound-ip-prefixes* ook opgeven.  Bijvoorbeeld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Bij het wijzigen van de *load-balancer-outbound-ports* en *load-balancer-idle-timeout* parameters van hun standaard, is dit van invloed op het gedrag van het load balancer-profiel, dat van invloed is op het hele cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Vereist quotum voor het aanpassen van toegewezenOutboundPorts
U moet voldoende uitgaande IP-capaciteit hebben op basis van het aantal vm's van uw knooppunt en de gewenste toegewezen uitgaande poorten. Als u wilt valideren dat u over voldoende uitgaande IP-capaciteit beschikt, gebruikt u de volgende formule: 
 
*outboundIPs* \* 64.000 \> *nodeVMs* \* *gewenstAllocatedOutboundPorts*.
 
Als u bijvoorbeeld 3 *nodeVM's*en 50.000 *gewenste ToegewezenOutboundPorts*hebt, moet u ten minste 3 *uitgaande IP's*hebben. Het wordt aanbevolen dat u extra uitgaande IP-capaciteit opte nemen dan wat je nodig hebt. Daarnaast moet u rekening houden met de clusterautoscaler en de mogelijkheid van node pool-upgrades bij het berekenen van uitgaande IP-capaciteit. Controleer voor de clusterautoscaler het huidige knooppuntaantal en het maximale aantal nodes en gebruik de hogere waarde. Voor het upgraden u rekening houden met een extra knooppuntVM voor elke knooppuntgroep waarmee u upgraden.
 
Wanneer *u IdleTimeoutInMinutes* instelt op een andere waarde dan de standaardwaarde van 30 minuten, moet u overwegen hoe lang uw workloads een uitgaande verbinding nodig hebben. Houd ook rekening met de standaard time-outwaarde voor een *Standaard* SKU-loadbalancer die buiten AKS wordt gebruikt, is 4 minuten. Een *IdleTimeoutInMinutes-waarde* die uw specifieke AKS-workload nauwkeuriger weergeeft, kan helpen de snat-uitputting te verminderen die wordt veroorzaakt door dat verbindingen niet meer worden gebruikt.

## <a name="restrict-access-to-specific-ip-ranges"></a>Toegang tot specifieke IP-bereiken beperken

De Network Security Group (NSG) die is gekoppeld aan het virtuele netwerk voor de load balancer, heeft standaard een regel om al het inkomende externe verkeer toe te staan. U deze regel bijwerken om alleen specifieke IP-bereiken toe te staan voor binnenkomend verkeer. In het volgende manifest wordt *loadBalancerSourceRanges* gebruikt om een nieuw IP-bereik op te geven voor binnenkomend extern verkeer:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

In het bovenstaande voorbeeld wordt de regel bijgewerkt om alleen binnenkomend extern verkeer uit het *MY_EXTERNAL_IP_RANGE-bereik* toe te staan. Meer informatie over het gebruik van deze methode om de toegang tot de load balancer-service te beperken is beschikbaar in de [Kubernetes-documentatie.][kubernetes-cloud-provider-firewall]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes-services vindt u in de [documentatie van Kubernetes Services.][kubernetes-services]

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports

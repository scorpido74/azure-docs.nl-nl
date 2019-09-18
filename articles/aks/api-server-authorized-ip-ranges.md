---
title: Door API server geautoriseerde IP-bereiken in azure Kubernetes service (AKS)
description: Meer informatie over het beveiligen van uw cluster met behulp van een IP-adres bereik voor toegang tot de API-server in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 59e64b7c84e589da57ea28d6655c9305f4fdc101
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058346"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Voor beeld-veilige toegang tot de API-server met behulp van geautoriseerde IP-adresbereiken in azure Kubernetes service (AKS)

In Kubernetes ontvangt de API-server aanvragen voor het uitvoeren van acties in het cluster, zoals het maken van resources of het schalen van het aantal knoop punten. De API-server is de centrale manier om met een cluster te communiceren en te beheren. Als u de beveiliging van het cluster wilt verbeteren en aanvallen wilt minimaliseren, moet de API-server alleen toegankelijk zijn vanuit een beperkt aantal IP-adresbereiken.

In dit artikel wordt beschreven hoe u geautoriseerde IP-adresbereiken van de API-server gebruikt om aanvragen voor het beheren van het vlak te beperken. Deze functie is momenteel beschikbaar als preview-product.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid][aks-support-policies]
> * [Veelgestelde vragen over ondersteuning voor Azure][aks-faq]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u werkt met clusters die gebruikmaken van [kubenet][kubenet].  Met [Azure container Networking interface (cni)][cni-networking] gebaseerde clusters hebt u niet de vereiste route tabel nodig om de toegang te beveiligen.  U moet de route tabel hand matig maken.  Zie [route tabellen beheren](https://docs.microsoft.com/azure/virtual-network/manage-route-table) voor meer informatie.

Door de API-server geautoriseerde IP-adresbereiken werken alleen voor nieuwe AKS-clusters die u maakt. In dit artikel wordt beschreven hoe u een AKS-cluster maakt met behulp van de Azure CLI.

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>AKS-preview CLI-extensie installeren

Als u geautoriseerde IP-adresbereiken van de API-server wilt configureren, hebt u de *AKS-preview cli-* extensie versie 0.4.1 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Functie vlag voor uw abonnement registreren

Als u geautoriseerde IP-adresbereiken van de API-server wilt gebruiken, moet u eerst een functie vlag voor uw abonnement inschakelen. Als u de functie vlag *APIServerSecurityPreview* wilt registreren, gebruikt u de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

> [!CAUTION]
> Wanneer u een functie op een abonnement registreert, kunt u de registratie van die functie op dit moment niet ongedaan maken. Nadat u enkele preview-functies hebt ingeschakeld, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Het duurt enkele minuten voordat de status is *geregistreerd*. U kunt de registratie status controleren met de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u de API-server geautoriseerde IP-bereiken configureert:

* U kunt momenteel geen Azure dev-ruimten gebruiken omdat de communicatie met de API-server ook wordt geblokkeerd.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Overzicht van door de API server geautoriseerde IP-adresbereiken

De Kubernetes API-server is hoe de onderliggende Kubernetes-Api's worden weer gegeven. Dit onderdeel biedt de interactie voor beheer hulpprogramma's, zoals `kubectl` of het Kubernetes-dash board. AKS biedt een cluster Master met één Tenant, met een speciale API-server. De API-server krijgt standaard een openbaar IP-adres en u moet de toegang beheren met behulp van op rollen gebaseerd toegangs beheer (RBAC).

Als u de toegang tot de anderszins toegankelijke AKS Control vlak/API-server wilt beveiligen, kunt u geautoriseerde IP-bereiken inschakelen en gebruiken. Met deze geautoriseerde IP-bereiken zijn alleen gedefinieerde IP-adresbereiken toegestaan om te communiceren met de API-server. Een aanvraag naar de API-server vanaf een IP-adres dat geen deel uitmaakt van deze toegestane IP-bereiken, wordt geblokkeerd. U moet RBAC blijven gebruiken om gebruikers en de acties die ze aanvragen te autoriseren.

Zie [Kubernetes core-concepten voor AKS][concepts-clusters-workloads]voor meer informatie over de API-server en andere cluster onderdelen.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Door API server geautoriseerde IP-adresbereiken werken alleen voor nieuwe AKS-clusters. Het is niet mogelijk om gemachtigde IP-bereiken in te scha kelen als onderdeel van de bewerking cluster create. Als u probeert toegestane IP-bereiken in te scha kelen als onderdeel van het cluster proces maken, hebben de cluster knooppunten tijdens de implementatie geen toegang tot de API-server omdat het uitgaande IP-adres op dat moment niet is gedefinieerd.

Maak eerst een cluster met behulp van de opdracht [AZ AKS Create][az-aks-create] . In het volgende voor beeld wordt een cluster met één knoop punt met de naam *myAKSCluster* gemaakt in de resource groep met de naam *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Uitgaande gateway maken voor firewall regels

Om ervoor te zorgen dat de knoop punten in een cluster betrouwbaar kunnen communiceren met de API-server wanneer u toegestane IP-bereiken in de volgende sectie inschakelt, maakt u een Azure-Firewall voor gebruik als uitgaande gateway. Het IP-adres van de Azure-firewall wordt vervolgens toegevoegd aan de lijst met geautoriseerde API-server-IP-adressen in de volgende sectie.

> [!WARNING]
> Door het gebruik van Azure Firewall kunnen aanzienlijke kosten in rekening worden gebracht voor een maandelijkse facturerings cyclus. De vereiste voor het gebruik van Azure Firewall hoeft alleen te worden gebruikt in deze eerste preview-periode. Zie voor meer informatie en kosten planning [Azure firewall prijzen][azure-firewall-costs].
>
> Als uw cluster gebruikmaakt van de [standaard SKU Load Balancer][standard-sku-lb], hoeft u de Azure firewall niet als de uitgaande gateway te configureren. Gebruik [AZ Network Public-IP List][az-network-public-ip-list] en geef de resource groep van uw AKS-cluster op, dat meestal begint met *MC_* . Hier wordt het open bare IP-adres voor uw cluster weer gegeven, dat u kunt white list. Bijvoorbeeld:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

Haal eerst de naam van de *MC_* -resource groep op voor het AKS-cluster en het virtuele netwerk. Maak vervolgens een subnet met behulp van de opdracht [AZ Network vnet subnet Create][az-network-vnet-subnet-create] . In het volgende voor beeld wordt een subnet met de naam *AzureFirewallSubnet* gemaakt met het CIDR *-bereik 10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Als u een Azure Firewall wilt maken, installeert u de *Azure-firewall cli-* extensie met behulp van de opdracht [AZ extension add][az-extension-add] . Maak vervolgens een firewall met behulp van de opdracht [AZ Network Firewall Create][az-network-firewall-create] . In het volgende voor beeld wordt een Azure-firewall gemaakt met de naam *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Een Azure-firewall wordt toegewezen aan een openbaar IP-adres dat verkeer uitloopt via. Maak een openbaar adres met behulp van de opdracht [AZ Network Public-IP Create][az-network-public-ip-create] en maak vervolgens een IP-configuratie op de firewall met behulp van de [AZ Network Firewall IP-config Create][az-network-firewall-ip-config-create] die de open bare IP toepast:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Maak nu de Azure firewall-netwerk regel om alle *TCP* -verkeer *toe te staan* met behulp van de opdracht [AZ Network Firewall Network-Rule Create][az-network-firewall-network-rule-create] . In het volgende voor beeld wordt een netwerk regel gemaakt met de naam *AllowTCPOutbound* voor verkeer met een bron-of doel adres:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Als u de Azure-firewall wilt koppelen aan de netwerk route, haalt u de bestaande gegevens van de route tabel, het interne IP-adres van de Azure-firewall en vervolgens het IP-adres van de API-server op. Deze IP-adressen worden in de volgende sectie opgegeven om te bepalen hoe het verkeer voor cluster communicatie moet worden gerouteerd.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Tot slot maakt u een route in de bestaande AKS-netwerk route tabel met behulp van de opdracht [AZ Network route tabel route Create][az-network-route-table-route-create] waarmee verkeer het Azure-firewall apparaat voor API-server communicatie kan gebruiken.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Noteer het open bare IP-adres van uw Azure Firewall-apparaat. Dit adres wordt toegevoegd aan de lijst met door de API server geautoriseerde IP-adresbereiken in de volgende sectie.

## <a name="enable-authorized-ip-ranges"></a>Toegestane IP-bereiken inschakelen

U geeft een lijst met geautoriseerde IP-adresbereiken op om de API-server geautoriseerde IP-bereiken in te scha kelen. Wanneer u een CIDR-bereik opgeeft, begint u met het eerste IP-adres in het bereik. *137.117.106.90/29* is bijvoorbeeld een geldig bereik, maar zorg ervoor dat u het eerste IP-adres in het bereik opgeeft, zoals *137.117.106.88/29*.

Gebruik [AZ AKS update][az-aks-update] opdracht en geef de *-API-server-geautoriseerde IP-bereiken* op die u wilt toestaan. Deze IP-adresbereiken zijn meestal adresbereiken die worden gebruikt door uw on-premises netwerken. Voeg het open bare IP-adres van uw eigen Azure-firewall toe die is verkregen in de vorige stap, zoals *20.42.25.196/32*.

In het volgende voor beeld worden geautoriseerde IP-adresbereiken van de API-server ingeschakeld in het cluster met de naam *myAKSCluster* in de resource groep genaamd *myResourceGroup*. De IP-adresbereiken die u wilt autoriseren, zijn *20.42.25.196/32* (het open bare IP-adres van Azure firewall), vervolgens *172.0.0.0/16* en *168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Gemachtigde IP-bereiken bijwerken of uitschakelen

Als u geautoriseerde IP-bereiken wilt bijwerken of uitschakelen, gebruikt u de opdracht [AZ AKS update][az-aks-update] . Geef het bijgewerkte CIDR-bereik op dat u wilt toestaan of geef een leeg bereik op om IP-adresbereiken van de API-server uit te scha kelen, zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de API-server geautoriseerde IP-adresbereiken ingeschakeld. Deze benadering is een onderdeel van hoe u een veilig AKS-cluster kunt uitvoeren.

Zie voor meer informatie [beveiligings concepten voor toepassingen en clusters in AKS][concepts-security] en [Aanbevolen procedures voor het beveiligen van het cluster en upgrades in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md

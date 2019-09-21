---
title: Een standaard SKU-load balancer gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het gebruik van een load balancer met een standaard-SKU om uw services beschikbaar te maken met Azure Kubernetes service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: d2a0ff5db6707c4f765c71937a7d0f0749401959
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172211"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Een standaard SKU-load balancer gebruiken in azure Kubernetes service (AKS)

Om toegang te bieden tot uw toepassingen in azure Kubernetes service (AKS), kunt u een Azure Load Balancer maken en gebruiken. Een load balancer die wordt uitgevoerd op AKS kan worden gebruikt als een intern of een extern load balancer. Een interne load balancer maakt een Kubernetes-service alleen toegankelijk voor toepassingen die worden uitgevoerd in hetzelfde virtuele netwerk als het AKS-cluster. Een externe load balancer ontvangt een of meer open bare Ip's voor binnenkomend verkeer en maakt een Kubernetes-service extern toegankelijk met behulp van de open bare Ip's.

Azure Load Balancer is beschikbaar in twee Sku's: *Basic* en *Standard*. Standaard wordt de *basis* -SKU gebruikt wanneer een service manifest wordt gebruikt om een Load Balancer te maken op AKS. Het gebruik van een *standaard* -SKU Load Balancer biedt extra functies en functionaliteit, zoals een grotere back-end-pool grootte en Beschikbaarheidszones. Het is belang rijk dat u de verschillen tussen de load balancers *Standard* en *Basic* begrijpt voordat u kiest voor gebruik. Wanneer u een AKS-cluster hebt gemaakt, kunt u de load balancer SKU voor dat cluster niet wijzigen. Zie voor meer informatie over de *Basic* -en *Standard* -sku's [Azure Load Balancer SKU-vergelijking][azure-lb-comparison].

In dit artikel wordt beschreven hoe u een Azure Load Balancer maakt en gebruikt met de *standaard* -SKU met Azure Kubernetes service (AKS).

In dit artikel wordt ervan uitgegaan dat u basis informatie krijgt over Kubernetes-en Azure Load Balancer concepten. Zie [Kubernetes core-concepten voor Azure Kubernetes service (AKS)][kubernetes-concepts] en [wat Azure Load Balancer?][azure-lb]voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.0.59 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

De AKS-Cluster service-principal moet machtigingen hebben om netwerk bronnen te beheren als u een bestaand subnet of een bestaande resource groep gebruikt. In het algemeen wijst u de rol *netwerk bijdrage* toe aan uw service-principal op de gedelegeerde resources. Zie [AKS toegang tot andere Azure-resources delegeren][aks-sp]voor meer informatie over machtigingen.

U moet een AKS-cluster maken dat de SKU voor de load balancer instelt op *Standard* in plaats van de standaard *basis*.

### <a name="install-aks-preview-cli-extension"></a>AKS-preview CLI-extensie installeren

Als u de standaard-SKU van Azure load balancer wilt gebruiken, hebt u de *AKS-preview cli-* extensie versie 0.4.12 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor een load balancer met de *standaard* -SKU:

* Er is ten minste één openbaar IP-of IP-voor voegsel vereist voor het toestaan van uitgaand verkeer van het AKS-cluster. Het open bare IP-of IP-voor voegsel is ook vereist voor het onderhouden van de connectiviteit tussen het besturings vlak en de agent knooppunten, en voor het behouden van de compatibiliteit met eerdere versies van AKS. U hebt de volgende opties voor het opgeven van open bare Ip's of IP-voor voegsels met een *standaard* -SKU Load Balancer:
    * Geef uw eigen open bare Ip's op.
    * Geef uw eigen open bare IP-voor voegsels op.
    * Geef een waarde op van Maxi maal 100 zodat het AKS-cluster ervoor kan zorgen dat veel *standaard* -SKU open bare ip's in dezelfde resource groep zijn gemaakt als het AKS-cluster, die meestal met *MC_* aan het begin wordt genoemd. AKS wijst het open bare IP-adres toe aan de *standaard* -SKU Load Balancer. Standaard wordt één openbaar IP-adres automatisch gemaakt in dezelfde resource groep als het AKS-cluster als er geen openbaar IP-adres, openbaar IP-voor voegsel of aantal Ip's is opgegeven. U moet ook open bare adressen toestaan en voor komen dat u een Azure Policy maakt waardoor het IP-adres niet kan worden gemaakt.
* Wanneer u de *standaard* -SKU voor een Load Balancer gebruikt, moet u Kubernetes versie 1,13 of hoger gebruiken.
* Het definiëren van de load balancer SKU kan alleen worden uitgevoerd wanneer u een AKS-cluster maakt. U kunt de load balancer SKU niet wijzigen nadat er een AKS-cluster is gemaakt.
* U kunt slechts één load balancer SKU in één cluster gebruiken.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie worden de meta gegevens van de resource groep opgeslagen, maar ook de resources die in Azure worden uitgevoerd als u geen andere regio opgeeft tijdens het maken van resources. Maak een resource groep met de opdracht [AZ Group Create][az-group-create] .

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>AKS-cluster maken
Als u een AKS-cluster wilt uitvoeren dat een load balancer met de *standaard* -SKU ondersteunt, moet uw cluster de para meter *Load Balancer-SKU* instellen op *Standard*. Met deze para meter maakt u een load balancer met de *standaard* -SKU wanneer het cluster wordt gemaakt. Wanneer u een *Load Balancer* -service op uw cluster uitvoert, wordt de configuratie van de *standaard* -SKU Load Balancer bijgewerkt met de configuratie van de service. Gebruik de opdracht [AZ AKS Create][az-aks-create] om een AKS-cluster te maken met de naam *myAKSCluster*.

> [!NOTE]
> De *Load Balancer-SKU-* eigenschap kan alleen worden gebruikt wanneer het cluster is gemaakt. U kunt de load balancer SKU niet wijzigen nadat er een AKS-cluster is gemaakt. U kunt ook slechts één type load balancer SKU gebruiken in één cluster.
> 
> Als u uw eigen open bare Ip's wilt gebruiken, gebruikt u de para meters *Load Balancer-uitgaand-ip's*of *Load Balancer-uitgaand-IP-voor voegsels* . Beide para meters kunnen ook worden gebruikt bij [het bijwerken van het cluster](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdracht regel-client. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [AZ AKS install-cli][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u het enkele knooppunt dat is gemaakt in de vorige stappen. Zorg ervoor dat de status van het knooppunt *Ready* is:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Controleren of uw cluster gebruikmaakt van de *standaard* -SKU

Gebruik de [AZ AKS show][az-aks-show] om de configuratie van uw cluster weer te geven.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Controleer of de eigenschap *loadBalancerSku* wordt weer gegeven als *standaard*.

## <a name="use-the-load-balancer"></a>De load balancer gebruiken

Als u de load balancer op uw cluster wilt gebruiken, maakt u een service manifest met het Service type *LoadBalancer*. Als u wilt weer geven load balancer werkt, maakt u een nieuw manifest met een voorbeeld toepassing die u in uw cluster kunt uitvoeren. Deze voorbeeld toepassing wordt weer gegeven via de load balancer en kan worden weer gegeven via een browser.

Maak een manifest met `sample.yaml` de naam zoals wordt weer gegeven in het volgende voor beeld:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

In het bovenstaande manifest worden twee implementaties geconfigureerd: *Azure-stem voor* en *Azure-stem*. Als u de implementatie van *Azure-stemmen-front* wilt configureren om te worden weer gegeven met behulp van de Load Balancer, maakt u een manifest met de naam `standard-lb.yaml` zoals weer gegeven in het volgende voor beeld:

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
```

De service *Azure-stemmen-front* maakt gebruik van het *Load Balancer* -type om de Load Balancer op uw AKS-cluster te configureren om verbinding te maken met de implementatie van *Azure-stemmen-front* .

Implementeer de voorbeeld toepassing en load balancer met behulp van de [kubectl-toepassing][kubectl-apply] en geef de naam op van de yaml-manifesten:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

De *standaard* -SKU Load Balancer is nu geconfigureerd om de voorbeeld toepassing beschikbaar te maken. Bekijk de service Details van *Azure-stem vooraan* met behulp van [kubectl Get][kubectl-get] om het open bare IP-adres van de Load Balancer te zien. Het open bare IP-adres van de load balancer wordt weer gegeven in de kolom *extern-IP* . Het kan een paar minuten duren voordat het IP-adres is gewijzigd van  *\<in\> behandeling* naar een werkelijk extern IP-adres, zoals wordt weer gegeven in het volgende voor beeld:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Ga in een browser naar het open bare IP-adres en controleer of u de voorbeeld toepassing ziet. In het bovenstaande voor beeld is `52.179.23.131`het open bare IP-adres.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> U kunt ook configureren dat de load balancer intern is en geen openbaar IP-adres weergeeft. Als u de Load Balancer als intern wilt configureren `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` , voegt u als aantekening toe aan de *Load Balancer* -service. [Hier][internal-lb-yaml]ziet u een voor beeld van een yaml-manifest, evenals meer informatie over een intern Load Balancer.

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Optioneel: het aantal beheerde open bare Ip's schalen

Wanneer u een *standaard* -SKU Load Balancer met beheerde uitgaande open bare ip's, die standaard worden gemaakt, kunt u het aantal beheerde uitgaande open bare ip's schalen met behulp van de para meter *Load-Balancer-managed-IP-Count* .

Voer de volgende opdracht uit om een bestaand cluster bij te werken. Deze para meter kan ook op een cluster worden ingesteld voor het maken van meerdere beheerde open bare IP-adressen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

In het bovenstaande voor beeld wordt het aantal beheerde uitgaande open bare Ip's ingesteld op *2* voor het *myAKSCluster* -cluster in *myResourceGroup*. 

U kunt ook de para meter met *taak verdeling-beheerde IP-aantal* gebruiken om het eerste aantal beheerde uitgaande open bare IP-adressen in te stellen bij het maken van het `--load-balancer-managed-outbound-ip-count` cluster door de para meter toe te voegen en op de gewenste waarde in te stellen. Het standaard aantal beheerde uitgaande open bare Ip's is 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Optioneel: Geef uw eigen open bare Ip's of voor voegsels op voor uitgang

Wanneer u een *standaard* -SKU Load Balancer gebruikt, wordt in het AKS-cluster automatisch een openbaar IP-adres gemaakt in dezelfde resource groep die is gemaakt voor het AKS-cluster en wordt het open bare IP-adres toegewezen aan de *standaard* -SKU Load Balancer. U kunt ook uw eigen open bare IP toewijzen tijdens het maken van het cluster of u kunt de load balancer eigenschappen van een bestaand cluster bijwerken.

Door meerdere IP-adressen of voor voegsels te maken, kunt u meerdere services voor het aanbrengen van een back-up definiëren wanneer u het IP-adres achter een enkel load balancer-object definieert. Het uitgaand eind punt van specifieke knoop punten is afhankelijk van de service waaraan ze zijn gekoppeld.

> [!IMPORTANT]
> U moet de *standaard* -SKU open bare ip's gebruiken om uw Load Balancer te doen met uw *standaard* SKU. U kunt de SKU van uw open bare Ip's controleren met behulp van de opdracht [AZ Network public-ip show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Gebruik de opdracht [AZ Network public-ip show][az-network-public-ip-show] om de id's van uw open bare ip's weer te geven.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

De bovenstaande opdracht toont de ID voor het open bare IP-adres *myPublicIP* in de resource groep *myResourceGroup* .

Gebruik de opdracht *AZ AKS update* met de para meter *Load-Balancer-outbound-ip's* om uw cluster bij te werken met uw open bare ip's.

In het volgende voor beeld wordt de para meter *Load-Balancer-outbound-ip's* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

U kunt ook open bare IP-voor voegsels gebruiken om uit te voeren met uw *standaard* SKU-Load Balancer. In het volgende voor beeld wordt de opdracht [AZ Network Public-IP prefix show][az-network-public-ip-prefix-show] gebruikt om de id's van uw open bare IP-voor voegsels weer te geven:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

De bovenstaande opdracht toont de ID voor het open bare IP-voor voegsel *myPublicIPPrefix* in de resource groep *myResourceGroup* .

In het volgende voor beeld wordt de para meter *Load Balancer-uitgaand-IP-voor voegsels* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> De open bare Ip's en IP-voor voegsels moeten zich in dezelfde regio bevinden en deel uitmaken van hetzelfde abonnement als uw AKS-cluster. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Uw eigen open bare IP of voor voegsels definiëren op het moment dat het cluster wordt gemaakt

U kunt uw eigen IP-adressen of IP-voor voegsels maken voor uitgaand verkeer tijdens het maken van het cluster ter ondersteuning van scenario's zoals white list-uitgangs eindpunten. Voeg de hierboven weer gegeven para meters toe aan de stap voor het maken van het cluster om uw eigen open bare Ip's en IP-voor voegsels te definiëren aan het begin van de levens cyclus van een cluster.

Gebruik de opdracht *AZ AKS Create* met de para meter *Load-Balancer-outbound-ip's* om aan het begin een nieuw cluster met uw open bare ip's te maken.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Gebruik de opdracht *AZ AKS Create* met de para meter *Load-Balancer-uitgaand-IP-voor voegsels* om een nieuw cluster te maken met uw open bare IP-voor voegsels aan het begin.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>De standaard SKU-load balancer configuratie opruimen

Als u de voorbeeld toepassing en load balancer configuratie wilt verwijderen, gebruikt u [kubectl verwijderen][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes Services vindt u in de [documentatie van Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update


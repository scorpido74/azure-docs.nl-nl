---
title: Virtuele knooppunten maken met Azure CLI
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van de Azure CLI om een AKS-cluster (Azure Kubernetes Services) te maken dat virtuele knooppunten gebruikt om pods uit te voeren.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: b6d44ceb9b447d670c4e51c951b547e90dfce38f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984671"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Een AKS-cluster (Azure Kubernetes Services) maken en configureren om virtuele knooppunten te gebruiken met de Azure CLI

Als u toepassingsworkloads snel wilt schalen in een AKS-cluster (Azure Kubernetes Service), u virtuele knooppunten gebruiken. Met virtuele knooppunten hebt u een snelle inrichting van pods en betaalt u slechts per seconde voor hun uitvoeringstijd. U hoeft niet te wachten tot Kubernetes cluster autoscaler VM compute nodes implementeert om de extra pods uit te voeren. Virtuele knooppunten worden alleen ondersteund met Linux-pods en knooppunten.

In dit artikel ziet u hoe u de virtuele netwerkbronnen en het AKS-cluster maken en configureren en vervolgens virtuele knooppunten inschakelen.

## <a name="before-you-begin"></a>Voordat u begint

Virtuele knooppunten maken netwerkcommunicatie mogelijk tussen pods die worden uitgevoerd in Azure Container Instances (ACI) en het AKS-cluster. Om deze communicatie te bieden, wordt een virtueel netwerksubnet gemaakt en worden gedelegeerde machtigingen toegewezen. Virtuele knooppunten werken alleen met AKS-clusters die zijn gemaakt met behulp van *geavanceerde* netwerken. Standaard worden AKS-clusters gemaakt met *basisnetwerken.* In dit artikel ziet u hoe u een virtueel netwerk en subnetten maakt en vervolgens een AKS-cluster implementeert dat geavanceerde netwerken gebruikt.

Als u ACI nog niet eerder hebt gebruikt, registreert u de serviceprovider bij uw abonnement. U de status van de ACI-providerregistratie controleren met de [opdracht lijst met AZ-provider,][az-provider-list] zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

De *Microsoft.ContainerInstance-provider* moet rapporteren als *Geregistreerd,* zoals weergegeven in de volgende voorbeelduitvoer:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Als de aanbieder als *NotRegistered*aangeeft, registreert u de aanbieder via het [az-providerregister][az-provider-register] zoals aangegeven in het volgende voorbeeld:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Regionale beschikbaarheid

De volgende regio's worden ondersteund voor implementaties met virtuele node:

* Australië Oost (Australië-Oost)
* Centrale V.S.(centralus)
* Oost-VS (eastus)
* Oost-VS 2 (eastus2)
* Japan-Oost (Japaneast)
* Noord-Europa (Noord-Europa)
* Zuidoost-Azië (zuidoostazië)
* West Central US (westcentralus)
* West-Europa (West-Europa)
* West-VS (westus)
* West US 2 (westus2)

## <a name="known-limitations"></a>Bekende beperkingen
De functionaliteit voor virtuele knooppunten is sterk afhankelijk van de functieset van ACI. De volgende scenario's worden nog niet ondersteund met virtuele knooppunten

* Met behulp van service principal om ACR-afbeeldingen te trekken. [Tijdelijke oplossing](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) is het gebruik van [Kubernetes geheimen](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Beperkingen van virtuele netwerken,](../container-instances/container-instances-vnet.md) waaronder VNet-peering, Kubernetes-netwerkbeleid en uitgaand verkeer naar het internet met netwerkbeveiligingsgroepen.
* Init containers
* [Hostaliassen](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenten](../container-instances/container-instances-exec.md#restrictions) voor exec in ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) zal geen pods implementeren op het virtuele knooppunt
* Virtuele knooppunten ondersteunen het plannen van Linux-pods. U de open source [Virtual Kubelet ACI-provider](https://github.com/virtual-kubelet/azure-aci) handmatig installeren om Windows Server-containers te plannen op ACI. 

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u de Cloud Shell wilt openen, selecteert **u Probeer deze** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/bash](https://shell.azure.com/bash)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u de CLI liever lokaal installeert en gebruikt, vereist dit artikel Azure CLI-versie 2.0.49 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Een resourcegroep maken met de opdracht [az group create][az-group-create]. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met de [opdracht vnet create van het AZ-netwerk.][az-network-vnet-create] In het volgende voorbeeld wordt een virtuele netwerknaam *myVnet* met een adresvoorvoegsel van *10.0.0.0/8*en een subnet met de naam *myAKSSubnet*. Het adresvoorvoegsel van dit subnet is standaard *op 10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Maak nu een extra subnet voor virtuele knooppunten met behulp van de [opdracht vnet-subnet van het AZ-netwerk.][az-network-vnet-subnet-create] In het volgende voorbeeld wordt een subnet met de naam *myVirtualNodeSubnet* met het adresvoorvoegsel van *10.241.0.0/16 .*

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Een service-principal maken

Een AKS-cluster heeft een service-principal van Azure Active Directory nodig om met andere Azure-resources te kunnen communiceren. Deze service-principal kan automatisch worden gemaakt door de Azure CLI of via de portal, of u kunt er een vooraf maken en hieraan extra machtigingen toewijzen.

Maak een service-principal met behulp van de opdracht [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. De parameter `--skip-assignment` zorgt ervoor dat eventuele extra machtigingen beperkt kunnen worden toegewezen.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Noteer de *appId* en *wachtwoord*. Deze waarden worden gebruikt in de volgende stappen.

## <a name="assign-permissions-to-the-virtual-network"></a>Machtigingen toewijzen aan het virtuele netwerk

Als u wilt dat uw cluster het virtuele netwerk kan gebruiken en beheren, moet u de AKS-serviceprincipal de juiste rechten verlenen om de netwerkbronnen te gebruiken.

Ten eerste, krijgen de virtuele netwerk bron-ID met behulp [van AZ-netwerk vnet show:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Als u de juiste toegang wilt verlenen voor het AKS-cluster om het virtuele netwerk te gebruiken, maakt u een roltoewijzing met de opdracht [az-toewijzingmaken.][az-role-assignment-create] Vervang `<appId`> en `<vnetId>` door de waarden die zijn verzameld in de vorige twee stappen.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

U implementeert een AKS-cluster in het AKS-subnet dat in een vorige stap is gemaakt. Krijg de ID van dit subnet met behulp [van az-netwerk vnet subnet show:][az-network-vnet-subnet-show]

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Gebruik de opdracht [az aks create][az-aks-create] om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Vervang `<subnetId>` de ID die in de `<appId>` vorige `<password>` stap is verkregen en vervolgens en met de waarden die in de vorige sectie zijn verzameld.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling.

## <a name="enable-virtual-nodes-addon"></a>Addon voor virtuele knooppunten inschakelen

Om virtuele knooppunten in te schakelen, gebruikt u nu de opdracht [az aks enable-addons.][az-aks-enable-addons] In het volgende voorbeeld wordt het subnet met de naam *myVirtualNodeSubnet* gebruikt dat in een vorige stap is gemaakt:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In deze stap worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

Het volgende voorbeeld output toont de enkele VM-knooppunt gemaakt en vervolgens de virtuele node voor Linux, *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Een voorbeeld-app implementeren

Maak een `virtual-node.yaml` bestand met de naam en kopie in de volgende YAML. Als u de container op het knooppunt wilt plannen, worden een [nodeSelector][node-selector] en [tolerantie][toleration] gedefinieerd.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Voer de toepassing uit met de opdracht [kubectl apply.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Gebruik de opdracht [kubectl][kubectl-get] `-o wide` get pods met het argument om een lijst met pods en het geplande knooppunt uit te voeren. De `aci-helloworld` pod is gepland op `virtual-node-aci-linux` het knooppunt.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Aan de pod wordt een intern IP-adres toegewezen van het subnet Azure virtual network dat is gedelegeerd voor gebruik met virtuele knooppunten.

> [!NOTE]
> Als u afbeeldingen gebruikt die zijn opgeslagen in Azure Container Registry, [configureert en gebruikt u een Geheim Kubernetes.][acr-aks-secrets] Een huidige beperking van virtuele knooppunten is dat u geen geïntegreerde Azure AD-serviceprincipal-verificatie gebruiken. Als u geen geheim gebruikt, kunnen pods die op virtuele `HTTP response status code 400 error code "InaccessibleImage"`knooppunten zijn gepland, de fout niet starten en rapporteren.

## <a name="test-the-virtual-node-pod"></a>De virtuele knooppuntpod testen

Als u de pod wilt testen die op het virtuele knooppunt wordt uitgevoerd, bladert u naar de demotoepassing met een webclient. Aangezien aan de pod een intern IP-adres is toegewezen, u deze verbinding snel testen vanuit een andere pod op het AKS-cluster. Maak een testpod en voeg er een terminalsessie aan toe:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
```

Installeer `curl` in de `apt-get`pod met behulp van:

```console
apt-get update && apt-get install -y curl
```

Krijg nu toegang tot `curl`het adres *http://10.241.0.4*van uw pod met behulp van , zoals . Geef uw eigen interne IP-adres op dat in de vorige `kubectl get pods` opdracht wordt weergegeven:

```console
curl -L http://10.241.0.4
```

De demo-toepassing wordt weergegeven, zoals weergegeven in de volgende verkorte voorbeelduitvoer:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Sluit de terminalsessie af `exit`op uw testpod met . Wanneer uw sessie is beëindigd, wordt de pod verwijderd.

## <a name="remove-virtual-nodes"></a>Virtuele knooppunten verwijderen

Als u geen virtuele knooppunten meer wilt gebruiken, u deze uitschakelen met de opdracht [az aks disable-addons.][az aks disable-addons] 

Ga indien nodig [https://shell.azure.com](https://shell.azure.com) naar Azure Cloud Shell in uw browser.

Verwijder eerst `aci-helloworld` de pod die op het virtuele knooppunt wordt uitgevoerd:

```console
kubectl delete -f virtual-node.yaml
```

In de volgende voorbeeldopdracht worden de virtuele Linux-knooppunten uitgeschakeld:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Verwijder nu de virtuele netwerkbronnen en resourcegroep:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een pod gepland op het virtuele knooppunt en een privé, intern IP-adres toegewezen. U in plaats daarvan een service-implementatie maken en verkeer naar uw pod leiden via een load balancer of ingress-controller. Zie [Een basiscontroller voor binnendringen maken in AKS][aks-basic-ingress]voor meer informatie.

Virtuele knooppunten zijn vaak een onderdeel van een schalingoplossing in AKS. Zie de volgende artikelen voor meer informatie over het schalen van oplossingen:

- [De horizontale pod-autoscaler van Kubernetes gebruiken][aks-hpa]
- [De Automatische Kubernetes-clusterautoscaler gebruiken][aks-cluster-autoscaler]
- [Bekijk het voorbeeld van Automatisch schalen voor virtuele knooppunten][virtual-node-autoscale]
- [Lees meer over de Virtual Kubelet open source bibliotheek][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

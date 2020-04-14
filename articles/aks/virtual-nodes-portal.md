---
title: Virtuele knooppunten maken met behulp van de portal in Azure Kubernetes Services (AKS)
description: Meer informatie over het gebruik van de Azure-portal om een AKS-cluster (Azure Kubernetes Services) te maken dat virtuele knooppunten gebruikt om pods uit te voeren.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7b9127c016fff78a8867dcecbe3260becdf02c65
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259116"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Een AKS-cluster (Azure Kubernetes Services) maken en configureren om virtuele knooppunten in de Azure-portal te gebruiken

Als u workloads snel wilt implementeren in een AKS-cluster (Azure Kubernetes Service), u virtuele knooppunten gebruiken. Met virtuele knooppunten hebt u een snelle inrichting van pods en betaalt u slechts per seconde voor hun uitvoeringstijd. In een schalingsscenario hoeft u niet te wachten tot de Kubernetes-clusterautoscaler VM-computenodes implementeert om de extra pods uit te voeren. Virtuele knooppunten worden alleen ondersteund met Linux-pods en knooppunten.

In dit artikel ziet u hoe u de virtuele netwerkbronnen en een AKS-cluster maken en configureren met virtuele knooppunten ingeschakeld.

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

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Selecteer in de linkerbovenhoek van de Azure-portal de optie **Een bron** > **Kubernetes-service maken**.

Configureer op de pagina **Basisbeginselen** de volgende opties:

- *PROJECTDETAILS*: selecteer een Azure-abonnement, en selecteer of maak vervolgens een Azure-resourcegroep, zoals *myResourceGroup*. Voer een **Kubernetes-clusternaam** in, zoals *myAKSCluster*.
- *CLUSTERDETAILS*: selecteer een regio, Kubernetes-versie en DNS-naamvoorvoegsel voor het AKS-cluster.
- *PRIMAIRE KNOOPPUNTPOOL*: Selecteer een VM-grootte voor de AKS-knooppunten. De VM-grootte kan **niet** meer worden gewijzigd als een AKS-cluster eenmaal is geïmplementeerd.
     - Selecteer het aantal knooppunten dat u in het cluster wilt implementeren. Stel voor dit artikel **het aantal knooppunten** in op *1*. Het aantal knooppunten kan nog **wel** worden gewijzigd als het cluster is geïmplementeerd.

Klik **op Volgende: Schalen**.

Selecteer **op** de pagina Schalen de optie *Ingeschakeld* onder **Virtuele knooppunten**.

![AKS-cluster maken en de virtuele knooppunten inschakelen](media/virtual-nodes-portal/enable-virtual-nodes.png)

Standaard wordt een Azure Active Directory-serviceprincipal gemaakt. Deze serviceprincipal wordt gebruikt voor clustercommunicatie en -integratie met andere Azure-services.

Het cluster is ook geconfigureerd voor geavanceerde netwerken. De virtuele knooppunten zijn geconfigureerd om hun eigen Azure virtual network subnet te gebruiken. Dit subnet heeft machtigingen gedelegeerd om Azure-bronnen te verbinden tussen het AKS-cluster. Als u nog geen gedelegeerd subnet hebt, maakt en configureert de Azure-portal het virtuele Azure-netwerk en subnet voor gebruik met de virtuele knooppunten.

Selecteer **Controleren + maken**. Nadat de validatie is voltooid, selecteert u **Maken**.

Het duurt enkele minuten om het AKS-cluster te maken en voor te bereiden voor gebruik.

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. De client `kubectl` is vooraf geïnstalleerd in Azure Cloud Shell.

Als u de Cloud Shell wilt openen, selecteert **u Probeer deze** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/bash](https://shell.azure.com/bash)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren om verbinding te maken met het Kubernetes-cluster. In het volgende voorbeeld worden de referenties opgehaald voor de clusternaam *myAKSCluster* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

Het volgende voorbeeld output toont de enkele VM-knooppunt gemaakt en vervolgens de virtuele node voor Linux, *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Een voorbeeld-app implementeren

Maak in de Azure Cloud `virtual-node.yaml` Shell een bestand met de naam en kopie in de volgende YAML. Als u de container op het knooppunt wilt plannen, worden een [nodeSelector][node-selector] en [tolerantie][toleration] gedefinieerd. Met deze instellingen kan de pod worden gepland op het virtuele knooppunt en wordt bevestigd dat de functie is ingeschakeld.

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

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Gebruik de opdracht [kubectl][kubectl-get] `-o wide` get pods met het argument om een lijst met pods en het geplande knooppunt uit te voeren. De `virtual-node-helloworld` pod is gepland op `virtual-node-linux` het knooppunt.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Aan de pod wordt een intern IP-adres toegewezen van het subnet Azure virtual network dat is gedelegeerd voor gebruik met virtuele knooppunten.

> [!NOTE]
> Als u afbeeldingen gebruikt die zijn opgeslagen in Azure Container Registry, [configureert en gebruikt u een Geheim Kubernetes.][acr-aks-secrets] Een huidige beperking van virtuele knooppunten is dat u geen geïntegreerde Azure AD-serviceprincipal-verificatie gebruiken. Als u geen geheim gebruikt, kunnen pods die op virtuele `HTTP response status code 400 error code "InaccessibleImage"`knooppunten zijn gepland, de fout niet starten en rapporteren.

## <a name="test-the-virtual-node-pod"></a>De virtuele knooppuntpod testen

Als u de pod wilt testen die op het virtuele knooppunt wordt uitgevoerd, bladert u naar de demotoepassing met een webclient. Aangezien aan de pod een intern IP-adres is toegewezen, u deze verbinding snel testen vanuit een andere pod op het AKS-cluster. Maak een testpod en voeg er een terminalsessie aan toe:

```console
kubectl run -it --rm virtual-node-test --image=debian
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

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een pod gepland op het virtuele knooppunt en een privé, intern IP-adres toegewezen. U in plaats daarvan een service-implementatie maken en verkeer naar uw pod leiden via een load balancer of ingress-controller. Zie [Een basiscontroller voor binnendringen maken in AKS][aks-basic-ingress]voor meer informatie.

Virtuele knooppunten zijn een onderdeel van een schalingoplossing in AKS. Zie de volgende artikelen voor meer informatie over het schalen van oplossingen:

- [De horizontale pod-autoscaler van Kubernetes gebruiken][aks-hpa]
- [De Automatische Kubernetes-clusterautoscaler gebruiken][aks-cluster-autoscaler]
- [Bekijk het voorbeeld van Automatisch schalen voor virtuele knooppunten][virtual-node-autoscale]
- [Lees meer over de Virtual Kubelet open source bibliotheek][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
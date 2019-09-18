---
title: 'Voor beeld: een Windows Server-container maken op een Azure Kubernetes service (AKS)-cluster'
description: Meer informatie over hoe u snel een Kubernetes-cluster maakt, een toepassing implementeert in een Windows Server-container in azure Kubernetes service (AKS) met behulp van de Azure CLI.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 8e00053d5ce7c481b026d2fe0ce590d7b8799d8a
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075455"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Voor beeld: een Windows Server-container maken op een Azure Kubernetes service (AKS)-cluster met behulp van de Azure CLI

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In dit artikel implementeert u een AKS-cluster met behulp van de Azure CLI. U kunt ook een ASP.NET-voorbeeld toepassing in een Windows Server-container implementeren in het cluster.

Deze functie is momenteel beschikbaar als preview-product.

![Afbeelding van bladeren naar ASP.NET-voorbeeld toepassing](media/windows-container/asp-net-sample-app.png)

In dit artikel wordt ervan uitgegaan dat u basis informatie krijgt over Kubernetes-concepten. Zie [Kubernetes core-concepten voor Azure Kubernetes service (AKS)][kubernetes-concepts]voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.0.61 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

U moet een extra knooppunt groep toevoegen nadat u het cluster hebt gemaakt waarop Windows Server-containers kunnen worden uitgevoerd. Het toevoegen van een extra knooppunt groep wordt in een latere stap behandeld, maar u moet eerst een paar preview-functies inschakelen.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid][aks-support-policies]
> * [Veelgestelde vragen over ondersteuning voor Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>AKS-preview CLI-extensie installeren

Als u Windows Server-containers wilt gebruiken, hebt u de *AKS-preview cli-* extensie versie 0.4.12 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Windows preview-functie registreren

Als u een AKS-cluster wilt maken dat meerdere knooppunt groepen kan gebruiken en Windows Server-containers kunt uitvoeren, moet u eerst de *WindowsPreview* -functie vlaggen inschakelen voor uw abonnement. De functie *WindowsPreview* maakt ook gebruik van pool clusters met meerdere knoop punten en schaal sets voor virtuele machines om de implementatie en configuratie van de Kubernetes-knoop punten te beheren. Registreer de functie vlag *WindowsPreview* met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Een AKS-cluster dat u maakt nadat u de functie vlag *WindowsPreview* hebt geregistreerd, gebruikt deze preview-cluster ervaring. Schakel preview-functies niet in voor productie abonnementen om regel matige, volledig ondersteunde clusters te blijven maken. Gebruik een afzonderlijk test-of ontwikkelings-Azure-abonnement voor het testen van preview-functies.

Het duurt enkele minuten voordat de registratie is voltooid. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Wanneer de registratie status is `Registered`, drukt u op CTRL-C om te stoppen met het bewaken van de status.  Vernieuw vervolgens de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppunt groepen:

* Er zijn meerdere knooppunt groepen beschikbaar voor clusters die zijn gemaakt nadat u de *WindowsPreview*hebt geregistreerd. Er zijn ook meerdere knooppunt groepen beschikbaar als u de *MultiAgentpoolPreview* -functie voor uw abonnement registreert. U kunt geen knooppunt groepen toevoegen of beheren met een bestaand AKS-cluster dat is gemaakt voordat deze functie is geregistreerd.
* U kunt de eerste knooppunt groep niet verwijderen.

Hoewel deze functie in preview is, zijn de volgende extra beperkingen van toepassing:

* Het AKS-cluster kan Maxi maal acht knooppunt groepen bevatten.
* Het AKS-cluster kan Maxi maal 400 knoop punten in deze acht knooppunt groepen hebben.
* De naam van de Windows Server-knooppunt groep heeft een limiet van 6 tekens.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie worden de meta gegevens van de resource groep opgeslagen, maar ook de resources die in Azure worden uitgevoerd als u geen andere regio opgeeft tijdens het maken van resources. Maak een resource groep met de opdracht [AZ Group Create][az-group-create] .

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

> [!NOTE]
> In dit artikel wordt gebruikgemaakt van de bash-syntaxis voor de opdrachten in deze zelf studie.
> Als u Azure Cloud Shell gebruikt, controleert u of de vervolg keuzelijst in de linkerbovenhoek van het venster Cloud Shell is ingesteld op **bash**.

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

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Als u een AKS-cluster wilt uitvoeren dat knooppunt Pools ondersteunt voor Windows Server-containers, moet uw cluster gebruikmaken van een netwerk beleid dat gebruikmaakt van de invoeg toepassing [Azure cni][azure-cni-about] (Geavanceerd). Zie [Azure cni-netwerken configureren][use-advanced-networking]voor meer informatie over het plannen van de vereiste subnet bereiken en netwerk overwegingen. Gebruik de opdracht [AZ AKS Create][az-aks-create] om een AKS-cluster te maken met de naam *myAKSCluster*. Met deze opdracht worden de benodigde netwerk bronnen gemaakt als deze nog niet bestaan.
  * Het cluster is geconfigureerd met twee knoop punten
  * De para meters *Windows-admin-password* en *Windows-admin-username* stellen de beheerders referenties in voor alle Windows Server-containers die op het cluster zijn gemaakt.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar functioneert, moet u ten minste twee knoop punten in de standaard knooppunt groep uitvoeren.

Geef uw eigen beveiligde *PASSWORD_WIN* op (Houd er rekening mee dat de opdrachten in dit artikel worden ingevoerd in een bash-shell):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.6 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!Note]
> Als er een fout is opgetreden bij het valideren van het wacht woord, probeert u de resource groep te maken in een andere regio.
> Probeer vervolgens het cluster te maken met de nieuwe resource groep.

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Af en toe kan het cluster langer dan een paar minuten duren. In deze gevallen Maxi maal 10 minuten toestaan. 

## <a name="add-a-windows-server-node-pool"></a>Een Windows Server-knooppunt groep toevoegen

Standaard wordt een AKS-cluster gemaakt met een knooppunt groep waarvoor Linux-containers kunnen worden uitgevoerd. Gebruik `az aks nodepool add` de opdracht om een extra knooppunt groep toe te voegen waarop Windows Server-containers kunnen worden uitgevoerd.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.6
```

Met de bovenstaande opdracht maakt u een nieuwe knooppunt groep met de naam *npwin* en voegt u deze toe aan de *myAKSCluster*. Bij het maken van een knooppunt groep voor het uitvoeren van Windows Server-containers, is de standaard waarde voor de *grootte van knoop punt-VM* *Standard_D2s_v3*. Als u de para meter van het *knoop punt-VM-grootte* wilt instellen, controleert u de lijst met [beperkte VM-grootten][restricted-vm-sizes]. De minimale aanbevolen grootte is *Standard_D2s_v3*. De bovenstaande opdracht gebruikt ook het standaard-subnet in de standaard-vnet dat `az aks create`is gemaakt tijdens het uitvoeren.

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

In de volgende voorbeeld uitvoer ziet u de knoop punten in het cluster. Zorg ervoor dat de status van alle knoop punten *gereed*is:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.6
aksnpwin987654                      Ready    agent   108s   v1.14.6
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In dit artikel wordt een manifest gebruikt om alle objecten te maken die nodig zijn om de voorbeeld toepassing ASP.NET uit te voeren in een Windows Server-container. Dit manifest bevat een [Kubernetes-implementatie][kubernetes-deployment] voor de voorbeeld toepassing ASP.net en een externe [Kubernetes-service][kubernetes-service] voor toegang tot de toepassing vanaf internet.

De voorbeeld toepassing ASP.NET wordt meegeleverd als onderdeel van de [.NET Framework][dotnet-samples] -voor beelden en wordt uitgevoerd in een Windows Server-container. AKS vereist dat Windows Server-containers worden gebaseerd op installatie kopieën van *Windows server 2019* of hoger. In het manifest bestand Kubernetes moet ook een [knooppunt kiezer][node-selector] worden gedefinieerd, zodat uw AKS-cluster de pod van uw ASP.net-voorbeeld toepassing uitvoert op een knoop punt waarop Windows Server-containers kunnen worden uitgevoerd.

Maak een bestand met de naam `sample.yaml` en kopieer de volgende YAML-definitie naar het bestand. Als u Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van `vi` of `nano`, zoals bij een virtueel of fysiek systeem:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implementeer de toepassing met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```azurecli-interactive
kubectl apply -f sample.yaml
```

In de volgende voorbeeld uitvoer ziet u dat de implementatie en service zijn gemaakt:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren. Af en toe kan de service langer dan een paar minuten duren om in te richten. In deze gevallen Maxi maal 10 minuten toestaan.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
kubectl get service sample --watch
```

In eerste instantie wordt het *externe IP-adres* voor de *voorbeeld* service weer gegeven als *in behandeling*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *EXTERNAL-IP*-adres is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Als u de voor beeld-app in actie wilt zien, opent u een webbrowser op het externe IP-adres van uw service.

![Afbeelding van bladeren naar ASP.NET-voorbeeld toepassing](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep, de container service en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [AKS Service Principal overwegingen en verwijderen][sp-delete]voor stappen voor het verwijderen van de Service-Principal.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Kubernetes-cluster geïmplementeerd en een ASP.NET-voorbeeld toepassing in een Windows Server-container geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u zojuist hebt gemaakt.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [Zelf studie voor AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

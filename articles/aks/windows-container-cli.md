---
title: Windows Server-container uitvoeren in Azure Kubernetes Service-cluster
description: Meer informatie over het snel maken van een Kubernetes-cluster, een toepassing implementeren in een Windows Server-container in Azure Kubernetes Service (AKS) met behulp van de Azure CLI.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 2aecebcc45cb24c9ab3a594aa4d74b1584c7ffa7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392666"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Preview - Een Windows Server-container maken op een AKS-cluster (Azure Kubernetes Service) met behulp van het Azure CLI

Azure Kubernetes Service (AKS) is een beheerde Kubernetes-service waarmee u snel clusters kunt implementeren en beheren. In dit artikel implementeert u een AKS-cluster met behulp van azure cli. U implementeert ook een ASP.NET voorbeeldtoepassing in een Windows Server-container naar het cluster.

Deze functie is momenteel beschikbaar als preview-product.

![Afbeelding van browsen naar ASP.NET voorbeeldtoepassing](media/windows-container/asp-net-sample-app.png)

Dit artikel gaat uit van een basiskennis van Kubernetes-concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service-cluster (AKS)][kubernetes-concepts] voor meer informatie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.61 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="before-you-begin"></a>Voordat u begint

U moet een extra knooppuntgroep toevoegen nadat u uw cluster hebt gemaakt waarmee Windows Server-containers kunnen worden uitgevoerd. Het toevoegen van een extra knooppuntgroep wordt in een latere stap behandeld, maar u moet eerst een paar voorbeeldfuncties inschakelen.

> [!IMPORTANT]
> AKS preview-functies zijn self-service opt-in. Previews worden geleverd "as-is" en "as available" en zijn uitgesloten van de service level agreements en beperkte garantie. AKS Previews worden gedeeltelijk gedekt door de klantenservice op basis van de beste inspanning. Als zodanig zijn deze functies niet bedoeld voor productiegebruik. Zie voor aanvullende infromation de volgende ondersteuningsartikelen:
>
> * [AKS-ondersteuningsbeleid][aks-support-policies]
> * [Veelgestelde vragen over Azure Support][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Aks-preview CLI-extensie installeren

Als u Windows Server-containers wilt gebruiken, hebt u de CLI-versie 0.4.12 of hoger *met AKS-Preview* nodig. Installeer de *aks-preview Azure CLI-extensie* met de opdracht [Az-extensie toevoegen][az-extension-add] en controleer vervolgens op beschikbare updates met de opdracht [update van AZ-extensie::][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Windows-voorbeeldfunctie registreren

Als u een AKS-cluster wilt maken dat meerdere knooppuntgroepen kan gebruiken en Windows Server-containers uitvoeren, schakelt u eerst de *functievlaggen van WindowsPreview* in op uw abonnement. De *WindowsPreview-functie* maakt ook gebruik van multi-node poolclusters en virtuele machineschaalset om de implementatie en configuratie van de Kubernetes-knooppunten te beheren. Registreer de *functievlag van WindowsPreview* met de opdracht [AZ-functieregister][az-feature-register] zoals in het volgende voorbeeld wordt weergegeven:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Elk AKS-cluster dat u maakt nadat u de *functievlag van WindowsPreview* hebt geregistreerd, gebruikt deze voorbeeldclusterervaring. Als u doorgaan met het maken van regelmatige, volledig ondersteunde clusters, schakelt u geen voorbeeldfuncties in op productieabonnementen. Gebruik een apart Azure-abonnement voor het testen van preview-functies.

Het duurt een paar minuten voordat de registratie is voltooid. Controleer de registratiestatus met de opdracht [az-functielijst:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Wanneer de registratiestatus is, `Registered`drukt u op Ctrl-C om de status niet meer te controleren.  Vernieuw vervolgens de registratie van de *Microsoft.ContainerService-resourceprovider* met de opdracht [AZ-providerregister:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die meerdere knooppuntgroepen ondersteunen:

* U de eerste knooppuntgroep niet verwijderen.

Terwijl deze functie in preview is, zijn de volgende aanvullende beperkingen van toepassing:

* Het AKS-cluster kan maximaal acht knooppuntpools hebben.
* Het AKS-cluster kan maximaal 400 knooppunten hebben in die acht knooppuntgroepen.
* De naam van het Windows Server-knooppunt heeft een limiet van 6 tekens.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie worden metagegevens van de brongroep opgeslagen, maar ook waar uw resources in Azure worden uitgevoerd als u geen andere regio opgeeft tijdens het maken van resources. Maak een resourcegroep met de opdracht [az group create][az-group-create].

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

> [!NOTE]
> In dit artikel wordt de syntaxis van Bash gebruikt voor de opdrachten in deze zelfstudie.
> Als u Azure Cloud Shell gebruikt, moet u ervoor zorgen dat de vervolgkeuzelijst linksboven in het Venster Cloud Shell is ingesteld op **Bash**.

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

Als u een AKS-cluster wilt uitvoeren dat knooppuntgroepen voor Windows Server-containers ondersteunt, moet uw cluster een netwerkbeleid gebruiken dat gebruikmaakt van [Azure CNI][azure-cni-about] -netwerkplug-in. Zie [Azure CNI-netwerkinstellingen configureren][use-advanced-networking]voor meer gedetailleerde informatie om de vereiste subnetbereiken en netwerkoverwegingen te helpen plannen. Gebruik de opdracht [az aks maken][az-aks-create] om een AKS-cluster met de naam *myAKSCluster*te maken. Met deze opdracht worden de benodigde netwerkbronnen gemaakt als deze niet bestaan.
  * Het cluster is geconfigureerd met twee knooppunten
  * De parameters *windows-admin-password* en *windows-admin-username* stellen de beheerdersreferenties in voor alle Windows Server-containers die op het cluster zijn gemaakt.

> [!NOTE]
> Als u wilt dat uw cluster betrouwbaar werkt, moet u ten minste twee (twee) knooppunten uitvoeren in de standaardknooppuntgroep.

Geef uw eigen beveiligde *PASSWORD_WIN* (vergeet niet dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Als u een fout bij het valideren van wachtwoorden krijgt, probeert u uw resourcegroep in een andere regio te maken.
> Probeer vervolgens het cluster te maken met de nieuwe resourcegroep.

> [!Note]
> Als u het AKS-cluster niet maken omdat de versie in deze regio niet wordt ondersteund, u de opdracht [az aks get-versions --location eastus] gebruiken om de lijst met ondersteunde versies voor deze regio te vinden.


Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in JSON-indeling. Af en toe kan het cluster langer dan een paar minuten duren om in te richten. Laat in deze gevallen maximaal 10 minuten toe. 

## <a name="add-a-windows-server-node-pool"></a>Een windows serverknooppuntgroep toevoegen

Standaard wordt een AKS-cluster gemaakt met een knooppuntgroep waarmee Linux-containers kunnen worden uitgevoerd. Gebruik `az aks nodepool add` de opdracht om een extra knooppuntgroep toe te voegen waarmee Windows Server-containers kunnen worden uitgevoerd.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

De bovenstaande opdracht maakt een nieuwe knooppuntpool met de naam *npwin* en voegt deze toe aan de *myAKSCluster.* Wanneer u een knooppuntgroep maakt voor het uitvoeren van Windows Server-containers, wordt de standaardwaarde voor *node-vm-grootte* *Standard_D2s_v3*. Als u ervoor kiest de parameter *node-vm-size* in te stellen, raadpleegt u de lijst met [beperkte VM-formaten][restricted-vm-sizes]. De minimale aanbevolen grootte is *Standard_D2s_v3*. De bovenstaande opdracht maakt ook gebruik van het standaardsubnet in het standaardvnet dat is gemaakt bij het uitvoeren `az aks create`van .

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
```

In de volgende voorbeelduitvoer worden alle knooppunten in het cluster weergegeven. Zorg ervoor dat de status van alle knooppunten *gereed*is:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In dit artikel wordt een manifest gebruikt om alle objecten te maken die nodig zijn om de ASP.NET voorbeeldtoepassing in een Windows Server-container uit te voeren. Dit manifest bevat een [Kubernetes-implementatie][kubernetes-deployment] voor de ASP.NET voorbeeldtoepassing en een externe [Kubernetes-service][kubernetes-service] om toegang te krijgen tot de toepassing vanaf het internet.

De ASP.NET voorbeeldtoepassing wordt geleverd als onderdeel van de [.NET Framework Samples][dotnet-samples] en wordt uitgevoerd in een Windows Server-container. AKS vereist dat Windows Server-containers zijn gebaseerd op afbeeldingen van *Windows Server 2019* of hoger. Het Kubernetes-manifestbestand moet ook een [knooppuntkiezer][node-selector] definiëren om uw AKS-cluster te vertellen dat de pod van uw ASP.NET voorbeeldtoepassing moet worden uitgevoerd op een knooppunt waarop Windows Server-containers kunnen worden uitgevoerd.

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

Implementeer de toepassing met de opdracht [kubectl apply][kubectl-apply] en geef de naam op van uw YAML-manifest:

```console
kubectl apply -f sample.yaml
```

In de volgende voorbeelduitvoer wordt de implementatie en service weergegeven die zijn gemaakt:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, maakt een Kubernetes-service de front-end van de toepassing beschikbaar op internet. Dit proces kan enkele minuten duren. Af en toe kan de service langer dan een paar minuten duren om in te richten. Laat in deze gevallen maximaal 10 minuten toe.

Gebruik de opdracht [kubectl get service][kubectl-get] met het argument `--watch` om de voortgang te controleren.

```console
kubectl get service sample --watch
```

Aanvankelijk wordt het *externe-IP* voor de *voorbeeldservice* weergegeven als *in behandeling*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Zodra het *Extern IP-adres* is gewijzigd van *in behandeling* in een echt openbaar IP-adres, gebruikt u `CTRL-C` om het controleproces van `kubectl` te stoppen. In de volgende voorbeelduitvoer ziet u een geldig openbaar IP-adres dat aan de service is toegewezen:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Als u de voorbeeld-app in actie wilt zien, opent u een webbrowser naar het externe IP-adres van uw service.

![Afbeelding van browsen naar ASP.NET voorbeeldtoepassing](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Als u een time-out van de verbinding ontvangt wanneer u de pagina probeert te laden, moet u controleren of de voorbeeld-app klaar is met de volgende opdracht [kubectl get pods --watch]. Soms wordt de windowscontainer niet gestart tegen de tijd dat uw externe IP-adres beschikbaar is.

## <a name="delete-cluster"></a>Cluster verwijderen

Gebruik de opdracht [az group delete][az-group-delete] om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Kubernetes-cluster geïmplementeerd en een ASP.NET voorbeeldtoepassing in een Windows Server-container geïmplementeerd. [Open het Kubernetes-webdashboard][kubernetes-dashboard] voor het cluster dat u zojuist hebt gemaakt.

Voor meer informatie over AKS en een volledig stapsgewijs voorbeeld van code tot implementatie gaat u naar de zelfstudie over Kubernetes-clusters.

> [!div class="nextstepaction"]
> [AKS-zelfstudie][aks-tutorial]

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

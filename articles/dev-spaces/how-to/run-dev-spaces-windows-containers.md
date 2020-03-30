---
title: Interactie met Windows-containers
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Meer informatie over het uitvoeren van Azure Dev Spaces op een bestaand cluster met Windows-containers
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Windows containers
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240490"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interactie met Windows-containers met Azure Dev Spaces

U Azure Dev Spaces inschakelen op zowel nieuwe als bestaande Kubernetes-naamruimten. Azure Dev Spaces wordt uitgevoerd en instrumentservices die worden uitgevoerd op Linux-containers. Deze services kunnen ook communiceren met toepassingen die worden uitgevoerd op Windows-containers in dezelfde naamruimte. In dit artikel ziet u hoe u Dev Spaces gebruiken om services uit te voeren in een naamruimte met bestaande Windows-containers. Op dit moment u niet debuggen of koppelen aan Windows-containers met Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Uw cluster instellen

In dit artikel wordt ervan uitgegaan dat u al een cluster hebt met zowel Linux- als Windows-knooppuntpools. Als u een cluster met groepen Linux- en Windows-knooppunten wilt maken, u de instructies [hier][windows-container-cli]volgen.

Maak verbinding met uw cluster met behulp van [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer wordt een cluster weergegeven met zowel een Windows- als een Linux-knooppunt. Zorg ervoor dat de status *klaar* is voor elk knooppunt voordat u verdergaat.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Breng een [smet toe][using-taints] op uw Windows-knooppunten. De smet op uw Windows-knooppunten voorkomt dat Dev Spaces Linux-containers plant om op uw Windows-knooppunten te worden uitgevoerd. Met de volgende opdracht wordt een taint van toepassing op het *Aksnpwin987654* Windows-knooppunt uit het vorige voorbeeld.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Wanneer u een smet toepast op een knooppunt, moet u een overeenkomende tolerantie configureren in de implementatiesjabloon van uw service om uw service op dat knooppunt uit te voeren. De voorbeeldtoepassing is al geconfigureerd met een [overeenkomende tolerantie][sample-application-toleration-example] voor de taint die u in de vorige opdracht hebt geconfigureerd.

## <a name="run-your-windows-service"></a>Uw Windows-service uitvoeren

Voer uw Windows-service uit op uw AKS-cluster en controleer of deze in *de status Actief* is. In dit artikel wordt een [voorbeeldtoepassing][sample-application] gebruikt om een Windows- en Linux-service aan te tonen die op uw cluster wordt uitgevoerd.

Kloon de voorbeeldtoepassing van GitHub `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` en navigeer naar de map:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

De voorbeeldtoepassing gebruikt [Helm 3][helm-installed] om de Windows-service op uw cluster uit te voeren. Navigeer naar `charts` de map en gebruik Helm voer de Windows-service uit:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

De bovenstaande opdracht gebruikt Helm om uw Windows-service uit te voeren in *de dev-naamruimte.* Als u geen naamruimte met de naam *dev*hebt, wordt deze gemaakt.

Gebruik `kubectl get pods` de opdracht om te controleren of uw Windows-service in uw cluster wordt uitgevoerd. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev-ruimten inschakelen

Schakel Dev Spaces in in dezelfde naamruimte die u hebt gebruikt om uw Windows-service uit te voeren. Met de volgende opdracht schakelt U Spaties in de *dev-naamruimte* in:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Uw Windows-service bijwerken voor Dev Spaces

Wanneer u Dev Spaces inschakelt op een bestaande naamruimte met containers die al worden uitgevoerd, probeert Dev Spaces standaard nieuwe containers die in die naamruimte worden uitgevoerd, te instrumenteren. Dev Spaces zal ook proberen en instrument alle nieuwe containers gemaakt voor service die al actief zijn in de naamruimte. Als u wilt voorkomen dat Dev Spaces een container in uw naamruimte uitvoert, voegt u de *koptekst zonder proxy* toe aan de `deployment.yaml`.

Toevoegen `azds.io/no-proxy: "true"` aan `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` het bestand:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Gebruik `helm list` de implementatie voor uw Windows-service:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

In het bovenstaande voorbeeld is de naam van uw implementatie *windows-service.* Werk uw Windows-service bij `helm upgrade`met de nieuwe configuratie met :

```cmd
helm upgrade windows-service . --namespace dev
```

Aangezien u `deployment.yaml`uw , Dev Spaces zal niet proberen en instrument uw service.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Uw Linux-toepassing uitvoeren met Azure Dev Spaces

Navigeer naar `webfrontend` de map `azds prep` `azds up` en gebruik de opdrachten en opdrachten om uw Linux-toepassing op uw cluster uit te voeren.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

De `azds prep --enable-ingress` opdracht genereert de Helm-grafiek en Dockerfiles voor uw toepassing.

> [!TIP]
> De [dockerfile- en helmdiagram](../how-dev-spaces-works-prep.md#prepare-your-code) voor uw project wordt gebruikt door Azure Dev Spaces om uw code te bouwen en uit te voeren, maar u deze bestanden wijzigen als u de manier waarop het project wordt gebouwd en uitgevoerd wilt wijzigen.

De `azds up` opdracht voert uw service uit in de naamruimte.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

U de service zien die wordt uitgevoerd door de openbare URL te openen, die wordt weergegeven in de uitvoer van de opdracht AZDs up. In dit voorbeeld is `http://dev.webfrontend.abcdef0123.eus.azds.io/`de openbare URL . Navigeer naar de service in een browser en klik bovenaan op *Over.* Controleer of u een bericht ziet van de *mywebapi-service* met de versie van Windows die de container gebruikt.

![Voorbeeld-app met Windows-versie van mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe Azure Dev Spaces u helpt complexere toepassingen te ontwikkelen voor meerdere containers en hoe u de samenwerking vereenvoudigen door te werken met verschillende versies of branches van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Teamontwikkeling in Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md

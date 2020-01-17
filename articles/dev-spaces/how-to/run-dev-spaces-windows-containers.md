---
title: Interactie met Windows-containers
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Meer informatie over het uitvoeren van Azure-ontwikkel ruimten op een bestaand cluster met Windows-containers
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, Windows-containers
ms.openlocfilehash: 886f71dcaaca6a636b385ef6b101f0a893ff7035
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156995"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interactie met Windows-containers met behulp van Azure dev Spaces

U kunt Azure dev Spaces inschakelen voor zowel nieuwe als bestaande Kubernetes-naam ruimten. Azure dev Spaces worden uitgevoerd en instrument services die worden uitgevoerd op Linux-containers. Deze services kunnen ook communiceren met toepassingen die worden uitgevoerd op Windows-containers in dezelfde naam ruimte. In dit artikel leest u hoe u met behulp van ontwikkel ruimten Services in een naam ruimte kunt uitvoeren met bestaande Windows-containers. Op dit moment kunt u geen fouten opsporen of koppelen aan Windows-containers met Azure dev Spaces.

## <a name="set-up-your-cluster"></a>Uw cluster instellen

In dit artikel wordt ervan uitgegaan dat u al een cluster hebt met zowel Linux-als Windows-knooppunt groepen. Als u een cluster met Linux-en Windows-knooppunt groepen wilt maken, kunt u de instructies [hier][windows-container-cli]volgen.

Maak verbinding met uw cluster met behulp van [kubectl][kubectl], de Kubernetes-opdracht regel-client. Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeeld uitvoer ziet u een cluster met zowel een Windows-als een Linux-knoop punt. Zorg ervoor dat de status *gereed* is voor elk knoop punt voordat u doorgaat.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Een [Taint][using-taints] Toep assen op uw Windows-knoop punten. De Taint op uw Windows-knoop punten voor komt dat ontwikkel ruimten van het plannen van Linux-containers worden uitgevoerd op uw Windows-knoop punten. Met de volgende opdracht voorbeeld opdracht wordt een Taint toegepast op het Windows-knoop punt *aksnpwin987654* vanuit het vorige voor beeld.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Wanneer u een Taint toepast op een knoop punt, moet u een overeenkomende tolerantie configureren in de implementatie sjabloon van uw service om uw service op dat knoop punt uit te voeren. De voorbeeld toepassing is al geconfigureerd met een [overeenkomende overeenkomst][sample-application-toleration-example] voor de Taint die u in de vorige opdracht hebt geconfigureerd.

## <a name="run-your-windows-service"></a>Uw Windows-service uitvoeren

Voer uw Windows-service uit op uw AKS-cluster en controleer of deze de status *actief* heeft. In dit artikel wordt een [voorbeeld toepassing][sample-application] gebruikt om te demonstreren welke Windows-en Linux-service op uw cluster wordt uitgevoerd.

Kloon de voorbeeld toepassing van GitHub en navigeer naar de map `dev-spaces/samples/existingWindowsBackend/mywebapi-windows`:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

De voorbeeld toepassing maakt gebruik van [helm][helm-installed] om de Windows-service op uw cluster uit te voeren. Ga naar de `charts` Directory en gebruik helm de Windows-service uitvoeren:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

De bovenstaande opdracht gebruikt helm om uw Windows-service uit te voeren in de naam ruimte voor *ontwikkel aars* . Als u geen naam ruimte met de naam *dev*hebt, wordt deze gemaakt.

Gebruik de `kubectl get pods` opdracht om te controleren of uw Windows-service wordt uitgevoerd in uw cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure dev Spaces inschakelen

Ontwikkel ruimten in dezelfde naam ruimte die u hebt gebruikt om uw Windows-service uit te voeren, in te scha kelen. Met de volgende opdracht worden ontwikkel ruimten in de naam ruimte voor *ontwikkel* aars ingeschakeld:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Uw Windows-service bijwerken voor ontwikkel ruimten

Wanneer u ontwikkelings ruimten voor een bestaande naam ruimte met containers die al worden uitgevoerd, inschakelt, worden in ontwikkel ruimten standaard de nieuwe containers die in die naam ruimte worden uitgevoerd. Dev Spaces proberen ook nieuwe containers te maken die zijn gemaakt voor de service die al wordt uitgevoerd in de naam ruimte. Als u wilt voor komen dat ontwikkel ruimten een container die wordt uitgevoerd in uw naam ruimte, voegt u de header *no-proxy* toe aan de `deployment.yaml`.

`azds.io/no-proxy: "true"` toevoegen aan het `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`-bestand:

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

Gebruik `helm list` om de implementatie van uw Windows-service weer te geven:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

In het bovenstaande voor beeld is de naam van uw implementatie *Windows-service*. Werk uw Windows-service bij met de nieuwe configuratie met behulp van `helm upgrade`:

```cmd
helm upgrade windows-service . --namespace dev
```

Sinds u uw `deployment.yaml`hebt bijgewerkt, zullen ontwikkel ruimten uw service niet proberen en instrumenteren.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Uw Linux-toepassing uitvoeren met Azure dev Spaces

Ga naar de map `webfrontend` en gebruik de opdrachten `azds prep` en `azds up` om uw Linux-toepassing op uw cluster uit te voeren.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

De `azds prep --public`-opdracht genereert het helm-diagram en Dockerfiles voor uw toepassing.

> [!TIP]
> De [Dockerfile-en helm-grafiek](../how-dev-spaces-works.md#prepare-your-code) voor uw project wordt gebruikt door Azure dev Spaces om uw code te bouwen en uit te voeren, maar u kunt deze bestanden aanpassen als u wilt wijzigen hoe het project wordt gemaakt en uitgevoerd.

De `azds up` opdracht voert uw service uit in de naam ruimte.

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

U ziet de service die wordt uitgevoerd door de open bare URL te openen, die wordt weer gegeven in de uitvoer van de azds omhoog-opdracht. In dit voor beeld is de open bare URL `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Ga naar de service in een browser en klik bovenaan op *info* . Controleer of er een bericht wordt weer gegeven van de *mywebapi* -service met de versie van Windows die in de container wordt gebruikt.

![Voor beeld-app met Windows-versie van mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure dev Spaces u helpt om complexere toepassingen te ontwikkelen in meerdere containers en hoe u samenwerkings ontwikkeling kunt vereenvoudigen door te werken met verschillende versies of vertakkingen van uw code in verschillende ruimten.

> [!div class="nextstepaction"]
> [Team ontwikkeling in azure dev Spaces][team-development-qs]

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

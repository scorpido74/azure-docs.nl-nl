---
title: Computer Vision-container gebruiken met Kubernetes en Helm
titleSuffix: Azure Cognitive Services
description: Implementeer de container Computer Vision in een Azure Container Instance en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458015"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Computer Vision-container gebruiken met Kubernetes en Helm

Een optie om uw Computer Vision-containers on-premises te beheren, is het gebruik van Kubernetes en Helm. Met Kubernetes en Helm om een Computer Vision-containerafbeelding te definiëren, maken we een Kubernetes-pakket. Dit pakket wordt on-premises geïmplementeerd in een Kubernetes-cluster. Tot slot gaan we onderzoeken hoe we de geïmplementeerde services kunnen testen. Zie [Computer Vision-containers installeren en uitvoeren](computer-vision-how-to-install-containers.md)voor meer informatie over het uitvoeren van Docker-containers zonder Kubernetes-orkestratie.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u computervision-containers on-premises gebruikt:

| Vereist | Doel |
|----------|---------|
| Azure-account | Als u geen Azure-abonnement hebt, maakt u een [gratis account][free-azure-account] voordat u begint. |
| Kubernetes CLI | De [Kubernetes CLI][kubernetes-cli] is vereist voor het beheren van de gedeelde referenties uit het containerregister. Kubernetes is ook nodig voor Helm, de Kubernetes package manager. |
| Helm CLI | Installeer de [Helm CLI][helm-install], die wordt gebruikt om een helm grafiek (container pakket definitie) te installeren. |
| Computer Vision-bron |Om de container te kunnen gebruiken, moet u beschikken over:<br><br>Een Azure **Computer Vision-bron** en de bijbehorende API-sleutel het eindpunt URI. Beide waarden zijn beschikbaar op de pagina's Overzicht en Sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina **Sleutels**<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de **pagina Overzicht**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Verbinding maken met het Kubernetes-cluster

De hostcomputer heeft naar verwachting een beschikbaar Kubernetes-cluster. Zie deze zelfstudie over [het implementeren van een Kubernetes-cluster](../../aks/tutorial-kubernetes-deploy-cluster.md) voor een conceptueel inzicht in het implementeren van een Kubernetes-cluster op een hostcomputer.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-referenties delen met het Kubernetes-cluster

Als u het `docker pull` Kubernetes-cluster vanuit het `containerpreview.azurecr.io` containerregister wilt toestaan aan de geconfigureerde afbeelding(en), moet u de dockerreferenties naar het cluster overbrengen. Voer [`kubectl create`][kubectl-create] de opdracht hieronder uit om een *dockerregistergeheim* te maken op basis van de referenties die zijn verstrekt vanuit de vereiste voor toegang voor containerregister.

Voer in de opdrachtlijn interface naar keuze de volgende opdracht uit. Zorg ervoor dat `<username>` `<password>`u `<email-address>` de referenties van het containerregister vervangt.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Als u al toegang `containerpreview.azurecr.io` hebt tot het containerregister, u in plaats daarvan een Kubernetes-geheim maken met behulp van de algemene vlag. Overweeg de volgende opdracht die wordt uitgevoerd tegen uw Docker-configuratie JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

De volgende uitvoer wordt afgedrukt op de console wanneer het geheim is gemaakt.

```console
secret "containerpreview" created
```

Voer het [`kubectl get`][kubectl-get] geheim uit met de `secrets` vlag om te controleren of het geheim is gemaakt.

```console
kubectl get secrets
```

Het uitvoeren `kubectl get secrets` van de afdrukken alle geconfigureerde geheimen.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Waarden van het Helmdiagram configureren voor implementatie

Begin met het maken van een gelezen map *en*plak vervolgens de volgende YAML-inhoud in een nieuw bestand met de naam *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Als u de standaardwaarden van de helmdiagram wilt configureren, `values.yaml`kopieert en plakt u de volgende YAML in een bestand met de naam . Vervang `# {ENDPOINT_URI}` de `# {API_KEY}` opmerkingen en opmerkingen door uw eigen waarden.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Als `billing` de `apikey` en waarden niet worden verstrekt, vervallen de services na 15 min. Ook verificatie mislukt omdat de services niet beschikbaar zijn.

Maak een *map sjablonen* onder de *leesmap.* Kopieer en plak de volgende YAML in een bestand met de naam `deployment.yaml`. Het `deployment.yaml` bestand zal dienen als een Helm-sjabloon.

> Sjablonen genereren manifestbestanden, die met YAML-indelingen bronbeschrijvingen zijn die Kubernetes kan begrijpen. [- Sjabloonhandleiding voor helmdiagram][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

De sjabloon geeft een load balancer-service en de implementatie van uw container/afbeelding voor Lezen op.

### <a name="the-kubernetes-package-helm-chart"></a>Het Kubernetes-pakket (Helm-diagram)

De *Helm-grafiek* bevat de configuratie waarvan dockerafbeelding(s) uit het `containerpreview.azurecr.io` containerregister moeten worden gehaald.

> Een [Helm-grafiek][helm-charts] is een verzameling bestanden die een gerelateerde set Kubernetes-bronnen beschrijven. Een enkele grafiek kan worden gebruikt om iets eenvoudigs te implementeren, zoals een pod met memcached of iets complexs, zoals een volledige web-app-stack met HTTP-servers, databases, caches, enzovoort.

De meegeleverde *Helm grafieken* trekken de docker beelden van `containerpreview.azurecr.io` de Computer Vision Service, en de bijbehorende service uit het containerregister.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>De helmdiagram op het Kubernetes-cluster installeren

Om de *helmdiagram*te installeren, moeten [`helm install`][helm-install-cmd] we de opdracht uitvoeren. Zorg ervoor dat u de installatieopdracht uitvoert vanuit de map boven de `read` map.

```console
helm install read ./read
```

Hier is een voorbeeld uitvoer die u zou verwachten te zien van een succesvolle installatie uitvoering:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

De Kubernetes-implementatie kan enkele minuten in beslag nemen. Voer de volgende opdracht uit om te controleren of beide pods en services correct zijn geïmplementeerd en beschikbaar zijn:

```console
kubectl get all
```

U mag verwachten dat u iets ziet dat lijkt op de volgende uitvoer:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Volgende stappen

Ga [hier][installing-helm-apps-in-aks]voor meer informatie over het installeren van toepassingen met Helm in Azure Kubernetes Service (AKS).

> [!div class="nextstepaction"]
> [Containers voor cognitieve services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md

---
title: Computer Vision-container gebruiken met Kubernetes en helm
titleSuffix: Azure Cognitive Services
description: Implementeer de Computer Vision-container in een Azure-container exemplaar en test deze in een webbrowser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 8/22/2019
ms.author: dapine
ms.openlocfilehash: 1627aea958707eaaef6ee79908a17afc2e8f7b45
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068975"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Computer Vision-container gebruiken met Kubernetes en helm

Een optie voor het on-premises beheren van uw Computer Vision-containers is het gebruik van Kubernetes en helm. Door Kubernetes en helm te gebruiken om de Tekst herkennen container installatie kopie te definiëren, maken we een Kubernetes-pakket. Dit pakket wordt on-premises geïmplementeerd op een Kubernetes-cluster. Ten slotte verkennen we hoe u de geïmplementeerde Services kunt testen. Zie [tekst herkennen containers installeren en uitvoeren](computer-vision-how-to-install-containers.md)voor meer informatie over het uitvoeren van docker-containers zonder Kubernetes-indeling.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u Computer Vision containers on-premises gebruikt:

|Vereist|Doel|
|--|--|
| Azure-Account | Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][free-azure-account] aan voordat u begint. |
| Toegang Container Registry | Om ervoor te zorgen dat Kubernetes de docker-installatie kopieën in het cluster kan ophalen, moet u toegang hebben tot het container register. U moet eerst [toegang aanvragen tot het container register][vision-preview-access] . |
| Kubernetes CLI | De [KUBERNETES cli][kubernetes-cli] is vereist voor het beheren van de gedeelde referenties in het container register. Kubernetes is ook vereist voordat helm, de Kubernetes Package Manager. |
| Helm CLI | Als onderdeel van de [helm cli][helm-install] -installatie moet u ook helm initialiseren, waarmee [Tiller][tiller-install]wordt geïnstalleerd. |
| Computer Vision resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure **Computer Vision** -resource en de bijbehorende API-sleutel de EINDPUNT-URI. Beide waarden zijn beschikbaar op de pagina overzicht en sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}** : Een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : Het eind punt op de pagina **overzicht**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Verbinding maken met het Kubernetes-cluster

Er wordt naar verwachting een beschik bare Kubernetes-cluster op de hostcomputer. Raadpleeg deze zelf studie over het [implementeren van een Kubernetes-cluster](../../aks/tutorial-kubernetes-deploy-cluster.md) voor een conceptuele uitleg over het implementeren van een Kubernetes-cluster op een hostcomputer.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-referenties delen met het Kubernetes-cluster

Als u het Kubernetes-cluster `docker pull` naar de geconfigureerde installatie kopieën in het `containerpreview.azurecr.io` container register wilt toestaan, moet u de docker-referenties naar het cluster overdragen. Voer de [`kubectl create`][kubectl-create] onderstaande opdracht uit om een *docker-REGI ster* te maken op basis van de referenties die zijn verschaft uit de toegangs vereisten voor container register.

Voer de volgende opdracht uit vanaf de opdracht regel interface van Choice. Zorg ervoor dat u de `<username>`, `<password>`en `<email-address>` vervangt door de container register referenties.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Als u al toegang hebt tot het `containerpreview.azurecr.io` container register, kunt u in plaats daarvan een Kubernetes-geheim maken met behulp van de generieke vlag. Bekijk de volgende opdracht die wordt uitgevoerd op basis van de JSON van de docker-configuratie.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

De volgende uitvoer wordt naar de console afgedrukt wanneer het geheim is gemaakt.

```console
secret "containerpreview" created
```

Als u wilt controleren of het geheim is gemaakt, voert [`kubectl get`][kubectl-get] u uit `secrets` met de vlag.

```console
kuberctl get secrets
```

Bij het `kubectl get secrets` uitvoeren van worden alle geconfigureerde geheimen afgedrukt.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Helm-grafiek waarden voor implementatie configureren

Begin met het maken van een map met de naam *tekst herkenning*, kopiëren en plak de volgende YAML-inhoud in een nieuw `Chart.yml`bestand met de naam.

```yaml
apiVersion: v1
name: text-recognizer
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-recognize-text to a Kubernetes cluster
```

Als u de standaard waarden van de helm-grafiek wilt configureren, kopieert en plakt u de `values.yaml`volgende yaml in een bestand met de naam. Vervang de `# {ENDPOINT_URI}` opmerkingen `# {API_KEY}` en door uw eigen waarden.

```yaml
# These settings are deployment specific and users can provide customizations

recognizeText:
  enabled: true
  image:
    name: cognitive-services-recognize-text
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-recognize-text
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Als de `billing` waarden `apikey` en niet worden weer gegeven, verlopen de Services na 15 minuten. De verificatie mislukt ook omdat de services niet beschikbaar zijn.

Maak een map *sjablonen* in de map *tekst-Recognizer* . Kopieer en plak de volgende YAML in een bestand met `deployment.yaml`de naam. Het `deployment.yaml` bestand moet worden gebruikt als een helm-sjabloon.

> Sjablonen genereren manifest bestanden. Dit zijn YAML bron beschrijvingen die Kubernetes kunnen begrijpen. [-Hand leiding voor helm-grafiek sjablonen][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: text-recognizer
spec:
  template:
    metadata:
      labels:
        app: text-recognizer-app
    spec:
      containers:
      - name: {{.Values.recognizeText.image.name}}
        image: {{.Values.recognizeText.image.registry}}{{.Values.recognizeText.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.recognizeText.image.args.eula}}
        - name: billing
          value: {{.Values.recognizeText.image.args.billing}}
        - name: apikey
          value: {{.Values.recognizeText.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.recognizeText.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: text-recognizer
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: text-recognizer-app
```

De sjabloon specificeert een load balancer service en de implementatie van uw container/afbeelding voor tekst herkenning.

### <a name="the-kubernetes-package-helm-chart"></a>Het Kubernetes-pakket (helm-grafiek)

Het *helm-diagram* bevat de configuratie van de docker-installatie kopie (n) die `containerpreview.azurecr.io` moet worden opgehaald uit het container register.

> Een [helm-grafiek][helm-charts] is een verzameling bestanden waarin een gerelateerde set Kubernetes-resources wordt beschreven. Eén grafiek kan worden gebruikt om een eenvoudig te implementeren, zoals een memcached Pod, of iets complex, zoals een volledige web-app-stack met HTTP-servers, data bases, caches, enzovoort.

De meegeleverde *helm-grafieken* halen de docker-installatie kopieën van de computer vision-service en het herkennen van `containerpreview.azurecr.io` tekst services uit het container register.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>De helm-grafiek op het Kubernetes-cluster installeren

Als u de *helm-grafiek*wilt installeren, moet u de [`helm install`][helm-install-cmd] opdracht uitvoeren. Zorg ervoor dat u de installatie opdracht uitvoert vanuit de map `text-recognizer` die zich boven de map bevindt.

```console
helm install text-recognizer --name text-recognizer
```

Hier volgt een voorbeeld uitvoer die u kunt verwachten van een geslaagde installatie-uitvoering:

```console
NAME:   text-recognizer
LAST DEPLOYED: Thu Aug 22 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                              READY  STATUS             RESTARTS  AGE
text-recognizer-57cb76bcf7-45sdh  0/1    ContainerCreating  0         0s

==> v1/Service
NAME             TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
text-recognizer  LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME             READY  UP-TO-DATE  AVAILABLE  AGE
text-recognizer  0/1    1           0          0s
```

Het volt ooien van de implementatie van Kubernetes kan enkele minuten duren. Voer de volgende opdracht uit om te controleren of zowel de peulen als de services goed zijn geïmplementeerd en beschikbaar zijn:

```console
kubectl get all
```

U ziet dat er iets lijkt op de volgende uitvoer:

```console
λ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/text-recognizer-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/text-recognizer   LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/text-recognizer   1/1     1            1           17s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/text-recognizer-57cb76bcf7   1         1         1       17s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het installeren van toepassingen met helm in azure Kubernetes service (AKS) [vindt u hier][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services containers][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-preview-access]: computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md

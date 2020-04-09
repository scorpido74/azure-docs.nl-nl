---
title: Spraakservicecontainers gebruiken met Kubernetes en Helm
titleSuffix: Azure Cognitive Services
description: Met Kubernetes en Helm maken we een Kubernetes-pakket om de spraak-naar-tekst- en tekst-naar-spraakcontainerafbeeldingen te definiëren. Dit pakket wordt on-premises geïmplementeerd in een Kubernetes-cluster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3c183f6d0e2d80ed497654448a726a1562bd046c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874337"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Spraakservicecontainers gebruiken met Kubernetes en Helm

Een optie om uw Spraakcontainers on-premises te beheren, is het gebruik van Kubernetes en Helm. Met Kubernetes en Helm maken we een Kubernetes-pakket om de spraak-naar-tekst- en tekst-naar-spraakcontainerafbeeldingen te definiëren. Dit pakket wordt on-premises geïmplementeerd in een Kubernetes-cluster. Tot slot onderzoeken we hoe we de geïmplementeerde services en verschillende configuratieopties kunnen testen. Zie Voor meer informatie over het uitvoeren van Docker-containers zonder Kubernetes-orkestratie [de servicecontainers voor spraakservice installeren en uitvoeren.](speech-container-howto.md)

## <a name="prerequisites"></a>Vereisten

De volgende voorwaarden voordat u spraakcontainers on-premises gebruikt:

| Vereist | Doel |
|----------|---------|
| Azure-account | Als u geen Azure-abonnement hebt, maakt u een [gratis account][free-azure-account] voordat u begint. |
| Toegang tot containerregister | Om kubernetes de dockerafbeeldingen in het cluster te laten trekken, heeft het toegang tot het containerregister nodig. |
| Kubernetes CLI | De [Kubernetes CLI][kubernetes-cli] is vereist voor het beheren van de gedeelde referenties uit het containerregister. Kubernetes is ook nodig voor Helm, de Kubernetes package manager. |
| Helm CLI | Installeer de [Helm CLI][helm-install], die wordt gebruikt om een helm grafiek (container pakket definitie) te installeren. |
|Spraakbron |Om deze containers te kunnen gebruiken, moet u beschikken over:<br><br>Een _Azure-bron_ voor spraak om de bijbehorende factureringssleutel en factureringseindpunt URI op te halen. Beide waarden zijn beschikbaar op de **pagina's Spraakoverzicht** en Sleutels van de Azure-portal en zijn vereist om de container te starten.<br><br>**{API_KEY}**: resourcesleutel<br><br>**{ENDPOINT_URI}**: voorbeeld van eindpunt URI is:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>De aanbevolen configuratie van de hostcomputer

Raadpleeg de gegevens van de [spraakservicecontainerhost als][speech-container-host-computer] referentie. Deze *helmgrafiek* berekent automatisch cpu- en geheugenvereisten op basis van het aantal decodes (gelijktijdige aanvragen) dat de gebruiker opgeeft. Bovendien wordt het aangepast op basis van de vraag of `enabled`optimalisaties voor audio/tekstinvoer zijn geconfigureerd als . Het helmdiagram standaard, twee gelijktijdige aanvragen en het uitschakelen van optimalisatie.

| Service | CPU / Container | Geheugen / Container |
|--|--|--|
| **Spraak naar tekst** | een decoder vereist een minimum van 1.150 millicores. Als `optimizedForAudioFile` deze is ingeschakeld, zijn 1.950 millicores vereist. (standaard: twee decoders) | Vereist: 2 GB<br>Beperkt: 4 GB |
| **Text-to-speech** | een gelijktijdige aanvraag vereist een minimum van 500 millicores. Als `optimizeForTurboMode` deze is ingeschakeld, zijn 1.000 millicores vereist. (standaard: twee gelijktijdige aanvragen) | Vereist: 1 GB<br> Beperkt: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Verbinding maken met het Kubernetes-cluster

De hostcomputer heeft naar verwachting een beschikbaar Kubernetes-cluster. Zie deze zelfstudie over [het implementeren van een Kubernetes-cluster](../../aks/tutorial-kubernetes-deploy-cluster.md) voor een conceptueel inzicht in het implementeren van een Kubernetes-cluster op een hostcomputer.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Docker-referenties delen met het Kubernetes-cluster

Als u het `docker pull` Kubernetes-cluster vanuit het `containerpreview.azurecr.io` containerregister wilt toestaan aan de geconfigureerde afbeelding(en), moet u de dockerreferenties naar het cluster overbrengen. Voer [`kubectl create`][kubectl-create] de opdracht hieronder uit om een *dockerregistergeheim* te maken op basis van de referenties die zijn verstrekt vanuit de vereiste voor toegang voor containerregister.

Voer in de opdrachtlijn interface naar keuze de volgende opdracht uit. Zorg ervoor dat `<username>` `<password>`u `<email-address>` de referenties van het containerregister vervangt.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Als u al toegang `containerpreview.azurecr.io` hebt tot het containerregister, u in plaats daarvan een Kubernetes-geheim maken met behulp van de algemene vlag. Overweeg de volgende opdracht die wordt uitgevoerd tegen uw Docker-configuratie JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

De volgende uitvoer wordt afgedrukt op de console wanneer het geheim is gemaakt.

```console
secret "mcr" created
```

Voer het [`kubectl get`][kubectl-get] geheim uit met de `secrets` vlag om te controleren of het geheim is gemaakt.

```console
kubectl get secrets
```

Het uitvoeren `kubectl get secrets` van de afdrukken alle geconfigureerde geheimen.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Waarden van het Helmdiagram configureren voor implementatie

Ga naar de [Microsoft Helm Hub][ms-helm-hub] voor alle openbaar beschikbare helmdiagrammen die door Microsoft worden aangeboden. In de Microsoft Helm Hub vindt u de **on-premises grafiek voor spraakvoor cognitieve services.** De **Cognitive Services Speech On-Premises** is de grafiek die we `config-values.yaml` installeren, maar we moeten eerst een bestand maken met expliciete configuraties. Laten we beginnen met het toevoegen van de Microsoft-repository aan onze Helm-instantie.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Vervolgens configureren we onze Helm-grafiekwaarden. Kopieer en plak de volgende YAML in een bestand met de naam `config-values.yaml`. Zie [helmdiagrammen aanpassen voor](#customize-helm-charts)meer informatie over het aanpassen van de **on-premises helmgrafiek**voor cognitive services. Vervang `# {ENDPOINT_URI}` de `# {API_KEY}` opmerkingen en opmerkingen door uw eigen waarden.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Als `billing` de `apikey` en waarden niet worden verstrekt, vervallen de services na 15 min. Ook verificatie mislukt omdat de services niet beschikbaar zijn.

### <a name="the-kubernetes-package-helm-chart"></a>Het Kubernetes-pakket (Helm-diagram)

De *Helm-grafiek* bevat de configuratie waarvan dockerafbeelding(s) uit het `containerpreview.azurecr.io` containerregister moeten worden gehaald.

> Een [Helm-grafiek][helm-charts] is een verzameling bestanden die een gerelateerde set Kubernetes-bronnen beschrijven. Een enkele grafiek kan worden gebruikt om iets eenvoudigs te implementeren, zoals een pod met memcached of iets complexs, zoals een volledige web-app-stack met HTTP-servers, databases, caches, enzovoort.

De meegeleverde *Helm-diagrammen* trekken de dockerafbeeldingen van de Spraakservice, zowel tekst-naar-spraak als de spraak-naar-tekstservices uit het `containerpreview.azurecr.io` containerregister.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>De helmdiagram op het Kubernetes-cluster installeren

Om de *helmgrafiek* te installeren, [`helm install`][helm-install-cmd] moeten we de `<config-values.yaml>` opdracht uitvoeren en het argument vervangen door het juiste pad en de bestandsnaam. De `microsoft/cognitive-services-speech-onpremise` Helm grafiek hieronder verwezen is beschikbaar op de [Microsoft Helm Hub hier][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Hier is een voorbeeld uitvoer die u zou verwachten te zien van een succesvolle installatie uitvoering:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

De Kubernetes-implementatie kan enkele minuten in beslag nemen. Voer de volgende opdracht uit om te controleren of beide pods en services correct zijn geïmplementeerd en beschikbaar zijn:

```console
kubectl get all
```

U mag verwachten dat u iets ziet dat lijkt op de volgende uitvoer:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Implementatie van Helm verifiëren met Helm-tests

De geïnstalleerde Helm grafieken definiëren *Helm tests*, die dienen als een gemak voor verificatie. Deze tests valideren de gereedheid van de service. Als u zowel **spraak-naar-tekst-** als **tekst-naar-spraakservices** wilt controleren, voeren we de [toetsopdracht Helm][helm-test] uit.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Deze tests mislukken als de `Running` POD-status niet is of `AVAILABLE` als de implementatie niet onder de kolom wordt vermeld. Wees geduldig, want dit kan meer dan tien minuten in beslag nemen.

Deze tests zullen verschillende statusresultaten opleveren:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Als alternatief voor het uitvoeren van de *helmtests*u de `kubectl get all` *externe IP-adressen* en bijbehorende poorten van de opdracht verzamelen. Open met het IP-adres en de `http://<external-ip>:<port>:/swagger/index.html` poort een webbrowser en navigeer naar de PAGINA(s) van de API-branie.

## <a name="customize-helm-charts"></a>Helmdiagrammen aanpassen

Helmdiagrammen zijn hiërarchisch. Hiërarchisch zijn maakt het mogelijk om grafiekovererving en is ook geschikt voor het concept van specificiteit, waarbij instellingen die specifieker zijn overerfde regels.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md

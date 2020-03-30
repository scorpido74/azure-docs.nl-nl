---
title: Aks-controllerlogboeken (Azure Kubernetes Service) weergeven
description: Meer informatie over het inschakelen en weergeven van de logboeken voor het hoofdknooppunt Kubernetes in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259381"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Logboeken van Kubernetes-hoofdknooppunten inschakelen en controleren in AKS (Azure Kubernetes Service)

Met Azure Kubernetes Service (AKS) worden de hoofdcomponenten zoals de *kube-apiserver* en *kube-controller-manager* als managed service geleverd. U maakt en beheert de knooppunten die de *kubelet-* en containerruntime uitvoeren en implementeert uw toepassingen via de beheerde Kubernetes API-server. Als u problemen wilt oplossen met uw toepassing en services, moet u mogelijk de logboeken bekijken die door deze hoofdcomponenten worden gegenereerd. In dit artikel ziet u hoe u Azure Monitor-logboeken gebruiken om de logboeken van de hoofdcomponenten van Kubernetes in te schakelen en op te vragen.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel is een bestaand AKS-cluster vereist dat wordt uitgevoerd in uw Azure-account. Als u nog geen AKS-cluster hebt, maakt u er een met de Azure CLI- of [Azure-portal.][portal-quickstart] [Azure CLI][cli-quickstart] Azure Monitor-logboeken werken met zowel Aks-clusters met RBAC als niet-RBAC.

## <a name="enable-diagnostics-logs"></a>Diagnostische logboeken inschakelen

Azure Monitor-logboeken bieden een querytaal- en analyseengine die inzichten biedt aan uw omgeving om gegevens uit meerdere bronnen te verzamelen en te controleren. Een werkruimte wordt gebruikt om de gegevens te verzamelen en te analyseren en kan worden geïntegreerd met andere Azure-services, zoals Application Insights en Security Center. Als u een ander platform wilt gebruiken om de logboeken te analyseren, u er in plaats daarvan voor kiezen diagnostische logboeken naar een Azure-opslagaccount of gebeurtenishub te verzenden. Zie [Wat zijn Azure Monitor-logboeken?][log-analytics-overview]

Azure Monitor-logboeken zijn ingeschakeld en beheerd in de Azure-portal. Als u logboekverzameling voor de Hoofdcomponenten van Kubernetes in uw AKS-cluster wilt inschakelen, opent u de Azure-portal in een webbrowser en voert u de volgende stappen uit:

1. Selecteer de resourcegroep voor uw AKS-cluster, zoals *myResourceGroup.* Selecteer niet de resourcegroep die uw afzonderlijke AKS-clusterbronnen bevat, zoals *MC_myResourceGroup_myAKSCluster_eastus*.
1. Kies aan de linkerkant **Diagnostische instellingen**.
1. Selecteer uw AKS-cluster, zoals *myAKSCluster,* en kies vervolgens voor **Diagnostische instelling toevoegen**.
1. Voer een naam in, zoals *myAKSClusterLogs,* selecteer vervolgens de optie verzenden **naar logboekanalyse**.
1. Selecteer een bestaande werkruimte of maak een nieuwe werkruimte. Als u een werkruimte maakt, geeft u een werkruimtenaam, een resourcegroep en een locatie op.
1. Selecteer in de lijst met beschikbare logboeken de logboeken die u wilt inschakelen. Veelvoorkomende logboeken zijn de *kube-apiserver*, *kube-controller-manager*en *kube-scheduler*. U aanvullende logboeken inschakelen, zoals *kube-audit* en *cluster-autoscaler.* U de verzamelde logboeken retourneren en wijzigen zodra Logboekanalyse-werkruimten zijn ingeschakeld.
1. Wanneer u klaar bent, selecteert **u Opslaan** om verzameling van de geselecteerde logboeken in te schakelen.

In de volgende voorbeeldportalschermafbeelding wordt het venster *Diagnostische instellingen* weergegeven:

![Logboekanalysewerkruimte inschakelen voor Azure Monitor-logboeken van AKS-cluster](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Een testpod plannen op het AKS-cluster

Als u een aantal logboeken wilt genereren, maakt u een nieuwe pod in uw AKS-cluster. Het volgende voorbeeld YAML-manifest kan worden gebruikt om een basis-NGINX-instantie te maken. Maak een `nginx.yaml` bestand met de naam in een editor naar keuze en plak de volgende inhoud:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Maak de pod met de [opdracht kubectl create][kubectl-create] en geef uw YAML-bestand op, zoals in het volgende voorbeeld wordt weergegeven:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Verzamelde logboeken weergeven

Het kan enkele minuten duren voordat de diagnostische logboeken zijn ingeschakeld en worden weergegeven in de werkruimte Log Analytics. Selecteer in de Azure-portal de brongroep voor uw Log Analytics-werkruimte, zoals *myResourceGroup,* en kies vervolgens uw logboekanalysebron, zoals *myAKSLogs*.

![Selecteer de werkruimte Log Analytics voor uw AKS-cluster](media/view-master-logs/select-log-analytics-workspace.png)

Kies Aan de linkerkant **Logboeken**. Als u de *kube-apiserver*wilt weergeven, voert u de volgende query in het tekstvak in:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Veel logboeken worden waarschijnlijk geretourneerd voor de API-server. Als u de query wilt beperken om de logboeken over de NGINX-pod weer te geven die in de vorige stap zijn gemaakt, voegt u een extra instructie toe *waar* u naar *pods/nginx* moet zoeken, zoals in de volgende voorbeeldquery wordt weergegeven:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

De specifieke logboeken voor uw NGINX-pod worden weergegeven, zoals in de volgende voorbeeldschermafbeelding wordt weergegeven:

![Queryresultaten van logboekanalyses voor een nginx-pod van een voorbeeld](media/view-master-logs/log-analytics-query-results.png)

Als u extra logboeken wilt weergeven, u de query voor de *naam categorie* bijwerken naar *kube-controller-manager* of *kube-scheduler,* afhankelijk van welke extra logboeken u inschakelt. Extra *waar* verklaringen vervolgens kunnen worden gebruikt om de gebeurtenissen die u zoekt te verfijnen.

Zie Gegevens bekijken of analyseren die zijn [verzameld met logboekanalyselogboekzoekopdrachten][analyze-log-analytics]voor meer informatie over het opvragen en filteren van uw logboekgegevens.

## <a name="log-event-schema"></a>Gebeurtenisschema voor logboeken

Om de logboekgegevens te analyseren, bevat de volgende tabel het schema dat voor elke gebeurtenis wordt gebruikt:

| Veldnaam               | Beschrijving |
|--------------------------|-------------|
| *Resourceid*             | Azure-bron die het logboek heeft geproduceerd |
| *Tijd*                   | Tijdstempel van wanneer het logboek is geüpload |
| *Categorie*               | Naam van container/component die het logboek genereert |
| *operationName*          | Altijd *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Volledige tekst van het logboek van de component |
| *properties.stream*      | *stderr* of *stdout* |
| *properties.pod*         | Pod naam waar het logboek vandaan kwam |
| *properties.containerID* | ID van de docker container dit logboek kwam uit |

## <a name="log-roles"></a>Logboekrollen

| Rol                     | Beschrijving |
|--------------------------|-------------|
| *aksService*             | De weergavenaam in het controlelogboek voor de bewerking van het controlevlak (van de hcpService) |
| *masterclient*           | De weergavenaam in het controlelogboek voor MasterClientCertificate, het certificaat dat u krijgt van az aks get-credentials |
| *nodeclient*             | De weergavenaam voor ClientCertificate, die wordt gebruikt door agentknooppunten |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de logboeken voor de Hoofdcomponenten van Kubernetes in uw AKS-cluster inschakelen en bekijken. Als u de Kubelet-logboeken wilt controleren en problemen verder wilt oplossen, u ook [de Kubelet-logboeken bekijken][kubelet-logs] en [SSH-knooppunttoegang inschakelen.][aks-ssh]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

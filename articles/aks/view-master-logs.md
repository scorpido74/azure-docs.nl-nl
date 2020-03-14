---
title: AKS-controller Logboeken (Azure Kubernetes service) weer geven
description: Meer informatie over het inschakelen en weer geven van de logboeken voor het hoofd knooppunt Kubernetes in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: f759f15cf98546cb95ba0adb5890885f85ca6aa1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259381"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Logboeken van Kubernetes-hoofd knooppunten inschakelen en controleren in azure Kubernetes service (AKS)

Met Azure Kubernetes service (AKS) worden de hoofd onderdelen, zoals de *uitvoeren-apiserver* en *uitvoeren-Controller-Manager* , als beheerde service verschaft. U maakt en beheert de knoop punten waarop de *kubelet* en de container runtime worden uitgevoerd en implementeert uw toepassingen via de beheerde Kubernetes API-server. Voor het oplossen van problemen met uw toepassing en services, moet u mogelijk de logboeken weer geven die door deze hoofd onderdelen zijn gegenereerd. In dit artikel wordt beschreven hoe u Azure Monitor-Logboeken kunt gebruiken om de logboeken van de Kubernetes-hoofd onderdelen in te scha kelen en te doorzoeken.

## <a name="before-you-begin"></a>Voordat u begint

Voor dit artikel is een bestaand AKS-cluster vereist dat wordt uitgevoerd in uw Azure-account. Als u nog geen AKS-cluster hebt, kunt u er een maken met behulp van [Azure cli][cli-quickstart] of [Azure Portal][portal-quickstart]. Azure Monitor logboeken werken met zowel RBAC-als niet-RBAC ingeschakelde AKS-clusters.

## <a name="enable-diagnostics-logs"></a>Diagnostische logboeken inschakelen

Azure Monitor-logboeken bieden een query taal en analyse-engine die inzicht biedt in uw omgeving om gegevens uit meerdere bronnen te verzamelen en te controleren. Een werk ruimte wordt gebruikt om de gegevens te sorteren en te analyseren en kan worden geïntegreerd met andere Azure-Services, zoals Application Insights en Security Center. Als u een ander platform wilt gebruiken om de logboeken te analyseren, kunt u in plaats daarvan Diagnostische logboeken naar een Azure-opslag account of Event Hub verzenden. Zie [Wat is Azure monitor-logboeken?][log-analytics-overview]voor meer informatie.

Azure Monitor-logboeken worden in de Azure Portal ingeschakeld en beheerd. Als u logboek verzameling voor de Kubernetes-hoofd onderdelen in uw AKS-cluster wilt inschakelen, opent u de Azure Portal in een webbrowser en voert u de volgende stappen uit:

1. Selecteer de resource groep voor uw AKS-cluster, zoals *myResourceGroup*. Selecteer niet de resource groep die uw afzonderlijke AKS-cluster resources bevat, zoals *MC_myResourceGroup_myAKSCluster_eastus*.
1. Klik aan de linkerkant op **Diagnostische instellingen**.
1. Selecteer uw AKS-cluster, zoals *myAKSCluster*, en kies vervolgens **Diagnostische instelling toevoegen**.
1. Voer een naam in, zoals *myAKSClusterLogs*, en selecteer vervolgens de optie om naar **log Analytics te verzenden**.
1. Selecteer een bestaande werk ruimte of maak een nieuwe. Als u een werk ruimte maakt, moet u een werkruimte naam, een resource groep en een locatie opgeven.
1. Selecteer in de lijst met beschik bare Logboeken de logboeken die u wilt inschakelen. Veelvoorkomende logboeken zijn de *uitvoeren-apiserver*, *uitvoeren-Controller-Manager*en *uitvoeren scheduler*. U kunt extra logboeken inschakelen, zoals *uitvoeren-audit* en *cluster-automatisch schalen*. U kunt de verzamelde logboeken retour neren en wijzigen als Log Analytics werk ruimten zijn ingeschakeld.
1. Wanneer u klaar bent, selecteert u **Opslaan** om het verzamelen van de geselecteerde Logboeken in te scha kelen.

In de volgende voorbeeld afbeelding van de portal wordt het venster *Diagnostische instellingen* weer gegeven:

![Log Analytics-werk ruimte inschakelen voor Azure Monitor-logboeken van AKS-cluster](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Een test pod plannen op het AKS-cluster

Als u een aantal logboeken wilt genereren, maakt u een nieuwe pod in uw AKS-cluster. Het volgende voor beeld van een YAML-manifest kan worden gebruikt voor het maken van een Basic NGINX-exemplaar. Maak een bestand met de naam `nginx.yaml` in een editor van uw keuze en plak de volgende inhoud:

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

Maak de Pod met de opdracht [kubectl Create][kubectl-create] en geef uw yaml-bestand op, zoals wordt weer gegeven in het volgende voor beeld:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Verzamelde logboeken weer geven

Het kan enkele minuten duren voordat de diagnostische logboeken zijn ingeschakeld en worden weer gegeven in de werk ruimte Log Analytics. Selecteer in de Azure Portal de resource groep voor uw Log Analytics werk ruimte, zoals *myResourceGroup*, en kies vervolgens uw log Analytics-resource, zoals *myAKSLogs*.

![De Log Analytics-werk ruimte voor uw AKS-cluster selecteren](media/view-master-logs/select-log-analytics-workspace.png)

Kies **Logboeken**aan de linkerkant. Als u de *uitvoeren-apiserver*wilt weer geven, voert u de volgende query in het tekstvak in:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

Veel logboeken worden waarschijnlijk geretourneerd voor de API-server. Als u de query wilt verkleinen om de logboeken te bekijken over de NGINX pod die u in de vorige stap hebt gemaakt, voegt u een extra *where* -instructie toe om te zoeken naar *peulen/NGINX* , zoals wordt weer gegeven in de volgende voorbeeld query:

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

De specifieke logboeken voor uw NGINX-pod worden weer gegeven, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

![Query resultaten van log Analytics voor een voor beeld van NGINX pod](media/view-master-logs/log-analytics-query-results.png)

Als u aanvullende logboeken wilt weer geven, kunt u de query voor de *categorie* naam bijwerken naar *uitvoeren-Controller-Manager* of *uitvoeren-scheduler*, afhankelijk van de aanvullende logboeken die u inschakelt. Aanvullende *where* -instructies kunnen vervolgens worden gebruikt om de gebeurtenissen te verfijnen die u zoekt.

Zie [verzamelde gegevens weer geven of analyseren met][analyze-log-analytics]logboek registratie van log Analytics voor meer informatie over het opvragen en filteren van uw logboek gegevens.

## <a name="log-event-schema"></a>Logboek gebeurtenis schema

De volgende tabel bevat informatie over het schema dat wordt gebruikt voor elke gebeurtenis om de logboek gegevens te analyseren:

| Veldnaam               | Beschrijving |
|--------------------------|-------------|
| *resourceId*             | Azure-resource die het logboek heeft geproduceerd |
| *tegelijk*                   | Tijds tempel van het moment waarop het logboek is geüpload |
| *rubriek*               | Naam van container/onderdeel voor het genereren van het logboek |
| *operationName*          | Altijd *micro soft. container service/managedClusters/diagnosticLogs/lezen* |
| *Eigenschappen. log*         | Volledige tekst van het logboek van het onderdeel |
| *Eigenschappen. Stream*      | *stderr* of *stdout* |
| *Eigenschappen. pod*         | De naam van de pod waaruit het logboek afkomstig is |
| *Properties. containerID* | ID van de docker-container waarvan dit logboek afkomstig is |

## <a name="log-roles"></a>Logboek rollen

| Rol                     | Beschrijving |
|--------------------------|-------------|
| *aksService*             | De weergave naam in het controle logboek voor de bewerking van het besturings vlak (van de hcpService) |
| *masterclient*           | De weergave naam in het controle logboek voor MasterClientCertificate, het certificaat dat u krijgt van AZ AKS Get-credentials |
| *nodeclient*             | De weergave naam voor ClientCertificate, die wordt gebruikt door agent knooppunten |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de logboeken voor de Kubernetes-hoofd onderdelen in uw AKS-cluster kunt inschakelen en bekijken. Als u verder wilt controleren en problemen wilt oplossen, kunt u ook [de Kubelet-logboeken weer geven][kubelet-logs] en [toegang tot SSH-knoop punt inschakelen][aks-ssh].

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

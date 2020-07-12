---
title: Implementaties controleren op Aanbevolen procedures
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het controleren op de implementatie van best practices in uw implementaties in azure Kubernetes service met uitvoeren-Advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 2b0078f1aff3ef81ee270f67de0fffddec3abab9
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255248"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Controleren op aanbevolen procedures voor Kubernetes in uw cluster

Er zijn verschillende aanbevolen procedures die u moet volgen op uw Kubernetes-implementaties om de beste prestaties en flexibiliteit voor uw toepassingen te garanderen. U kunt het hulp programma uitvoeren-Advisor gebruiken om te zoeken naar implementaties die niet volgen op deze suggesties.

## <a name="about-kube-advisor"></a>Over uitvoeren-Advisor

Het [uitvoeren-hulp programma][kube-advisor-github] is een enkele container die is ontworpen om te worden uitgevoerd op uw cluster. Er wordt een query uitgevoerd op de Kubernetes API-server voor informatie over uw implementaties en er wordt een aantal voorgestelde verbeteringen geretourneerd.

Het uitvoeren-hulp programma kan rapporteren over de resource aanvraag en limieten die ontbreken in PodSpecs voor Windows-toepassingen, evenals Linux-toepassingen, maar het hulp programma uitvoeren Advisor zelf moet op een Linux-pod worden gepland. U kunt een pod plannen om uit te voeren op een knooppunt groep met een specifiek besturings systeem met behulp van een [knooppunt kiezer][k8s-node-selector] in de configuratie van de pod.

> [!NOTE]
> Het hulp programma uitvoeren Advisor wordt op basis van de beste inspanningen ondersteund door micro soft. Problemen en suggesties moeten worden ingediend op GitHub.

## <a name="running-kube-advisor"></a>Uitvoeren van uitvoeren-Advisor

Als u het hulp programma wilt uitvoeren op een cluster dat is geconfigureerd voor op [rollen gebaseerd toegangs beheer (RBAC)](./azure-ad-integration-cli.md), met behulp van de volgende opdrachten. Met de eerste opdracht maakt u een Kubernetes-service account. Met de tweede opdracht wordt het hulp programma uitgevoerd in een pod met dat Service account en wordt de Pod voor verwijdering geconfigureerd nadat deze is afgesloten. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Als u geen RBAC gebruikt, kunt u de opdracht als volgt uitvoeren:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Binnen een paar seconden ziet u een tabel met mogelijke verbeteringen in uw implementaties.

![Uitvoeren-Advisor-uitvoer](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Controles uitgevoerd

Het hulp programma valideert diverse Kubernetes aanbevolen procedures, elk met hun eigen voorgestelde herstel.

### <a name="resource-requests-and-limits"></a>Resource-aanvragen en -limieten

Kubernetes biedt ondersteuning [voor het definiëren van resource aanvragen en limieten voor pod-specificaties][kube-cpumem]. De aanvraag definieert de minimale CPU en het geheugen die nodig zijn om de container uit te voeren. De limiet bepaalt de maximale CPU en het geheugen dat moet worden toegestaan.

Standaard zijn er geen aanvragen of limieten ingesteld op pod-specificaties. Dit kan ertoe leiden dat knoop punten worden overbelast en dat containers worden geen. Het uitvoeren-hulp programma markeert een geheel aantal zonder aanvragen en limieten.

## <a name="cleaning-up"></a>Opschonen

Als RBAC is ingeschakeld op uw cluster, kunt u de `ClusterRoleBinding` na het uitvoeren van het hulp programma opschonen met de volgende opdracht:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Als u het hulp programma uitvoert op een cluster dat niet is ingeschakeld voor RBAC, is geen opschoning vereist.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met de Azure Kubernetes-service oplossen](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors

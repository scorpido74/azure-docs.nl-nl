---
title: Implementaties controleren op aanbevolen procedures
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het controleren op implementatie van aanbevolen procedures in uw implementaties op Azure Kubernetes Service met behulp van kube-advisor
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 17e21c142dc354de7b72bc17396b19366027c5cd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668399"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Controleren op aanbevolen procedures voor Kubernetes in uw cluster

Er zijn verschillende best practices die u moet volgen op uw Kubernetes-implementaties om de beste prestaties en veerkracht voor uw toepassingen te garanderen. U de kube-advisor-tool gebruiken om te zoeken naar implementaties die deze suggesties niet volgen.

## <a name="about-kube-advisor"></a>Over kube-adviseur

De [kube-advisor tool][kube-advisor-github] is een enkele container ontworpen om te worden uitgevoerd op uw cluster. Het vraagt de Kubernetes API-server voor informatie over uw implementaties en retourneert een reeks voorgestelde verbeteringen.

De kube-advisor tool kan rapporteren over resource request en beperkingen ontbreken in PodSpecs voor Windows-toepassingen en Linux-toepassingen, maar de kube-advisor tool zelf moet worden gepland op een Linux-pod. U een pod plannen om een knooppuntgroep met een specifiek besturingssysteem uit te voeren met behulp van een [knooppuntkiezer][k8s-node-selector] in de configuratie van de pod.

> [!NOTE]
> De kube-advisor tool wordt ondersteund door Microsoft op een best-effort basis. Problemen en suggesties moeten worden ingediend op GitHub.

## <a name="running-kube-advisor"></a>Lopend kube-adviseur

Als u het gereedschap wilt uitvoeren op een cluster dat is geconfigureerd voor [rbac (role-based access control),](azure-ad-integration.md)met behulp van de volgende opdrachten. De eerste opdracht maakt een Kubernetes-serviceaccount. Met de tweede opdracht wordt het gereedschap in een pod uitgevoerd met dat serviceaccount en wordt de pod geconfigureerd voor verwijdering nadat deze is afgesloten. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

Als u RBAC niet gebruikt, u de opdracht als volgt uitvoeren:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Binnen enkele seconden ziet u een tabel met een beschrijving van mogelijke verbeteringen in uw implementaties.

![Kube-adviseur output](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Uitgevoerde controles

De tool valideert verschillende Kubernetes best practices, elk met hun eigen voorgestelde herstel.

### <a name="resource-requests-and-limits"></a>Resource-aanvragen en -limieten

Kubernetes ondersteunt het definiëren van [resourceaanvragen en limieten voor podspecificaties.][kube-cpumem] De aanvraag definieert de minimale CPU en geheugen die nodig zijn om de container uit te voeren. De limiet definieert de maximale CPU en geheugen die moeten worden toegestaan.

Standaard worden er geen aanvragen of limieten ingesteld op podspecificaties. Dit kan leiden tot knooppunten worden overscheduled en containers worden uitgehongerd. De kube-advisor tool markeert pods zonder aanvragen en limieten ingesteld.

## <a name="cleaning-up"></a>Opschonen

Als RBAC is ingeschakeld in uw cluster, u het `ClusterRoleBinding` gereedschap opschonen nadat u het gereedschap hebt uitgevoerd met de volgende opdracht:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Als u het hulpprogramma uitvoert tegen een cluster dat niet rbac-ingeschakeld is, is er geen opschoning vereist.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure Kubernetes Service oplossen](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
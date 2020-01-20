---
title: '(AFGESCHAFT) Zelfstudie voor de Azure Container Service: toepassing schalen'
description: 'Zelfstudie Azure Container Service: Toepassing schalen'
author: dlepow
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b0aa78a519567a8e1ffd76e26f1d9ea3ca701fca
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274177"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>(AFGESCHAFT) Kubernetes-schillen en Kubernetes-infrastructuur schalen

> [!TIP]
> Voor de bijgewerkte versie van deze zelf studie die gebruikmaakt van de Azure Kubernetes-service, raadpleegt u [zelf studie: toepassingen schalen in azure Kubernetes service (AKS)](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Als u de zelfstudies volgt, hebt u een werkend Kubernetes-cluster in Azure Container Service en hebt u de Azure Voting-app geïmplementeerd. 

In deze zelfstudie, deel vijf van zeven, schaalt u de schillen in de app en probeert u automatisch schalen van schillen uit. U leert ook hoe u het aantal knooppunten van de Azure VM-agent schaalt om de capaciteit van het cluster voor het hosten van werkbelastingen te wijzigen. Dit zijn de uitgevoerde taken:

> [!div class="checklist"]
> * Kubernetes-schillen handmatig schalen
> * Schillen die de front-end van de app uitvoeren, automatisch schalen
> * De knooppunten van de Kubernetes Azure-agent schalen

In de volgende zelfstudies wordt de Azure Vote-toepassing bijgewerkt en wordt Log Analytics geconfigureerd om het Kubernetes-cluster te controleren.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een toepassing verpakt in een containerinstallatiekopie, is deze installatiekopie geüpload naar Azure Container Registry en is een Kubernetes-cluster gemaakt. De toepassing is vervolgens in het Kubernetes-cluster uitgevoerd. 

Als u deze stappen niet hebt uitgevoerd en deze zelfstudie wilt volgen, gaat u terug naar [Zelfstudie 1: Containerinstallatiekopieën maken](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Schillen handmatig schalen

Tot nu toe zijn de Azure Vote-front-end en het Redis-exemplaar geïmplementeerd, elk met één replica. U kunt dit controleren door de opdracht [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) uit te voeren.

```azurecli-interactive
kubectl get pods
```

Uitvoer:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Wijzig het aantal schillen in de `azure-vote-front`-implementatie handmatig met behulp van de opdracht [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale). In dit voorbeeld wordt het aantal verhoogd naar 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Voer [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) uit om te controleren of Kubernetes de schillen heeft gemaakt. Na ongeveer een minuut worden de extra schillen uitgevoerd:

```azurecli-interactive
kubectl get pods
```

Uitvoer:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Schillen automatisch schalen

Kubernetes biedt ondersteuning voor het [automatisch horizontaal schalen van schillen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) om zo het aantal schillen in een implementatie aan te passen op basis van het CPU-gebruik of andere geselecteerde metrische gegevens. 

Als u automatisch schalen wilt gebruiken, moeten CPU-aanvragen en -limieten voor uw schillen zijn gedefinieerd. In de `azure-vote-front`-implementatie vraagt de front-end container om 0,25 CPU, met een limiet van 0,5 CPU. De instellingen zien er als volgt uit:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

In het volgende voorbeeld wordt de opdracht [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) gebruikt om het aantal schillen in de `azure-vote-front`-implementatie automatisch te schalen. Als het CPU-gebruik in dit geval hoger is dan 50%, wordt het aantal schillen automatisch verhoogd naar maximaal 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Voer de volgende opdracht uit om de status van het automatisch schalen te bekijken:

```azurecli-interactive
kubectl get hpa
```

Uitvoer:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Bij een minimale belasting van de Azure Vote-app neemt het aantal schilreplica's na enkele minuten automatisch af tot 3.

## <a name="scale-the-agents"></a>De agents schalen

Als u uw Kubernetes-cluster in de vorige zelfstudie hebt gemaakt met standaardopdrachten, heeft deze drie agentknooppunten. U kunt het aantal agents handmatig aanpassen als u meer of minder containerwerkbelastingen in uw cluster plant. Gebruik de opdracht [az acs scale](/cli/azure/acs#az-acs-scale) en geef het aantal agents op met de parameter `--new-agent-count`.

In het volgende voorbeeld wordt het aantal agentknooppunten in het Kubernetes-cluster genaamd *myK8sCluster* verhoogd tot 4. Het uitvoeren van deze opdracht duurt enkele minuten.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

De uitvoer van de opdracht toont het aantal agentknooppunten in de waarde van `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u verschillende schaalfuncties in uw Kubernetes-cluster gebruikt. Behandelde taken zijn:

> [!div class="checklist"]
> * Kubernetes-schillen handmatig schalen
> * Schillen die de front-end van de app uitvoeren, automatisch schalen
> * De knooppunten van de Kubernetes Azure-agent schalen

Ga naar de volgende zelfstudie om te leren hoe u de toepassing bijwerkt in Kubernetes.

> [!div class="nextstepaction"]
> [Een toepassing bijwerken in Kubernetes](./container-service-tutorial-kubernetes-app-update.md)


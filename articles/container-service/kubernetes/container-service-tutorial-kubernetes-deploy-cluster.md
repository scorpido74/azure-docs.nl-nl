---
title: '(AFGESCHAFT) Zelfstudie Azure Container Service: Cluster implementeren'
description: 'Zelfstudie Azure Container Service: Cluster implementeren'
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b8821f3bb3d48786697cbc4137baf530856774fd
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274009"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(AFGESCHAFT) Een Kubernetes-cluster implementeren in Azure Container Service

> [!TIP]
> Voor de bijgewerkte versie van deze zelf studie die gebruikmaakt van de Azure Kubernetes-service, raadpleegt u [zelf studie: een Azure Kubernetes service (AKS)-cluster implementeren](../../aks/tutorial-kubernetes-deploy-cluster.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes biedt een gedistribueerd platform voor toepassingen in containers. Met Azure Container Service kunt u eenvoudig en snel een Kubernetes-cluster inrichten dat gereed is voor productie. In deze zelfstudie, deel 3 van 7, wordt een Kubernetes-cluster in Azure Container Service geïmplementeerd. Dit zijn de uitgevoerde stappen:

> [!div class="checklist"]
> * Een Kubernetes ACS-cluster implementeren
> * Installatie van de Kubernetes-CLI (kubectl)
> * Configuratie van kubectl

In de volgende zelfstudies wordt de Azure Vote-toepassing geïmplementeerd in het cluster, geschaald en bijgewerkt. Ook wordt Log Analytics geconfigureerd om het Kubernetes-cluster te controleren.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een containerinstallatiekopie gemaakt en geüpload naar een Azure Container Registry-exemplaar. Als u deze stappen niet hebt uitgevoerd en deze zelfstudie wilt volgen, gaat u terug naar [Zelfstudie 1: Containerinstallatiekopieën maken](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Een Kubernetes-cluster maken

Maak een Kubernetes-cluster in Azure Container Service met de opdracht [az acs create](/cli/azure/acs#az-acs-create). 

In het volgende voorbeeld wordt een cluster genaamd `myK8sCluster` gemaakt in de resourcegroep genaamd `myResourceGroup`. Deze resourcegroep is gemaakt in de [vorige zelfstudie](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

In sommige gevallen, zoals met een beperkte proefversie, heeft een Azure-abonnement beperkte toegang tot Azure-resources. Als de implementatie mislukt vanwege beperkte beschikbare kernen, verminder dan het aantal standaardagenten door `--agent-count 1` toe te voegen aan de opdracht [az acs create](/cli/azure/acs#az-acs-create). 

Na enkele minuten is de implementatie voltooid en retourneert json opgemaakte informatie over de ACS-implementatie.

## <a name="install-the-kubectl-cli"></a>De CLI kubectl installeren

Gebruik [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), de Kubernetes-opdrachtregelclient, als u vanaf uw clientcomputer verbinding wilt maken met het Kubernetes-cluster. 

Als u Azure Cloud Shell gebruikt, is kubectl al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Als u werkt in Linux of Mac OS, moet u het mogelijk uitvoeren met sudo. In Windows moet u ervoor zorgen dat uw shell wordt uitgevoerd als administrator.

```azurecli-interactive
az acs kubernetes install-cli 
```

In Windows is de standaardinstallatie *c:\program files (x86)\kubectl.exe*. Mogelijk moet u dit bestand toevoegen aan het Windows-pad. 

## <a name="connect-with-kubectl"></a>Verbinden met kubectl

Als u kubectl zo wilt configureren dat de client verbinding maakt met uw Kubernetes-cluster, voert u de opdracht [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) uit.

```azurecli-interactive
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Als u de verbinding met uw cluster wilt controleren, voert u de opdracht [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) uit.

```console
kubectl get nodes
```

Uitvoer:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

Nadat de zelfstudie is voltooid, hebt u een ACS Kubernetes-cluster dat gereed is voor werkbelastingen. In de volgende zelfstudies wordt een toepassing met meerdere containers geïmplementeerd in dit cluster, geschaald, bijgewerkt en gecontroleerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Azure Container Service Kubernetes-cluster geïmplementeerd. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * Een Kubernetes ACS-cluster is geïmplementeerd
> * De Kubernetes-CLI (kubectl) is geïnstalleerd
> * Kubectl is geconfigureerd

Ga naar de volgende zelfstudie om te leren hoe u de toepassing uitvoert in het cluster.

> [!div class="nextstepaction"]
> [Toepassing implementeren in Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)

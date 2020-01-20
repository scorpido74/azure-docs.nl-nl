---
title: (AFGESCHAFT) Snelstartgids - Azure Kubernetes-cluster voor Linux
description: Leer snel een Kubernetes-cluster te maken voor Linux-containers in Azure Container Service met de Azure CLI.
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 5c182d6119f59daaf21e4b4e1304363eeb0c11e5
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273500"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-linux-containers"></a>(AFGESCHAFT) Een Kubernetes-cluster voor Linux-containers implementeren

> [!TIP]
> Voor de bijgewerkte versie van deze Snelstartgids die gebruikmaakt van de Azure Kubernetes-service, raadpleegt u [Quick Start: een Azure Kubernetes service (AKS)-cluster implementeren](../../aks/kubernetes-walkthrough.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In deze snelstartgids wordt een Kubernetes-cluster geïmplementeerd met behulp van de Azure CLI. Vervolgens wordt er een toepassing met meerdere containers die bestaat uit een web-front-end en een Redis-exemplaar, geïmplementeerd en uitgevoerd op het cluster. Zodra de toepassing is voltooid, is deze toegankelijk via internet. 

De voorbeeldtoepassing gebruikt in dit document is geschreven in Python. De concepten en de stappen die hier worden beschreven, kunnen worden gebruikt om een containerinstallatiekopie te implementeren in een Kubernetes-cluster. De code-, Dockerfile- en vooraf gemaakte Kubernetes-manifestbestanden die zijn gerelateerd aan dit project, zijn beschikbaar op [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

In deze snelstartgids wordt ervan uitgegaan dat u over basiskennis van Kubernetes-concepten beschikt. Zie de [Kubernetes-documentatie]( https://kubernetes.io/docs/home/) voor gedetailleerde informatie over Kubernetes.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *Europa West*.

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

Uitvoer:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Een Kubernetes-cluster maken

Maak een Kubernetes-cluster in Azure Container Service met de opdracht [az acs create](/cli/azure/acs#az-acs-create). In het volgende voorbeeld wordt een cluster gemaakt met de naam *myK8sCluster* met een Linux-hoofdknooppunt en drie knooppunten van de Linux-agent.

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

In sommige gevallen, zoals met een beperkte proefversie, heeft een Azure-abonnement beperkte toegang tot Azure-resources. Als de implementatie mislukt vanwege beperkte beschikbare kernen, verminder dan het aantal standaardagenten door `--agent-count 1` toe te voegen aan de opdracht [az acs create](/cli/azure/acs#az-acs-create). 

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over het cluster in json-indeling. 

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), de Kubernetes-opdrachtregelclient. 

Als u Azure CloudShell gebruikt, is kubectl al geïnstalleerd. Als u lokaal wilt installeren, kunt u de opdracht [az acs kubernetes install-cli](/cli/azure/acs/kubernetes) gebruiken.

Als u kubectl zo wilt configureren dat de client verbinding maakt met uw Kubernetes-cluster, voert u de opdracht [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) uit. In deze stap worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

Uitvoer:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>De toepassing uitvoeren

In een Kubernetes-manifestbestand wordt een gewenste status voor het cluster gedefinieerd, inclusief zaken zoals welke containerinstallatiekopieën moeten worden uitgevoerd. In dit voorbeeld worden met behulp van een manifest alle objecten gemaakt die nodig zijn om de Azure Vote-toepassing uit te voeren. 

Maak een bestand met de naam `azure-vote.yml` en kopieer hierin de volgende YAML. Als u werkt in Azure Cloud Shell, kan dit bestand worden gemaakt met behulp van vi of Nano, zoals bij een virtueel of fysiek systeem.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Gebruik de opdracht [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) om de toepassing uit te voeren.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

Uitvoer:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>De toepassing testen

Terwijl de toepassing wordt uitgevoerd, wordt er een [Kubernetes-service](https://kubernetes.io/docs/concepts/services-networking/service/) gemaakt die de front-end van de toepassing beschikbaar maakt op internet. Dit proces kan enkele minuten duren. 

Gebruik de opdracht [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Eerst wordt het **externe IP-adres** voor de service *azure-vote-front* weergegeven als *in behandeling*. Nadat het externe IP-adres is gewijzigd van *in behandeling* naar een *IP-adres*, gebruikt u `CTRL-C` om het controleproces van kubectl te stoppen. 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Nu kunt u naar het externe IP-adres browsen voor een overzicht van de Azure Vote-toepassing.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Cluster verwijderen
U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Code ophalen

In deze snelstartgids zijn vooraf gemaakte containerinstallatiekopieën gebruikt om een Kubernetes-implementatie te maken. De gerelateerde toepassingscode, Dockerfile en het Kubernetes-manifestbestand zijn beschikbaar op GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een Kubernetes-cluster geïmplementeerd en vervolgens een toepassing met meerdere containers naar het cluster geïmplementeerd. 

Voor meer informatie over Azure Container Service en een volledig voorbeeld van code tot implementatie gaat u naar de zelfstudie Kubernetes-cluster.

> [!div class="nextstepaction"]
> [Een ACS Kubernetes-cluster beheren](./container-service-tutorial-kubernetes-prepare-app.md)

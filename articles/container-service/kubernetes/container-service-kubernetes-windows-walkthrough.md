---
title: KEUR Quick Start-Azure Kubernetes-cluster voor Windows
description: Leer snel een Kubernetes-cluster te maken voor Windows-containers in Azure Container Service met de Azure CLI.
author: dlepow
ms.service: container-service
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 2e36de9f2a6af3643b6f609339d413968f6a8d6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277648"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-windows-containers"></a>KEUR Kubernetes-cluster voor Windows-containers implementeren

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze handleiding vindt u informatie over het gebruik van de Azure CLI voor de implementatie van een [Kubernetes](https://kubernetes.io/docs/home/)-cluster in [Azure Container Service](../container-service-intro.md). Zodra het cluster is geïmplementeerd, verbindt u het met het Kubernetes `kubectl`-opdrachtregelprogramma en implementeert u uw eerste Windows-container.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli). 

> [!NOTE]
> De ondersteuning voor Windows-containers in Kubernetes in Azure Container Service is momenteel beschikbaar als preview. 
>

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Een Kubernetes-cluster maken
Maak een Kubernetes-cluster in Azure Container Service met de opdracht [az acs create](/cli/azure/acs#az-acs-create). 

In het volgende voorbeeld wordt een cluster gemaakt met de naam *myK8sCluster* met een Linux-hoofdknooppunt en twee knooppunten van de Windows-agent. In dit voorbeeld worden de SSH-sleutels gemaakt die nodig zijn om verbinding te maken met de Linux-master. In dit voorbeeld wordt *azureuser* gebruikt als gebruikersnaam voor iemand met beheerdersrechten. Het wachtwoord is *myPassword12*. Werk deze waarden bij met waarden die geschikt zijn voor uw omgeving. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Na enkele minuten is de opdracht voltooid en ziet u informatie over uw implementatie.

## <a name="install-kubectl"></a>Kubectl installeren

Als u verbinding wilt maken met het Kubernetes-cluster vanaf uw client computer, gebruikt u [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) de Kubernetes-opdracht regel-client. 

Als u Azure CloudShell gebruikt, is `kubectl` al geïnstalleerd. Als u lokaal wilt installeren, kunt u de opdracht [az acs kubernetes install-cli](/cli/azure/acs/kubernetes) gebruiken.

In het volgende voorbeeld van Azure CLI wordt `kubectl` op uw systeem geïnstalleerd. In Windows moet u deze opdracht uitvoeren als beheerder.

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>Verbinden met kubectl

Als u `kubectl` zo wilt configureren dat er verbinding wordt gemaakt met uw Kubernetes-cluster, voert u de opdracht [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) uit. In het volgende voorbeeld wordt de clusterconfiguratie voor uw Kubernetes-cluster gedownload.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Als u de verbinding met uw cluster van uw computer wilt verifiëren, probeert u het volgende uit te voeren:

```azurecli-interactive
kubectl get nodes
```

`kubectl` biedt een lijst met de hoofd- en agent-knooppunten.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>Een Windows IIS-container implementeren

U kunt een Docker-container uitvoeren binnen een Kubernetes-*schil*, die een of meer containers bevat. 

In dit eenvoudige voorbeeld wordt een JSON-bestand gebruikt om een ISS-container (Microsoft Internet Information Server) op te geven. De schil wordt vervolgens gemaakt met de opdracht `kubctl apply`. 

Maak een lokaal bestand met de naam `iis.json` en kopieer de volgende tekst. Met dit bestand geeft u Kubernetes de opdracht om IIS uit te voeren op Windows Server 2016 Nano Server met behulp van een openbare containerinstallatiekopie van [Docker Hub](https://hub.docker.com/r/microsoft/iis/). De container gebruikt poort 80, maar is in eerste instantie alleen toegankelijk vanuit het clusternetwerk.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Als u de schil wilt starten, typt u:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Als u de implementatie wilt bijhouden, typt u:
  
```azurecli-interactive
kubectl get pods
```

Tijdens de implementatie van de schil is de status `ContainerCreating`. Het kan enkele minuten duren voordat de container de status `Running` krijgt.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>De welkomstpagina van IIS weergeven

Als u de schil beschikbaar wilt maken met een openbaar IP-adres, typt u de volgende opdracht:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Deze opdracht zorgt ervoor dat Kubernetes een service en een Azure load balancer-regel met een openbaar IP-adres voor de service maakt. 

Voer de volgende opdracht uit om de status van de service te bekijken.

```azurecli-interactive
kubectl get svc
```

Het IP-adres wordt in eerste instantie weergegeven als `pending`. Na een paar minuten wordt het externe IP-adres van de `iis`-schil ingesteld:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

U kunt de gewenste webbrowser gebruiken om de standaard IIS-welkomstpagina weer te geven op het externe IP-adres:

![Afbeelding van bladeren naar IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Cluster verwijderen
U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, de containerservice en alle gerelateerde resources te verwijderen wanneer u het cluster niet meer nodig hebt.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een Kubernetes-cluster geïmplementeerd, het verbonden met `kubectl` en een schil met een IIS-container geïmplementeerd. Voor meer informatie over Azure Container Service gaat u naar de Kubernetes-zelfstudie.

> [!div class="nextstepaction"]
> [Een ACS Kubernetes-cluster beheren](container-service-tutorial-kubernetes-prepare-app.md)

---
title: Ontwikkelen op Azure Kubernetes Service (AKS) met Helm
description: Gebruik Helm met AKS en Azure Container Registry om toepassingscontainers in een cluster te verpakken en uit te voeren.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873431"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Snelstart: ontwikkelen op Azure Kubernetes Service (AKS) met Helm

[Helm][helm] is een open-source verpakkingstool die u helpt bij het installeren en beheren van de levenscyclus van Kubernetes-toepassingen. Net als bij Linux-pakketmanagers zoals *APT* en *Yum*, wordt Helm gebruikt om Kubernetes-grafieken te beheren, die pakketten zijn met vooraf geconfigureerde Kubernetes-bronnen.

In dit artikel ziet u hoe u Helm gebruiken om een toepassing op AKS te verpakken en uit te voeren. Zie [Bestaande toepassingen installeren met Helm in AKS][helm-existing]voor meer informatie over het installeren van een bestaande toepassing met Helm.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker geïnstalleerd en geconfigureerd. Docker biedt pakketten voor de configuratie van Docker op een [Mac][docker-for-mac]-, [Windows][docker-for-windows]- of [Linux][docker-for-linux]-systeem.
* [Helm v3 geïnstalleerd][helm-install].

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
Als u Helm wilt gebruiken om uw toepassing in uw AKS-cluster uit te voeren, hebt u een Azure Container Registry nodig om uw containerafbeeldingen op te slaan. In het onderstaande voorbeeld wordt [az acr-maken][az-acr-create] gebruikt om een ACR met de naam *MyHelmACR* te maken in de myresourcegroep van De *Groep Van MyResourceGroup* met de *Basic* SKU. U moet uw eigen unieke registernaam opgeven. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer de *loginServer-waarde* voor uw ACR, omdat deze in een latere stap wordt gebruikt. In het onderstaande voorbeeld is *myhelmacr.azurecr.io* de *loginServer* voor *MyHelmACR.*

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Als u de ACR-instantie wilt gebruiken, moet u zich eerst aanmelden. Gebruik de [inlogopdracht az acr][az-acr-login] om in te loggen. In het onderstaande voorbeeld wordt u aangemeld bij een ACR met de naam *MyHelmACR.*

```azurecli
az acr login --name MyHelmACR
```

De opdracht retourneert een geslaagd bericht *dat is gelukt* als deze is voltooid.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

Maak een AKS-cluster. De onderstaande opdracht maakt een AKS-cluster genaamd MyAKS en hecht MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Uw AKS-cluster heeft toegang tot uw ACR nodig om de containerafbeeldingen op te halen en uit te voeren. De bovenstaande opdracht verleent ook het *MyAKS-cluster* toegang tot uw *MyHelmACR* ACR.

## <a name="connect-to-your-aks-cluster"></a>Verbinding maken met uw AKS-cluster

Gebruik [kubectl][kubectl], de Kubernetes-opdrachtregelclient, als u vanaf uw lokale computer verbinding wilt maken met het Kubernetes-cluster.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][] om `kubectl` te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voorbeeld worden referenties voor het AKS-cluster met de naam *MyAKS* in de *MyResourceGroup:*

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

Deze quickstart maakt gebruik van [een voorbeeld node.js-toepassing uit de voorbeeldopslagplaats Azure Dev Spaces][example-nodejs]. Kloon de toepassing van GitHub `dev-spaces/samples/nodejs/getting-started/webfrontend` en navigeer naar de map.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Een Dockerfile maken

Maak een nieuw *Dockerfile-bestand* met als volgt:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>De voorbeeldtoepassing bouwen en pushen naar de ACR

Haal het adres van de loginserver op met de opdracht [az acr-lijst][az-acr-list] en query's voor de *loginServer:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Gebruik Docker om de voorbeeldtoepassingscontainer naar de ACR te bouwen, te taggen en te pushen:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>Uw helmdiagram maken

Genereer de `helm create` Helm-grafiek met de opdracht.

```console
helm create webfrontend
```

Breng de volgende updates uit voor *webfrontend/values.yaml:*

* Wijzigen `image.repository` in`<acrLoginServer>/webfrontend`
* Wijzigen `service.type` in`LoadBalancer`

Bijvoorbeeld:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Update `appVersion` `v1` naar in *webfrontend/Chart.yaml*. Bijvoorbeeld

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Voer uw Helm-diagram uit

Gebruik `helm create` de opdracht om uw toepassing te installeren met behulp van de Helm-grafiek.

```console
helm install webfrontend webfrontend/
```

Het duurt een paar minuten voordat de service een openbaar IP-adres retourneert. Als u de voortgang `kubectl get service` wilt controleren, gebruikt u de opdracht met de *parameter horloge:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigeer naar de load balancer van uw `<EXTERNAL-IP>` toepassing in een browser met de om de voorbeeldtoepassing te bekijken.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de opdracht verwijderen van de [AZ-groep][az-group-delete] om de brongroep, het AKS-cluster, het containerregister, de daar opgeslagen containerafbeeldingen en alle bijbehorende bronnen te verwijderen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie de Helm-documentatie voor meer informatie over het gebruik van Helm.

> [!div class="nextstepaction"]
> [Helm documentatie][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations

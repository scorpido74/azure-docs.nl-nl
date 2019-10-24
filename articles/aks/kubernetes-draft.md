---
title: Ontwikkelen op Azure Kubernetes service (AKS) met Draft
description: Concept gebruiken met AKS en Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2019
ms.locfileid: "67303532"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Quickstart: Ontwikkelen op Azure Kubernetes service (AKS) met Draft

Concept is een open source-hulp programma waarmee u toepassings containers kunt inpakken en uitvoeren in een Kubernetes-cluster. Met concept kunt u een toepassing snel opnieuw implementeren naar Kubernetes wanneer er code wijzigingen optreden zonder dat u uw wijzigingen in versie beheer hoeft door te voeren. Zie de [documentatie over concepten op github][draft-documentation]voor meer informatie over concepten.

In dit artikel leest u hoe u een concept pakket gebruikt en hoe u een toepassing uitvoert op AKS.


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker is geïnstalleerd en geconfigureerd. Docker biedt pakketten die docker configureren op een [Mac][docker-for-mac]-, [Windows][docker-for-windows]-of [Linux][docker-for-linux] -systeem.
* [Helm is geïnstalleerd](https://github.com/helm/helm/blob/master/docs/install.md).
* [Concept geïnstalleerd][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken

Maak een AKS-cluster. Met de onderstaande opdrachten maakt u een resource groep met de naam MyResourceGroup en een AKS-cluster met de naam MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
Als u concepten wilt gebruiken om uw toepassing uit te voeren in uw AKS-cluster, hebt u een Azure Container Registry nodig om de container installatie kopieën op te slaan. In het onderstaande voor beeld wordt [AZ ACR Create][az-acr-create] gebruikt om een ACR te maken met de naam *MyDraftACR* in de resource groep *MyResourceGroup* met de *basis* -SKU. U moet een eigen unieke register naam opgeven. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer de waarde *login server* voor uw ACR omdat deze wordt gebruikt in een latere stap. In het onderstaande voor beeld is *mydraftacr.azurecr.io* de *login server* voor *mydraftacr*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
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


Als u het ACR-exemplaar wilt gebruiken, moet u zich eerst aanmelden. Gebruik de opdracht [AZ ACR login][az-acr-login] om u aan te melden. In het onderstaande voor beeld wordt u aangemeld bij een ACR met de naam *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

De opdracht retourneert het bericht *Aanmelden geslaagd* wanneer deze is uitgevoerd.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Een vertrouwens relatie maken tussen het AKS-cluster en ACR

Uw AKS-cluster moet ook toegang hebben tot uw ACR om de container installatie kopieën te halen en uit te voeren. U verleent toegang tot de ACR vanuit AKS door een vertrouwens relatie tot stand te brengen. Als u een vertrouwens relatie tot stand wilt brengen tussen een AKS-cluster en een ACR-REGI ster, moet u machtigingen verlenen voor de Azure Active Directory service-principal die wordt gebruikt door het AKS-cluster voor toegang tot het ACR Met de volgende opdrachten worden machtigingen verleend aan de service-principal van het *MyAKS* -cluster in de *MyResourceGroup* op de *MyDraftACR* ACR in de *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Verbinding maken met uw AKS-cluster

Als u verbinding wilt maken met het Kubernetes-cluster vanaf uw lokale computer, gebruikt u [kubectl][kubectl], de Kubernetes-opdracht regel-client.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voor beeld worden referenties opgehaald voor het AKS-cluster met de naam *MyAKS* in de *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Een service account maken voor helm

Voordat u helm kunt implementeren in een AKS-cluster met RBAC, hebt u een service account en een rol-koppeling voor de Tiller-service nodig. Zie voor meer informatie over het beveiligen van helm/Tiller in een op RBAC ingeschakeld cluster [Tiller, naam ruimten en RBAC][tiller-rbac]. Als op uw AKS-cluster geen RBAC is ingeschakeld, slaat u deze stap over.

Maak een bestand met `helm-rbac.yaml` de naam en kopieer de volgende YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Maak het service account en de functie binding met `kubectl apply` de opdracht:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Helm configureren
Als u een Basic-Tiller in een AKS-cluster wilt implementeren, gebruikt u de opdracht [helm init][helm-init] . Als het cluster niet is ingesteld op RBAC, `--service-account` verwijdert u het argument en de waarde.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Concept configureren

Als u concept nog niet op uw lokale computer hebt geconfigureerd `draft init`, voert u de volgende handelingen uit:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

U moet ook een concept configureren om de *login server* van uw ACR te gebruiken. De volgende opdracht wordt `draft config set` gebruikt voor `mydraftacr.azurecr.io` gebruik als een REGI ster.

```console
draft config set registry mydraftacr.azurecr.io
```

U hebt Draft zo geconfigureerd dat uw ACR wordt gebruikt. concepten kunnen container installatie kopieën naar uw ACR pushen. Wanneer uw toepassing in uw AKS-cluster wordt uitgevoerd, zijn er geen wacht woorden of geheimen vereist om naar het ACR-REGI ster te pushen of te halen. Omdat er een vertrouwens relatie tot stand is gebracht tussen uw AKS-cluster en uw ACR, vindt verificatie plaats op Azure Resource Manager niveau, met behulp van Azure Active Directory.

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

In deze Snelstartgids wordt [een voor beeld van een Java-toepassing uit de GitHub-opslag plaats][example-java]gebruikt. Kloon de toepassing van github en navigeer naar de `draft/examples/example-java/` map.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>De voorbeeld toepassing uitvoeren met concept

Gebruik de `draft create` opdracht om de toepassing voor te bereiden.

```console
draft create
```

Met deze opdracht maakt u de artefacten die worden gebruikt voor het uitvoeren van de toepassing in een Kubernetes-cluster. Deze items bestaan uit een Dockerfile, een helm-grafiek en een *concept. toml* -bestand, het concept configuratie bestand.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Gebruik de `draft up` opdracht om de voorbeeld toepassing in uw AKS-cluster uit te voeren.

```console
draft up
```

Met deze opdracht wordt de Dockerfile gemaakt voor het maken van een container installatie kopie, wordt de installatie kopie naar uw ACR gepusht en wordt het helm-diagram geïnstalleerd om de toepassing in AKS te starten. De eerste keer dat u deze opdracht uitvoert, kan het enige tijd duren om de container installatie kopie te pushen en op te halen. Zodra de basis lagen in de cache zijn opgeslagen, wordt de tijd die nodig is om de toepassing te implementeren aanzienlijk verminderd.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Verbinding maken met de actieve voorbeeld toepassing vanaf uw lokale computer

Als u de toepassing wilt testen, `draft connect` gebruikt u de opdracht.

```console
draft connect
```

Met deze opdracht wordt een beveiligde verbinding met de Kubernetes pod. Wanneer dit is voltooid, kan de toepassing worden geopend op de meegeleverde URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Ga naar de toepassing in een browser met behulp van de `localhost` URL om de voorbeeld toepassing te bekijken. In het bovenstaande voor beeld is `http://localhost:49804`de URL. Stop de verbinding met `Ctrl+c`behulp van.

## <a name="access-the-application-on-the-internet"></a>Toegang tot de toepassing op Internet

In de vorige stap hebt u een proxy verbinding gemaakt met de toepassing pod in uw AKS-cluster. Tijdens het ontwikkelen en testen van uw toepassing kunt u de toepassing beschikbaar maken op internet. Als u een toepassing op internet beschikbaar wilt stellen, kunt u een Kubernetes-service maken met een type [LoadBalancer][kubernetes-service-loadbalancer].

Update `charts/example-java/values.yaml` voor het maken van een *Load Balancer* -service. Wijzig de waarde van *service. type* van *ClusterIP* in *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Sla de wijzigingen op, sluit het bestand en voer `draft up` uit om de toepassing opnieuw uit te voeren.

```console
draft up
```

Het duurt enkele minuten voordat de service een openbaar IP-adres heeft geretourneerd. Als u de voortgang wilt bewaken `kubectl get service` , gebruikt u de opdracht met de para meter *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navigeer naar het load balancer van uw toepassing in een browser met behulp van het *externe-IP-adres* om de voorbeeld toepassing te bekijken. In het bovenstaande voor beeld is `52.175.224.118`het IP-adres.

## <a name="iterate-on-the-application"></a>Herhaal de toepassing

U kunt de toepassing herhalen door lokaal wijzigingen aan te brengen en `draft up`opnieuw uit te voeren.

Het bericht bijwerken dat is geretourneerd op [regel 7 van src/main/Java/HelloWorld/Hello. java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Voer de `draft up` opdracht uit om de toepassing opnieuw te implementeren:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Als u de bijgewerkte toepassing wilt bekijken, gaat u naar het IP-adres van uw load balancer opnieuw en controleert u of uw wijzigingen worden weer gegeven.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep, het AKS-cluster, het container register, de container installatie kopieën die daar zijn opgeslagen en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [AKS Service Principal overwegingen en verwijderen][sp-delete]voor stappen voor het verwijderen van de Service-Principal.

## <a name="next-steps"></a>Volgende stappen

Zie de documentatie over concepten op GitHub voor meer informatie over het gebruik van concept.

> [!div class="nextstepaction"]
> [Documentatie over concepten][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

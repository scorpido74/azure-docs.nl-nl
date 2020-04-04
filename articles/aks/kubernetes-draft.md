---
title: Ontwikkelen op Azure Kubernetes Service (AKS) met Concept
description: Gebruik Concept met AKS en Azure Container Registry om toepassingscontainers in een cluster te verpakken en uit te voeren.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 820af2d8ddf03997eea559fbc5270e84f30a805a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632882"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Snelstart: ontwikkelen op Azure Kubernetes Service (AKS) met Concept

Draft is een open-source tool die helpt bij het verpakken en uitvoeren van toepassingscontainers in een Kubernetes-cluster. Met Concept u een toepassing snel opnieuw implementeren naar Kubernetes als er codewijzigingen plaatsvinden zonder dat u uw wijzigingen in versiebeheer hoeft in te voeren. Zie de [conceptdocumentatie op GitHub][draft-documentation]voor meer informatie over Concept.

In dit artikel ziet u hoe u Concept gebruiken om een toepassing op AKS te verpakken en uit te voeren.


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, u een [gratis account](https://azure.microsoft.com/free)maken.
* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker geïnstalleerd en geconfigureerd. Docker biedt pakketten voor de configuratie van Docker op een [Mac][docker-for-mac]-, [Windows][docker-for-windows]- of [Linux][docker-for-linux]-systeem.
* [Helm v2 geïnstalleerd][helm-install].
* [Concept geïnstalleerd][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

Maak een AKS-cluster. De onderstaande opdrachten maken een resourcegroep genaamd MyResourceGroup en een AKS-cluster genaamd MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
Als u Concept wilt gebruiken om uw toepassing in uw AKS-cluster uit te voeren, hebt u een Azure Container Registry nodig om uw containerafbeeldingen op te slaan. In het onderstaande voorbeeld wordt [az acr-maken][az-acr-create] gebruikt om een ACR met de naam *MyDraftACR* te maken in de *myresourcegroep van MyResourceGroup* met de *Basic* SKU. U moet uw eigen unieke registernaam opgeven. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer de *loginServer-waarde* voor uw ACR, omdat deze in een latere stap wordt gebruikt. In het onderstaande voorbeeld is *mydraftacr.azurecr.io* de *loginServer* voor *MyDraftACR.*

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


Als u Concept wilt gebruiken in de ACR-instantie, moet u zich eerst aanmelden. Gebruik de [inlogopdracht az acr][az-acr-login] om in te loggen. In het onderstaande voorbeeld wordt u aangemeld bij een ACR met de naam *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

De opdracht retourneert een geslaagd bericht *dat is gelukt* als deze is voltooid.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Vertrouwen tussen AKS-cluster en ACR

Uw AKS-cluster heeft ook toegang tot uw ACR nodig om de containerafbeeldingen op te trekken en uit te voeren. U geeft toegang tot de ACR van AKS door het vestigen van een vertrouwensrelatie. Als u vertrouwen wilt stellen tussen een AKS-cluster en een ACR-register, verleent u machtigingen voor de Azure Active Directory-serviceprincipal die door het AKS-cluster wordt gebruikt om toegang te krijgen tot het ACR-register. Met de volgende opdrachten worden machtigingen verleend aan de serviceprincipal van het *MyAKS-cluster* in de *MyResourceGroup* aan de *ACR van MyDraftACR* in de *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

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

## <a name="create-a-service-account-for-helm"></a>Een serviceaccount voor Helm maken

Voordat u Helm implementeren in een AKS-cluster met RBAC-functie, hebt u een serviceaccount en rolbinding nodig voor de Tiller-service. Zie [Tiller, Namespaces en RBAC][tiller-rbac]voor meer informatie over het beveiligen van Helm / Tiller in een cluster met RBAC. Als uw AKS-cluster niet is ingeschakeld, slaat u deze stap over.

Maak een `helm-rbac.yaml` bestand met de naam en kopie in de volgende YAML:

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

Maak het serviceaccount en `kubectl apply` de rolbinding met de opdracht:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Helm configureren
Als u een basis-Tiller wilt implementeren in een AKS-cluster, gebruikt u de opdracht [helminit.][helm-init] Als uw cluster niet is ingeschakeld, `--service-account` verwijdert u het argument en de waarde.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Concept configureren

Als u Concept niet hebt geconfigureerd op `draft init`uw lokale machine, voert u het uitvoeren van:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

U moet concept ook configureren om de *loginServer* van uw ACR te gebruiken. De volgende `draft config set` opdracht `mydraftacr.azurecr.io` wordt gebruikt om te gebruiken als een register.

```console
draft config set registry mydraftacr.azurecr.io
```

U hebt Concept geconfigureerd om uw ACR te gebruiken en Concept kan containerafbeeldingen naar uw ACR pushen. Wanneer Concept uw toepassing in uw AKS-cluster uitvoert, hoeven er geen wachtwoorden of geheimen te worden toegevoegd aan of te trekken uit het ACR-register. Aangezien er een vertrouwensrelatie is gemaakt tussen uw AKS-cluster en uw ACR, vindt verificatie plaats op Azure Resource Manager-niveau met Azure Active Directory.

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

Deze quickstart maakt gebruik [van een voorbeeld Java-toepassing uit de Draft GitHub repository][example-java]. Kloon de toepassing van GitHub `draft/examples/example-java/` en navigeer naar de map.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>De voorbeeldtoepassing uitvoeren met Concept

Gebruik `draft create` de opdracht om de toepassing voor te bereiden.

```console
draft create
```

Met deze opdracht worden de artefacten gemaakt die worden gebruikt om de toepassing in een Kubernetes-cluster uit te voeren. Deze items omvatten een Dockerfile, een Helm-diagram en een *draft.toml-bestand,* het conceptconfiguratiebestand.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Als u de voorbeeldtoepassing in uw `draft up` AKS-cluster wilt uitvoeren, gebruikt u de opdracht.

```console
draft up
```

Met deze opdracht wordt het Dockerbestand gebouwd om een containerafbeelding te maken, wordt de afbeelding naar uw ACR gepusht en wordt de Helm-grafiek geïnstalleerd om de toepassing in AKS te starten. De eerste keer dat u deze opdracht uitvoert, kan het indrukken en trekken van de containerafbeelding enige tijd duren. Zodra de basislagen in de cache zijn opgeslagen, wordt de tijd die nodig is om de toepassing te implementeren drastisch verkort.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Verbinding maken met de lopende voorbeeldtoepassing van uw lokale machine

Als u de toepassing `draft connect` wilt testen, gebruikt u de opdracht.

```console
draft connect
```

Deze opdracht maakt een beveiligde verbinding met de Kubernetes-pod. Wanneer de toepassing is voltooid, kan deze worden geopend op de opgegeven URL.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Navigeer naar de toepassing in `localhost` een browser met de URL om de voorbeeldtoepassing te bekijken. In het bovenstaande voorbeeld `http://localhost:49804`is de URL . De verbinding `Ctrl+c`stoppen met .

## <a name="access-the-application-on-the-internet"></a>Toegang tot de toepassing op het internet

Met de vorige stap is een proxyverbinding gemaakt met de toepassingspod in uw AKS-cluster. Als u uw toepassing ontwikkelt en test, u de toepassing op Internet beschikbaar stellen. Als u een toepassing op het internet wilt blootstellen, u een Kubernetes-service maken met een type [LoadBalancer.][kubernetes-service-loadbalancer]

Bijwerken `charts/example-java/values.yaml` om een *LoadBalancer-service* te maken. Wijzig de waarde van *service.type* van *ClusterIP* naar *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Sla de wijzigingen op, sluit `draft up` het bestand en voer deze uit om de toepassing opnieuw uit te voeren.

```console
draft up
```

Het duurt een paar minuten voordat de service een openbaar IP-adres retourneert. Als u de voortgang `kubectl get service` wilt controleren, gebruikt u de opdracht met de *parameter horloge:*

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Navigeer naar de load balancer van uw toepassing in een browser met het *EXTERNE-IP-adres* om de voorbeeldtoepassing te bekijken. In het bovenstaande voorbeeld `52.175.224.118`is het IP .

## <a name="iterate-on-the-application"></a>Herhalen op de aanvraag

U uw toepassing herhalen door lokaal wijzigingen `draft up`aan te brengen en opnieuw uit te voeren.

Werk het bericht terug op [lijn 7 van src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Voer `draft up` de opdracht uit om de toepassing opnieuw te implementeren:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Als u de bijgewerkte toepassing wilt bekijken, navigeert u opnieuw naar het IP-adres van uw load balancer en controleert u of de wijzigingen worden weergegeven.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de opdracht verwijderen van de [AZ-groep][az-group-delete] om de brongroep, het AKS-cluster, het containerregister, de daar opgeslagen containerafbeeldingen en alle bijbehorende bronnen te verwijderen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.

## <a name="next-steps"></a>Volgende stappen

Zie de conceptdocumentatie op GitHub voor meer informatie over het gebruik van Concept.

> [!div class="nextstepaction"]
> [Conceptdocumentatie][draft-documentation]


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
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac

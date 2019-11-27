---
title: Zelf studie-container register voorbereiden om installatie kopie te implementeren
description: Zelfstudie voor Azure Container Instances, deel 2 van 3 - Azure-containerregister voorbereiden
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: d8a14acb196b257d96792444fe41e7e9f6b73592
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533325"
---
# <a name="tutorial-deploy-an-azure-container-registry-and-push-a-container-image"></a>Zelf studie: een Azure container Registry implementeren en een container installatie kopie pushen

Dit is deel 2 van een zelfstudie met drie delen. In [deel één](container-instances-tutorial-prepare-app.md) van de zelfstudie hebt u een Docker-containerinstallatiekopie gemaakt voor een Node.js-webtoepassing. In deze zelfstudie pusht u de installatiekopie naar Azure Container Registry. Als u nog geen containerinstallatiekopie hebt gemaakt, gaat u terug naar [Zelfstudie 1: Containerinstallatiekopie maken](container-instances-tutorial-prepare-app.md).

Azure Container Registry is uw privéregister van Docker in Azure. In deze zelfstudie maakt u een Azure Container Registry-exemplaar in uw abonnement. Vervolgens pusht u de eerder gemaakte containerinstallatiekopie naar dit exemplaar. In dit artikel, deel twee uit de reeks, voert u de volgende handelingen uit:

> [!div class="checklist"]
> * Een Azure Container Registry-exemplaar maken
> * Een containerinstallatiekopie taggen voor het Azure Container Registry
> * De installatiekopie uploaden naar het register

In het volgende artikel, de laatste in de reeks, implementeert u de container uit uw privéregister naar Azure Container Instances.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Azure Container Registry maken

Voordat u het containerregister maakt, hebt u een *resourcegroep* nodig om dit in te implementeren. Een resourcegroep is een logische verzameling waarin alle Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create][az-group-create]. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Nadat u de resource groep hebt gemaakt, maakt u een Azure container Registry met de opdracht [AZ ACR Create][az-acr-create] . De containerregisternaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. Vervang `<acrName>` door een unieke naam voor het register:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Hier volgt voorbeelduitvoer voor een nieuwe Azure Container Registry met de naam *mycontainerregistry082* (hier ingekort weergegeven):

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
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

In de rest van de zelfstudie wordt naar `<acrName>` verwezen als tijdelijke aanduiding voor de containerregisternaam die u in deze stap hebt gekozen.

## <a name="log-in-to-container-registry"></a>Aanmelden bij het containerregister

U moet u aanmelden bij het Azure Container Registry-exemplaar voordat u installatiekopieën kunt pushen. Gebruik de opdracht [az acr login][az-acr-login] om de bewerking te voltooien. U moet de unieke naam van het containerregister opgeven die u hebt gekozen toen u het maakte.

```azurecli
az acr login --name <acrName>
```

De opdracht retourneert `Login Succeeded` nadat deze is voltooid:

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Containerinstallatiekopieën taggen

Als u een containerinstallatiekopie naar een privéregister zoals Azure Container Registry wilt pushen, moet u de installatiekopie eerst labelen met de volledige naam van de aanmeldingsserver van het register.

Haal eerst de volledige naam van de aanmeldingsserver op voor uw Azure Container Registry. Voer de volgende opdracht [AZ ACR show][az-acr-show] uit en vervang `<acrName>` door de naam van het REGI ster dat u zojuist hebt gemaakt:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Bijvoorbeeld, als de naam van het register *mycontainerregistry082* is:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

U kunt nu de lijst met uw lokale installatie kopieën weer geven met de opdracht [docker-installatie kopieën][docker-images] :

```bash
docker images
```

Samen met eventuele andere installatiekopieën op uw computer ziet u nu de installatiekopie *aci-tutorial-app* die u hebt gebouwd in de [vorige zelfstudie](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Tag de installatiekopie *aci-tutorial-app* met de loginServer van het containerregister. Voeg ook het label `:v1` toe aan het eind van de installatiekopienaam om het versienummer van de installatiekopie aan te geven. Vervang `<acrLoginServer>` door het resultaat van de opdracht [AZ ACR show][az-acr-show] die u eerder hebt uitgevoerd.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Voer `docker images` opnieuw uit om het labelen te controleren:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Nu u de *ACI-zelf studie-app-* installatie kopie hebt gelabeld met de volledige naam van de aanmeldings server van uw persoonlijke REGI ster, kunt u deze naar het REGI ster pushen met de opdracht [docker push][docker-push] . Vervang `<acrLoginServer>` door de volledige naam van de aanmeldingsserver die u in de vorige stap hebt verkregen.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

De bewerking `push` duurt minimaal enkele seconden tot maximaal enkele minuten afhankelijk van de internetverbinding, en de uitvoer ziet er ongeveer als volgt uit:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Installatiekopieën vermelden in Azure Container Registry

Als u wilt controleren of de installatie kopie die u zojuist hebt gepusht, inderdaad in uw Azure container Registry staat, vermeldt u de installatie kopieën in uw REGI ster met de opdracht [AZ ACR Repository List][az-acr-repository-list] . Vervang `<acrName>` door de naam van het containerregister.

```azurecli
az acr repository list --name <acrName> --output table
```

Bijvoorbeeld:

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

Als u de *Tags* voor een specifieke installatie kopie wilt zien, gebruikt u de opdracht [AZ ACR repository show-Tags][az-acr-repository-show-tags] .

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

De uitvoer ziet er als volgt uit:

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Container Registry voorbereid voor gebruik met Azure Container Instances en hebt u een containerinstallatiekopie naar het register gepusht. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * Er is een exemplaar van Azure Container Registry (ACR) geïmplementeerd
> * Er is een containerinstallatiekopie getagd voor Azure Container Registry
> * Er is een installatiekopie naar Azure Container Registry geüpload

Ga verder met de volgende zelfstudie voor meer informatie over het implementeren van de container in Azure met behulp van Azure Container Instances:

> [!div class="nextstepaction"]
> [Container implementeren in Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli

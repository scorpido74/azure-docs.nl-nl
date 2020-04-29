---
title: Installatie kopie bouwen met native Buildpack in de Cloud
description: Gebruik de opdracht AZ ACR Pack build om een container installatie kopie te bouwen vanuit een app en naar Azure Container Registry te pushen, zonder een Dockerfile te gebruiken.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087082"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Een installatie kopie bouwen en pushen vanuit een app met behulp van een native Buildpack in de Cloud

De Azure CLI- `az acr pack build` opdracht maakt [`pack`](https://github.com/buildpack/pack) gebruik van het CLI-hulp programma, van [Buildpacks](https://buildpacks.io/), voor het bouwen van een app en het pushen van de installatie kopie naar een Azure container Registry. Deze functie biedt een optie om snel een container installatie kopie te bouwen op basis van de bron code van uw toepassing in node. js, Java en andere talen, zonder dat u een Dockerfile hoeft te definiëren.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de voor beelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.70 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="use-the-build-command"></a>De opdracht build gebruiken

Voer de opdracht [AZ ACR Pack build][az-acr-pack-build] uit om een container installatie kopie te bouwen en te pushen met behulp van de native Buildpacks van de Cloud. Terwijl u met `az acr pack build` de opdracht [AZ ACR build][az-acr-build] een installatie kopie van een Dockerfile-bron en gerelateerde code bouwt en pusht, kunt u rechtstreeks een toepassings bron structuur opgeven.

Geef ten minste het volgende op wanneer u uitvoert `az acr pack build`:

* Een Azure container Registry waarin u de opdracht uitvoert
* Een afbeeldings naam en-label voor de resulterende afbeelding
* Een van de [ondersteunde context locaties](container-registry-tasks-overview.md#context-locations) voor ACR-taken, zoals een lokale map, een github opslag plaats of een externe tarball
* De naam van een Buildpack Builder-installatie kopie die geschikt is voor uw toepassing. Azure Container Registry de installatie kopieën van de opbouw `cloudfoundry/cnb:0.0.34-cflinuxfs3` functie opslaat, zoals voor snellere builds.  

`az acr pack build`ondersteunt andere functies van ACR-taken, zoals het [uitvoeren van variabelen](container-registry-tasks-reference-yaml.md#run-variables) en [taak uitvoer logboeken](container-registry-tasks-logs.md) die zijn gestreamd en ook worden opgeslagen voor later ophalen.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Voor beeld: node. js-installatie kopie bouwen met Cloud Foundry Builder

In het volgende voor beeld wordt een container installatie kopie samengesteld uit een node. js-app in de [Azure-samples/nodejs-docs-Hello-World-](https://github.com/Azure-Samples/nodejs-docs-hello-world) opslag plaats met behulp van de `cloudfoundry/cnb:0.0.34-cflinuxfs3` Builder. Deze opbouw functie wordt opgeslagen in de cache van Azure Container Registry `--pull` . Daarom is een para meter niet vereist:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In dit voor beeld `node-app` wordt de installatie `1.0` kopie samengesteld met het label en gepusht naar het container register *myregistry* . In dit voor beeld wordt de naam van het doel register expliciet voor de naam van de installatie kopie geplaatst. Als u niets opgeeft, wordt de naam van de aanmeldings server van het REGI ster automatisch voor de naam van de installatie kopie geplaatst.

De opdracht uitvoer toont de voortgang van het bouwen en pushen van de installatie kopie. 

Nadat de installatie kopie is gemaakt, kunt u deze uitvoeren met docker, als u deze hebt geïnstalleerd. Meld u eerst aan bij uw REGI ster:

```azurecli
az acr login --name myregistry
```

Voer de installatie kopie uit:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Blader naar `localhost:1337` in uw favoriete browser om de voor beeld-web-app te bekijken. Druk `[Ctrl]+[C]` op om de container te stoppen.

## <a name="example-build-java-image-with-heroku-builder"></a>Voor beeld: een Java-installatie kopie bouwen met heroku Builder

In het volgende voor beeld wordt een container installatie kopie samengesteld uit de Java-app in de [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) opslag plaats met behulp van de `heroku/buildpacks:18` Builder. De `--pull` para meter geeft aan dat de opdracht de nieuwste installatie kopie van de opbouw functie moet ophalen. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In dit voor beeld `java-app` wordt de installatie kopie samengesteld met de run-id van de opdracht en gepusht naar het container register *myregistry* .

De opdracht uitvoer toont de voortgang van het bouwen en pushen van de installatie kopie. 

Nadat de installatie kopie is gemaakt, kunt u deze uitvoeren met docker, als u deze hebt geïnstalleerd. Meld u eerst aan bij uw REGI ster:

```azurecli
az acr login --name myregistry
```

Voer de installatie kopie uit en vervang uw afbeeldings code voor *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Blader naar `localhost:8080` in uw favoriete browser om de voor beeld-web-app te bekijken. Druk `[Ctrl]+[C]` op om de container te stoppen.


## <a name="next-steps"></a>Volgende stappen

Nadat u een container installatie kopie hebt gemaakt en `az acr pack build`gepusht met, kunt u deze als een wille keurige installatie kopie implementeren naar een doel van uw keuze. Azure-implementatie opties zijn onder andere uitvoeren in [app service](../app-service/containers/tutorial-custom-docker-image.md) of [Azure Kubernetes service](../aks/tutorial-kubernetes-deploy-cluster.md), onder andere.

Zie [builds van container installatie kopieën automatiseren en onderhoud uitvoeren met ACR-taken](container-registry-tasks-overview.md)voor meer informatie over functies van ACR-taken.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

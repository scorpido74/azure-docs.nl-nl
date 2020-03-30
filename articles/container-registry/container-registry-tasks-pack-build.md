---
title: Afbeelding maken met Cloud Native Buildpack
description: Gebruik de opdracht az acr pack build om een containerafbeelding vanuit een app te bouwen en naar Azure Container Registry te pushen, zonder een Dockerfile te gebruiken.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087082"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Een afbeelding vanuit een app maken en pushen met een Cloud Native Buildpack

De opdracht `az acr pack build` Azure [`pack`](https://github.com/buildpack/pack) CLI maakt gebruik van het GEREEDSCHAP CLI, van [Buildpacks,](https://buildpacks.io/)om een app te bouwen en de afbeelding in een Azure-containerregister te duwen. Deze functie biedt een optie om snel een containerafbeelding te bouwen op basis van de broncode van uw toepassing in Node.js, Java en andere talen zonder dat u een Dockerfile hoeft te definiëren.

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de voorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.70 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="use-the-build-command"></a>De opdracht Bouwen gebruiken

Voer de opdracht [az acr pack build uit][az-acr-pack-build] om een containerafbeelding te maken en te pushen met Cloud Native Buildpacks. Terwijl de [az acr build-opdracht][az-acr-build] een afbeelding van een Dockerfile-bron en gerelateerde code bouwt en pusht, geef `az acr pack build` je direct een toepassingsbronstructuur op.

Geef ten minste het volgende `az acr pack build`op wanneer u uitvoert:

* Een Azure-containerregister waarbij u de opdracht uitvoert
* Een afbeeldingsnaam en -tag voor de resulterende afbeelding
* Een van de [ondersteunde contextlocaties](container-registry-tasks-overview.md#context-locations) voor ACR-taken, zoals een lokale map, een GitHub-repo of een externe tarball
* De naam van een Buildpack builder afbeelding geschikt voor uw toepassing. Azure Container Registry caches `cloudfoundry/cnb:0.0.34-cflinuxfs3` builder images zoals for faster builds.  

`az acr pack build`ondersteunt andere functies van ACR-takenopdrachten, waaronder [uitvoeren van variabelen](container-registry-tasks-reference-yaml.md#run-variables) en [taakbeheerlogboeken](container-registry-tasks-logs.md) die worden gestreamd en ook worden opgeslagen voor later ophalen.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Voorbeeld: Afbeelding van Node.js bouwen met cloudfoundry-bouwer

In het volgende voorbeeld wordt een containerafbeelding gemaakt vanuit een Node.js-app in de [repo Azure-Samples/nodejs-docs-hello-world,](https://github.com/Azure-Samples/nodejs-docs-hello-world) met behulp van de `cloudfoundry/cnb:0.0.34-cflinuxfs3` bouwer. Deze bouwer wordt in de cache `--pull` opgeslagen door Azure Container Registry, dus een parameter is niet vereist:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In dit `node-app` voorbeeld wordt `1.0` de afbeelding met de tag gemaakt en wordt deze naar het *containerregister van myregistry* gepusht. In dit voorbeeld wordt de naam van het doelregister expliciet voorbereid op de afbeeldingsnaam. Als dit niet is opgegeven, wordt de naam van de registerlogserver automatisch voorbereid op de naam van de afbeelding.

Opdrachtuitvoer toont de voortgang van het bouwen en pushen van de afbeelding. 

Nadat de afbeelding is gebouwd, u deze uitvoeren met Docker, als u deze hebt geïnstalleerd. Eerste teken in uw register:

```azurecli
az acr login --name myregistry
```

Voer de afbeelding uit:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Blader `localhost:1337` naar in uw favoriete browser om de voorbeeldweb-app te bekijken. Druk `[Ctrl]+[C]` om de container te stoppen.

## <a name="example-build-java-image-with-heroku-builder"></a>Voorbeeld: Java-afbeelding bouwen met Heroku-bouwer

In het volgende voorbeeld wordt een containerafbeelding gemaakt vanuit de Java-app in `heroku/buildpacks:18` de [repo buildpack/sample-java-app,](https://github.com/buildpack/sample-java-app) met behulp van de bouwer. De `--pull` parameter geeft aan dat de opdracht de nieuwste builderafbeelding moet trekken. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In dit `java-app` voorbeeld wordt de afbeelding gemaakt die is getagd met de run-ID van de opdracht en wordt deze naar het *containerregister van myregistry* gepusht.

Opdrachtuitvoer toont de voortgang van het bouwen en pushen van de afbeelding. 

Nadat de afbeelding is gebouwd, u deze uitvoeren met Docker, als u deze hebt geïnstalleerd. Eerste teken in uw register:

```azurecli
az acr login --name myregistry
```

Voer de afbeelding uit en vervang uw afbeeldingstag door *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Blader `localhost:8080` naar in uw favoriete browser om de voorbeeldweb-app te bekijken. Druk `[Ctrl]+[C]` om de container te stoppen.


## <a name="next-steps"></a>Volgende stappen

Nadat u een containerafbeelding `az acr pack build`hebt gemaakt en met, u deze als elke afbeelding implementeren voor een doel naar keuze. Azure-implementatieopties omvatten het uitvoeren ervan in onder andere [App Service](../app-service/containers/tutorial-custom-docker-image.md) of Azure [Kubernetes Service.](../aks/tutorial-kubernetes-deploy-cluster.md)

Zie Het bouwen en onderhouden [van containerafbeeldingen automatiseren met ACR-taken](container-registry-tasks-overview.md)voor meer informatie over acr-taken.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

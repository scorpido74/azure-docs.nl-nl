---
title: Installatiekopieën vergrendelen
description: Stel kenmerken in voor een containerafbeelding of -opslagplaats, zodat deze niet kan worden verwijderd of overschreven in een Azure-containerregister.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659693"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Een containerafbeelding vergrendelen in een Azure-containerregister

In een Azure-containerregister u een afbeeldingsversie of een opslagplaats vergrendelen, zodat deze niet kan worden verwijderd of bijgewerkt. Als u een afbeelding of een opslagplaats wilt vergrendelen, werkt u de kenmerken ervan bij met de [acr-update van][az-acr-repository-update]de opdracht Azure CLI. 

Dit artikel vereist dat u de Azure CLI uitvoert in Azure Cloud Shell of lokaal (versie 2.0.55 of hoger aanbevolen). Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

> [!IMPORTANT]
> Dit artikel is niet van toepassing op het vergrendelen van een volledig register, bijvoorbeeld met **Instellingen > Sloten** in de Azure-portal of `az lock` opdrachten in de Azure CLI. Als u een registerbron vergrendelt, u geen gegevens maken, bijwerken of verwijderen in opslagplaatsen. Het vergrendelen van een register heeft alleen invloed op beheerbewerkingen, zoals het toevoegen of verwijderen van replicaties of het verwijderen van het register zelf. Meer informatie in [Bronnen vergrendelen om onverwachte wijzigingen te voorkomen](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scenario's

Standaard is een getagde afbeelding in Azure Container Registry *mutable,* dus met de juiste machtigingen u een afbeelding met dezelfde tag herhaaldelijk bijwerken en pushen naar een register. Containerafbeeldingen kunnen ook worden [verwijderd](container-registry-delete.md) als dat nodig is. Dit gedrag is handig wanneer u afbeeldingen ontwikkelt en een grootte voor uw register moet behouden.

Wanneer u echter een containerafbeelding implementeert voor productie, hebt u mogelijk een *onveranderlijke* containerafbeelding nodig. Een onveranderlijke afbeelding is een afbeelding die u niet per ongeluk verwijderen of overschrijven.

Zie [Aanbevelingen voor het taggen en versien van containerafbeeldingen](container-registry-image-tag-version.md) voor strategieën om afbeeldingen in uw register te taggen en te versien.

Gebruik de opdracht [az acr repository update][az-acr-repository-update] om repository-kenmerken in te stellen, zodat je kunt:

* Een afbeeldingsversie of een hele opslagplaats vergrendelen

* Een afbeeldingsversie of -opslagplaats beschermen tegen verwijdering, maar updates toestaan

* Leesbewerkingen (pull) op een afbeeldingsversie of een hele opslagplaats voorkomen

Zie de volgende secties voor voorbeelden. 

## <a name="lock-an-image-or-repository"></a>Een afbeelding of opslagplaats vergrendelen 

### <a name="show-the-current-repository-attributes"></a>De huidige repository-kenmerken weergeven
Voer de volgende opdracht [az acr repository uit][az-acr-repository-show] om de huidige kenmerken van een repository te bekijken:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>De huidige afbeeldingskenmerken weergeven
Voer de volgende opdracht [az acr repository uit][az-acr-repository-show] om de huidige kenmerken van een tag te bekijken:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Een afbeelding vergrendelen op tag

Voer de volgende opdracht [az acr repository uit][az-acr-repository-update] om de *myrepo/myimage:tag* image in *myregistry*te vergrendelen:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Een afbeelding vergrendelen door manifestdigest

Als u een *myrepo/myimage-afbeelding* wilt vergrendelen die is geïdentificeerd door `sha256:...`manifestdigest (SHA-256-hash, weergegeven als ), voert u de volgende opdracht uit. (Voer de [opdracht showmanifesten van AZ ACR Repository][az-acr-repository-show-manifests] uit om de manifestsamenvatting te vinden die is gekoppeld aan een of meer afbeeldingstags.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Een opslagplaats vergrendelen

Als u de *myrepo/myimage-opslagplaats* en alle afbeeldingen erin wilt vergrendelen, voert u de volgende opdracht uit:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Een afbeelding of opslagplaats beschermen tegen verwijdering

### <a name="protect-an-image-from-deletion"></a>Een afbeelding beschermen tegen verwijdering

Voer de volgende opdracht uit om de *myrepo/myimage:tag-afbeelding* te laten bijwerken, maar niet te verwijderen:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Een opslagplaats beschermen tegen verwijdering

Met de volgende opdracht wordt de *myrepo/myimage* repository ingesteld, zodat deze niet kan worden verwijderd. Afzonderlijke afbeeldingen kunnen nog steeds worden bijgewerkt of verwijderd.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Leesbewerkingen op een afbeelding of opslagplaats voorkomen

Voer de volgende opdracht uit om leesbewerkingen (pull) op de *myrepo/myimage:tag-afbeelding* te voorkomen:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Voer de volgende opdracht uit om leesbewerkingen op alle afbeeldingen in de *myrepo/myimage-opslagplaats* te voorkomen:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Een afbeelding of opslagplaats ontgrendelen

Voer de volgende opdracht uit om het standaardgedrag van de *myrepo/myimage:tag-afbeelding* te herstellen, zodat deze kan worden verwijderd en bijgewerkt:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Voer de volgende opdracht uit om het standaardgedrag van de *myrepo/myimage-opslagplaats* en alle afbeeldingen te herstellen, zodat deze kunnen worden verwijderd en bijgewerkt:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel heb je geleerd over het gebruik van de opdracht [az acr repository update][az-acr-repository-update] om verwijdering of het bijwerken van afbeeldingsversies in een repository te voorkomen. Zie de naslagverwijzing naar de az [acr repository-update.To][az-acr-repository-update] set additional attributes, see the az acr repository update command reference.

Als u de kenmerken wilt zien die zijn ingesteld voor een afbeeldingsversie of -opslagplaats, gebruikt u de opdracht [az acr repository show.][az-acr-repository-show]

Zie [Containerafbeeldingen verwijderen in Azure Container Registry][container-registry-delete]voor meer informatie over verwijderingsbewerkingen.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md


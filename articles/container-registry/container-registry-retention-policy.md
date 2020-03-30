---
title: Beleid om niet-gelabelde manifesten te behouden
description: Meer informatie over het inschakelen van een bewaarbeleid in uw Azure-containerregister voor het automatisch verwijderen van niet-gelabelde manifesten na een bepaalde periode.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454810"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Een bewaarbeleid instellen voor niet-gelabelde manifesten

Azure Container Registry biedt u de mogelijkheid om een *bewaarbeleid* in te stellen voor opgeslagen afbeeldingsmanifesten die geen bijbehorende tags hebben *(niet-gelabelde manifesten).* Wanneer een bewaarbeleid is ingeschakeld, worden niet-gelabelde manifesten in het register automatisch verwijderd na een aantal dagen die u hebt ingesteld. Deze functie voorkomt dat het register zich vult met artefacten die niet nodig zijn en helpt u opslagkosten te besparen. Als `delete-enabled` het kenmerk van een niet-gelabeld manifest is ingesteld op `false`, kan het manifest niet worden verwijderd en is het bewaarbeleid niet van toepassing.

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de opdrachtvoorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.74 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

> [!IMPORTANT]
> Deze functie is momenteel in preview en er zijn enkele [beperkingen van toepassing.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

> [!WARNING]
> Stel een bewaarbeleid in met zorg- verwijderde afbeeldingsgegevens is ONHERSTELBAAR. Als u systemen hebt die afbeeldingen op manifesten wissen (in tegenstelling tot de naam van de afbeelding), moet u geen bewaarbeleid instellen voor niet-gelabelde manifesten. Als u niet-gelabelde afbeeldingen verwijderde, wordt voorkomen dat deze systemen de afbeeldingen uit uw register halen. In plaats van te trekken door manifest, overwegen de vaststelling van een *unieke tagging* regeling, een [aanbevolen beste praktijk](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Preview-beperkingen

* Alleen **Premium** een Premium-containerregister kan worden geconfigureerd met een bewaarbeleid. Zie [SKU's](container-registry-skus.md)voor Azure Container Registry voor informatie over registerservicelagen.
* U alleen een bewaarbeleid instellen voor niet-gelabelde manifesten.
* Het bewaarbeleid is momenteel alleen van toepassing op manifesten die niet zijn getagd *nadat* het beleid is ingeschakeld. Bestaande niet-gecodeerde manifesten in het register zijn niet onderworpen aan het beleid. Zie voorbeelden in [Containerafbeeldingen verwijderen in Azure Container Registry](container-registry-delete.md)als u bestaande niet-gecodeerde manifesten wilt verwijderen.

## <a name="about-the-retention-policy"></a>Over het bewaarbeleid

Azure Container Registry doet referentietelling voor manifesten in het register. Wanneer een manifest niet is getagd, controleert het het bewaarbeleid. Als een bewaarbeleid is ingeschakeld, wordt een manifestverwijderingsbewerking in de wachtrij geplaatst, met een specifieke datum, afhankelijk van het aantal dagen dat in het beleid is ingesteld.

Een afzonderlijke wachtrijbeheertaak verwerkt voortdurend berichten, schalen indien nodig. Stel dat u twee manifesten, 1 uur uit elkaar, hebt ontlabeld in een register met een bewaarbeleid van 30 dagen. Twee berichten zouden in de wachtrij staan. Vervolgens, 30 dagen later, ongeveer 1 uur uit elkaar, de berichten zou worden opgehaald uit de wachtrij en verwerkt, ervan uitgaande dat het beleid nog steeds van kracht.

## <a name="set-a-retention-policy---cli"></a>Een bewaarbeleid instellen - CLI

In het volgende voorbeeld ziet u hoe u de Azure CLI gebruiken om een bewaarbeleid in te stellen voor niet-gelabelde manifesten in een register.

### <a name="enable-a-retention-policy"></a>Een bewaarbeleid inschakelen

Standaard is er geen bewaarbeleid ingesteld in een containerregister. Als u een bewaarbeleid wilt instellen of bijwerken, voert u de opdracht voor [het behoud van AZ acr config][az-acr-config-retention-update] uit in de Azure CLI. U een aantal dagen tussen 0 en 365 opgeven om de niet-gecodeerde manifesten te behouden. Als u geen aantal dagen opgeeft, stelt de opdracht een standaardinstelling in van 7 dagen. Na de bewaartermijn worden alle niet-gelabelde manifesten in het register automatisch verwijderd.

In het volgende voorbeeld wordt een bewaarbeleid van 30 dagen ingesteld voor niet-gelabelde manifesten in het register *van myregistry:*

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

In het volgende voorbeeld wordt een beleid ingesteld om een manifest in het register te verwijderen zodra het niet is getagd. Maak dit beleid door een bewaartermijn van 0 dagen in te stellen. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Een bewaarbeleid valideren

Als u het voorgaande beleid inschakelt met een bewaarperiode van 0 dagen, u snel controleren of niet-gelabelde manifesten worden verwijderd:

1. Duw een `hello-world:latest` testafbeelding naar uw register of vervang een andere testafbeelding naar keuze.
1. Tag de `hello-world:latest` afbeelding ontdien, bijvoorbeeld met de [opdracht az acr repository untag.][az-acr-repository-untag] Het niet-gelabelde manifest blijft in het register.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. Binnen enkele seconden wordt het niet-gelabelde manifest verwijderd. U de verwijdering verifiëren door manifesten in de repository op te nemen, bijvoorbeeld met behulp van de opdracht [az acr repository show-manifests.][az-acr-repository-show-manifests] Als de testafbeelding de enige in de repository was, wordt de repository zelf verwijderd.

### <a name="disable-a-retention-policy"></a>Een bewaarbeleid uitschakelen

Voer de opdracht az [acr config retention show][az-acr-config-retention-show] uit om het bewaarbeleid in een register te bekijken:

```azurecli
az acr config retention show --registry myregistry
```

Voer de opdracht [az acr config retention update][az-acr-config-retention-update] uit om `--status disabled`een bewaarbeleid in een register uit te schakelen en stelt u in:

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Een bewaarbeleid instellen - portal

U ook het bewaarbeleid van een register instellen in de [Azure-portal.](https://portal.azure.com) In het volgende voorbeeld ziet u hoe u de portal gebruiken om een bewaarbeleid in te stellen voor niet-gelabelde manifesten in een register.

### <a name="enable-a-retention-policy"></a>Een bewaarbeleid inschakelen

1. Navigeer naar uw Azure-containerregister. Selecteer **Onder Beleid**de optie **Behouden** (voorbeeld).
1. Selecteer **Ingeschakeld**in **Status**.
1. Selecteer een aantal dagen tussen 0 en 365 om de niet-gecodeerde manifesten te behouden. Selecteer **Opslaan**.

![Een bewaarbeleid inschakelen in Azure-portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Een bewaarbeleid uitschakelen

1. Navigeer naar uw Azure-containerregister. Selecteer **Onder Beleid**de optie **Behouden** (voorbeeld).
1. Selecteer **Uitgeschakeld**in **Status**. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over opties voor [het verwijderen van afbeeldingen en opslagplaatsen](container-registry-delete.md) in Azure Container Registry

* Meer informatie over het [automatisch verwijderen](container-registry-auto-purge.md) van geselecteerde afbeeldingen en manifesten uit een register

* Meer informatie over opties voor [het vergrendelen van afbeeldingen en manifesten](container-registry-image-lock.md) in een register

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests

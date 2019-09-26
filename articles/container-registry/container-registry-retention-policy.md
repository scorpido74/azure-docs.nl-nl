---
title: Beleid voor het bewaren van niet-gecodeerde manifesten in Azure Container Registry
description: Meer informatie over het inschakelen van een Bewaar beleid in uw Azure container Registry voor het automatisch verwijderen van niet-gecodeerde manifesten na een opgegeven periode.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306001"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Een Bewaar beleid voor niet-gecodeerde manifesten instellen

Azure Container Registry biedt u de mogelijkheid om een *Bewaar beleid* in te stellen voor opgeslagen afbeeldings manifesten die geen gekoppelde labels (niet-*gecodeerde manifesten*) hebben. Wanneer een Bewaar beleid is ingeschakeld, worden niet-gecodeerde manifesten in het REGI ster automatisch verwijderd na een aantal dagen dat u instelt. Deze functie voor komt dat het REGI ster wordt gevuld met artefacten die niet nodig zijn en helpt u bij het besparen van opslag kosten. Als het `delete-enabled` kenmerk van een niet-gelabeld manifest is ingesteld `false`op, kan het manifest niet worden verwijderd en is het Bewaar beleid niet van toepassing.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de opdracht voorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.74 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functie worden mogelijk nog gewijzigd voordat de functie algemeen beschikbaar wordt.

> [!WARNING]
> Een Bewaar beleid met caret instellen: verwijderde afbeeldings gegevens zijn onherstelbaar. Als u systemen hebt die installatie kopieën pullen per manifest Digest (in plaats van de naam van de installatie kopie), moet u geen Bewaar beleid voor niet-gecodeerde manifesten instellen. Als u niet-gelabelde afbeeldingen verwijdert, kunnen die systemen de installatie kopieën niet uit het REGI ster halen. In plaats van op manifest te halen, kunt u overwegen om een uniek schema voor *labels* te gebruiken, een [Aanbevolen best practice](container-registry-image-tag-version.md).

Zie [container installatie kopieën in azure container Registry verwijderen](container-registry-delete.md)als u de tags of manifesten met één afbeelding wilt verwijderen met Azure cli-opdrachten.

## <a name="preview-limitations"></a>Preview-beperkingen

* Alleen een **Premium** container Registry kan worden geconfigureerd met een Bewaar beleid. Zie [Azure container Registry sku's](container-registry-skus.md)voor meer informatie over de service lagen van het REGI ster.
* U kunt alleen een Bewaar beleid voor niet-gecodeerde manifesten instellen.

## <a name="set-a-retention-policy---cli"></a>Een Bewaar beleid instellen-CLI

In het volgende voor beeld ziet u hoe u de Azure CLI gebruikt om een Bewaar beleid voor niet-gecodeerde manifesten in een REGI ster in te stellen.

### <a name="enable-a-retention-policy"></a>Een Bewaar beleid inschakelen

Standaard is geen Bewaar beleid ingesteld in een container register. Als u een Bewaar beleid wilt instellen of bijwerken, voert u de opdracht [AZ ACR config retention update][az-acr-config-retention-update] uit in de Azure cli. U kunt een aantal dagen tussen 0 en 365 opgeven om de niet-gecodeerde manifesten te bewaren. Als u geen aantal dagen opgeeft, stelt de opdracht een standaard waarde van 7 dagen in. Na de Bewaar periode worden alle niet-gelabelde manifesten in het REGI ster automatisch verwijderd.

In het volgende voor beeld wordt een Bewaar beleid van 30 dagen voor niet-gecodeerde manifesten in het REGI ster *myregistry*:

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

In het volgende voor beeld wordt een beleid ingesteld voor het verwijderen van een manifest in het REGI ster zodra het een label heeft. Dit beleid maken door een Bewaar periode van 0 dagen in te stellen:

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Een Bewaar beleid uitschakelen

Als u wilt zien hoe het Bewaar beleid is ingesteld in een REGI ster, voert u de opdracht [AZ ACR config retentie show][az-acr-config-retention-show] uit:

```azurecli
az acr config retention show --name myregistry
```

Als u een Bewaar beleid wilt uitschakelen in een REGI ster, voert u de opdracht [AZ ACR config retention update][az-acr-config-retention-update] uit en stelt `--status disabled`u het volgende in:

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Een Bewaar beleid instellen-Portal

U kunt ook het Bewaar beleid van een REGI ster instellen in de [Azure Portal](https://portal.azure.com). In het volgende voor beeld ziet u hoe u de portal kunt gebruiken om een Bewaar beleid voor niet-gecodeerde manifesten in een REGI ster in te stellen.

### <a name="enable-a-retention-policy"></a>Een Bewaar beleid inschakelen

1. Navigeer naar uw Azure container Registry. Onder **beleids regels**selecteert u **bewaren** (preview-versie).
1. Selecteer in **status** **ingeschakeld**.
1. Selecteer een aantal dagen tussen 0 en 365 om de niet-gecodeerde manifesten te bewaren. Selecteer **Opslaan**.

![Een Bewaar beleid inschakelen in Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Een Bewaar beleid uitschakelen

1. Navigeer naar uw Azure container Registry. Onder **beleids regels**selecteert u **bewaren** (preview-versie).
1. Selecteer in **status**de optie **uitgeschakeld**. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over opties voor het [verwijderen van installatie kopieën en opslag](container-registry-delete.md) plaatsen in azure container Registry

* Meer informatie over het [automatisch verwijderen](container-registry-auto-purge.md) van geselecteerde installatie kopieën en manifesten uit een REGI ster

* Meer informatie over opties voor het [vergren delen van afbeeldingen en manifesten](container-registry-image-lock.md) in een REGI ster

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show

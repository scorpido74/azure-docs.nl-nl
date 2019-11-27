---
title: Service lagen en Sku's
description: Meer informatie over de functies en limieten in de Basic-, Standard-en Premium-Service lagen (Sku's) van Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456271"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry Sku's

Azure Container Registry (ACR) is beschikbaar in meerdere service lagen, ook wel Sku's genoemd. Deze Sku's bieden voorspel bare prijzen en verschillende opties om af te stemmen op de capaciteits-en gebruiks patronen van uw privé-docker-REGI ster in Azure.

| SKU | Beschrijving |
| --- | ----------- |
| **Basic** | Een rendabel toegangspunt voor ontwikkelaars die meer willen leren over Azure Container Registry. Basis registers hebben dezelfde programmatische mogelijkheden als standaard en Premium (zoals integratie van Azure Active Directory [verificatie](container-registry-authentication.md#individual-login-with-azure-ad), het [verwijderen van afbeeldingen][container-registry-delete]en [webhooks][container-registry-webhook]). De inbegrepen opslag-en afbeeldings doorvoer zijn echter het meest geschikt voor lagere gebruiks scenario's. |
| **Standard** | Standaard-registers bieden dezelfde mogelijkheden als basis, met meer inbegrepen opslag en de door Voer van de installatie kopie. Standard-registers moeten voldoen aan de behoeften van de meeste productiescenario's. |
| **Premium** | Premium-registers bieden de hoogste hoeveelheid inbegrepen opslag en gelijktijdige bewerkingen, waardoor scenario's met hoge volumes mogelijk zijn. Naast een hogere afbeeldings doorvoer, voegt Premium functies toe, zoals [geo-replicatie][container-registry-geo-replication] voor het beheren van één REGI ster in meerdere regio's, [vertrouwen van inhoud](container-registry-content-trust.md) voor het ondertekenen van installatie kopieën, [firewalls en virtuele netwerken (preview)](container-registry-vnet.md) tot Beperk de toegang tot het REGI ster. |

De Basic-, Standard-en Premium-Sku's bieden allemaal dezelfde programmatische mogelijkheden. Ze profiteren ook van [installatie kopie opslag][container-registry-storage] die volledig wordt beheerd door Azure. Het kiezen van een SKU op een hoger niveau biedt meer prestaties en schaal baarheid. Met meerdere service lagen kunt u aan de slag met Basic en vervolgens converteren naar Standard en Premium als uw register gebruik toeneemt.

## <a name="sku-features-and-limits"></a>SKU-functies en-limieten

De volgende tabel bevat een overzicht van de functies en limieten van de service lagen Basic, Standard en Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Sku's wijzigen

U kunt de SKU van een REGI ster wijzigen met de Azure CLI of in de Azure Portal. U kunt vrij verplaatsen tussen Sku's als de SKU waarnaar u overschakelt, de vereiste maximale opslag capaciteit heeft. 

### <a name="azure-cli"></a>Azure CLI

Als u wilt scha kelen tussen Sku's in de Azure CLI, gebruikt u de opdracht [AZ ACR update][az-acr-update] . Als u bijvoorbeeld wilt overschakelen op Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

Selecteer in het **overzicht** van het container register in de Azure Portal **Update**en selecteer vervolgens een nieuwe **SKU** in de vervolg keuzelijst SKU.

![SKU'S voor container Registry bijwerken in Azure Portal][update-registry-sku]

## <a name="pricing"></a>Prijzen

Zie [container Registry prijzen][container-registry-pricing]voor prijs informatie over elk van de Azure container Registry sku's.

Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie over de prijzen voor gegevens overdracht. 

## <a name="next-steps"></a>Volgende stappen

**Azure Container Registry-schema**

Ga naar het [ACR-plan][acr-roadmap] op github voor informatie over aanstaande functies in de service.

**Azure Container Registry UserVoice**

Bied en stem op nieuwe functie suggesties in [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md

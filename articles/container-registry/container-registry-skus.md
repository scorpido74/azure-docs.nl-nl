---
title: Servicelagen en SKU's
description: Meer informatie over de functies en limieten in de Basis-, Standaard- en Premium-servicelagen (SKU's) van Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456271"
---
# <a name="azure-container-registry-skus"></a>SKU's voor Azure-containerregister

Azure Container Registry (ACR) is beschikbaar in meerdere servicelagen, bekend als SKU's. Deze SKU's bieden voorspelbare prijzen en verschillende opties voor het afstemmen op de capaciteit en gebruikspatronen van uw privé Docker-register in Azure.

| SKU | Beschrijving |
| --- | ----------- |
| **Basic** | Een rendabel toegangspunt voor ontwikkelaars die meer willen leren over Azure Container Registry. Basisregisters hebben dezelfde programmatische mogelijkheden als Standard en Premium (zoals Azure Active [Directory-authenticatie-integratie,](container-registry-authentication.md#individual-login-with-azure-ad) [imageverwijdering][container-registry-delete]en [webhooks).][container-registry-webhook] De meegeleverde opslag- en beelddoorvoer zijn echter het meest geschikt voor scenario's voor minder gebruik. |
| **Standaard** | Standaardregisters bieden dezelfde mogelijkheden als Basic, met een verhoogde opslag- en beelddoorvoer. Standard-registers moeten voldoen aan de behoeften van de meeste productiescenario's. |
| **Premium** | Premiumregisters bieden de hoogste hoeveelheid opgenomen opslag en gelijktijdige bewerkingen, waardoor scenario's met een hoog volume mogelijk zijn. Naast een hogere beelddoorvoer voegt Premium functies toe zoals [geo-replicatie][container-registry-geo-replication] voor het beheren van één register in meerdere regio's, [contentvertrouwensrelatie](container-registry-content-trust.md) voor het ondertekenen van afbeeldingen, [firewalls en virtuele netwerken (preview)](container-registry-vnet.md) om de toegang tot het register te beperken. |

De Basic,Standard en Premium SKU's bieden allemaal dezelfde programmatische mogelijkheden. Ze profiteren ook allemaal van [beeldopslag][container-registry-storage] die volledig door Azure wordt beheerd. Het kiezen van een SKU op een hoger niveau biedt meer prestaties en schaal. Met meerdere servicelagen u aan de slag met Basic en vervolgens converteren naar Standard en Premium naarmate uw registergebruik toeneemt.

## <a name="sku-features-and-limits"></a>SKU-functies en -limieten

In de volgende tabel worden de functies en limieten van de serviceniveaus Basic, Standard en Premium beschreven.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Veranderende SKU's

U de SKU van een register wijzigen met de Azure CLI of in de Azure-portal. U vrij schakelen tussen SKU's zolang de SKU waarnaar u overstapt de vereiste maximale opslagcapaciteit heeft. 

### <a name="azure-cli"></a>Azure-CLI

Als u wilt gaan tussen SKU's in de Azure CLI, gebruikt u de opdracht [AZ ACR-update.][az-acr-update] Bijvoorbeeld om over te schakelen naar Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

Selecteer in het **containerregisteroverzicht** in de Azure-portal De optie **Bijwerken**en selecteer vervolgens een nieuwe **SKU** in de vervolgkeuzelijst SKU.

![Containerregister SKU bijwerken in Azure-portal][update-registry-sku]

## <a name="pricing"></a>Prijzen

Zie [Containerregistry-prijzen][container-registry-pricing]voor prijsinformatie over elk van de SKU's voor Azure Container Registry.

Zie [Bandbreedteprijsdetails](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie over de prijzen voor gegevensoverdrachten. 

## <a name="next-steps"></a>Volgende stappen

**Roadmap voor Azure-containerregister**

Bezoek de [ACR Roadmap][acr-roadmap] op GitHub voor informatie over aankomende functies in de service.

**Azure Container Registry UserVoice**

Verzenden en stemmen over nieuwe functie suggesties in [ACR UserVoice][container-registry-uservoice].

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

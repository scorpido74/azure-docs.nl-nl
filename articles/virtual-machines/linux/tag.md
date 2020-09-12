---
title: Een virtuele Azure-machine labelen met behulp van de CLI
description: Meer informatie over het coderen van een virtuele machine met behulp van de Azure CLI.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a417e7cff4c7afb601861ddfe09eec171f0cf15
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320610"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Een virtuele Linux-machine in azure labelen
In dit artikel worden verschillende manieren beschreven om een virtuele Linux-machine in azure te labelen via het Resource Manager-implementatie model. Tags zijn door de gebruiker gedefinieerde sleutel/waarde-paren die rechtstreeks kunnen worden geplaatst op een resource of resource groep. Azure ondersteunt momenteel Maxi maal 50 Tags per resource en resource groep. Labels kunnen worden geplaatst op een resource op het moment dat ze worden gemaakt of worden toegevoegd aan een bestaande resource. Let op: Tags worden alleen ondersteund voor resources die zijn gemaakt via het Resource Manager-implementatie model.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Labelen met Azure CLI

Als u wilt beginnen, hebt u de nieuwste [Azure cli](/cli/azure/install-azure-cli) geïnstalleerd en aangemeld bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index#az-login).

U kunt alle eigenschappen van een bepaalde virtuele machine, inclusief de tags, weer geven met behulp van deze opdracht:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Als u een nieuwe VM-tag wilt toevoegen via de Azure CLI, kunt u de `azure vm update` opdracht gebruiken samen met de para meter tag **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Als u tags wilt verwijderen, kunt u de para meter **--Remove** gebruiken in de `azure vm update` opdracht.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Nu we tags hebben toegepast op onze resources Azure CLI en de portal, gaan we de gebruiks gegevens bekijken om de tags in de facturerings portal te bekijken.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Volgende stappen
* Zie [Azure Resource Manager Overview][Azure Resource Manager Overview] en [Tags gebruiken om uw Azure-resources te organiseren][Using Tags to organize your Azure Resources]voor meer informatie over het coderen van uw Azure-resources.
* Als u wilt zien hoe Tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources, raadpleegt u [inzicht in uw Azure-factuur][Understanding your Azure Bill] en [krijgt u inzicht in uw Microsoft Azure Resource verbruik][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]: ../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../cost-management-billing/manage/usage-rate-card-overview.md

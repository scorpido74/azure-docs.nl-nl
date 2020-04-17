---
title: Een azure Linux-virtuele machine taggen
description: Meer informatie over het taggen van een azure Linux virtuele machine die is gemaakt in Azure met behulp van het Resource Manager-implementatiemodel.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: fd4a93f4c0b2f052fe5c9890bee01e5da0dcead2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460950"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Een virtuele Linux-machine taggen in Azure
In dit artikel worden verschillende manieren beschreven om een virtuele Linux-machine in Azure te taggen via het Implementatiemodel Resource Manager. Tags zijn door de gebruiker gedefinieerde sleutel-/waardeparen die rechtstreeks op een resource of een resourcegroep kunnen worden geplaatst. Azure ondersteunt momenteel maximaal 50 tags per resource- en resourcegroep. Tags kunnen op het moment van maken op een resource worden geplaatst of aan een bestaande resource worden toegevoegd. Houd er rekening mee dat tags alleen worden ondersteund voor bronnen die zijn gemaakt via het implementatiemodel van Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagging met Azure CLI

Om te beginnen moet u de nieuwste [Azure CLI-installatie](/cli/azure/install-azure-cli) en ingelogd zijn op een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index#az-login)

U alle eigenschappen voor een bepaalde virtuele machine, inclusief de tags, bekijken met behulp van deze opdracht:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Als u een nieuwe VM-tag wilt toevoegen `azure vm update` via de Azure CLI, u de opdracht samen met de tagparameter **gebruiken --ingesteld:**

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Als u tags wilt verwijderen, u `azure vm update` de parameter **-verwijderen** in de opdracht gebruiken.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Nu we tags hebben toegepast op onze bronnen Azure CLI en de Portal, laten we eens kijken naar de gebruiksgegevens om de tags in de factureringsportal te zien.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Volgende stappen
* Zie Overzicht van [Azure Resource Manager][Azure Resource Manager Overview] en Het [gebruik van tags voor][Using Tags to organize your Azure Resources]meer informatie over het taggen van uw Azure-resources.
* Zie [Inzicht in uw Azure-factuur][Understanding your Azure Bill] en inzicht krijgen in [uw Microsoft Azure-bronverbruik][Gain insights into your Microsoft Azure resource consumption]als u wilt zien hoe tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources.

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
